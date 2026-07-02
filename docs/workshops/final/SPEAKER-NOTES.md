# Speaker Notes — A Grande Final (F5 + F6) · notas do instrutor

> **Notas do apresentador/facilitador** · Workshop "Living Lab Azure-Native" (FIFA 2026 Tickets) · **A ÚLTIMA aula** — a Grande Final.
> **Use junto com:** [`slides.md`](./slides.md) (o deck que você projeta) · [`STORYBOARD-APRESENTACAO.md`](./STORYBOARD-APRESENTACAO.md) (o prompt de 11 slides do `.pptx`, DNA das Quartas) · [`docs/runbooks/final-portal-guide.md`](../../runbooks/final-portal-guide.md) (o guia de portal que a turma segue nos momentos hands-on).
> **Stories:** [3.3](../../stories/3.3.story.md) · [3.4](../../stories/3.4.story.md) · **Arquitetura:** ADE-008 (re-arquitetura da Final, sem orquestração externa) · ADE-009 (X-Gateway-Key)

**Como estas notas se organizam (LEIA ISTO):** cada seção abaixo corresponde a **um slide do deck** (`slides.md`), na **mesma ordem em que você clica**. O cabeçalho de cada seção repete o título do slide e indica o número do slide equivalente no storyboard/`.pptx` (`≈ Storyboard SN`). Onde o slide dispara um **momento hands-on**, há um bloco **▶ PAUSA O DECK → GUIA** que te leva ao `final-portal-guide.md` e te diz quando **voltar** ao deck. Assim o que você fala bate exatamente com o que está projetado.

> **Premissa de fidelidade (Art. IV — No Invention):** todo número, nome de tool, nó e arquivo aqui bate com o deck, o guia e o código real. Se a turma perguntar "onde isso está no código?", aponte: `FifaTicketTools.cs` (as 7 tools), `gemini.ts` (chatbot), `FlowEventType.cs` / `flowNodes.ts` (os 5 nós). **A Final é:** sem orquestração externa (n8n **removido**), **7 tools read-only**, **`gemini-2.5-flash`**, **5 nós**, notificação pós-compra **inline** na Function Consumer, chave Gemini **só no proxy server-side**, `X-Gateway-Key` fechando o bypass ao McpServer. **Identidade (Story 3.5 / ADE-007):** `bcrypt` + `entra_oid` na **mesma linha `users`** (vínculo, não substituição), JIT **`GET /api/v2/me`** (resolve-or-provision), fence **`CiamOnly`** (admin nunca provisiona cliente). Não invente APIs, rotas, claims, tools nem nós.

---

## Mapa deck → lab (cole no flip chart · sessão contínua ≈ 5–6h)

| Fase da aula | Slides do deck | O que acontece | Tempo | Acumulado |
|---|---|---|---|---|
| **Abertura** | 1–4 (capa, recap, frase, só o novo) | Reancorar na jornada + vender as 4 tecnologias novas | 20 min | 0:20 |
| **F5 conceitos** | 5–10 (divisor F5, MCP, 7 tools, RAG, RAG≠vetor, regra de ouro) | Ensinar MCP + RAG + a regra de ouro **por construção** | 30 min | 0:50 |
| **▶ F5 hands-on** | *(deck pausado)* → guia **Fases 1–4** | Subir o McpServer + chatbot Gemini | 2h00–2h50 | ~3:40 |
| **F5 clímax** | slide 11 (regra de ouro AO VIVO) → guia **Fase 5** | A demo "cria um alerta pra mim" — segurança por construção | 20 min | ~4:00 |
| **Intervalo** | — | — | 15 min | ~4:15 |
| **F6 conceitos** | 12–16 (divisor F6, MID + cofre KV, identidade unificada, observabilidade, 5 nós) | Managed Identity (+ Key Vault, entrega Blindar) · identidade unificada v1↔CIAM · observabilidade/SignalR | 25 min | ~4:40 |
| **▶ F6 hands-on** | *(deck pausado)* → guia **Fases 6–8** | Criar SignalR/MID + FlowEvents + rota `/flow` | 1h30–2h30 | ~6:30 |
| **F6 clímax** | slides 16–18 (5 nós, onde foi o n8n, simplificar) → guia **Fase 9** | O smoke: a bolinha atravessa 5 nós ao vivo | 30 min | ~7:00 |
| **Fechamento** | 19–22 (arquitetura, 5 missões, encerramento, obrigado) → guia entrega | Fork + retrospectiva das 5 missões + celebração | 25 min | ~7:25 |

> **Mindset do facilitador:** o deck é **enxuto de propósito** — as **quatro tecnologias novas** (MCP · RAG · Managed Identity/Key Vault · observabilidade SignalR) + os **conceitos-chave** (regra de ouro, **identidade unificada**, "onde foi o n8n"). Ele **não reexplica** compra async, gateway ou a identidade das Quartas — o slide de identidade só **ACRESCENTA** a unificação nova (base v1 ↔ CIAM, Story 3.5). O ouro didático da Final está em **dois momentos ao vivo**: a **regra de ouro** (slide 11, Fase 5 do guia) e o **smoke dos 5 nós** (slides 16–18, Fase 9 do guia). Entregue os dois com a turma ainda com energia.
>
> **A restrição dura (repita no slide 2):** a Final **ADICIONA**. Nada das Oitavas/Quartas deixa de funcionar. A compra é a mesma; a Final só acrescenta **voz** (chatbot que lê) e **visão** (visualizador que mostra).
>
> **Regra de ouro da arquitetura (repita antes do fork):** o **Portal cria os recursos vazios; o Actions só publica código**. Nenhum recurso Azure nasce do workflow.

---

# ABERTURA (slides 1–4 · 20 min)

## Slide 1 — CAPA: "A Grande Final · Voz & Visão" · ≈ Storyboard S1 [~3 min]

**Como abrir (fala):**
> "Chegamos à **Grande Final**. Nas Oitavas você construiu a **compra**; nas Quartas, o **gateway** e a **identidade**. Hoje, nas duas últimas fases, a aplicação ganha **voz** e **visão**: um chatbot que conversa com o estado real da Copa, e uma tela onde a própria arquitetura **se acende** enquanto uma compra atravessa o sistema."

**Enfatize:** esta é a **última aula** — o fecho do Living Lab inteiro. A faixa `Oitavas (F1) → Quartas (F2/F3) → Final (F5/F6)` no rodapé é o mapa da jornada.

**Gancho → próximo slide:** "Antes de mostrar o que é novo, vamos lembrar de onde viemos — em um slide só."

## Slide 2 — "A jornada até aqui (recap em 1 slide)" · ≈ Storyboard S1 (subtítulo da capa; sem slide dedicado no `.pptx`) [~4 min]

**Fala:** percorra a tabela de baixo para cima: Oitavas = a compra assíncrona (Function → Service Bus → Consumer → SQL); Quartas = o gateway YARP guardião + identidade (CIAM cliente / admin workforce); a Final = voz + visão.

**O ponto que NÃO pode faltar (a restrição dura):**
> "Leiam o rodapé do slide: a Final **ADICIONA**. **Retro-compatibilidade é regra dura** — nada das fases anteriores deixa de funcionar. Gateway, identidade, backend v1 e SQL das Quartas continuam **idênticos**. Hoje só empilhamos **observação** por cima: um chatbot que **lê**, e um visualizador que **mostra**."

**Gancho:** "E o que exatamente é novo? Uma frase primeiro — depois a lista."

## Slide 3 — "A frase do dia" · ≈ frase-âncora (rodapé do S1 no `.pptx`) [~2 min]

**Fala (leia a frase projetada, devagar):**
> *"Voz para perguntar. Visão para enxergar. E segurança **por construção** — não por roteamento."*

**Enfatize:** guarde a expressão **"segurança por construção, não por roteamento"**. Ela volta no clímax do F5 (slide 11). "Um chatbot que só tem sentidos; uma tela onde a arquitetura se acende."

**Gancho:** "Concretamente, são quatro tecnologias novas. Só elas. Nada de reexplicar o que vocês já dominam."

## Slide 4 — "Só o que é NOVO nesta aula" · ≈ Storyboard S2 (Stack da fase) [~6 min]

**Fala:** "As fases anteriores já deram compra async, gateway, identidade, Container Apps e SQL. **Hoje** são **quatro** peças novas — e o deck inteiro é só sobre elas:"

1. **MCP** — o protocolo que dá **ferramentas** ao LLM.
2. **RAG (por tool-use)** — o chatbot **recupera** um fato real **antes** de responder.
3. **Managed Identity + Key Vault** — os apps leem os segredos do **cofre** (e a telemetria) **sem chave em claro**.
4. **Observabilidade ao vivo** — Flow Visualizer + **Azure SignalR**.

**Diga o combinado de honestidade:** "Este deck **não** reexplica o que já foi dado. Se em algum momento você achar que estou repetindo Oitavas/Quartas, me corrija — não é o objetivo."

> **Nota:** no `.pptx` (storyboard, DNA das Quartas) esta lista é o slide **S2 — "Stack da fase · As tecnologias que vamos usar"**, com uma linha por tecnologia (MCP, RAG por tool-use, `gemini-2.5-flash`, Managed Identity + Key Vault, `Azure SignalR` Free/Default). No reveal (`slides.md`) o recap (Slide 2) e a lista (Slide 4) são slides separados. Fale a mesma coisa nos dois formatos.

**Gancho → F5:** "Vamos abrir pela **voz**. Bloco F5."

---

# F5 — VOZ · CONCEITOS (slides 5–10 · ~30 min)

> **Objetivo do bloco (fala de abertura):**
> "Vamos entender três coisas antes de pôr a mão: **o que é MCP**, **o que é RAG por tool-use** e por que **a regra de ouro — o chatbot nunca escreve no banco — passa a valer por construção**. Depois vocês sobem o McpServer e o chatbot no Portal, e no fim eu provo tudo isso **ao vivo**."
>
> **Frase âncora do bloco:** *"O LLM raciocina; o McpServer tem os fatos. E o McpServer só tem **sentidos**."*

## Slide 5 — Divisor "Bloco F5 — VOZ" · ≈ abertura de S4 [~1 min]

**Fala:** "F5 = a aplicação ganha **voz**. MCP, RAG e a regra de ouro **por construção**. Cor de acento verde-azulado = leitura, sentidos."

**Gancho:** "A primeira peça nova: o protocolo que transforma um LLM de 'papagaio' em algo que **usa ferramentas**."

## Slide 6 — "TECNOLOGIA 1 DE 4 · MCP (Model Context Protocol)" · ≈ Storyboard S4 [~7 min]

**Fala (siga a estrutura do slide):**
- **O que é?** "Um **protocolo padrão** para expor 'ferramentas' que um LLM descobre e chama em runtime — pensem no **USB-C das integrações de IA**: um plugue só, qualquer ferramenta."
- **Como funciona (aponte o diagrama):** `Chatbot → tools/list (descobre) → tools/call (executa, JSON-RPC) → McpServer (interno, atrás do gateway) → SELECT no SQL`.
- **Os 4 recursos:** `tools/list` (descoberta em runtime) · `tools/call` (JSON-RPC 2.0, chamada tipada) · **Server .NET** (SDK oficial, **ingress interno**) · **7 tools read-only** (todas `[McpServerTool(ReadOnly = true)]`).

**Momento de segurança a plantar aqui (colhe no slide 11):**
> "Reparem em duas palavras: **interno** e **read-only**. O McpServer **nunca** é chamado pelo browser — só o gateway, dentro do mesmo Container Apps Environment, fala com ele. E as sete ferramentas **só leem**. Guardem isso."

**Pergunta para a turma:** "Se o McpServer é interno e o browser nunca o alcança, **quem** injeta a identidade do usuário nele?" → o **gateway**, via header `X-Entra-OID`. (Reforço: "E se alguém der `curl` direto forjando esse header? **401** — falta o `X-Gateway-Key`, que só o gateway tem.")

**Aprofundamento teórico (para EXPLICAR o MCP, não só apontar):**
> "Antes do MCP, dar 'ferramentas' a um LLM era colar a descrição das funções **no prompt**, na mão, e cada aplicação inventava o seu próprio jeito. O MCP transforma isso num **protocolo aberto e padronizado** — daí a analogia do **USB-C**: antes cada aparelho tinha o seu conector; hoje um plugue só serve pra tudo. O MCP faz o mesmo para 'LLM ↔ ferramentas/dados': **um contrato único** que qualquer cliente e qualquer servidor falam.
>
> O coração são **dois verbos**. O `tools/list` é a **descoberta**: o cliente pergunta ao servidor 'quais ferramentas você tem?' e recebe um **catálogo tipado** — nome, descrição e o **schema dos argumentos** de cada uma. E isso é **em runtime**: o modelo não precisa saber de antemão o que existe; ele **descobre**. O `tools/call` é a **invocação**: chamar uma ferramenta pelo nome, com argumentos que batem com o schema. Os dois trafegam em **JSON-RPC 2.0** — um formato simples de 'chamada de procedimento remoto': um objeto com `method`, `params` e um `id` que casa a pergunta com a resposta.
>
> Por que um **padrão** importa, e não só 'colar as tools no prompt'? Três ganhos concretos: (1) **descoberta dinâmica** — uma 8ª ferramenta adicionada no servidor aparece sozinha no `tools/list`, sem tocar no cliente; (2) **contrato tipado** — o modelo conhece os argumentos exatos, então erra menos; (3) **desacoplamento** — o McpServer pode evoluir (trocar o banco, otimizar a query) sem o chatbot saber. Aqui é literal: o McpServer é um **serviço .NET** (SDK oficial `ModelContextProtocol`), com **ingress interno**, atrás do gateway — o browser **nunca** fala com ele; quem chama `tools/list`/`tools/call` é o servidor, do lado seguro."

**Gancho → próximo slide:** "Quais são, exatamente, essas sete ferramentas? Todas de leitura — vamos ver a lista."

## Slide 7 — "As 7 ferramentas (todas de leitura)" · ≈ Storyboard S4 (companheiro) [~4 min]

**Fala:** leia as sete em voz alta, agrupando:
`consultar_disponibilidade` · `verificar_ingresso` · `consultar_bracket` — e — `consultar_partidas` · `consultar_classificacao` · `consultar_time` · `consultar_estadio`.

**O ponto (aponte o rodapé do slide):**
> "Cada uma faz um **`SELECT` parametrizado** (Dapper) no SQL real. **Nenhuma** escreve. Não existe uma tool de escrita — nem escondida, nem desabilitada: **ela não existe**. Guardem esta frase: *a auditoria de segurança mais simples que existe é ler o `tools/list`.* Sete verbos, todos de leitura. Fim."

**Erro comum a antecipar (já mencione):** "Se no hands-on o `tools/list` retornar **8** e não 7, a branch de vocês não partiu do estado pós-Story 3.1 (McpServer só-sentidos). Têm de ser exatamente **7** `ReadOnly = true`."

**Gancho:** "Ferramentas prontas. Mas quem **decide** qual chamar quando você pergunta em português? É a segunda tecnologia: RAG."

## Slide 8 — "TECNOLOGIA 2 DE 4 · RAG (grounding por tool-use)" · ≈ Storyboard S5 [~6 min]

**Fala (siga a estrutura):**
- **O que é?** "**Retrieval-Augmented Generation**: o modelo **recupera** um fato de uma fonte externa **antes** de responder — em vez de 'lembrar' e **inventar**."
- **Como funciona (aponte):** `"Quando o Brasil joga?" → Gemini escolhe a tool → consultar_partidas → SQL real → resposta fundamentada`.
- **Os 4 recursos:** **Function calling (`AUTO`)** (o Gemini decide **qual** das 7 tools chamar) · **Grounding** (a resposta vem do **banco**, não do conhecimento paramétrico do modelo) · **Chave server-side** (a `GEMINI_API_KEY` fica no **proxy `/llm`**, nunca no browser) · **`gemini-2.5-flash`** (o modelo do lab).

**Momento de segurança (a chave Gemini):**
> "A chave do Gemini **nunca** vai para o browser. O front só conhece a URL do **proxy** (`VITE_LLM_PROXY_URL`, que é o gateway). O McpServer expõe `/llm/{provider}/{*path}`, injeta a `GEMINI_API_KEY` como header server-side e encaminha ao endpoint oficial. E o workflow tem um **guard** que **falha o build** se qualquer key vazar no bundle."

**Se perguntarem do modelo:** o runtime usa **`gemini-2.5-flash`**. O comentário de cabeçalho do `gemini.ts` ainda cita `2.0-flash` — **inconsistência conhecida e inofensiva**, fora de escopo corrigir.

**Aprofundamento teórico (o que RAG É de verdade):**
> "Para explicar RAG, comece pelo **problema**. Um LLM guarda **conhecimento paramétrico** — tudo o que ele 'decorou' nos bilhões de parâmetros durante o treino. Isso tem duas limitações fatais para a nossa Copa: é **estático** (congelou na data do treino — ele não sabe o placar de ontem) e **alucina** em fatos específicos (ele foi treinado pra ser **plausível**, não **exato**; quando não sabe, inventa com confiança). Pergunte 'quantos ingressos VIP sobraram para a final?' e o conhecimento paramétrico é a **pior** fonte possível: o número muda a cada compra.
>
> **RAG — Retrieval-Augmented Generation — resolve invertendo a ordem.** Em vez de 'gerar direto do que decorei', o sistema primeiro **recupera** um fato de uma fonte externa e **injeta esse fato no contexto** do modelo; só então o modelo gera a resposta, agora **fundamentada** naquele fato. A própria sigla é o roteiro: *Retrieval* (recuperar) · *Augmented* (aumentado) · *Generation* (geração). A analogia: é a diferença entre responder **de cabeça** e responder **depois de consultar a ficha na sua frente**. O conhecimento do modelo continua útil para **conversar** (entender a pergunta, redigir bem); mas o **fato** vem de fora.
>
> O princípio 'recuperar antes de gerar' é **universal** — o que muda é **como** você recupera (os dois sabores vêm no próximo slide). Aqui o mecanismo de recuperação é o **function calling**: o Gemini não busca o fato sozinho, ele **pede** uma ferramenta (o loop completo eu abro no clímax do F5, slide 11). O que importa agora: a resposta que a turma vê no chatbot **não saiu da memória do modelo** — saiu de um `SELECT` no SQL real, embrulhado em linguagem natural pelo Gemini."

**Gancho:** "Mas cuidado com a palavra RAG — ela costuma significar 'banco vetorial'. Aqui é diferente, e é importante nomear certo."

## Slide 9 — "RAG aqui NÃO é vector store" · ≈ Storyboard S5 (destaque honesto) [~4 min]

**Fala (percorra a tabela):** RAG clássico = embeddings + banco vetorial + busca por similaridade + recupera "trechos". RAG desta aula = **ferramentas MCP** + **`SELECT` parametrizado** + recupera **o dado exato e vivo**.

**O ponto (honestidade arquitetural):**
> "O **padrão** é o mesmo — *recuperar antes de gerar*. A **implementação** é **grounding via MCP**, não vetores. Chamem pelo nome certo: é **RAG por function-calling**. Isso importa porque um aluno que sai daqui achando que 'fez um vector store' vai procurar embeddings que não existem no código."

**Aprofundamento teórico (os dois sabores de RAG — e por que o nosso):**
> "Existem **dois jeitos** de fazer a etapa de 'recuperar'. O **RAG clássico** usa **embeddings + banco vetorial**: você transforma textos em **vetores numéricos** (embeddings) que capturam **significado**, guarda num banco vetorial e, na hora da pergunta, busca os vetores **mais parecidos** — **busca por similaridade semântica**. É o jeito certo para **conhecimento não-estruturado**: um manual, uma base de artigos, PDFs — onde a tarefa é 'me traga os trechos mais relevantes sobre X'.
>
> O **nosso** RAG é o **grounding por tool-use**: o modelo chama uma **ferramenta MCP** que faz um **`SELECT` parametrizado** no banco. Não há vetor, não há similaridade — há uma **consulta exata**. E isso é uma **escolha deliberada, não preguiça**: os nossos dados são **estruturados e transacionais** — preço de ingresso, disponibilidade, status de um bilhete, tabela de um grupo. Para esse tipo de dado, 'o mais parecido' é **a resposta errada**: você não quer o preço *aproximadamente* parecido, quer **o preço exato daquela categoria, naquela partida, agora**. Busca semântica devolveria 'algo próximo'; um `SELECT` devolve 'o dado, exato e vivo'.
>
> A regra prática pra levar pra casa: **dado não-estruturado (texto) → embeddings/vector store; dado estruturado/transacional (banco) → tool-use/`SELECT`.** Os dois são RAG — 'recuperar antes de gerar' — mas confundir os sabores leva o aluno a caçar um banco vetorial que **não existe** no nosso código, ou a tentar responder 'quantos ingressos sobraram' com similaridade, que é a ferramenta errada para a pergunta."

**Gancho → conceito-chave:** "Agora o conceito que amarra o F5 inteiro — e o motivo de tudo isso ser **seguro**."

## Slide 10 — "CONCEITO-CHAVE · A regra de ouro (agora trivial)" · ≈ Storyboard S3 (conceito-chave central) [~5 min]

> **Slide de conceito — dê destaque de voz. É o preparo para a demo ao vivo do slide 11.**

**Fala:**
> "Nas fases anteriores, a segurança de uma **ação** dependia de **rotear** essa ação por um caminho seguro — fila, orquestração, validação. **Agora** é diferente: o McpServer é **só sentidos**. **Não existe** um vetor de escrita para o LLM chamar. A regra 'o chatbot nunca escreve no banco' vale **por construção**."

**Leia a frase-âncora projetada:**
> *"O que não existe não pode ser chamado."*

**Reforço:** "Um LLM **alucina** argumentos o tempo todo. Mas não há tool de escrita para receber a alucinação. **Segurança por construção, não por roteamento** — a mesma expressão do slide 3."

**Gancho → clímax:** "E eu não vou pedir que vocês **acreditem** nisso. Vou **provar** ao vivo. Mas antes, vamos pôr a mão: subir o McpServer e o chatbot no Portal."

> **▶ PAUSA O DECK → GUIA (F5 hands-on · Fases 1–4 do `final-portal-guide.md`) · ~2h00–2h50**
>
> Aqui você **sai do deck** e conduz a turma pelo guia do portal. Volte ao deck no slide 11 para o clímax.
>
> **Fase 1 — Deploy do McpServer (Container App, ingress INTERNO) [~50 min].** Diga: "Este serviço vive **atrás** do gateway, ingress **interno** ('Limited to Container Apps Environment') — sem endereço público. **Target port = 8080** sempre (é o que o `Dockerfile` expõe; outra porta = **502**)." Três App Settings: `SqlConnectionString` (as 7 tools), `GEMINI_API_KEY` (usada pelo proxy), `GATEWAY_SHARED_SECRET` (a trava `X-Gateway-Key`).
>   - **O "P0" a explicar (momento aha de segurança):** "Por que **rebuildar o gateway** (`acao=gateway`)? Porque só a partir do hardening (ADE-009) o gateway injeta `X-Gateway-Key` **também** no cluster `mcp-server`. A imagem das Quartas não tinha o McpServer no conjunto confiável. Sem o rebuild, o segredo não chega e você toma **401** mesmo com Bearer válido."
>   - **O mesmo segredo das Quartas:** o `Gateway__AdminSharedSecret` gerado nas Quartas é **reusado** aqui. Se ninguém anotou, gere um novo (`openssl rand -hex 24`) e reaplique em **todos** os serviços confiáveis.
>   - **Checkpoint (diga o critério):** "`tools/list` via gateway lista **exatamente 7 tools, todas `readOnly: true`**; `POST /mcp` **sem** `X-Cache: HIT`; e o McpServer **não** responde por URL pública."
>   - **Erros comuns (Apêndice C do guia):** `tools/list`=8 → branch pré-Story 3.1 · 401 no `POST /mcp` com Bearer válido → segredo divergente **ou** gateway não rebuildado · 502 → `McpServerUrl` errado ou porta ≠ 8080 · McpServer com URL pública → ingress criado External, recriar interno.
>
> **Fase 2 — Chatbot conversando com o estado real da Copa [~30 min].** Diga: "O chatbot descobre as 7 tools via `tools/list` e deixa o **Gemini** decidir qual chamar (function calling `AUTO`)." **Demonstre ≥3 perguntas** e mostre, no painel do chatbot, **qual tool** foi escolhida: *"Quando o Brasil joga?"* → `consultar_partidas` · *"Como está o grupo A?"* → `consultar_classificacao` · *"Me fala do Maracanã"* → `consultar_estadio`. **Momento aha:** "Você perguntou em português; o Gemini traduziu para uma **tool call**; o dado veio do **SQL real**." Erro comum: "chat indisponível" → `VITE_LLM_PROXY_URL` não setado no build; "responde sem dados reais" → `SqlConnectionString` ausente/errada no McpServer.
>
> **Fases 3–4 — Roteamento, cache e identidade (referência, sem configurar) [~15 min].** Só entendimento: o gateway roteia `/mcp` e `/llm/**` para o cluster `mcp-server`; **`POST` não é cacheado**; o cache de borda (30s) roda **pós-autenticação** (hardening 4.4); o McpServer **lê** `X-Entra-OID` só para **logging mascarado** — **nunca** revalida o JWT. **O gateway é o guardião único** — por isso o McpServer pode ficar atrás dele sem reimplementar autenticação.
>
> **▶ VOLTE AO DECK no slide 11 para o clímax do F5.**

## Slide 11 — "⭐ A regra de ouro AO VIVO" · ≈ Storyboard S3 (conceito-chave central · a demo) · guia Fase 5 [~20 min]

> ⭐ **Este é o momento central da aula inteira. Volte ao deck, projete este slide, e faça a demo com o chatbot que a turma acabou de subir. Não corra.**

**Roteiro detalhado da fala:**

1. **Prepare o palco.** "Até agora o chatbot só **leu** dados. Vamos testar o limite: peça a ele uma **ação**." Peça a um aluno (ou você) para digitar no chatbot:
   > *"Cria um alerta pra mim quando abrir ingresso VIP."*

2. **Deixe a turma observar junto.** O chatbot **não tem** essa ferramenta. O `tools/list` só expõe **7 tools de leitura** — não existe nenhuma tool de **escrita** para o Gemini chamar.

3. **Diga a frase-chave (a do slide):**
   > "Reparem no que aconteceu. Eu não **bloqueei** a ação com um IF, uma regra, um roteamento. A ação simplesmente **não pode acontecer** porque **a ferramenta não existe**. Isso é **segurança por construção, não por roteamento**. O que não existe não pode ser chamado."

4. **A nuance honesta (NÃO PULE — é o que separa uma boa aula de uma aula ingênua):**
   > "Atenção a uma sutileza: o LLM **pode** responder em texto algo como 'pronto, criei o alerta'. Isso é **alucinação de texto** — **não** é uma tool call. **Nenhuma escrita ocorre** no banco. A 'promessa' no texto **não é uma ação**. O único jeito de escrever seria uma tool call de escrita — e ela **não existe**. A segurança não depende de o LLM 'se comportar'; depende de o vetor de escrita **não existir**."

**Ponto a reforçar:** a "mão" de ação (uma antiga ferramenta de criar alerta, do desenho original) **foi removida** — o McpServer é só **sentidos**. Você **não precisa** explicar filas, webhooks ou roteamento para provar a segurança: **basta olhar a lista de ferramentas**.

**Aprofundamento teórico (como o chatbot funciona por baixo — o loop de function calling):**
> "Vale abrir a 'caixa preta' do chatbot, porque é isso que torna a regra de ouro **inevitável**. O ciclo tem **quatro tempos**. **(1)** O front manda ao Gemini a **pergunta do usuário** mais o **catálogo de ferramentas** (o que veio do `tools/list` — as 7 tools com seus schemas). **(2)** O Gemini, em **function calling `AUTO`**, **não responde texto** — ele responde uma **intenção de chamada**: 'quero chamar `consultar_partidas` com time=Brasil'. Repare: o modelo **não executa nada**, ele **pede**. **(3)** Quem **executa** é a **nossa aplicação** — o pedido vai pelo gateway → McpServer → `SELECT` no SQL — e o resultado real volta. **(4)** O front devolve esse resultado ao Gemini, que só então **gera a resposta final** em português, fundamentada no dado. É um **loop**: pergunta → o modelo pede uma ferramenta → o app executa → o resultado volta → o modelo responde (e poderia pedir outra ferramenta, e o ciclo repete).
>
> A analogia: o Gemini é um **cérebro sem mãos**. Ele é ótimo em decidir *qual* ferramenta usar e *como* interpretar o resultado — mas **braço quem tem é a aplicação**. O modelo nunca toca o banco; ele emite um **pedido tipado** que o **nosso código** decide honrar. (E, no caminho, a `GEMINI_API_KEY` vive no **proxy server-side**: o front fala com o gateway, o McpServer injeta a chave; o browser nunca a vê.)
>
> **Agora amarre com a regra de ouro:** olhem o **passo 2**. O modelo só pode **pedir** uma ferramenta que **exista no catálogo** — e o catálogo tem **7 verbos, todos de leitura**. Então, por mais que o usuário peça 'cria um alerta', o loop **não tem uma ação de escrita para o modelo pedir**: o passo 2 não consegue nem **formular** a intenção de escrever. Não é um `IF` que bloqueia; é a **ausência da ferramenta** que torna a escrita impossível. É por isso que 'segurança por construção' não é slogan — é uma **propriedade do loop**."

**Pergunta para fechar o momento:** "Qual é a auditoria de segurança mais simples que existe aqui?" → **Ler o `tools/list`.** Sete verbos, todos de leitura. Fim.

**Erro comum (tabela do guia):** aluno vê o LLM "prometer" a ação e conclui que houve escrita. **Corrija na hora:** peça para checar o banco / o `tools/list`. **Texto não é tool call.**

**Checkpoint (diga o critério):** "A turma **viu** que o chatbot não executa ações; e o material **não menciona** nenhuma 'mão' ou tool de escrita."

**Gancho → intervalo/F6:** "A voz está provada: uma IA que consulta o real **sem nunca poder alterá-lo**. Depois do café, a aplicação ganha **visão** — uma tela onde a arquitetura se acende."

> ☕ **Intervalo (15 min)** — quebra natural entre F5 (voz) e F6 (visão).

---

# F6 — VISÃO · CONCEITOS (slides 12–16 · ~25 min)

> **Objetivo do bloco (fala de abertura):**
> "No F5 a aplicação ganhou **voz**. Agora ela ganha **visão**: um serviço que lê os rastros de uma compra real e um visualizador onde uma 'bolinha' atravessa **cinco nós** animados — a mesma compra, rastreável de ponta a ponta pelo `correlationId`. E, no caminho, duas tecnologias novas: **Managed Identity** e **Azure SignalR**."
>
> **Frase âncora do bloco:** *"Uma compra, um `correlationId`, cinco nós. Observabilidade distribuída que você consegue **ver**."*

## Slide 12 — Divisor "Bloco F6 — VISÃO" · ≈ abertura de S7 [~1 min]

**Fala:** "F6 = **visão**: Managed Identity, observabilidade e SignalR. Cor de acento roxo = observabilidade." 

**Gancho:** "A terceira tecnologia nova responde a uma pergunta: como um serviço lê a telemetria **sem guardar nenhum segredo**?"

## Slide 13 — "TECNOLOGIA 3 DE 4 · Managed Identity" · ≈ Storyboard S6 [~6 min]

**Fala (siga a estrutura):**
- **O que é?** "Uma identidade do **Azure AD gerenciada pela plataforma**: o serviço se autentica **sem senha e sem segredo no código**."
- **Como funciona (aponte):** `FlowEvents (System-assigned MI) → role Log Analytics Reader no workspace → LogsQueryClient consulta os traces (Kusto)`.
- **Os 4 recursos:** **System-assigned** (nasce e morre com o Container App) · **RBAC** (recebe o papel `Log Analytics Reader`, o mínimo necessário) · **Sem credencial** (nenhuma connection string de telemetria a guardar/rotacionar) · **Fail-visível** (sem o papel, o `LogsQueryClient` toma **403** e os nós **nunca acendem**).

**O ponto que mais trava no hands-on (antecipe já):**
> "Guardem o **fail-visível**: se vocês esquecerem de conceder `Log Analytics Reader` à Managed Identity, não dá erro barulhento — os **nós simplesmente não acendem** e você toma **403** no `LogsQueryClient`. É o erro nº1 do F6."

**Aprofundamento teórico (o que é Managed Identity de verdade):**
> "Toda vez que um serviço precisa falar com outro no Azure — ler telemetria, abrir o cofre, conectar no banco — ele precisa **provar quem é**. O jeito antigo é uma **credencial no código/config**: connection string, chave, segredo. O problema é estrutural: esse segredo **existe** — logo pode **vazar** (no git, num log, num screenshot), precisa ser **rotacionado**, e alguém tem que **guardá-lo** com segurança (o 'problema do primeiro segredo': para proteger um segredo, você precisa de outro segredo…).
>
> **Managed Identity vira o jogo: o serviço deixa de *ter* um segredo e passa a *ser* uma identidade.** A plataforma cria, no **Entra ID** (o Azure AD), uma identidade **gerenciada por ela** e a **cola** no seu recurso. Em runtime, o código pede um token a um **endpoint de metadados interno da plataforma** (o IMDS) — um endereço que só responde de **dentro** daquela instância — e recebe um **token OAuth2** de curta duração, **sem nenhum segredo no código**. Emissão, validade, rotação: tudo é a plataforma. A analogia: em vez de carregar uma **chave** que qualquer um que a copie usa, você tem um **crachá** que o prédio reconhece como *você* — e não adianta fotografar.
>
> O que a identidade **pode fazer** é o **RBAC** (papéis). A identidade em si não dá acesso a nada; você **concede um papel** com o **menor privilégio**: `Log Analytics Reader` para **ler telemetria** (F6), `Key Vault Secrets User` para **ler o cofre** (Blindar). E há **duas variedades**: **system-assigned** (nasce e morre **com** o recurso — some se você apaga o app) e **user-assigned** (um recurso **independente**, que **sobrevive** e pode ser **compartilhado** por vários apps). A aula usa **as duas de propósito** (ADE-010): a **user-assigned compartilhada** lê o Key Vault (um único grant que sobrevive à recriação manual do McpServer/FlowEvents) e a **system-assigned por-app** fala com o SQL, onde cada serviço precisa do **seu** privilégio mínimo. **O 'aha' didático:** é a **mesma mecânica de identidade sem-senha** para telemetria **E** para segredos — aprende-se uma vez, aplica-se nos dois (é a 'irmandade' das MIs que fecho no próximo slide)."

**Reforço Blindar — o cofre (a MESMA identidade lê os segredos):**
> "O deck junta aqui a outra metade da história (não há mais um slide só de Key Vault — ele vive nesta tecnologia). **A mesma** Managed Identity, com a role `Key Vault Secrets User`, também **lê os segredos do Key Vault**. As chaves — SQL, Gemini, SignalR e o **segredo do gateway** — **saem do claro** das App Settings e vão para o **cofre que já existe** (`kv-dev-tk-cin-001`, nada recriado), **in-place, sem downtime**: o valor não muda, só o **lar**. **Ganho estrutural (não é higiene):** o `X-Gateway-Key` que o gateway **injeta** e o que os serviços **validam** viram **um secret só** → a igualdade fica **estrutural**, não dá mais para divergir por engano e cair com 401. **Frase de efeito:** *'a mesma Managed Identity que lê a telemetria é a que apaga o segredo em claro.'* Honestidade: **SQL sem senha** via MI é o **próximo nível/Fase 2** (backend v1 segue com senha por retro-compat); a **KV reference das chaves é entregue agora**. Base: **ADE-010**."

**Gancho:** "Guardem essa ideia — **modernizar sem apagar**. O próximo conceito-chave leva exatamente isso para a **identidade dos usuários**."

## Slide 14 — "CONCEITO-CHAVE · Identidade unificada: modernizar sem destruir" · ≈ Storyboard S8 · Story 3.5 / ADE-007 [~5 min]

> **Slide de conceito-chave — o gêmeo, na Final, do "modernizar sem destruir" das Quartas. É a face ADITIVA de "evoluir sem quebrar" (contraste com o n8n, SUBTRATIVO, slides 17–18). Base: Story 3.5 / ADE-007.**

**Fala:**
> "Nas Quartas vocês viram a base v1 (login com senha **bcrypt**) e a chegada do **CIAM**. A Final **fecha o círculo**: o usuário que **já existia no v1** ganha um **`entra_oid` do CIAM** vinculado **na mesma linha `users`** — o `password` bcrypt fica **intacto**, o `entra_oid` é **adicionado** ao lado. É **vínculo, não substituição**: o bcrypt **não migra** (a Microsoft gerencia a credencial do CIAM), então os dois **coexistem** na mesma linha. Modernizar **sem destruir**."

**O mecanismo (JIT resolve-or-provision):**
> "Como isso acontece na prática? Um endpoint novo, **`GET /api/v2/me`**, faz **resolve-or-provision** em três tempos: **(1) resolve por `oid`** (já existe linha? devolve o `id`); **(2) link por email** (não tem por `oid`, mas há um usuário v1 com o **mesmo email**? **vincula** o `entra_oid` naquela linha); **(3) provisiona** (não há nenhum? cria uma linha `users` nova). São **dois caminhos de cobertura**: **eager** — a migração em lote que vocês fizeram nas Quartas — e **lazy** — este JIT, que resolve no **1º login/compra** de quem chega novo pelo CIAM."

**O insight de negócio (o 'porquê' que a turma sente):**
> "Aqui está o que muda para o produto: um cliente **nato-CIAM** — cadastrado direto no External ID, sem nunca ter tido conta v1 — **antes não conseguia comprar**, porque o checkout exigia um `users.id` do v1 que ele não tinha. A unificação o torna **cidadão de primeira classe**: ele compra, e passa a ser visível ao dashboard admin (que lê `users`). O JIT não é enfeite — é o que **destrava a compra** para esse público."

**A blindagem (NÃO pule — é segurança):**
> "Um detalhe crítico: o endpoint é protegido pelo fence **`CiamOnly`**. **Só** um token de **cliente CIAM** aciona o resolve-or-provision — um token de **admin/workforce** (que também carrega um `oid`) é **rejeitado** (403). Sem esse fence, um admin provisionaria/​vincularia uma linha de **cliente** com a identidade do **operador**, corrompendo a base. O admin workforce está **explicitamente fora** do eixo de unificação."

**A frase de efeito:**
> "O login novo não apaga o usuário antigo — ele o **adota**."

**A moldura (o contraste com os slides do n8n):** "Este é o lado **ADITIVO** de 'evoluir sem quebrar': a identidade **adiciona** o vínculo, **nada é apagado**. Mais à frente (n8n) vem o lado **SUBTRATIVO**: **remover** uma peça inteira. Aditivo × subtrativo — as duas faces de modernizar destruindo o mínimo."

**Gancho:** "Voltando ao fio da **observação em tempo real**: falta o **canal** que empurra a telemetria para a sua tela — o SignalR."

## Slide 15 — "TECNOLOGIA 4 DE 4 · Observabilidade ao vivo (SignalR)" · ≈ Storyboard S7 [~6 min]

**Fala (siga a estrutura):**
- **O que é?** "A **mesma** telemetria — **App Insights + Log Analytics, já no ar** — alimenta **duas** coisas: o **Flow Visualizer ao vivo** (**FlowEvents** + **Azure SignalR** empurram eventos por WebSocket) **e** a **observabilidade nível-produção** da compra. Nada recriado, ~US$0."
- **Como funciona (aponte):** `traces (correlationId) → FlowEvents lê via Kusto → TraceEventMapper classifica cada trace num nó → SignalR → a rota /flow acende os nós`.
- **Os 4 recursos:** **Trace-driven** (o motor lê **traces correlacionados**; é agnóstico a quem os emitiu) · **Azure SignalR (Free_F1)** com **Service Mode `Default`** (⚠️ **não** Serverless — o FlowHub é hospedado pelo serviço) · **CORS restrito** (o WebSocket usa credentials → **origin exato** do front, nunca `*`) · **5 nós** (a "bolinha" atravessa a jornada em **< 30s**).

**Reforce o lado observabilidade (nível-produção, ~US$0 — reusa App Insights + Log Analytics, base ADE-010):**
> "A mesma telemetria que acende os nós também dá **observabilidade de produção**, de graça, porque reusa o App Insights e o Log Analytics que já estão no ar: **tracing ponta-a-ponta por `correlationId`** (Transaction Search / Application Map), um **Workbook** da jornada da compra (latência por hop, falhas, backlog do Service Bus) e **alertas úteis** — **5xx no gateway** e **dead-letter no Service Bus**."

**A amarração didática (segurança ≙ observabilidade):**
> "Reparem no padrão: a MI que lê o **Log Analytics** (`Log Analytics Reader`) é **irmã** da MI que lê o **Key Vault** (`Key Vault Secrets User`, que vimos na tecnologia Managed Identity, slide 13). Uma identidade gerenciada com uma role *Reader* lendo um recurso gerenciado, **sem segredo**. É a **mesma disciplina**, contada duas vezes — segurança e observabilidade."

**Aprofundamento teórico (por que SignalR — o problema do tempo real):**
> "Pensem no que o Flow Visualizer precisa: quando a compra chega no nó 3, a **tela do aluno** tem que acender o nó 3 **naquele instante**. O jeito ingênuo seria o browser **perguntar sem parar** — 'já chegou? já chegou?' — de segundo em segundo. Isso é **polling**, e é ruim por três motivos: **desperdício** (99% das perguntas respondem 'ainda não'), **latência** (o evento pode acontecer logo depois da pergunta e você só vê no ciclo seguinte) e **escala** (cada browser martelando o servidor). O modelo certo é o **inverso**: o **servidor empurra** o evento para o browser **no momento em que acontece**. Isso é **push** — e é o problema que o SignalR resolve.
>
> **Azure SignalR é o serviço gerenciado de tempo real** do Azure. Por baixo ele usa **WebSocket** — uma conexão que fica **aberta** nos dois sentidos, ao contrário do HTTP normal (pergunta → resposta → fecha) — e, quando o WebSocket não é possível (rede/proxy no caminho), **degrada sozinho** para outras técnicas de transporte. Os conceitos: uma **conexão** é cada browser ligado; um **Hub** é o ponto lógico onde o servidor 'fala' com os clientes — o nosso chama **`FlowHub`**. Ser **gerenciado** significa que o Azure aguenta o número de conexões e a entrega; você não opera um servidor de WebSocket na unha.
>
> Um detalhe que **derruba o lab se errado**: o **Service Mode**. No modo **`Default`**, o **nosso** FlowEvents (.NET, via `AddAzureSignalR`) **hospeda** o `FlowHub` — o SignalR é o 'megafone', mas a **lógica** do Hub roda no nosso serviço. No modo **`Serverless`** não há servidor hospedando o Hub (esse modo é feito para Azure Functions/webhooks), e o nosso FlowEvents **recusaria** a conexão. Por isso: **Default**, nunca Serverless. Fechando o quadro do F6: o FlowEvents **lê os traces** por `correlationId` (Kusto, com a Managed Identity), o `TraceEventMapper` decide **qual nó** cada trace acende, e o **SignalR empurra** o evento para a rota `/flow` — a bolinha acende os 5 nós **ao vivo**, sem o browser perguntar nada. **(É aqui, no lab, que você pausa e mostra a compra atravessando a tela.)**"

**Erros a plantar (colhe no hands-on):** "Dois clássicos: criar o SignalR em **Serverless** (ele recusa por tier — tem de ser **Default**), e esquecer o **origin exato** no CORS (WebSocket com credentials não aceita `*`)."

**Gancho:** "Cinco nós. Que nós são esses? O grande final visual da aula."

## Slide 16 — "Os 5 nós (o grande final visual)" · ≈ Storyboard S7 (companheiro) [~3 min]

**Fala:** aponte o diagrama `0 Gateway YARP → 1 Function Entry → 2 Service Bus → 3 Function Consumer → 4 SQL`. Destaque as três linhas da tabela:
- **Nó 0 — Gateway YARP:** injeta `X-Correlation-ID` (o **nó zero** do tracing).
- **Nó 3 — Function Consumer:** grava no SQL **e emite a notificação pós-compra INLINE**.
- **Nó 4 — SQL:** linha em `purchases.correlation_id` — fim.

> "Uma compra, um `correlationId`, **cinco** nós. Guardem o número **cinco** — daqui a pouco alguém vai procurar um sexto." (planta o slide 17)

**Gancho → hands-on:** "Vocês vão **ver** esses cinco nós acenderem. Mas primeiro, mãos ao Portal: criar o SignalR, a Managed Identity e o FlowEvents."

> **▶ PAUSA O DECK → GUIA (F6 hands-on · Fases 6–8 do `final-portal-guide.md`) · ~1h30–2h30**
>
> Saia do deck e conduza pelo guia. Volte ao deck nos slides 16→18 para o smoke.
>
> **Fase 6 — Azure SignalR + Managed Identity [~50 min].** Pontos: **SignalR Free (Free_F1), Service Mode `Default`** (não Serverless — erro clássico) · **CORS = origin exato** do frontend (`https://<seu-front>.azurewebsites.net`, nunca `*`) · **Container App do FlowEvents é EXTERNO** (≠ McpServer!): "Accepting traffic from anywhere", **Transport = `Auto`** (habilita WebSocket), **Target port = 8080** · **Managed Identity + role `Log Analytics Reader`** no workspace (**sem o papel, 403 e os nós nunca acendem**).
>   - **Pergunta para a turma (contraste — momento aha):** "Por que o McpServer é **interno** e o FlowEvents é **externo**?" → o McpServer guarda dados sensíveis atrás do gateway (só o gateway fala com ele); o FlowEvents é **leitura de telemetria** que o front consome via gateway e por WebSocket.
>   - **Erros comuns (Apêndice D):** nós nunca acendem/403 → MI sem `Log Analytics Reader` · SignalR não conecta → ingress sem transport `Auto` ou CORS sem origin · SignalR recusa por tier → criado em Serverless.
>
> **Fase 7 — Gateway (`FlowEventsUrl`) + frontend (`/flow`) [~25 min].** O gateway **já** roteia FlowEvents (`FlowEventsDestinationConfigFilter`, desde a Story 2.6, reusado); só falta a **URL real** (`FlowEventsUrl`). Duas rotas: `/flow-events/api/{**}` (recent / {id} / replay) e `/flow-events/hubs/{**}` (o Hub SignalR, WebSocket). O gateway continua o **NÓ ZERO** (injeta `X-Correlation-ID` também nas requests ao FlowEvents).
>   - **⚠️ Escopo importante (não confundir com o F5):** o cluster `flow-events` **NÃO** recebe `X-Gateway-Key` (fora do escopo da ADE-009). **NÃO** configure `GATEWAY_SHARED_SECRET` no FlowEvents — diferente do McpServer.
>
> **Fase 8 — publicar o frontend com `/flow` (`acao=frontend`).** Confirme `VITE_FLOW_EVENTS_BASE_URL = {gateway}/flow-events`.
>
> **▶ VOLTE AO DECK nos slides 16→18 e conduza a Fase 9 (o smoke) ao vivo.**

## Slide 17 — "CONCEITO-CHAVE · Onde foi o n8n?" · ≈ Storyboard S9 · guia Fase 9 [~8 min]

> ⭐ **O grande final visual. Volte ao deck, faça uma compra REAL e conduza a turma ao vivo (guia Fase 9). Este slide fecha a missão "Simplificar" — cuidado de linguagem.**

**Primeiro, o smoke (guia Fase 9) [~30 min de sala, aqui a parte visual]:**
1. "Vamos fazer uma **compra v2 de verdade**: login CIAM, comprar um ingresso."
2. "Naveguem para **`/flow`** e **olhem juntos**." A bolinha deve atravessar **exatamente 5 nós, em < 30s**, com o **mesmo `correlationId`** em cada hop.
3. "Abram o **Sheet de inspeção** de cada nó: o `correlationId` é o **mesmo** do começo ao fim."

**Aí, o momento "onde foi o n8n?" (OBRIGATÓRIO — projete este slide):**
> "Quem acompanhou o desenho original talvez esperasse um **sexto** nó — a orquestração da notificação pós-compra. Ele **não existe mais**. Nós **removemos a orquestração externa**. A notificação virou uma etapa **inline** dentro da própria **Function Consumer** — o nó 3. É a **Function que orquestra o pós-compra**, no mesmo processo em que grava a compra."

**Leia a frase-âncora projetada:**
> *"É a **Function** que orquestra o pós-compra."*

**Peça a inspeção (momento aha):** "Abram o payload do **nó 3, Function Consumer**, e **procurem a notificação**. Ela está **ali dentro** — um log estruturado correlacionado. Ela **não tem bolinha própria**."

> ⚠️ **Cuidado de linguagem (para você, instrutor):** diga **"a Function orquestra o pós-compra"**. **NUNCA** diga "automação no-code" nem cite orquestração externa como algo **presente**. Na Final o n8n **não existe** — é só **"o que removemos"**.

**Erros comuns (Apêndice D):** diagrama com 6 nós / falta o Gateway → branch pré-Story 3.1 (confirmar `flowNodes.ts` com **5** entradas) · bolinha para no nó 2 → Consumer com backlog ou atraso de ingestão do Kusto (aguardar) · `correlationId` some → SignalR desconectado ou `VITE_FLOW_EVENTS_BASE_URL` errado · 502 em `/flow-events/**` → `FlowEventsUrl` ausente no gateway.

**Gancho:** "Mas remover um componente tem um preço. Vamos ser honestos sobre o trade-off."

## Slide 18 — "A lição: simplificar > substituir" · ≈ Storyboard S9 (companheiro) [~3 min]

**Fala (percorra a tabela antes → agora):** pós-compra: orquestração externa (Container App + Postgres) → **inline** na Function Consumer · peças: +2 recursos, +1 pin de imagem → **0** (some do stack) · nós no visualizer: 6 → **5** · rastreabilidade: trace correlacionado → **trace correlacionado (igual)**.

**O trade-off, dito honestamente:**
> "Ganhamos **simplicidade**: menos peças, **menos pontos de falha**, **menos custo**. Pagamos com uma perda **visual**: a notificação não aparece como um nó separado — fica dobrada no nó 3. É um trade-off **consciente**; a observabilidade dela vive no **log correlacionado** do Consumer. **Cinco nós, não seis.** Remover um componente em vez de trocá-lo é uma decisão de arquitetura — e a lição de encerramento do Living Lab."

**Pergunta para a turma:** "Se um aluno procurar o 'nó de notificação' e não achar, o que você responde?" → é o trade-off aceito: **5 nós, notificação inline no Consumer**. Não é bug; é design.

**A moldura de contraste (amarre com o slide de identidade):** "Fechem o par: lá no slide da **identidade unificada**, a evolução foi **ADITIVA** — a gente **adicionou** o vínculo CIAM, nada apagado. Aqui é **SUBTRATIVA** — a gente **removeu** uma peça inteira e a função continua inline. **As duas faces de 'evoluir sem quebrar':** às vezes você soma (identidade), às vezes você some (n8n). Modernizar bem é destruir o mínimo."

**Checkpoint (diga o critério):** "**5 nós exatos**, `correlationId` ponta-a-ponta em **< 30s**; a notificação encontrada **dentro** do nó Function Consumer; **zero** referência a um 6º nó ou a orquestração externa."

**Gancho → fechamento:** "Vamos ver a foto completa — tudo o que vocês montaram, em um diagrama só."

---

# FECHAMENTO (slides 19–22 · 25 min)

## Slide 19 — "ARQUITETURA · A foto completa — tudo que você construiu" · ≈ Storyboard S10 [~6 min]

> **Este é o slide da JORNADA INTEIRA (é a última aula) — não só a Final. Percorra da esquerda (atores/identidade) para a direita (serviços), depois as duas faixas transversais. Vá por camadas para não poluir.**

**1. Identidade — de onde vem a request (Quartas F2/F3):** "Dois públicos, dois emissores: o **cliente** entra pelo **Entra External ID (CIAM)**, o **admin** pelo **Entra ID workforce**. O gateway valida os **dois** — JWT **dual-issuer** — com a mesma mecânica."

**2. A borda — o NÓ 0, guardião único:** "**Tudo** passa pelo **Gateway YARP**. Ele valida o token, injeta `X-Entra-OID` (identidade) e `X-Gateway-Key` (prova de origem), faz **cache pós-autenticação**, rate-limit e CORS. Nada alcança um serviço sem passar por aqui."

**3. As quatro trilhas que saem do gateway:**
- **Identidade unificada (Final 3.5):** `/api/v2/me` (JIT resolve-or-provision) → **Azure SQL `users`** — onde o **bcrypt v1** e o **`entra_oid` CIAM** convivem na mesma linha.
- **Compra assíncrona — 5 nós (Oitavas F1):** `Function Entry → Service Bus → Function Consumer (notificação inline) → Azure SQL`.
- **Voz (Final F5):** **McpServer** interno (7 tools read-only) → SQL (`SELECT`); proxy `/llm` → **Gemini** (chave server-side).
- **Visão (Final F6):** **FlowEvents** (Managed Identity → **Kusto** por `correlationId`) → **Azure SignalR** → rota `/flow`.

**4. As duas faixas transversais (a re-arquitetura da Final, EPIC-004):**
- **Segurança / Blindar:** **Managed Identity + Key Vault** — os serviços leem os segredos **do cofre** via MI, sem chave em claro; o `X-Gateway-Key` fecha o bypass ao McpServer.
- **Observabilidade:** **App Insights + Log Analytics** — traces correlacionados por `correlationId`; é **exatamente** o que a trilha da **Visão consome** (o FlowEvents lê o Kusto). Segurança e observabilidade compartilham a disciplina de identidade gerenciada.
- **Dados:** o **Azure SQL** é compartilhado (users, purchases) — o mesmo banco que a compra grava, a voz lê e a identidade unifica.

**Legenda das fases (marque no diagrama):** F1 = compra (5 nós) · F2/F3 = identidade + gateway · F5 = voz · F6 = visão · Blindar = MI+KV/observabilidade.

**Feche o slide:** "Isto é a foto de um sistema **Azure-native completo** — assíncrono, com gateway guardião, identidade federada, um chatbot que só lê, uma tela que se acende, segredos no cofre e tudo rastreável. Vocês construíram **do zero**, camada por camada, ao longo de todas as fases. **Zero n8n, zero PostgreSQL, segredos no cofre — e retro-compatível**: nada das fases anteriores quebrou." *(O draw.io de topologia completa é a Story 4.6, ainda não feita — por ora, este slide é a foto.)*

> **▶ FLUXO DE ENTREGA (guia · fluxo 100% web, padrão Quartas) [~10 min].** Antes da retrospectiva, conduza o fork: "Agora sim o fork. Lembrem: **o Portal criou os recursos vazios; o Actions só publica código.**"
> - **Fork NOVO, com TODAS as branches** — na tela de fork, **desmarque** *Copy the `main` branch only*. **Não reusem** o fork das Quartas: **Sync fork só atualiza a `main`, não traz branches novas.**
> - **Habilitar o workflow:** abrir um **PR `lab-a-final` → `main` no próprio fork** e fazer o merge (é o "exercício" — faz o `lab-a-final.yml` aparecer no Actions). **Nunca** PR no repo da TFTEC.
> - Rodar os `acao` na ordem: **`mcp-server` → `gateway` → `flow-events` → `frontend`** (ou **`tudo`**).

**Gancho:** "Está tudo no ar. Vamos amarrar o que vocês **provaram** — as **cinco** missões."

## Slide 20 — "As 5 missões da Final (retrospectiva)" · ≈ Storyboard S11 (parte) [~7 min]

**Fala (percorra a tabela, com orgulho):**
- **Voz (F5):** uma IA consulta dados reais **com segurança** — 7 sentidos, zero escrita; a regra de ouro vale **por construção**.
- **Visão (F6):** observabilidade distribuída — uma compra animada em **5 nós** por `correlationId`.
- **Blindar (hardening):** o gateway é o **guardião único** — `X-Gateway-Key` fecha o bypass direto ao McpServer; **os segredos vão para o Key Vault** (lidos por Managed Identity), não em claro; chave Gemini **nunca** no bundle; cache pós-auth.
- **Unificar (identidade):** a base **v1 (bcrypt) ↔ CIAM** convive na **mesma linha `users`**; o JIT `/api/v2/me` torna o cliente **nato-CIAM** cidadão de primeira classe (fence `CiamOnly`). É a face **ADITIVA** — nada é apagado.
- **Simplificar (re-arquitetura):** **menos peças** (notificação inline), mesma função — retro-compat. É a face **SUBTRATIVA** — uma peça inteira some.

**Perguntas de discussão para fechar (as mesmas do guia):**
- Por que o McpServer tem ingress **interno** e o FlowEvents **externo**?
- Se alguém der `curl` direto no McpServer forjando `X-Entra-OID`, o que acontece? (**401** — falta o `X-Gateway-Key`.)
- Onde está a chave do Gemini? (no **proxy server-side**; o front só conhece a URL do proxy.)
- Por que a notificação pós-compra não tem nó próprio? (**trade-off** da re-arquitetura: inline no Consumer.)

**Gancho:** "E o que isso tudo significa, junto? O encerramento."

## Slide 21 — "ENCERRAMENTO · O Living Lab completo" · ≈ Storyboard S11 [~4 min]

> **Tom celebrativo — este é o fecho do workshop inteiro, não só da fase.**

**Fala de fechamento (leia com a turma):**
> "Vocês começaram com **uma compra de ingresso** e terminaram com um sistema **Azure-native** completo: assíncrono (Service Bus), com **gateway** e **identidade federada**; um **chatbot** que conversa com os dados **sem nunca poder alterá-los**; e uma tela onde a própria arquitetura **se acende** diante dos seus olhos. Isso é uma **Grande Final**. Parabéns — vocês construíram tudo, do zero, com as próprias mãos."

**A lição que se leva (deixe no ar):** guardião único · segurança **por construção** · observabilidade correlacionada · **simplicidade deliberada** (remover > substituir). "É o que se leva para **qualquer** sistema em produção depois do workshop."

## Slide 22 — "Obrigado!" · ≈ fecho do `.pptx` [~2 min]

**Fala:** "Você construiu tudo — do zero, com as próprias mãos. `Oitavas → Quartas → Final`, Living Lab Azure-Native."

**Aponte:** o **quiz de encerramento** está no guia do aluno (`final-portal-guide.md`). Não está no repo — é link externo (Google Forms), mesmo padrão de Oitavas/Quartas.

---

## Apêndices úteis para o instrutor (referência rápida)

- **Chave Gemini** (se alguém não tiver): Apêndice A do guia — conta Gmail exclusiva → https://aistudio.google.com/apikey → *Create API key in new project*. Modelo do lab: **`gemini-2.5-flash`**.
- **Modelo real vs. comentário:** Apêndice B — runtime é `gemini-2.5-flash`; o comentário de cabeçalho do `gemini.ts` ainda cita `2.0-flash` (inofensivo, fora de escopo corrigir).
- **Troubleshooting F5 (usar durante as Fases 1–5):** Apêndice C do guia — 401 no `/mcp` / 502 / `tools/list`=8 / guard de key no bundle.
- **Troubleshooting F6 (usar durante as Fases 6–9):** Apêndice D do guia — 6 nós / 403 (MI sem `Log Analytics Reader`) / WebSocket / tier Serverless / nó de notificação inexistente.
- **As 7 tools (tenha à mão):** `consultar_disponibilidade` · `verificar_ingresso` · `consultar_bracket` · `consultar_partidas` · `consultar_classificacao` · `consultar_time` · `consultar_estadio`. Código: `FifaTicketTools.cs`.
- **Os 5 nós (tenha à mão):** 0 Gateway YARP · 1 Function Entry · 2 Service Bus · 3 Function Consumer (notificação **inline**) · 4 SQL. Código: `FlowEventType.cs` / `flowNodes.ts`.

---

## Lembretes finais para o facilitador

- **Estas notas seguem o DECK, slide a slide.** Os dois momentos ao vivo (**slide 11** = regra de ouro; **slides 16–18** = smoke dos 5 nós) são onde o deck e o hands-on se encontram — não corra por eles.
- **Dois "PAUSA O DECK":** depois do slide 10 (F5 hands-on, guia Fases 1–4) e depois do slide 16 (F6 hands-on, guia Fases 6–8). Volte sempre ao deck para o clímax.
- **"Segurança por construção, não por roteamento"** é o fio condutor — plante no slide 3, ensine no slide 10, **prove** no slide 11.
- **"A senha… " não; aqui a frase-chave é a alucinação de texto:** no slide 11, a nuance "o LLM pode *dizer* que criou o alerta, mas texto **não é** tool call" é obrigatória. Não pule.
- **Sempre 5 nós, nunca 6.** O n8n só aparece como **"o que removemos"** (slides 17–18). Nunca como componente presente, nunca "automação no-code".
- **Retro-compatibilidade é regra dura:** repita no slide 2 e no slide 19. Nada de Oitavas/Quartas deixou de funcionar.
- Tom: prático, honesto sobre trade-offs (Key Vault **entregue** — mas SQL sem senha é showcase/Fase 2; notificação invisível = trade-off), sem hype. A turma é técnica e respeita transparência — igual à F1/F2.
