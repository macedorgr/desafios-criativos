# Desafio Criativo: Planejando Automações com N8N

## ⚠️ Nota sobre a Execução do Desafio

Devido a limitações técnicas no ambiente local referentes à instalação do **Docker**, não foi possível prosseguir com o ambiente de execução necessário para instanciar e rodar o servidor local do **N8N**. 

Por conta da impossibilidade de subir a imagem/container do N8N via Docker, os testes operacionais, validação dos nós e execução prática do fluxo planejado não puderam ser concluídos nesta etapa.

---

## 📝 Planejamento da Automação (Prompt Projetado)

Apesar das restrições de ambiente local, a etapa de estruturação da ideia e definição das diretrizes da automação foi elaborada conforme a proposta metodológica do desafio:

```text
Atue como um especialista em N8N.

Crie uma automação para qualificação e roteamento automático de tickets de suporte recebidos por formulário.

Público:
Equipe de Atendimento e Suporte Técnico.

Ferramentas envolvidas:
Typeform, Google Sheets, OpenAI (ChatGPT) e Discord/Slack.

Fluxo:
1. Receber uma nova submissão de formulário de suporte.
2. Analisar o sentimento e categorizar a urgência do problema usando IA (OpenAI).
3. Registrar o chamado categorizado na planilha de acompanhamento.
4. Enviar uma notificação para o canal do Discord/Slack com a prioridade e o resumo do chamado.

Regras:
- Se a urgência for classificada como "Alta", enviar uma notificação com menção imediata ao time de plantão.
- Ignorar envios sem descrição detalhada ou sem e-mail de contato do cliente.

Explique quais nós do N8N devem ser utilizados e a lógica de funcionamento do workflow.
```