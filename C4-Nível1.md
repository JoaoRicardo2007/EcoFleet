# C4 Nível 1 - Diagrama de Contexto

```mermaid
mindmap
  root((Plataforma de Mobilidade))
    Pessoas
      Cliente
        Inicia e encerra corridas
        Paga via PIX ou cartão
      Operador de Frota
        Monitora veículos
        Acompanha bateria e corridas
    Sistemas externos
      Veículos IoT
        Enviam GPS e bateria a cada 5s
        Protocolo MQTT sobre TLS
      PSP de Pagamento
        Processa PIX e cartão
        Confirma pagamento via webhook
      Serviço de Notificação
        Push
        SMS
        E-mail
```
