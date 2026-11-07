# Decisões técnicas — Stack do EngMan Copilot

**Responsável:** Miguel (Pessoa 4 — Arquitetura, Stack e Segurança)
**Etapa:** Sprint 01 (documentação; nenhuma das opções abaixo foi executada ou testada)

## 1. Regra que orienta as escolhas

A solução precisa ter um **caminho de execução 100% gratuito**. Nenhuma etapa pode depender de API paga ou de chave de API. Modelos pagos podem ser citados como alternativa, mas não como requisito. A seção 6 descreve o caminho gratuito completo e os planos B e C.

## 2. Comparação de LLMs

| Critério | Llama 3.1 8B | Qwen 2.5 7B Instruct |
|----------|--------------|----------------------|
| Custo de uso | Gratuito via Ollama local | Gratuito via Ollama local |
| Licença e termos | Llama Community License (termos e política de uso próprios) | Apache 2.0 (permissiva) |
| Qualidade em pt-BR | Português entre os idiomas oficialmente suportados | Português suportado, mas a qualidade ainda precisa ser testada |
| Janela de contexto | Até 128K tokens (declarado) | Até 128K tokens (declarado) |
| Privacidade (execução local) | Permite processamento local | Pode rodar localmente |
| Integração com Python | Biblioteca `ollama` | Biblioteca `ollama` |
| Maturidade e documentação | Ecossistema consolidado | Boa documentação e ecossistema |

**Notas**
- A janela declarada **não** é a janela efetiva: o Ollama usa por padrão um contexto bem menor, que deve ser configurado (`num_ctx`) e acompanhado do consumo de memória.
- Qualidade em pt-BR: não há teste nesta sprint. A coluna reflete apenas o que a documentação oficial informa.
- Requisitos de hardware dos dois modelos (porte 7B/8B, quantizados) ainda não foram medidos pelo grupo. A recomendação do Ollama é confirmada na página de cada modelo.

### Escolha: Qwen 2.5 7B Instruct

**Justificativa.** Licença Apache 2.0, suporte documentado ao português, execução local gratuita via Ollama (menor dependência de serviços externos e maior controle sobre os dados) e porte compatível com hardware mais limitado.

**Trade-off aceito.** Priorizamos licença permissiva, privacidade local e custo zero. Aceitamos que a qualidade em português brasileiro ainda não foi validada, enquanto o Llama 3.1 já tem suporte oficial listado, e que o desempenho em hardware limitado precisa ser medido.

**Plano B.** Se a validação posterior (Aulas 09–14, usando o eval set) mostrar qualidade insuficiente em pt-BR, trocar para o Llama 3.1 8B. A troca é simples porque ambos usam a mesma biblioteca `ollama`.

## 3. Comparação de Vector Stores

| Critério | ChromaDB | FAISS |
|----------|----------|-------|
| Custo | Gratuito, open source | Gratuito e open source; exige recursos computacionais |
| Licença | Apache 2.0 | MIT |
| Privacidade | Pode operar localmente | Pode operar localmente |
| Integração com Python | Sim | Wrappers para Python/NumPy |
| Maturidade e documentação | Boa documentação oficial | Projeto consolidado, desenvolvido pela Meta |
| Qualidade em pt-BR | Não se aplica diretamente | Depende do modelo de embeddings |
| Busca vetorial | Sim | Sim |
| Armazenamento de documentos | Integrado | Armazena índices vetoriais; documentos e metadados exigem estrutura complementar |
| Filtros por metadados | Suportados | Exigem implementação complementar |
| Janela de contexto | Não se aplica diretamente | Não se aplica diretamente |

**Nota.** A qualidade da busca em português **não** é propriedade do Vector Store: depende do modelo de embeddings e da configuração da busca (tamanho do chunk, `top-k`). Por isso, as linhas de pt-BR e de janela de contexto não são critérios de decisão entre os dois.

### Escolha: ChromaDB

**Justificativa.** O projeto exige guardar metadados junto a cada trecho (TAG do ativo, tipo de ativo, data, tipo de documento) para permitir busca filtrada. O ChromaDB gerencia vetores, documentos e metadados em uma estrutura integrada, com integração direta com Python, o que simplifica o RAG sobre manuais, procedimentos e históricos de manutenção.

**Trade-off aceito.** Priorizamos simplicidade de implementação e filtros por metadados integrados. Aceitamos abrir mão do controle fino sobre índices e do desempenho em grande escala do FAISS, o que não pesa em uma base pequena e simulada.

## 4. Demais camadas da stack

| Camada | Proposta | Justificativa | Alternativa |
|--------|----------|---------------|-------------|
| Orquestração | Chamada direta via SDK (Ollama) | Menos dependências e alinhada às funções simples do Módulo 3 (`chamar_llm()`, histórico, `montar_prompt()`) | LangChain (LCEL), caso a orquestração cresça |
| Embeddings | Sentence Transformers local, com modelo multilíngue (por exemplo, `paraphrase-multilingual-MiniLM-L12-v2`) | Custo zero, nenhum dado sai da máquina e suporte multilíngue, necessário para pt-BR | nomic-embed-text via Ollama: o texto é voltado principalmente ao inglês, e a versão Cloud envia dados a servidor externo |
| Interface | Terminal interativo, com Gradio como evolução | Previsto no plano, sem implementação nesta sprint | Streamlit |
| Ambiente | VS Code com Python 3.10+ | Declarar no README | Google Colab (plano B) |
| Versionamento | Git e GitHub público | Obrigatório desde a Sprint 01 | — |

## 5. O que fica fora do repositório

Nenhum `.py`, notebook, chave de API ou `.env` com valores reais entra no repositório da Sprint 01.

## 6. Alternativa gratuita documentada

| Camada | Caminho principal (custo zero) |
|--------|--------------------------------|
| LLM | Qwen 2.5 7B Instruct via Ollama local |
| Vector Store | ChromaDB local |
| Embeddings | Sentence Transformers local |
| Orquestração | SDK Ollama em Python |
| Ambiente | VS Code ou Jupyter local, Python 3.10+ |

**Plano B (hardware insuficiente).** Rodar o mesmo conjunto no **Google Colab** (nível gratuito), ou usar um modelo menor no Ollama.

**Plano C (somente se necessário).** APIs com nível gratuito (Groq, Google AI Studio/Gemini, Hugging Face Inference API). Ressalvas: os dados saem da máquina (usar **apenas dados simulados**), há limites de uso e os termos podem mudar.

**Modelos pagos** (GPT, Claude etc.) podem ser mencionados como opção, nunca como requisito.

## 7. Limitações e validações pendentes

1. Testar a qualidade do Qwen 2.5 7B em pt-BR com o eval set (Aulas 09–14).
2. Medir memória e velocidade em hardware real e registrar o `num_ctx` configurado.
3. Avaliar a qualidade da recuperação vetorial (modelo de embeddings, tamanho do chunk, `top-k`).

## 8. Fontes

- https://ollama.com/library/llama3.1
- https://github.com/meta-llama/llama-models/blob/main/models/llama3_1/MODEL_CARD.md
- https://github.com/meta-llama/llama-models/blob/main/models/llama3_1/LICENSE
- https://github.com/ollama/ollama-python
- https://ollama.com/library/qwen2.5
- https://huggingface.co/Qwen/Qwen2.5-7B-Instruct
- https://qwen.readthedocs.io/en/v2.5/
