# Playbooks Ansible — instalação nativa sem Docker

Quatro playbooks independentes para instalar serviços **diretamente no sistema operacional** de máquinas virtuais Linux:

| Arquivo | Serviço | Método | Porta padrão no laboratório |
|---|---|---|---|
| `playbooks/01-qdrant.yml` | Qdrant | Binário + systemd | 6333, loopback |
| `playbooks/02-neo4j.yml` | Neo4j Community | Repositório APT + systemd | 7474/7687, revisar rede |
| `playbooks/03-kafka.yml` | Apache Kafka KRaft | Tarball + Java + systemd | 9092, loopback |
| `playbooks/04-rabbitmq.yml` | RabbitMQ | Repositório APT + systemd | 5672, revisar rede |

## Escopo

Projeto demonstrativo para **Ubuntu 24.04 / Debian compatível**, x86_64; RabbitMQ também declara suporte a Ubuntu 22.04, Debian 12/13. **Não usa Docker**. Os exemplos Kafka e Qdrant vinculam-se a localhost e não expõem a rede. Cada serviço exige dimensionamento, TLS, credenciais, firewall e configuração de produção antes do uso real. Evite instalar todos os serviços em uma única VM com pouca RAM.

## Preparação

Na máquina controladora:

```bash
sudo apt update && sudo apt install -y ansible openssh-client git
ansible --version
```

Na VM gerenciada: acesso SSH, usuário com `sudo` e Python 3. Ajuste `inventory/hosts.ini` incluindo a VM no grupo `[servicos]` (o grupo vem sem hosts ativos para evitar instalações acidentais). **Não envie senhas/chaves privadas ao GitHub.**

## Comandos

```bash
ansible -i inventory/hosts.ini servicos -m ping
ansible-playbook --syntax-check playbooks/01-qdrant.yml
ansible-playbook -i inventory/hosts.ini playbooks/01-qdrant.yml -K
ansible-playbook -i inventory/hosts.ini playbooks/02-neo4j.yml -K
ansible-playbook -i inventory/hosts.ini playbooks/03-kafka.yml -K
ansible-playbook -i inventory/hosts.ini playbooks/04-rabbitmq.yml -K
```

`-K` solicita a senha sudo, se necessária. Teste **um playbook por vez**, de preferência em VMs descartáveis isoladas. Para testar diretamente no Linux Mint, adicione `localhost ansible_connection=local` ao grupo `[servicos]` no inventário, **somente se você realmente quiser instalar os serviços no computador**.

## Evidências recomendadas

```bash
sudo systemctl status qdrant --no-pager
sudo systemctl status neo4j --no-pager
sudo systemctl status kafka --no-pager
sudo systemctl status rabbitmq-server --no-pager
sudo rabbitmq-diagnostics -q ping
curl --fail http://127.0.0.1:6333/healthz
```

## Pontos para revisão antes da homologação

- **Qdrant:** binário fixado em `v1.20.0` para Linux x86_64; confirmar nome do artefato da release, checksum e política de upgrades. A API sem autenticação está limitada ao localhost neste exemplo.
- **Neo4j:** pacote Community proveniente do repositório oficial. **Definir senha inicial com `neo4j-admin dbms set-initial-password` antes de iniciar pela primeira vez**, ou seguir o processo de troca de senha do produto. Não automatizamos credenciais no Git. Recomenda-se fixar versão após homologação.
- **Kafka:** nó único KRaft para laboratório, sem TLS/SASL nem alta disponibilidade. Dados em `/var/lib/kafka`. A configuração de rede está limitada a localhost.
- **RabbitMQ:** repositórios Team RabbitMQ; conferir suporte e compatibilidade Erlang/versão. Plugin de UI opcional, não ativado. Configurar contas, rede e políticas separadamente.
- **Segurança:** os exemplos não representam um blueprint de produção. Faça revisão de firewall, secrets com Ansible Vault, logs, observabilidade, backup, disponibilidade e hardening antes de usar em máquinas reais.

## Status de validação

Código de referência preparado para revisão. **Nenhuma instalação real em VM foi executada neste ambiente.** É necessário executar `--syntax-check` e testes de integração no ambiente-alvo antes de declarar sucesso.

## Documentação oficial

- Qdrant: https://qdrant.tech/documentation/installation/
- Neo4j: https://neo4j.com/docs/operations-manual/current/installation/linux/debian/
- Kafka: https://kafka.apache.org/quickstart/
- RabbitMQ: https://www.rabbitmq.com/docs/install-debian
