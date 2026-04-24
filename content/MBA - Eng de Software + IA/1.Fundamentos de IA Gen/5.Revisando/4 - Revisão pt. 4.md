---
title: 4 - Revisão pt. 4
draft: false
tags:
---
Nesta última parte do módulo, a aula focou nas barreiras técnicas dos LLMs e em como os engenheiros lidam com elas em produção. O professor enfatizou que a **alucinação** é uma característica inerente e até necessária para a criatividade do modelo , exigindo o padrão de **Human-in-the-Loop (HITL)** para tarefas críticas (como geração de código).

Foi discutida a dificuldade dos modelos com **raciocínio simbólico** (lógica e matemática) e a ausência de conhecimento em tempo real. Para resolver isso, utilizam-se buscas na web restritas a fontes confiáveis e a técnica de **RAG (Retrieval-Augmented Generation)**. A aula também incluiu exercícios práticos sobre a manipulação do comportamento do LLM via **Temperatura, Top-K e Top-P** , cálculo de similaridade de **Embeddings** e finalizou com uma categorização das três arquiteturas principais do Transformer: **Encoder-only (BERT)**, **Decoder-only (GPT)** e **Encoder-Decoder (BART)**.

### 2. 🔍 Deep Dive: Conceitos & Teoria

- **Controle de Geração (Temperatura, Top-K, Top-P)**:
    
    - _Na Aula:_ Ajustar esses parâmetros controla a criatividade e a coerência do modelo. O Top-K limita as opções às "K" palavras mais prováveis.
        
    - _Deep Dive (Pesquisa):_ A **Temperatura** matematicamente divide os _logits_ (pontuações cruas da rede neural) antes de aplicar a função _Softmax_. Uma temperatura de 0 torna o modelo determinístico (sempre escolhe a maior probabilidade). O **Top-P (Nucleus Sampling)**, introduzido por Holtzman et al. (2019), é considerado superior ao Top-K moderno, pois ele seleciona dinamicamente a quantidade de palavras com base na massa de probabilidade cumulativa $P$, adaptando-se a contextos onde o modelo tem muita certeza ou muita incerteza.
        
- **Raciocínio Simbólico vs. Probabilidade**:
    
    - _Na Aula:_ LLMs têm dificuldade com raciocínio lógico e simbólico.
        
    - _Deep Dive (Pesquisa):_ Como o professor explicou, LLMs são máquinas probabilísticas. Se você perguntar $345 \times 892$, ele não "calcula", ele tenta prever os tokens numéricos mais prováveis. O "Estado da Arte" (SOTA) para resolver isso hoje é o padrão **PAL (Program-Aided Language Models)**. Em vez de pedir a resposta matemática, o engenheiro instrui o LLM a escrever um script Python que resolva o problema. O framework executa o código em uma _sandbox_ e devolve a resposta exata.
        
- **Arquiteturas (Encoder vs. Decoder)**:
    
    - _Na Aula:_ O professor afirmou que o Encoder (BERT) é para compreensão textual, o Decoder (GPT-4) é para geração, e o Encoder-Decoder (BART) para tradução/sumarização.
        
    - _⚠️ Nota do Pesquisador (Atualização de Mercado):_ O professor apresentou a visão clássica (correta até 2020-2021). Ele afirmou que _"Utilizar o GPT-4 para uma tarefa de compreensão não seria a melhor opção; um modelo BERT seria mais adequado"_. **No SOTA atual, isso não é mais verdade.** Devido ao ganho colossal de escala (Scale Laws), modelos _Decoder-only_ gigantes (como GPT-4o, Claude 3.5 e Llama 3) superam amplamente os modelos BERT em benchmarks de compreensão (NLU), classificação e análise de sentimentos. Hoje, modelos como o BERT sobrevivem na indústria especificamente por serem muito leves e baratos para gerar **Embeddings** e rodar em dispositivos com poucos recursos.
        

### 3. 🛠️ Engenharia: Arquiteturas e Agentes

- **Padrão/Framework: Human-in-the-Loop (HITL) & Agentic RAG**
    
    - _Funcionamento:_ Em sistemas corporativos críticos, a IA não tem a palavra final. O HITL adiciona um "freio" no pipeline, exigindo aprovação ou edição humana.
        
    - _Exemplo da Aula:_ Revisão obrigatória de código gerado por IA antes de ir para produção ou de FAQs. Restringir a busca na web para evitar alucinações com fontes duvidosas.
        
    - _Referência Externa/SOTA:_ Na engenharia moderna com **LangGraph** ou **Google ADK**, o HITL é implementado através de _breakpoints_ (pontos de interrupção) no grafo de execução. O Agente faz a pesquisa (usando um **Agentic RAG** que cruza a base da empresa com uma busca web com _whitelist_ de domínios ) e gera a resposta. A execução congela e envia a resposta para uma interface de usuário. Após o humano clicar em "Aprovar" ou "Modificar", o grafo retoma a execução.
        

### 4. 📚 Bibliografia Estendida e Referências (Pesquisa)

- **Papers Recomendados:**
    
    - _The Curious Case of Neural Text Degeneration (Holtzman et al., 2019)_: O paper que introduziu o **Top-P (Nucleus Sampling)**, mostrando por que ele gera textos mais naturais e coerentes do que o Top-K.
        
    - _Lost in the Middle: How Language Models Use Long Contexts (Liu et al., 2023)_: Aborda a limitação da "Janela de Contexto" (Context Window) citada na aula. Prova que os LLMs lembram muito bem do começo e do fim de um texto longo, mas ignoram completamente as informações que estão no meio.
        
    - _PAL: Program-aided Language Models (Gao et al., 2022)_: A base teórica de como fazer LLMs resolverem problemas lógicos e matemáticos escrevendo código.
        

### 5. ⚠️ Pontos de Atenção e Trade-offs

- **O Perigo do Web Search Aberto:** O professor alertou que deixar o modelo buscar livremente na web aumenta a margem de erro, pois ele pode assimilar _fake news_ ou fontes duvidosas como verdade. A solução de engenharia é criar uma ferramenta (_tool_) de busca que restrinja os parâmetros apenas para URLs institucionais ou acadêmicas.
    
- **Truncamento por Limite de Contexto:** Como visto no _hands-on_ do professor, o limite de tokens da janela de contexto não é apenas financeiro; ultrapassá-lo causa o corte (truncamento) abrupto da requisição ou resposta.
    

### 6. 📝 Quiz Prático

1. **Segundo a aula, por que não é recomendado tentar eliminar 100% da "margem de alucinação" de um modelo de linguagem?**
    
    - _Resposta:_ Porque a alucinação é um subproduto da natureza probabilística do modelo. Essa mesma margem que causa alucinações é o que permite ao modelo ser criativo, fluído e capaz de gerar conteúdos inéditos. Se zerarmos essa margem, o modelo se torna robótico ou incapaz de operar bem em tarefas de geração.
        
2. **Qual é a diferença prática entre os parâmetros Top-K e Top-P no controle de geração de respostas?**
    
    - _Resposta:_ O **Top-K** corta o vocabulário baseando-se em um número fixo (ex: seleciona apenas entre as 50 palavras mais prováveis). O **Top-P** (Nucleus Sampling) usa uma base percentual cumulativa (ex: seleciona palavras até que a soma das probabilidades delas atinja 90%), adaptando a quantidade de opções à "certeza" do modelo naquele momento da frase.
        
3. **Para mitigar os riscos em tarefas críticas como geração de código ou atendimento ao cliente, qual padrão de engenharia o professor recomenda?**
    
    - _Resposta:_ O padrão de supervisão humana, conhecido no mercado como **Human-in-the-Loop (HITL)**. Ele garante que a IA atue como um "rascunho" ou assistente, e o humano atue como validador e decisor final.
        
4. **Desafio (Pesquisa + Aula): O professor sugeriu que modelos Encoder-only (como o BERT) são melhores que o GPT-4 para tarefas de compreensão. Por que a engenharia moderna discorda disso na prática geral, mas ainda mantém o BERT vivo para casos específicos?**
    
    - _Resposta:_ A engenharia moderna provou (via "Leis de Escala" / _Scale Laws_) que modelos Decoder-only gigantescos adquirem capacidades _emergentes_ de compreensão que superam o BERT simplesmente pelo volume massivo de dados e parâmetros. No entanto, o BERT continua vivo porque um GPT-4 custa milhares de vezes mais caro em processamento. Para tarefas muito simples e de alto volume (como classificar se um tweet é positivo ou negativo, ou gerar _Embeddings_ rápidos para um vetor de busca), usar o BERT localmente é incrivelmente mais barato e possui menor latência.