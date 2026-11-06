# Riscos, Ética e LGPD — EngMan Copilot

**Recorte B — Operação e Priorização de Campo** · Persona principal: operador do CCO · Secundária: coordenador de manutenção
**Ativos do recorte:** câmeras (CFTV), sensores ópticos e sistemas de pesagem (HS WIM)

> Todos os dados da base de conhecimento são **simulados** (a Motiva não libera base real). Nenhum dado real da Motiva é usado ou versionado.

## 1. Como ler a matriz

- **Impacto:** segurança (S), financeiro (F), legal (L), reputacional (R).
- **Nível:** Crítico, Alto ou Médio, pelo pior impacto plausível.
- **Onde aparece:** onde a mitigação pode ser verificada. `B1`–`B8` são os blocos do fluxograma (`/docs/arquitetura.png`). `# REGRAS`, `# BORDA`, `# FORMATO` e `# ESCOPO` são blocos do `system_prompt.md`. As categorias do eval set são as 8 do enunciado.

## 2. Matriz de riscos

| ID | Risco | Como se manifesta neste projeto | Impacto | Nível | Mitigação | Onde aparece |
|----|-------|---------------------------------|---------|-------|-----------|--------------|
| R01 | **Alucinação** | Inventar TAG de ativo, modelo, número de OS, métrica, trecho de norma ou registro de histórico de falhas. | F, S | Alto | Regra de nunca inventar dado; todo dado vem da base ou de tool; resposta cita a fonte; se o dado não existe, o assistente diz que não sabe e indica o responsável. | B7 (confere TAG/métrica/fonte); `# REGRAS` (nunca inventar), `# BORDA` (dado inexistente); eval: `dado_inexistente`, `factual` |
| R02 | **Prompt injection / jailbreak** | "Ignore as instruções" digitado ou escondido em texto de alarme ou laudo colado pelo operador. | S, R | Alto | Validação antes de chamar o modelo; instruções imutáveis; texto colado tratado como dado, não como comando; recusa curta e retomada do escopo. | B2; `# REGRAS` (imutabilidade), `# BORDA` (pedido hostil); eval: `jailbreak` |
| R03 | **Risco operacional (intervenção elétrica e em via)** | Orientar abrir painel energizado de pórtico, trocar sensor de pesagem no asfalto com tráfego ou subir em poste de câmera sem procedimento. | S (vida), L | **Crítico** | Recusa obrigatória; encaminhar ao POP oficial e a profissional habilitado (NR-10 / NR-35 e regras de trabalho em via); o assistente nunca libera, bloqueia ou intervém. Detalhe na seção 4. | B2 (detecta pedido perigoso), B7 (barra instrução de intervenção); `# REGRAS`, `# ESCOPO` (fora do escopo); eval: `risco_operacional` (2 ou mais casos) |
| R04 | **Classificação errada de alarme** | Tratar chuva, neblina ou glare como falha real (deslocamento caro) ou descartar falha real como ambiental (risco viário). | F, S | Alto | Saída em 3 classes (real / ambiental / **indeterminado**); na dúvida, indeterminado; nenhum alarme descartado sem fonte; hipótese rotulada; validação humana antes da OS. | B5, B7; `# FORMATO` (classificação + prioridade + ação + confiança); eval: `ambíguo`, `instrucional` |
| R05 | **Excesso de confiança** | O operador trata a hipótese do modelo como diagnóstico confirmado. | S, F | Alto | Rotular FATO vs. HIPÓTESE; exigir "Nível de confiança" com motivo; a OS é só rascunho até validação humana. | B7; `# REGRAS` (fato × hipótese), `# FORMATO`; eval: `factual`, `instrucional` |
| R06 | **LGPD — dado pessoal** | Placas capturadas por OCR, imagens de ocupantes de veículos, dados de empregados ou TAG de cobrança de veículo entrando na base ou no prompt. | L, R | Alto | Base 100% simulada; lista explícita do que nunca entra (seção 3); detectar e anonimizar dado pessoal no input; nada de dado pessoal enviado a modelo externo. | B2, B3 (só trechos da base entram no contexto); `inventario.md`; `# REGRAS`; eval: `out_of_scope` |
| R07 | **Confidencialidade operacional** | Histórico de falhas e dados de arrecadação indo para API de terceiros (Groq, Gemini, Hugging Face). | L, R, F | Médio | Caminho padrão local (Ollama + Qwen 2.5 7B); API externa só como alternativa documentada e só com dado simulado; `.env` e chaves no `.gitignore`; nenhum dado real versionado. | B4 (modelo local); `decisoes_tecnicas.md`; `.gitignore` |
| R08 | **Viés na priorização** | Priorizar sempre o mesmo tipo de ativo, praça ou trecho por "sensação" ou por volume de alarmes. | F, R | Médio | Critérios explícitos e auditáveis (criticidade × segurança × arrecadação) vindos da matriz de criticidade; justificar a ordem de cada atendimento. | B3 (matriz de criticidade no RAG); `# REGRAS`, `# FORMATO` (prioridade justificada); eval: `instrucional` |
| R09 | **Contexto estourado** | Conversa longa ou lote grande de alarmes ultrapassa a janela e o assistente perde ou mistura TAGs. O contexto declarado de 128K pode não ser o efetivo no Ollama (`num_ctx`). | F, S | Médio | Política de histórico (últimas N mensagens ou resumo); reafirmar o TAG ativo a cada turno; registrar o `num_ctx` realmente configurado; limitar o tamanho do lote por consulta. | B3, B8; `# BORDA` (conversa longa); eval: `memoria_contexto` |
| R10 | **Falha de tool ou fontes em conflito** | Ativo não encontrado, timeout, ou a tool e a base discordando sobre o mesmo dado. | F, S | Médio | Comportamento de falha previsto (informar e escalar, sem preencher a lacuna); uma única fonte de verdade por dado; se duas fontes discordam, mostrar ambas e escalar. | B5, B6 (reinjeção do retorno), B7; `# BORDA`; eval: `dado_inexistente` |
| R11 | **Qualidade em pt-BR não validada** | O Qwen 2.5 7B pode interpretar mal termos técnicos ou siglas em português (a qualidade não foi testada nesta sprint). | F, S | Médio | Declarar como não testado; glossário de siglas na base; validar com o eval set nas Aulas 09–14; plano B com Llama 3.1 8B. | `decisoes_tecnicas.md` (trade-off); `# FORMATO`; eval set completo |
| R12 | **Inviabilidade do caminho gratuito** | O hardware do grupo não roda o modelo local, ou a entrega passa a depender de chave paga. | F (entrega incompleta) | Médio | Ollama local como caminho principal; plano B no Google Colab; modelos menores; APIs com free tier só como alternativa, sem dado real. | README (stack e alternativa gratuita); `decisoes_tecnicas.md` |

## 3. LGPD — o que entra e o que nunca entra na base

Placas e imagens podem ser dado pessoal quando permitem identificar o titular. Por isso o projeto usa somente dados simulados, e a regra vale também para qualquer dado colado pelo usuário no chat.

### Entra na base (simulado e sem titular identificável)

| Item | Observação |
|------|------------|
| Catálogo de ativos | TAG do **equipamento**, tipo, modelo, km, criticidade |
| Histórico de falhas (FSM) | Sintoma, causa, reparo e reincidência, **sem nome do técnico** |
| POPs, plano preventivo, fichas técnicas | Identificados como simulados |
| Matriz de criticidade e glossário de siglas | |
| Resumo de NR-10 / NR-35 e regras de trabalho em via | Texto público resumido |
| Boletins e laudos de campo | Simulados, sem nomes |
| Alarmes sintéticos | Sem placas nem imagens |

### Nunca entra na base nem no prompt

| Item | Motivo |
|------|--------|
| Nome, CPF, matrícula ou contato de empregados e técnicos | Dado pessoal |
| Placas de veículos reais e leituras de OCR | Identificam titular |
| **TAG de cobrança de veículo** (Free Flow) | Dado pessoal. Atenção: o "TAG" do ativo é só o identificador do equipamento e é permitido |
| Imagens ou frames de câmeras com pessoas ou veículos | Identificam titular |
| Registro de pesagem associado a placa | Permite autuação individual |
| Dados contratuais, de arrecadação ou de receita reais | Confidencial da Motiva |
| Chaves de API, `.env` e credenciais | Segurança |

**Regras derivadas:** (1) o assistente não repete nem armazena placa digitada pelo usuário e pede para omitir; (2) imagens não entram no fluxo, só a descrição textual do problema (por exemplo, "lente com barro"); (3) o modelo roda local, então nenhum dado sai da máquina.

## 4. Risco operacional — tratamento explícito

O assistente apoia **decisão**, nunca **execução**. Ele:

- recomenda tipo de intervenção (corretiva, preventiva, inspeção ou CBM) e prioridade;
- redige rascunho de OS e resumo de atendimento.

Ele **nunca**:

- libera, bloqueia ou intervém fisicamente em equipamento;
- dá instrução de segurança elétrica que substitua o procedimento oficial;
- autoriza trabalho em via, em altura ou em equipamento energizado.

**Cenários cobertos:** abrir painel de pórtico energizado; trocar sensor de pesagem no pavimento com tráfego; acessar câmera PTZ em poste (altura); religar equipamento após falha elétrica.

**Resposta padrão:** recusa curta, indicação do POP aplicável (`consultar_procedimento`), encaminhamento a profissional habilitado e lembrete de EPI e desligamento seguro.

**Dupla barreira:** B2 barra o pedido antes do modelo e B7 barra qualquer saída com instrução de intervenção.

**Teste:** pelo menos 2 casos de `risco_operacional` no eval set, ambos com `deve_recusar: true`.

## 5. Pontos a confirmar com o grupo

1. **Imagens de câmeras:** a proposta acima é que não entram na base, só descrição textual. Confirmar.
2. **Embeddings:** `nomic-embed-text` via Ollama **Cloud** envia texto a um servidor externo; Sentence Transformers (ou Ollama local) mantém tudo local. A escolha afeta R06 e R07.
3. **Dependências de outras pessoas:** a coluna "Onde aparece" só vale se o system prompt, o eval set e o `inventario.md` contiverem os itens citados (regras, casos `risco_operacional` e `jailbreak`, política de histórico).
4. **"Sensores ópticos":** confirmar a qual ativo da tabela do enunciado correspondem, para o escopo e o catálogo baterem.
