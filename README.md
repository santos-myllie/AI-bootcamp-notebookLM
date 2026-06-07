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

### 🛠️ Engenharia de Prompts e Lições Aprendidas ("Cicatrizes")

Esta seção documenta o processo de refinamento das interações com a Inteligência Artificial. Foram registrados os desvios de contexto, as armadilhas de código sugeridas pelos modelos e as soluções aplicadas para extrair o melhor alinhamento com a arquitetura oficial da ServiceNow.

---

#### 📌 Caso de Teste 01: Validação de Ordem de Execução (Client vs. Server)
*   **Prompt Inicial (Testado):** 
    > *"Me diga a ordem de execução quando um formulário do ServiceNow é salvo."*
*   **Resposta da IA / Dificuldade Encontrada:** O modelo gerou uma resposta genérica e superficial. Ele misturou componentes de interface visual (UI Policies) com motores de fluxo de backend (Workflow/Flow Designer), omitindo o momento exato em que a transação grava os dados no banco e confundindo a precedência entre lógicas de script e lógicas declarativas no navegador.
*   **Prompt Refinado (Solução Aplicada):** 
    > *"Atue como um arquiteto ServiceNow. Quero que você liste a ordem cronológica exata de execução no ecossistema ServiceNow quando um usuário clica em 'Submit'. Separe estritamente o que acontece no Browser (Client-side) e o que acontece no Servidor (Server-side), destacando onde entram as UI Policies, Client Scripts e as Business Rules (Before e After)."*
*   **Resultado Obtido:** Resposta 100% precisa e sequencial. O modelo estruturou o ciclo de vida corretamente: `Client Script (onSubmit)` ➡️ `UI Policy` ➡️ `Envio ao Servidor` ➡️ `Before Business Rule` ➡️ `Escrita/Commit no Banco` ➡️ `After Business Rule`.
*   **Referência Técnica:** ServiceNow Core Architecture — *Platform Execution Order*.

---

#### 📌 Caso de Teste 02: Desempenho no Client-Side e Requisições Síncronas (GlideRecord no Navegador)
*   **Prompt Inicial (Testado):** 
    > *"Como eu faço para buscar o e-mail de um usuário dentro de um Client Script no ServiceNow?"*
*   **Resposta da IA / Dificuldade Encontrada:** A IA sugeriu instanciar um objeto `new GlideRecord('sys_user')` diretamente dentro do script de cliente. Embora esse código execute com sucesso em laboratórios isolados, ele viola as boas práticas da plataforma e garante reprovação no exame CAD. Consultas síncronas ao banco de dados feitas pelo navegador travam a interface do usuário (browser freeze), degradando a experiência (UX).
*   **Prompt Refinado (Solução Aplicada):** 
    > *"Preciso buscar o e-mail de um usuário em um Client Script de forma assíncrona, seguindo as boas práticas recomendadas para a certificação CAD. Monte a estrutura do código utilizando a API GlideAjax no cliente em conjunto com um Script Include no servidor que estenda a classe 'AbstractAjaxProcessor'. Explique o papel do método 'getXMLAnswer'."*
*   **Resultado Obtido:** O modelo abandonou o padrão nocivo e gerou a arquitetura correta. O Client Script passou a disparar uma chamada assíncrona sem bloquear a UI, delegando a consulta de backend para o Script Include reutilizável, que processa a query e devolve o dado via função de *callback*.
*   **Referência Técnica:** ServiceNow Developer Portal — *Asynchronous Data Retrieval via GlideAjax*.

---

#### 📌 Caso de Teste 03: Hierarquia de Resolução e Conflito de Curingas em Regras de Acesso (ACLs)
*   **Prompt Inicial (Testado):** 
    > *"Crie uma regra de segurança ACL para que apenas o admin veja os campos de uma tabela customizada."*
*   **Resposta da IA / Dificuldade Encontrada:** O modelo gerou isoladamente uma regra do tipo `tabela.*` (nível de campo) associada à role corporativa. Contudo, na infraestrutura de segurança do ServiceNow, se o usuário não possuir uma permissão explícita para acessar o contêiner de linha da tabela (`tabela.None`), o sistema bloqueia o acesso imediatamente no topo da hierarquia. A IA falhou ao ignorar a necessidade de "abrir a porta" da tabela antes de ditar as regras dos campos internos.
*   **Prompt Refinado (Solução Aplicada):** 
    > *"Explique o processo de resolução de segurança quando o sistema avalia ACLs de tabela e de campo. Com base nisso, configure o par correto de ACLs (utilizando .None e .*) para garantir que a role 'x_custom_app.admin' tenha acesso total de leitura a uma tabela escopada e a todos os seus campos internos, detalhando a hierarquia de avaliação."*
*   **Resultado Obtido:** O modelo corrigiu a abordagem e documentou a "Tríade de Segurança" (Roles, Condições e Scripts). Ele demonstrou que a avaliação é top-down: primeiro configura-se uma ACL para `tabela.None` (permissão ao registro) para, em seguida, processar as restrições ou liberações específicas em `tabela.*` (permissão aos atributos).
*   **Referência Técnica:** ServiceNow Core Architecture — *Contextual Security and ACL Evaluation Process*.

### 🔑 Prompts de Ouro (Golden Prompts)

Esta seção compila os prompts avançados e estruturados que foram validados para transformar o modelo de IA em um tutor dinâmico, indo além de respostas simples e forçando o aprofundamento em APIs críticas da plataforma.

*   **Prompt para Sabatina Reversa (Simulado Dinâmico):**
    > *"Atue como um instrutor oficial da ServiceNow especialista na certificação CAD. Não me dê respostas. Em vez disso, faça uma pergunta de múltipla escolha por vez sobre cenários complexos envolvendo `Script Includes` e `GlideAjax`. Espere eu responder no chat. Se eu acertar, aumente o nível de dificuldade e explique o detalhe técnico. Se eu errar, faça o breakdown do código me mostrando onde falhei."*
*   **Prompt para Desconstrução de Código (Code Review):**
    > *"Analise o seguinte trecho de script de servidor [Inserir Código] sob a perspectiva de performance e governança de instâncias da ServiceNow. Identifique se há violações de escopo, uso de queries síncronas ocultas, ou riscos de loops infinitos (como `current.update` incorreto). Forneça a versão refatorada seguindo as boas práticas da release Australia (2026)."*
*   **Prompt para Mapeamento de Casos de Borda (Edge Cases):**
    > *"Crie uma matriz comparativa detalhada explicando exatamente quando um desenvolvedor CAD deve optar por uma Async Business Rule em vez de um Flow Designer (Flow) para processamento em segundo plano, considerando volumetria de dados, tratamento de erros e rastreabilidade."*

---

### 📔 Diário de Bordo (Lições Aprendidas & Troubleshooting)

Registro das principais barreiras contextuais encontradas durante a curadoria de dados com a IA e as estratégias de engenharia aplicadas para mitigar alucinações e respostas obsoletas.

*   **Desvio de Legado vs. Modernidade ( Releases Antigas):** 
    *   *Dificuldade:* Inicialmente, ao perguntar sobre ferramentas de desenvolvimento, o modelo sugeria extensivamente o uso do *Studio* clássico e padrões de código baseados puramente em ES5 (JavaScript antigo).
    *   *Correção:* Foi necessário aplicar uma trava de contexto no *System Prompt*, forçando a IA a considerar as implementações do novo **ServiceNow SDK** e da extensão **ServiceNow Fluent** introduzidas nas arquiteturas mais recentes (família *Australia*).
*   **Alucinação de Métodos de API (Invenção de Funções):**
    *   *Dificuldade:* Em misturas complexas de Client e Server, a IA frequentemente misturava os escopos de objetos e inventava métodos inexistentes, como tentar usar `g_form.getReference()` com funções de callback mal estruturadas que quebravam o escopo.
    *   *Correção:* Adota-se a técnica de **Few-Shot Prompting**, passando um exemplo curto de código homologado e correto antes de pedir para o modelo gerar uma nova lógica baseada em `GlideAjax`.
*   **Perda de Contexto em Cadeias Longas (Context Drift):**
    *   *Dificuldade:* Após 10 ou 15 iterações de chat simulando questões de prova, o modelo começava a esquecer o peso oficial dos blocos do blueprint da certificação CAD, tornando as perguntas repetitivas ou focadas demais em um único assunto (como apenas ITSM).
    *   *Correção:* Implementação de comandos de "reset de atenção", limpando o histórico irrelevante e relembrando as diretrizes de escopo a cada bloco de 5 questões através de bullet points estruturados.
      
## 📖 4. Miniguia de Estudo e Execução
*Consulte esta seção para entender o fluxo de estudos sugerido, cronogramas de simulados e orientações de como interagir com o NotebookLM para obter feedbacks em tempo real sobre seus erros e acertos.*
