---
title: 4 - Estratégias de Amostragem Top-K e Top-P
draft: false
tags:
---
### 1. ⚡ Resumo Expandido

A aula aprofundou-se nos mecanismos de **decodificação estocástica** (stochastic decoding) utilizados por Large Language Models (LLMs) para selecionar o próximo token em uma sequência. O professor explicou que a geração de texto não é um processo determinístico simples onde a máquina escolhe sempre a palavra com a maior probabilidade (conhecido como _Greedy Decoding_). Se assim fosse, o texto se tornaria repetitivo, robótico e pobre em criatividade.

Para controlar a aleatoriedade e a qualidade do texto, foram apresentadas duas técnicas fundamentais que atuam sobre a distribuição de probabilidade da saída do modelo (Logits), após a aplicação da temperatura:

1. **Top-K Sampling:** Uma técnica de truncamento "hard" onde o modelo considera apenas os $K$ tokens mais prováveis, zerando a probabilidade de todos os outros. Isso força o modelo a escolher entre as melhores opções fixas, eliminando a cauda longa de tokens improváveis.
    
2. **Top-P (Nucleus) Sampling:** Uma técnica de truncamento dinâmica onde o modelo seleciona o menor conjunto de tokens cuja **probabilidade acumulada** atinge um limiar $P$ (ex: 90% ou 0.9). Isso permite que o vocabulário de escolha se expanda ou contraia dependendo da certeza do modelo sobre a próxima palavra.
    

Essas estratégias são essenciais para evitar alucinações severas (escolha de tokens muito improváveis) ou loops de repetição (escolha determinística excessiva), equilibrando coerência e criatividade.

---

### 2. 🔍 Deep Dive: Conceitos & Teoria

#### **Top-K Sampling**

- **Na Aula:** O professor descreveu como uma variável $K$ (ex: 5) que limita a escolha às 5 palavras com maior probabilidade, descartando o resto, independentemente de quão prováveis ou improváveis sejam as descartadas.
    
- **Deep Dive (Pesquisa):**
    
    - **Definição:** Top-K redistribui a massa de probabilidade para os $K$ tokens mais prováveis ($V^{(k)}$):
        
        $$P'(x_i) = \frac{P(x_i)}{\sum_{x_j \in V^{(k)}} P(x_j)}$$
        
        se $x_i \in V^{(k)}$, caso contrário 0.
        
    - **Origem:** Popularizado no contexto de LLMs pelo paper _"Hierarchical Neural Story Generation"_ (Fan et al., 2018).
        
    - **Limitação:** O valor de $K$ é estático. Em uma distribuição "achatada" (muitas palavras possíveis, ex: "O nome dela é..."), um $K$ pequeno pode eliminar opções válidas. Em uma distribuição "afiada" (poucas opções, ex: "São Paulo é um..."), um $K$ grande pode incluir opções absurdas.
        

#### **Top-P (Nucleus Sampling)**

- **Na Aula:** Descrito como uma estratégia baseada em percentual. O modelo soma as probabilidades do token mais provável para o menos provável até atingir $P$ (ex: 90%). O conjunto de escolha varia dinamicamente: pode ser 2 palavras ou 100 palavras, dependendo da certeza do modelo.
    
- **Deep Dive (Pesquisa):**
    
    - **Definição:** Seleciona o menor conjunto de tokens $V^{(p)}$ tal que $\sum_{x \in V^{(p)}} P(x) \ge p$.
        
    - **Origem:** Introduzido no paper seminal _"The Curious Case of Neural Text Degeneration"_ (Holtzman et al., 2019). Os autores demonstraram que a cauda longa da distribuição de probabilidade (tokens com baixa probabilidade) é onde a maioria dos erros de degeneração e alucinação ocorre.
        
    - **Vantagem SOTA:** É considerado o padrão ouro para geração de texto aberta (chatbots, histórias) porque se adapta à incerteza do contexto passo a passo.
        

---

### 3. 🛠️ Engenharia: Arquiteturas e Agentes

#### **Implementação em Frameworks (Hugging Face / LangChain)**

- **Funcionamento:** Na prática de engenharia, esses parâmetros são passados durante a chamada de inferência. A maioria das APIs modernas (OpenAI, Anthropic, Google Vertex AI) permite configurar ambos, embora a precedência matemática geralmente aplique Top-K primeiro (se definido) e depois Top-P.
    
- **Exemplo da Aula:** O professor mencionou o uso do _Playground_ do Hugging Face para ajustar esses parâmetros visualmente.
    
- **Referência Externa (Hugging Face `transformers`):**
    
    ```
    # Exemplo de configuração de geração
    outputs = model.generate(
        inputs,
        do_sample=True,      # Habilita amostragem estocástica
        temperature=0.7,     # Ajusta a "curva" da probabilidade
        top_k=50,            # Filtra os 50 melhores
        top_p=0.95           # Nucleus Sampling de 95%
    )
    ```
    

#### **Agentes e Controle de Geração**

- **Contexto de Agentes:** No livro _Agentic Design Patterns_, a confiabilidade é crítica. Para agentes que usam ferramentas (**Tool Use**), a precisão é vital.
    
- **Estratégia SOTA:**
    
    - **Agentes de Raciocínio (Reasoning/Math):** Devem usar `top_p` baixo (ou 0, tornando-se Greedy) para garantir que sigam a lógica mais provável passo a passo.
        
    - **Agentes Criativos (Brainstorming):** Beneficiam-se de `top_p` alto (perto de 1.0) combinado com temperatura alta.
        
    - **Nota do Pesquisador:** Em arquiteturas como **Chain-of-Thought (CoT)**, uma estratégia comum é o _Self-Consistency_ (Wang et al., 2022), onde se usa `top_p` alto para gerar múltiplos caminhos de raciocínio diversos e, em seguida, vota-se na resposta mais comum.
        

---

### 4. 📚 Bibliografia Estendida e Referências (Pesquisa)

- **Paper Essencial:** _The Curious Case of Neural Text Degeneration_ (Holtzman et al., 2019). Este é a leitura obrigatória para entender por que Top-P foi criado para substituir ou complementar o Top-K.
    
- **Artigo Técnico:** _How to generate text: using different decoding methods for language generation with Transformers_ (Blog do Hugging Face). Explica visualmente a diferença entre Beam Search, Greedy, Top-K e Top-P.
    
- **Paper de Aplicação:** _Self-Consistency Improves Chain of Thought Reasoning in Language Models_ (Wang et al., 2022). Mostra como usar a aleatoriedade controlada (via Top-K/P) para melhorar a performance em tarefas complexas.
    

---

### 5. ⚠️ Pontos de Atenção e Trade-offs

- **Risco de "Chatbot Chato":** O professor alertou que sem aleatoriedade (apenas Greedy ou Top-K muito restritivo), o modelo se torna repetitivo e robótico.
    
- **Risco de Incoerência:** Um `top_k` muito alto ou `top_p` próximo de 1.0, especialmente com temperatura alta, inclui tokens da "cauda longa", que podem resultar em frases gramaticalmente corretas mas semanticamente absurdas (alucinações).
    
- **Interação Top-K vs Top-P:** Se você definir ambos, a maioria das bibliotecas aplica o filtro Top-K _primeiro_. Isso pode reduzir a eficácia do Top-P se $K$ for muito pequeno. _Recomendação SOTA:_ Geralmente, ajusta-se apenas um dos dois, ou usa-se um $K$ alto (ex: 40-50) apenas como uma rede de segurança para o Top-P.
    

---

### 6. 📝 Quiz Prático

1. **Qual a principal limitação do Top-K sampling que o Top-P (Nucleus) sampling resolve?**
    
    a) O Top-K é computacionalmente mais caro.
    
    b) O Top-K usa um número fixo de tokens, o que é ineficiente quando a distribuição de probabilidade é muito plana ou muito aguda.
    
    c) O Top-K não funciona com modelos baseados em Transformers.
    
    d) O Top-K elimina a aleatoriedade do modelo completamente.
    
2. **Se definirmos `top_p = 0.9` em um modelo de geração de texto, o que o modelo fará?**
    
    a) Selecionará aleatoriamente entre os 90% dos tokens menos prováveis.
    
    b) Selecionará o menor conjunto de tokens cuja soma das probabilidades atinja pelo menos 90%.
    
    c) Selecionará os 90 tokens mais prováveis.
    
    d) Aumentará a temperatura do modelo em 90%.
    
3. **Para um Agente de IA encarregado de escrever código SQL (onde a sintaxe deve ser exata), qual configuração é a mais adequada?**
    
    a) Alta temperatura e Top-P alto (criatividade máxima).
    
    b) Baixa temperatura e Top-P baixo (baixa aleatoriedade, alta precisão).
    
    c) Top-K = 0 (considerar todo o vocabulário).
    
    d) Apenas Top-K alto.
    
4. **(Desafio SOTA) Em um cenário de "Chain-of-Thought" com Self-Consistency, por que aumentaríamos o Top-P ou a Temperatura durante a fase de geração de raciocínio?**
    
    a) Para fazer o modelo responder mais rápido.
    
    b) Para gerar caminhos de raciocínio diversificados que possam ser comparados posteriormente via votação.
    
    c) Para garantir que o modelo sempre dê a mesma resposta.
    
    d) Para economizar tokens.
    

---

**Gabarito:** 1-b, 2-b, 3-b, 4-b