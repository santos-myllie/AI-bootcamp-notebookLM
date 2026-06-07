# AI-Bootcamp: ServiceNow CAD Preparation

Este repositório e notebook foram desenvolvidos para centralizar materiais de estudo, resumos estruturados e simulados práticos focados na preparação para a certificação **ServiceNow Certified Application Developer (CAD)**.

---

## 🚀 1. Contexto e Objetivos
O objetivo principal deste projeto é atuar como um acelerador de aprendizado utilizando Inteligência Artificial (NotebookLM). O ambiente foi projetado para:
* Simular o padrão técnico das questões da banca examinadora da ServiceNow.
* Validar conhecimentos em cenários complexos de desenvolvimento escopado.
* Servir como uma base de conhecimento consultiva e portátil para o dia a dia de estudos.

## 📚 2. Curadoria de Fontes (Knowledge Base)
A base de dados de IA foi alimentada e treinada com um ecossistema rigoroso de informações:
* **Blueprints e Resumos Técnicos:** Guias detalhados sobre os principais tópicos do exame (Scoping, UI, Client/Server Scripting, ACLs e Update Sets).
* **Bancos de Questões:** Simulados reais e cenários práticos revisados e comentados pela comunidade para mitigar respostas incorretas ou desatualizadas.

## 🛠️ 3. Engenharia de Prompts e Lições Aprendidas ("Cicatrizes")
Esta seção documenta a evolução da interação com os modelos de linguagem, refinando a extração de dados:
* **Prompts de Ouro:** Engenharia de comandos e perguntas estratégicas utilizadas para sabatinar o estudante ou aprofundar conceitos de APIs específicas (como `GlideRecord` e `GlideAjax`).
* **Diário de Bordo (Cicatrizes):** Registro de prompts que falharam, respostas inconsistentes da IA, dificuldades de contexto e como esses desvios foram corrigidos para alcançar o melhor output técnico.

* Caso de Teste 01: Validação de Ordem de Execução (Client vs. Server)
Prompt Inicial (Testado):

"Me diga a ordem de execução quando um formulário do ServiceNow é salvo."

Resposta da IA / Dificuldade Encontrada: A IA gerou uma lista genérica misturando UI Policies com ferramentas de backend (como instâncias de Workflow), mas esqueceu de especificar o momento exato em que o banco de dados é alterado e confundiu a ordem entre Client Scripts e UI Policies.

Prompt Refinado (Solução):

"Atue como um arquiteto ServiceNow. Quero que você liste a ordem cronológica exata de execução no ecossistema ServiceNow quando um usuário clica em 'Submit'. Separe estritamente o que acontece no Browser (Client-side) e o que acontece no Servidor (Server-side), destacando onde entram as UI Policies, Client Scripts e as Business Rules (Before e After)."

Resultado Obtido: Resposta 100% precisa. O modelo mapeou a sequência correta: Client Script (onSubmit) -> UI Policy -> Envio ao Servidor -> Before Business Rule -> Escrita no Banco -> After Business Rule.

Referência Técnica: Documentação Oficial ServiceNow (Família Australia / Yokohama) - Platform Execution Order.

## 📖 4. Miniguia de Estudo e Execução
*Consulte esta seção para entender o fluxo de estudos sugerido, cronogramas de simulados e orientações de como interagir com o NotebookLM para obter feedbacks em tempo real sobre seus erros e acertos.*
