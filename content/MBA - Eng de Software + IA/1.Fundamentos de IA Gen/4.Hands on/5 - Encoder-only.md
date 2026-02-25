---
title: 5 - Encoder-only
draft: false
tags:
---
### 1. ⚡ Resumo Expandido

A aula abordou a fundação das arquiteturas de **Large Language Models (LLMs)**, focando especificamente na topologia **Encoder-only** (Apenas Codificador). O professor contextualizou a evolução histórica, partindo do paper seminal _"Attention Is All You Need"_ (2017), que introduziu os Transformers, até o lançamento do **BERT** pelo Google em 2018.

Diferente dos modelos geradores (como o GPT), que são **Decoder-only** e preveem o próximo token (autoregressivos), os modelos Encoder-only são desenhados para **compreensão profunda e representação semântica**. A característica definidora é a **Atenção Bidirecional**: o modelo "lê" a frase inteira de uma vez, permitindo que cada token "enxergue" tanto o contexto à esquerda quanto à direita simultaneamente.

O treinamento desses modelos baseia-se primordialmente no **Masked Language Modeling (MLM)**, onde palavras são ocultadas aleatoriamente e o modelo deve recuperá-las baseando-se no contexto. As principais aplicações de mercado discutidas foram **Análise de Sentimento**, **NER (Reconhecimento de Entidades Nomeadas)** e, crucialmente para sistemas modernos, a **Busca Semântica** via Embeddings.

---

### 2. 🔍 Deep Dive: Conceitos & Teoria

#### **Arquitetura Encoder-Only e Atenção Bidirecional**

- **Na Aula:** O professor explicou que essa arquitetura usa apenas a primeira parte do Transformer para "entender" o texto, olhando para frente e para trás na sentença ao mesmo tempo, diferentemente do GPT que só olha para o passado.
    
- **Deep Dive (Pesquisa):**
    
    - **Definição Técnica:** O Encoder é composto por uma pilha de camadas que possuem dois subcomponentes principais: um mecanismo de **Self-Attention Multi-Head** e uma rede **Feed-Forward** posicionwise.
        
    - **Diferença Crítica:** No Encoder, a matriz de atenção **não possui mascaramento causal** (look-ahead mask). Isso significa que, matematicamente, a representação do token na posição $i$ pode atender diretamente à informação na posição $i+k$ (futuro) e $i-k$ (passado) na mesma camada.
        
    - **Origem:** Paper _"BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding"_ (Devlin et al., 2018).
        

#### **Masked Language Modeling (MLM)**

- **Na Aula:** Descrito como a técnica de "tapar buracos", onde palavras são ocultadas e o modelo aprende prevendo-as.
    
- **Deep Dive (Pesquisa):**
    
    - **O Processo:** Tipicamente, 15% dos tokens de entrada são escolhidos. Desses, 80% são substituídos pelo token `[MASK]`, 10% por uma palavra aleatória e 10% mantidos originais.
        
    - **Objetivo de Função:** O modelo minimiza a perda (Cross-Entropy Loss) apenas na previsão dos tokens mascarados. Isso força o modelo a aprender representações contextuais profundas, pois ele precisa entender a sintaxe e a semântica para preencher a lacuna corretamente.
        

#### **Embeddings e Similaridade de Cosseno**

- **Na Aula:** O professor usou o exemplo de "Como fazer um bolo" e "Receita de bolo" terem vetores (embeddings) próximos, permitindo busca semântica em vez de busca por palavra-chave.
    
- **Deep Dive (Pesquisa):**
    
    - **Dense Vector Representations:** A saída do token especial `[CLS]` (no BERT) ou a média das saídas da última camada (Mean Pooling) gera um vetor denso (ex: 768 dimensões) que representa a sentença inteira.
        
    - **SOTA Atual:** Hoje, modelos como **Nomic-Embed** ou **Jina-Embeddings** superam o BERT original no benchmark **MTEB** (Massive Text Embedding Benchmark), suportando janelas de contexto muito maiores (8k+ tokens) contra os 512 tokens originais do BERT.
        

---

### 3. 🛠️ Engenharia: Arquiteturas e Agentes

#### **Padrão: RAG (Retrieval-Augmented Generation)**

- **Funcionamento:** Em sistemas de agentes modernos, modelos Encoder-only são a espinha dorsal do módulo de **Memória**. Eles não geram a resposta final, mas são responsáveis por converter a consulta do usuário e os documentos da base de conhecimento em vetores.
    
- **Exemplo da Aula:** O professor mencionou a "Busca Semântica".
    
- **Aplicação Prática (Engenharia):**
    
    1. **Ingestão:** Documentos são quebrados em chunks.
        
    2. **Encoding:** Um modelo Encoder (ex: `text-embedding-3-small` ou `all-MiniLM-L6-v2`) converte chunks em vetores.
        
    3. **Armazenamento:** Vetores vão para um Vector DB (Pinecone, Weaviate).
        
    4. **Retrieval:** A query do usuário passa pelo _mesmo_ Encoder e busca-se os vizinhos mais próximos (k-NN).
        

#### **Família de Modelos (Zoologia do BERT)**

- **BERT (Base):** O original.
    
- **RoBERTa (Robustly Optimized BERT):** (Liu et al., 2019) - Removeu a tarefa de "Next Sentence Prediction" (NSP) do treinamento do BERT e usou muito mais dados/computação. Geralmente performa melhor que o BERT.
    
- **DistilBERT:** (Sanh et al., 2019) - Usa **Knowledge Distillation** para reduzir o tamanho do modelo em 40% retendo 97% da performance. Ideal para _edge devices_ ou latência baixa.
    
- **ALBERT:** (Lan et al., 2019) - Usa fatoração de matrizes e compartilhamento de parâmetros entre camadas para reduzir o consumo de memória (embora nem sempre reduza a latência de inferência).
    

---

### 4. 📚 Bibliografia Estendida e Referências (Pesquisa)

- **Paper Seminal:** [BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding](https://arxiv.org/abs/1810.04805) - Leitura obrigatória para entender a base da NLP moderna.
    
- **Blog Técnico:** "The Illustrated BERT" por Jay Alammar. É a referência visual definitiva para entender como os vetores fluem no Encoder.
    
- **Biblioteca Padrão:** `sentence-transformers` (SBERT). É a biblioteca Python mais utilizada para gerar embeddings de alta qualidade usando arquiteturas baseadas em BERT/RoBERTa.
    
- **Benchmark:** [MTEB Leaderboard](https://huggingface.co/spaces/mteb/leaderboard) no Hugging Face. Essencial para escolher qual modelo Encoder usar para busca semântica hoje.
    

---

### 5. ⚠️ Pontos de Atenção e Trade-offs

- **Não Gerativo:** O professor alertou corretamente: **não use BERT para gerar texto**. Ele não é autoregressivo. Tentar gerar texto com ele é computacionalmente proibitivo e produz resultados ruins (Gibbs sampling).
    
- **Limite de Contexto (Context Window):** A maioria dos modelos baseados em BERT clássico (e seus derivados diretos) tem um limite rígido de **512 tokens**. Para documentos longos, é necessário fazer "chunking" (fatiamento) ou usar arquiteturas mais novas como _Longformer_ ou modelos de embedding modernos que suportam 8k tokens.
    
- **Custo Computacional de Atenção:** A atenção bidirecional tem complexidade quadrática $O(n^2)$ em relação ao tamanho da entrada. Isso torna o processamento de textos muito longos pesado.
    

---

### 6. 📝 Quiz Prático

1. **Por que a arquitetura Encoder-only (como o BERT) é considerada "bidirecional" enquanto a Decoder-only (como o GPT) é "unidirecional"?**
    
    a) Porque o BERT lê dois textos ao mesmo tempo.
    
    b) Porque o mecanismo de atenção do BERT permite ver tokens passados e futuros simultaneamente, enquanto o GPT usa mascaramento para ver apenas o passado.
    
    c) Porque o BERT gera texto de trás para frente.
    
2. **Qual é a principal técnica de treinamento (loss function) utilizada para ensinar modelos Encoder-only a entender contexto?**
    
    a) Next Token Prediction (NTP).
    
    b) Reinforcement Learning from Human Feedback (RLHF).
    
    c) Masked Language Modeling (MLM).
    
3. **Em um sistema de Agente Inteligente usando RAG, qual o papel típico de um modelo Encoder-only (como o `all-MiniLM`)?**
    
    a) Gerar a resposta final para o usuário.
    
    b) Criar os embeddings (vetores numéricos) dos documentos e da pergunta para permitir a busca semântica.
    
    c) Orquestrar a chamada de ferramentas (Function Calling).
    
4. **(Desafio SOTA)** O professor mencionou o **ALBERT** como sendo "mais eficiente" em memória, mas "mais caro" (lento) em alguns casos. Tecnicamente, por que o ALBERT pode ser mais lento na inferência ou treino que o DistilBERT, mesmo tendo menos parâmetros?
    
    a) Porque ele usa uma GPU mais antiga.
    
    b) Porque ele compartilha pesos entre as camadas (cross-layer parameter sharing), o que obriga a execução sequencial completa das 12 ou 24 camadas lógicas, não reduzindo a profundidade computacional, apenas o armazenamento físico.
    
    c) Porque ele usa uma arquitetura Decoder.
    

---

**Gabarito:** 1-b, 2-c, 3-b, 4-b