
# Ambiente de testes — Ansible

## Infraestrutura

- Provedor de testes: AWS EC2
- Sistema operacional: Ubuntu Server 24.04 LTS
- Arquitetura: x86_64
- Tipo final da instância: m7i-flex.large
- Memória RAM: 8 GiB
- Armazenamento: EBS gp3, 20 GiB
- Ferramenta de automação: Ansible
- Comunicação: SSH
- Implantação: nativa, sem Docker

## Serviços validados

| Serviço | Teste funcional | Idempotência |
|---|---|---|
| Qdrant | API HTTP respondeu | changed=0 |
| Neo4j | Consulta Cypher executada | changed=0 |
| Apache Kafka | Produção e consumo de mensagem | changed=0 |
| RabbitMQ | Publicação e consumo de mensagem | changed=0 |

## Limitações conhecidas

- O provisionamento completo ainda não foi validado em uma VM limpa.
- O Neo4j teve sua primeira configuração de senha realizada manualmente antes da revisão do playbook.
- O Kafka apresentou status de falha no systemd durante o desligamento, apesar de ter encerrado seus componentes.
- A configuração foi validada em Ubuntu na AWS; ainda falta testar em outro ambiente de virtualização.
- Os playbooks são exemplos de laboratório e exigem revisão de segurança antes de uso em produção.
