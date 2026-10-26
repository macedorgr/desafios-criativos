# 🎯 Desafio Criativo: Planejando Automações com N8N

## 📋 Sobre o Projeto
Este repositório contém o planejamento completo e a documentação do **Desafio Criativo: Planejando Automações com N8N**, proposto pela **DIO (Digital Innovation One)**. 

O objetivo principal desta entrega é demonstrar a capacidade de estruturar uma automação complexa utilizando engenharia de prompts, definindo contexto, etapas do fluxo, integração entre sistemas e regras de negócio de forma clara e rigorosa.

---

## ⚠️ Nota sobre a Execução Prática do Workflow

Durante o desenvolvimento do desafio, **não foi possível realizar a execução prática, testes operacionais e validação dos nós no ambiente do N8N**.

### **Justificativa Técnica:**
* **Barreira de Infraestrutura Local:** Ocorreram falhas e incompatibilidades técnicas na instalação e inicialização do **Docker Desktop** no ambiente operacional local (dependências de virtualização via BIOS/VT-x e problemas na integração com o subsistema WSL2).
* **Impacto no N8N:** Como a instância do N8N seria subida localmente via container Docker (`docker run` / Docker Compose), a impossibilidade de executar o serviço impediu a criação do ambiente de runtime para conectar as APIs e testar o fluxo em tempo real.

Apesar da limitação técnica na camada de infraestrutura, o **planejamento metodológico, a engenharia do prompt final e o detalhamento da arquitetura de nós do N8N** foram concluídos integralmente a seguir.

---

## 🧱 Passo a Passo do Desafio Criativo

### 🧱 Passo 1: Definição da Automação

**Quero criar uma automação no N8N para:**
Qualificação, categorização por IA e roteamento automático de tickets de suporte recebidos por formulário.

* **Público ou responsável:** Equipe de Atendimento e Suporte Técnico.
* **Resultado esperado:** Triagem automatizada dos chamados com base no nível de urgência, registro padronizado dos dados em uma planilha de acompanhamento e notificação imediata via canal de comunicação para o time responsável.

---

### 🧱 Passo 2: Contexto e Regras de Negócio

* **Ferramentas envolvidas:** 
  Typeform (ou Google Forms), OpenAI API (ChatGPT), Google Sheets e Slack / Discord (ou E-mail).

* **Fluxo desejado:**
  1. Capturar os dados enviando uma resposta no formulário de atendimento.
  2. Enviar a descrição do problema para a API da OpenAI para análise de sentimento, categorização da dúvida e classificação de urgência (Alta, Média, Baixa).
  3. Salvar o ticket já qualificado e categorizado na planilha do Google Sheets.
  4. Rotear e enviar uma notificação personalizada para o time de suporte no Slack/Discord.

* **Regras importantes:**
  * Ignorar submissões que não possuam um e-mail de contato válido ou que estejam sem descrição do problema.
  * Se a urgência for classificada como **"Alta"**, acionar uma menção imediata (`@channel` / `@here`) no canal de suporte do Slack para atendimento prioritário.
  * Se for uma dúvida simples (Urgência "Baixa"), apenas registrar na planilha e enviar um e-mail com resposta padrão/FAQ automática ao cliente.

---

### 🧱 Passo 3: Prompt Final Estruturado

Below is the complete prompt generated to direct an AI to build and explain the N8N workflow:

```text
Atue como um especialista em N8N e engenheiro de automação de processos.

Crie uma automação para qualificação e roteamento automático de tickets de suporte recebidos por formulário.

Público:
Equipe de Atendimento e Suporte Técnico.

Ferramentas envolvidas:
Typeform / Google Forms, OpenAI (GPT-4o), Google Sheets e Slack / Discord.

Fluxo:
1. Receber uma nova submissão do formulário de suporte.
2. Analisar o conteúdo e categorizar a urgência do problema (Alta, Média, Baixa) e a área responsável usando a API da OpenAI.
3. Registrar o chamado completo e qualificado em uma planilha do Google Sheets.
4. Rotear o ticket e enviar uma notificação no Slack com as informações sintetizadas.

Regras:
- Ignorar registros que não apresentem um e-mail de contato válido ou campo de descrição preenchido.
- Se a urgência for "Alta", enviar notificação no canal do Slack marcando a equipe de plantão em destaque.
- Se a urgência for "Baixa" ou dúvida simples, enviar uma resposta automática por e-mail com um link para a base de conhecimento.

Explique quais nós do N8N devem ser utilizados, a ordem das conexões e a lógica de funcionamento do workflow.
```

---

## 🛠️ Detalhamento Técnico da Arquitetura no N8N

Para a implementação real do fluxo planejado no N8N, a arquitetura visual seria composta pelos seguintes nós e conexões:

```
[Form Trigger] ──► [If / Filter] ──► [OpenAI Node] ──► [Google Sheets] ──► [Switch] ──┬──► [Slack Node (Alta)]
                                                                                      └──► [Gmail Node (Baixa)]
```

### **Descrição dos Nós Utilizados:**

1. **`n8n-nodes-base.typeformTrigger` (ou `Webhook Node`):**
   * *Função:* Atua como o gatilho (Trigger) do workflow, escutando em tempo real novas entradas de formulário.
2. **`n8n-nodes-base.filter` / `If Node`:**
   * *Função:* Valida a regra de negócio inicial, checando se os campos de e-mail e descrição contêm dados válidos antes de prosseguir.
3. **`@n8n/n8n-nodes-langchain.openAi` (ou `HTTP Request Node`):**
   * *Função:* Processa a descrição do usuário utilizando prompt estruturado para retornar um JSON padronizado contendo `categoria`, `sentimento` e `nivel_urgencia`.
4. **`n8n-nodes-base.googleSheets`:**
   * *Função:* Executa a operação de `Append Row` para gravar os dados estruturados do chamado em uma planilha corporativa.
5. **`n8n-nodes-base.switch`:**
   * *Função:* Divide o fluxo de execução com base no nível de urgência retornado pela IA (`Alta`, `Média`, `Baixa`).
6. **`n8n-nodes-base.slack` / `Discord Node`:**
   * *Função:* Envia mensagens formatadas via Webhook para canais específicos, aplicando alerta prioritário para tickets com urgência "Alta".
7. **`n8n-nodes-base.gmail`:**
   * *Função:* Dispara e-mails automáticos com confirmação de abertura de chamado ou links para autoatendimento.

---

## 🚀 Plano de Ação para Resolução da Barreira Técnica

Para superação do problema de infraestrutura e viabilização de testes em entregas futuras, foram traçadas as seguintes estratégias:

1. **Adequação do Ambiente Local (Docker & WSL2):**
   * Acessar a BIOS/UEFI da máquina para habilitar as instruções de virtualização hardware (Intel VT-x / AMD-V).
   * Atualizar o Kernel do subsistema Linux no Windows via `wsl --update` e reconfigurar o Docker Desktop para utilizar o backend WSL2.
2. **Uso de Soluções Gerenciadas (Cloud):**
   * Migrar os testes para o [n8n Cloud](https://n8n.io/cloud/), utilizando o período de avaliação gratuita sem necessidade de configuração de servidores ou containers locais.
   * Realizar deploy simplificado do container N8N em plataformas PaaS com plano gratuito/baixo custo (ex.: Render, Railway ou VPS).

---

## 📚 Referências e Links Úteis

* 📖 [Documentação Oficial do n8n](https://docs.n8n.io/)
* 🐳 [Guia de Instalação do n8n via Docker](https://docs.n8n.io/hosting/installation/docker/)
* 🤖 [Integrações e Nós de IA no n8n](https://docs.n8n.io/integrations/builtin/cluster-nodes/sub-nodes/n8n-nodes-langchain.lmchatopenai/)
* 🎓 [Plataforma Digital Innovation One (DIO)](https://www.dio.me/)

---
*Projeto elaborado para o Desafio Criativo de Automação com N8N da DIO.*
