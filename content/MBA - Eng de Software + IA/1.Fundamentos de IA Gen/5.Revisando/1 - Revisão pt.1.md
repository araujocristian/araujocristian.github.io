---
title: Revisão pt.1
draft: false
tags:
---
### 1. ⚡ Resumo Expandido

A aula abordou a jornada evolutiva da Inteligência Artificial, saindo do campo teórico de Alan Turing em 1950, passando por modelos estatísticos (N-gramas e Cadeias de Markov), até chegar à revolução do Deep Learning e das Redes Neurais. O grande ponto de inflexão destacado foi o ano de 2017, com o surgimento da arquitetura **Transformer**, que superou as limitações das Redes Neurais Recorrentes (RNNs) e pavimentou o caminho para os grandes modelos de linguagem (LLMs).

Além da base técnica (como o funcionamento de **tokens** e **embeddings**), o professor fez um alerta fundamental sobre o momento do mercado: a necessidade de alinhar expectativas para evitar um novo "Inverno da IA". A aula foi finalizada mostrando a evolução multimodal da IA generativa, que saiu da geração de textos para imagens (via GANs e VAEs) e, mais recentemente, para vídeos fotorrealistas de alta fidelidade.

### 2. 🔍 Deep Dive: Conceitos & Teoria

- **A Arquitetura Transformer & Self-Attention**:
    
    - _Na Aula:_ O professor citou o paper do Google de 2017, "Attention is All You Need", como o divisor de águas que introduziu o mecanismo de atenção.
        
    - _Deep Dive (Pesquisa):_ O grande triunfo do paper de Vaswani et al. foi eliminar completamente a recorrência (o loop sequencial das RNNs/LSTMs). O mecanismo de **Scaled Dot-Product Attention** e **Multi-Head Attention** permitiu que o modelo avaliasse o peso e a relevância de _todas_ as palavras de uma sequência simultaneamente. Isso não apenas resolveu o problema de "esquecimento" de contexto longo, mas permitiu o treinamento em paralelo utilizando milhares de GPUs, o que viabilizou o treinamento de modelos na escala do GPT-4 e Gemini.
        
- **Modelos Ocultos de Markov (HMMs)**:
    
    - _Na Aula:_ Foram citados como a base estatística dos anos 90/2000 para tentar prever a próxima palavra ou ação.
        
    - _Deep Dive (Pesquisa):_ Os HMMs sofrem de uma limitação matemática fundamental chamada **Propriedade de Markov**, que assume que o estado futuro depende _apenas_ do estado atual imediatamente anterior, ignorando o histórico de longo prazo. Na linguagem natural, onde o sujeito de uma frase pode estar separado do verbo por dezenas de palavras, os HMMs falham criticamente.
        
- **Multimodalidade (De GANs a Diffusion e Veo 3)**:
    
    - _Na Aula:_ O professor citou as **GANs** (Generative Adversarial Networks), criadas por Ian Goodfellow em 2014, formadas por um gerador e um discriminador. Também citou a evolução do vídeo usando modelos recentes.
        
    - _Nota do Pesquisador (SOTA):_ Embora as GANs tenham sido revolucionárias, a indústria de geração de imagens de altíssima qualidade (como Midjourney e Stable Diffusion) pivotou fortemente para os **Diffusion Models**. Estes operam adicionando ruído gaussiano a uma imagem e treinando a rede para reverter esse ruído. No cenário de vídeo, como mencionado na aula, o avanço é exponencial: o modelo **Veo 3.1** do Google DeepMind (lançado recentemente) já permite não apenas a geração de vídeos em 1080p e 4K, mas também a geração nativa de _áudio e trilha sonora_ atrelados à física do vídeo, superando a fase do "cinema mudo" da IA.
        

### 3. 🛠️ Engenharia: Arquiteturas e Agentes

Se a aula estabeleceu como os LLMs raciocinam (gerando tokens de forma autorregressiva), a engenharia moderna se concentra em como transformar esses LLMs passivos em **Agentes** proativos.

- **Padrão/Framework: ReAct (Reason and Act) & RAG**
    
    - _Funcionamento:_ Um LLM puro sofre de **alucinações** (factuais, estruturais e contextuais) porque baseia suas respostas apenas em probabilidades estatísticas de seu treinamento. Para resolver isso, usamos padrões agenticos. O padrão **RAG (Retrieval-Augmented Generation)** conecta o modelo a bancos de dados vetoriais externos para injetar contexto real.
        
    - _Exemplo da Aula:_ O professor mencionou que LLMs não têm conhecimento em tempo real (se foi treinado em 2023, não sabe de 2024).
        
    - _Referência Externa:_ Frameworks como **LangChain**, **CrewAI** e **Google Agent Development Kit (ADK)** implementam o RAG e o **Tool Use (Function Calling)**. Empresas corporativas utilizam essas bibliotecas para que o LLM primeiro _pesquise_ a resposta no banco de dados da empresa antes de formular a frase, garantindo a rastreabilidade (citação da fonte).
        

### 4. 📚 Bibliografia Estendida e Referências (Pesquisa)

- **Papers Recomendados:**
    
    - _Attention Is All You Need (Vaswani et al., 2017)_: Leitura obrigatória. É a "Bíblia" fundacional da era moderna da IA Generativa.
        
    - _Generative Adversarial Nets (Goodfellow et al., 2014)_: Essencial para entender a lógica de modelos que aprendem competindo entre si.
        
- **Artigos/Blogs de Engenharia:**
    
    - _Google DeepMind Research (Veo Models)_: Para acompanhar o estado da arte na geração de vídeos com física simulada e áudio nativo (Veo 3.1).
        
- **Ferramentas Relacionadas:**
    
    - **Google AgentSpace**: Plataforma corporativa que facilita a criação de agentes e pipelines RAG conectados aos dados da empresa (Google Drive, Jira, etc.) sem exigir código profundo.
        

### 5. ⚠️ Pontos de Atenção e Trade-offs

- **O Risco do Hype ("Inverno da IA"):** O professor alertou brilhantemente que a IA é uma excelente assistente, mas não é "o exterminador do futuro" nem uma solução mágica para tudo. Superestimar a tecnologia pode frustrar investimentos corporativos e secar o mercado.
    
- **Limitações do Context Window (Janela de Contexto):** Inserir informações demais no modelo pode causar diluição de relevância (o modelo esquece o meio do texto) e eleva severamente o custo computacional.
    
- **Segurança e Human-in-the-Loop (HITL):** Na engenharia de agentes, nunca deixamos um modelo operar processos críticos (como pagamentos) 100% sozinho. É imperativo adotar padrões de **Guardrails** e **HITL** para que um humano aprove a decisão antes de sua execução final.
    

### 6. 📝 Quiz Prático

1. **Qual foi o principal problema das Redes Neurais Recorrentes (RNNs) que a arquitetura Transformer conseguiu resolver em 2017?**
    
    - _Resposta:_ O problema do processamento puramente sequencial, que causava a perda de informações em contextos longos (desvanecimento do gradiente) e impedia a paralelização do treinamento.
        
2. **Como funcionam estruturalmente as GANs (Generative Adversarial Networks), citadas pelo professor?**
    
    - _Resposta:_ Elas funcionam através da competição de duas redes neurais: um Gerador (que cria dados falsos tentando imitar a realidade) e um Discriminador (que tenta descobrir se o dado é real ou gerado). Ambas evoluem juntas nesse embate.
        
3. **O que causa a chamada "alucinação" nos LLMs e qual técnica é mais usada hoje para mitigá-la?**
    
    - _Resposta:_ A alucinação ocorre porque o modelo gera texto com base em probabilidades estatísticas, sem ter a "certeza" dos fatos ou atualizações em tempo real. A técnica mais comum para mitigar isso é o RAG (Retrieval-Augmented Generation), que ancora o modelo em bases de dados externas e confiáveis.
        
4. **Desafio (Pesquisa + Aula): Por que a engenharia de prompts evoluiu para a engenharia de "Agentes" (usando frameworks como ReAct)?**
    
    - _Resposta:_ Porque a engenharia de prompts estática limita o modelo a um sistema reativo de "pergunta-resposta". A engenharia de Agentes permite que o LLM opere em um loop autônomo onde ele _Pensa_ (Reason), decide usar uma _Ferramenta externa_ (Act - como buscar na web ou rodar um código python), e observa o resultado antes de dar a resposta final ao usuário, tornando o sistema dinâmico e capaz de resolver problemas complexos por conta própria.
        