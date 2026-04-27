# Funcionalidade: Agendar envio de mensagem no Chatwoot

Este pacote contém um patch e os arquivos alterados para adicionar o botão **Agendar mensagem** no composer da conversa.

## O que foi implementado

- Botão com ícone de calendário ao lado do botão de envio.
- Modal simples com campo de data e hora.
- Nova rota backend: `POST /api/v1/accounts/:account_id/conversations/:conversation_id/scheduled_messages`.
- Nova tabela `scheduled_messages`.
- Job `ScheduledMessages::SendJob`, que usa o `Messages::MessageBuilder` original do Chatwoot para criar a mensagem no horário agendado.
- Endpoint para listar pendentes e cancelar:
  - `GET /scheduled_messages`
  - `DELETE /scheduled_messages/:id`

## Arquivos alterados/criados

- `config/routes.rb`
- `app/javascript/dashboard/api/inbox/message.js`
- `app/javascript/dashboard/store/modules/conversations/actions.js`
- `app/javascript/dashboard/components/widgets/WootWriter/ReplyBottomPanel.vue`
- `app/javascript/dashboard/components/widgets/conversation/ReplyBox.vue`
- `app/models/scheduled_message.rb`
- `app/controllers/api/v1/accounts/conversations/scheduled_messages_controller.rb`
- `app/jobs/scheduled_messages/send_job.rb`
- `db/migrate/20260427000000_create_scheduled_messages.rb`

## Como aplicar com patch

Na raiz do projeto Chatwoot:

```bash
patch -p1 < chatwoot_agendamento_mensagens.patch
bundle exec rails db:migrate
```

Depois reinicie os serviços web/worker.

## Observação importante

Nesta primeira versão, o agendamento foi limitado a **mensagens de texto**. O botão fica bloqueado quando houver anexos ou áudio gravado, porque anexos exigem tratamento adicional de ActiveStorage/retention para garantir que o arquivo continue disponível até o horário do envio.
