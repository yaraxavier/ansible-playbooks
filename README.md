# Ansible Playbooks

Automação de instalação e configuração de serviços de infraestrutura em servidores Linux utilizando **Ansible**, sem Docker.

O projeto contém quatro playbooks independentes para provisionamento nativo de serviços em máquinas virtuais ou servidores físicos compatíveis.

## Serviços

| Serviço | Finalidade | Instalação |
|---|---|---|
| Qdrant | Banco de dados vetorial para buscas semânticas e aplicações de IA | Binário e systemd |
| Neo4j | Banco de dados orientado a grafos para armazenar entidades e relacionamentos | APT e systemd |
| Apache Kafka | Plataforma de streaming e processamento de eventos | Java, KRaft e systemd |
| RabbitMQ | Broker de mensagens para comunicação assíncrona entre aplicações | Erlang, APT e systemd |

## Estrutura

```text
ansible-playbooks/
├── playbooks/
│   ├── 01-qdrant.yml
│   ├── 02-neo4j.yml
│   ├── 03-kafka.yml
│   └── 04-rabbitmq.yml
├── inventory/
│   └── hosts.example.ini
├── docs/
│   └── ambiente-teste.md
├── .gitignore
└── README.md
```

## Requisitos

- Ansible instalado na máquina controladora
- Servidor Ubuntu 24.04 ou distribuição Debian compatível
- Acesso SSH ao servidor
- Usuário com permissões `sudo`
- Python 3 na máquina gerenciada
- Recursos de CPU, memória e disco adequados ao serviço

## Utilização

Clone o repositório:

```bash
git clone https://github.com/yaraxavier/ansible-playbooks.git
cd ansible-playbooks
```

Crie o inventário local:

```bash
cp inventory/hosts.example.ini inventory/hosts.ini
```

Edite `inventory/hosts.ini` com o endereço IP e o usuário SSH do servidor.

Verifique a conectividade:

```bash
ansible servicos -i inventory/hosts.ini -m ping
```

Execute o playbook desejado:

**Qdrant**
```bash
ansible-playbook -i inventory/hosts.ini playbooks/01-qdrant.yml
```

**Neo4j**
```bash
ansible-playbook -i inventory/hosts.ini playbooks/02-neo4j.yml
```

**Apache Kafka**
```bash
ansible-playbook -i inventory/hosts.ini playbooks/03-kafka.yml
```

**RabbitMQ**
```bash
ansible-playbook -i inventory/hosts.ini playbooks/04-rabbitmq.yml
```

Os playbooks utilizam elevação de privilégios (`become`). Em servidores que exigem senha para `sudo`, adicione `-K` ao comando.

## Funcionamento

Cada playbook realiza a instalação das dependências, a configuração do serviço e seu gerenciamento pelo `systemd`.

As configurações são aplicadas diretamente no sistema operacional. Os serviços não dependem de contêineres e podem ser administrados pelas ferramentas padrão do Linux.

Os playbooks foram desenvolvidos para execução independente, permitindo configurar somente o serviço necessário.

## Ambiente de validação

Os serviços foram testados em uma máquina virtual Ubuntu Server 24.04, hospedada na AWS EC2, com arquitetura x86_64.

Foram realizados testes de inicialização, funcionamento dos serviços e reexecução dos playbooks.

Os detalhes do ambiente estão disponíveis em `docs/ambiente-teste.md`.

## Referências

- [Qdrant](https://qdrant.tech/documentation/)
- [Neo4j](https://neo4j.com/docs/)
- [Apache Kafka](https://kafka.apache.org/documentation/)
- [RabbitMQ](https://www.rabbitmq.com/docs)
- [Ansible](https://docs.ansible.com/)
