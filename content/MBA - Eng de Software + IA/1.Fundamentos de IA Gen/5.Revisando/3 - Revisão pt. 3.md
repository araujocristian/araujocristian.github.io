---
title: 3 - Revisão pt. 3
draft: false
tags:
---
Nesta terceira parte, a aula focou em como tornar os modelos gigantescos viáveis econômica e computacionalmente. O professor apresentou o problema central: o treinamento tradicional (Full Fine-Tuning) e a inferência em precisão máxima (32-bits) são inacessíveis para a maioria das empresas e pesquisadores.

Para resolver isso, três estratégias SOTA (_State of the Art_) foram apresentadas: **LoRA**, que atua como um "plugin" de matrizes menores injetado no modelo sem alterar seus pesos originais; **Quantização**, que comprime a precisão matemática dos números do modelo, permitindo que rodem em laptops; e **MoE (Mixture of Experts)**, uma mudança arquitetural que ativa apenas frações específicas ("especialistas") da rede neural a cada pergunta, economizando processamento computacional massivo.

### 2. 🔍 Deep Dive: Conceitos & Teoria

- **LoRA (Low-Rank Adaptation)**:
    
    - _Na Aula:_ Funciona como um "plugin inteligente" de baixo rank (dimensão). Adiciona diretrizes entre as camadas sem modificar os bilhões de parâmetros originais, tornando o fine-tuning muito mais barato e rápido.
        
    - _Deep Dive (Pesquisa):_ Publicado pela Microsoft em 2021 (_"LoRA: Low-Rank Adaptation of Large Language Models"_). Matematicamente, o LoRA congela a matriz de pesos original ($W$) e injeta matrizes de decomposição menores ($A$ e $B$). Durante o treinamento, apenas $A$ e $B$ são atualizadas. Isso reduz o número de parâmetros treináveis em até **10.000 vezes** e a necessidade de memória da GPU em 3 vezes, sem perda perceptível de qualidade.
        
- **Quantização (INT8, INT4)**:
    
    - _Na Aula:_ Redução da precisão dos números (de 32-bits para 8 ou 4-bits). Diminui o uso de memória permitindo rodar em laptops ou dispositivos embarcados (Edge AI) com mínimo impacto no desempenho.
        
    - _Deep Dive (Pesquisa):_ Modelos de IA são essencialmente arquivos gigantes cheios de números de ponto flutuante (FP16 ou FP32). A quantização "arredonda" esses números para inteiros (INT8 ou INT4). O estado da arte hoje é a **Quantização Consciente de Ativação (AWQ)** e o **GPTQ**, que descobrem quais pesos são "críticos" e os mantêm em alta precisão, comprimindo apenas os menos importantes. Uma das inovações mais recentes, o **QLoRA** (Dettmers et al., 2023), permite até fazer o Fine-Tuning de um modelo de 65 bilhões de parâmetros em uma única GPU consumidora (de 48GB).
        
- **MoE (Mixture of Experts)**:
    
    - _Na Aula:_ Em vez de ativar o modelo inteiro, um _middleware_ (roteador) decide quais "especialidades" ativar para cada input, economizando tempo e energia.
        
    - _Deep Dive (Pesquisa):_ Modelos MoE substituem a camada densa (Feed-Forward) por vários _experts_. Um modelo MoE de 8 especialistas com 7 Bilhões de parâmetros (como o famoso **Mixtral 8x7B**) pode ter 47 bilhões de parâmetros no total, mas para cada token (palavra), a rede do _Router_ (Roteador) escolhe apenas os 2 melhores especialistas. Assim, a inferência (processamento) tem o custo e a velocidade de um modelo de ~12 bilhões de parâmetros, mas com a capacidade e inteligência de um de quase 50 bilhões.
        

### 3. 🛠️ Engenharia: Arquiteturas e Agentes

- **Padrão/Framework: PEFT (Parameter-Efficient Fine-Tuning) & Inferência Local**
    
    - _Funcionamento:_ A biblioteca `PEFT` da Hugging Face é o padrão ouro na engenharia para aplicar LoRA ou QLoRA. Para a inferência quantizada (rodar no laptop), a comunidade de engenharia usa o framework `llama.cpp` e o formato de arquivo **GGUF**, que otimiza a alocação de memória RAM e VRAM dinamicamente.
        
    - _Exemplo da Aula:_ O professor citou o caso de rodar LLMs em laptops e reduzir custos em empresas.
        
    - _Referência Externa:_ Plataformas como **Ollama** ou **LM Studio** encapsulam toda a complexidade da Quantização. Você pode baixar um modelo como `llama3:8b` via Ollama, e ele automaticamente rodará a versão quantizada (em 4-bits) na sua máquina, consumindo apenas ~5GB de RAM.
        

### 4. 📚 Bibliografia Estendida e Referências (Pesquisa)

- **Papers Recomendados:**
    
    - _LoRA: Low-Rank Adaptation of Large Language Models (Hu et al., 2021)_: Essencial para entender a matemática da matriz de decomposição.
        
    - _QLoRA: Efficient Finetuning of Quantized LLMs (Dettmers et al., 2023)_: Revolucionou a democratização da IA ao provar que podemos quantizar um modelo para 4-bits e treiná-lo (via LoRA) simultaneamente.
        
    - _Mixtral of Experts (Jiang et al., 2024)_: A prova prática de que a técnica MoE cria modelos Open Source que competem frente a frente com o GPT-4 da OpenAI (que, segundo especulações, também é um MoE gigantesco).
        
- **Ferramentas Relacionadas:**
    
    - **vLLM:** Uma biblioteca de inferência projetada para servir LLMs em produção de forma ultra-rápida, usando técnicas como PagedAttention.
        

### 5. ⚠️ Pontos de Atenção e Trade-offs

- _Nota do Pesquisador sobre Quantização:_ O professor mencionou que o impacto na performance é mínimo. Isso é verdade até 4-bits (INT4) usando algoritmos modernos. No entanto, descer para **2-bits ou 3-bits** frequentemente resulta em degradação severa da qualidade (o modelo fica burro ou perde capacidade de raciocínio crítico).
    
- _Nota do Pesquisador sobre o MoE:_ O professor disse que economiza recursos computacionais (processamento/energia). Isso está correto. **Porém, atenção:** Um modelo MoE ainda exige que _todos os pesos dos especialistas estejam carregados na memória VRAM (Placa de vídeo)_. Ou seja, um MoE é rápido para processar (baixa latência), mas ainda assim requer hardwares com muita capacidade de memória RAM para simplesmente ser armazenado.
    
- _Limitações do LoRA:_ LoRA é excelente para ensinar "estilos", formatos (como JSON) ou tons de resposta. Contudo, não é a melhor ferramenta para injetar _conhecimento factual massivo_ (ex: ensinar manuais inteiros de medicina que o modelo não conhecia). Para injeção de conhecimento, o padrão **RAG** (da aula anterior) é mais efetivo.
    

### 6. 📝 Quiz Prático

1. **De forma simples, qual é a principal diferença entre um Full Fine-Tuning e o uso de LoRA?**
    
    - _Resposta:_ O Full Fine-Tuning altera todos os bilhões de parâmetros originais da rede neural, sendo caro e demorado. O LoRA congela a rede original e apenas treina pequenas matrizes adicionais inseridas entre as camadas, economizando drasticamente tempo, dinheiro e hardware.
        
2. **Como a técnica de Quantização (INT4/INT8) permite que inteligências artificiais rodem em laptops comuns?**
    
    - _Resposta:_ Ela reduz a precisão matemática dos números (pesos e ativações) que compõem o modelo. Em vez de arquivos enormes usando precisão de 32 bits para cada número, usam-se 8 ou 4 bits. Isso reduz o tamanho do arquivo e o consumo de memória RAM a uma fração do original.
        
3. **Na arquitetura Mixture of Experts (MoE), o que impede o modelo de processar a requisição inteira usando todos os seus parâmetros?**
    
    - _Resposta:_ A existência de um _Router_ (Roteador/Middleware). A cada passo (ou token), essa rede roteadora avalia qual "especialista" (sub-rede) é o mais capacitado para tratar aquele dado e direciona o processamento apenas para ele (ex: ativando 2 de 8 especialistas).
        
4. **Desafio (Pesquisa + Aula): Um engenheiro tenta carregar um modelo MoE de 47 bilhões de parâmetros, onde apenas 12 bilhões ficam ativos por inferência, esperando que isso caiba em uma GPU de 16GB. Onde está o erro de julgamento desse engenheiro?**
    
    - _Resposta:_ O engenheiro confundiu custo computacional (FLOPs) com requisitos de memória (VRAM). Embora apenas a parte equivalente a 12 bilhões de parâmetros seja processada por vez (o que o torna rápido), **todos** os 47 bilhões de parâmetros precisam estar fisicamente carregados e armazenados na memória da GPU para que o Roteador possa escolhê-los. Logo, o modelo não caberá em uma GPU de apenas 16GB.