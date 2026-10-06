# Plataforma de Mobilidade: Telemetria e Cobrança Instantânea

Documentação de arquitetura de uma plataforma que ingere telemetria de veículos em tempo real e cobra o cliente automaticamente via PIX ou cartão ao encerrar a corrida. O projeto usa o **modelo C4** e diagramas em **Mermaid**, renderizados direto no GitHub.

## O desafio

> Ingestão contínua de telemetria (GPS e bateria a cada 5s por veículo) e cobrança instantânea via PIX/Cartão ao encerrar a corrida.

O trabalho entrega:

1. **Drivers:** 2 ASRs (requisitos arquiteturalmente significativos) e 1 restrição de negócio.
2. **C4 Nível 1:** Diagrama de Contexto.
3. **C4 Nível 2:** Diagrama de Contêineres.

## Documentação

| Parte | Descrição | Link |
|-------|-----------|------|
| Drivers | ASRs e restrição de negócio | [docs/01-drivers](docs/01-drivers/README.md) |
| C4 Nível 1 | Contexto: pessoas e sistemas externos | [docs/02-c4-contexto](docs/02-c4-contexto/README.md) |
| C4 Nível 2 | Contêineres: serviços, bancos e mensageria | [docs/03-c4-containers](docs/03-c4-containers/README.md) |

## Resumo dos drivers

- **ASR 1, ingestão:** 10.000 veículos simultâneos (~2.000 msgs/s, picos de 3x), dado disponível em menos de 2s e sem perda de mensagens.
- **ASR 2, cobrança:** iniciar em até 3s, confirmar em até 10s, de forma idempotente (sem cobrança duplicada) e com 99,95% de disponibilidade.
- **Restrição de negócio:** nada de PAN/CVV na plataforma. Tokenização via PSP com PCI-DSS, PIX via PSP autorizado pelo Banco Central e conformidade com a LGPD para dados de localização.

## Principais decisões de arquitetura

- **MQTT + Kafka** para desacoplar a ingestão dos demais serviços e absorver picos sem perder mensagens.
- **TimescaleDB** para a série temporal de GPS e bateria, com gravação em lote.
- **Redis** para consulta rápida da última posição do veículo.
- **Cobrança orientada a eventos** (`RideFinished`), com chave de idempotência e padrão **outbox** para evitar cobrança duplicada.
- **Adaptador de Pagamento** isolado, que trata webhooks e retentativas do PSP.
- **Tokenização no PSP**, então a plataforma guarda apenas o token do cartão.

## Tecnologias

| Camada | Tecnologia |
|--------|-----------|
| Back-end | C# / ASP.NET Core |
| Mensageria | Apache Kafka, EMQX (MQTT) |
| Bancos de dados | PostgreSQL, TimescaleDB |
| Cache | Redis |
| Gateway de API | YARP / Kong |
| Front-end | Flutter ou React Native (app), React (painel) |
| Documentação | C4 Model + Mermaid |

## Estrutura do repositório

```
.
├── README.md
└── docs
    ├── 01-drivers
    │   └── README.md
    ├── 02-c4-contexto
    │   └── README.md
    └── 03-c4-containers
        └── README.md
```

## Como visualizar os diagramas

Os diagramas estão em blocos ` ```mermaid ` e o GitHub renderiza automaticamente ao abrir cada README. Para editar e testar fora do GitHub, use o [Mermaid Live Editor](https://mermaid.live).

## Autores

- **João Ricardo Fernandes Vieira dos Santos // 16037766**
- **Kawê Keven dos Santos Figueredo // 16036728**
- **Alan Sousa dos Santos // 16034571**
- **Thiago José Teles Gois // 16037933**
- **Paulo Guilherme Oliveira de Lima // 16035427**
