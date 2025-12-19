# Mensagens Interativas (Botões e Listas)

Este projeto já suporta o envio de **botões** e **listas** pelo WhatsApp.
Abaixo está um guia prático para uso via API.

## Endpoints

- `POST /message/sendButtons/:instanceName` — Envia mensagem com botões
- `POST /message/sendList/:instanceName` — Envia mensagem com listas (Single Select)

Todos os endpoints exigem header `apikey` com o token global.

## Autenticação

- Header: `apikey: <SEU_TOKEN_GLOBAL>`
- Você encontra o token global nas configs (`AUTHENTICATION.API_KEY.KEY`).

## Botões — Tipos e Limites

- `reply`: até **3** por mensagem; **não misturar** com outros tipos
- `url`: abre uma URL
- `call`: inicia uma chamada telefônica
- `copy`: copia um código
- `pix`: pagamento PIX, apenas **1** por mensagem; **não misturar** com outros tipos

### Exemplo (reply)
```json
{
  "number": "5599999999999",
  "title": "Escolha uma opção",
  "description": "Selecione abaixo:",
  "footer": "AtendAI",
  "buttons": [
    { "type": "reply", "id": "opt1", "displayText": "Opção 1" },
    { "type": "reply", "id": "opt2", "displayText": "Opção 2" }
  ]
}
```

### Exemplo (url)
```json
{
  "number": "5599999999999",
  "title": "Acesse:",
  "buttons": [
    { "type": "url", "displayText": "Abrir site", "url": "https://example.com" }
  ]
}
```

### Exemplo (pix)
```json
{
  "number": "5599999999999",
  "title": "Pagamento",
  "buttons": [
    { "type": "pix", "name": "Minha Loja", "key": "chave@pix.com", "keyType": "email" }
  ]
}
```

## Listas — Estrutura

- Suporta múltiplas `sections` com `rows`
- Cada `row` possui `title`, `rowId` e `description` (opcional)

### Exemplo
```json
{
  "number": "5599999999999",
  "title": "Lista de opções",
  "description": "Escolha um item",
  "buttonText": "Ver opções",
  "footerText": "AtendAI",
  "sections": [
    {
      "title": "Grupo A",
      "rows": [
        { "title": "Item 1", "rowId": "item1", "description": "Descrição opcional" },
        { "title": "Item 2", "rowId": "item2" }
      ]
    },
    {
      "title": "Grupo B",
      "rows": [
        { "title": "Item 3", "rowId": "item3" }
      ]
    }
  ]
}
```

## Leitura de Respostas

- Botões: `buttonsResponseMessage.selectedButtonId` ou `templateButtonReplyMessage.selectedId`
- Listas: `listResponseMessage.singleSelectReply.selectedRowId`

O helper `src/utils/getConversationMessage.ts` expõe essas propriedades já normalizadas.

## Dicas

- `number` no formato internacional (apenas dígitos): `DDI + DDD + número`
- Use o arquivo `Extras/examples/interactive.http` para testar rapidamente (REST Client)
- Payloads exemplo em: `Extras/examples/buttons.json` e `Extras/examples/list.json`

## Erros Comuns

- Misturar `reply` com outros tipos de botões (não permitido)
- Usar mais de 3 botões `reply`
- Usar mais de 1 botão `pix` ou misturar `pix` com outros tipos