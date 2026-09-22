# Documentação do Agente

## Caso de Uso

### Problema
> Qual problema financeiro seu agente resolve?

Prova de Autoria de Raciocínio
> Como o agente resolve esse problema de forma proativa?

o sistema, no momento da submissão, pede que o analista responda a uma pergunta específica 
e não previsível sobre o próprio caso, gerada na hora, se ele não souber responder, 
o parecer é sinalizado como não fundamentado antes de virar decisão oficial,
não como investigação retroativa.
### Público-Alvo
> Quem vai usar esse agente?

Analista de Crédito
Compliace e Prevenção a lavagem de dinheiro
Subscritores de seguro
Auditoria Interna
Relacionamento/gerente de conta em operações estruturadas
---

## Persona e Tom de Voz

### Nome do Agente
ACE
### Personalidade
> Como o agente se comporta? (ex: consultivo, direto, educativo)

Objetivo 
### Tom de Comunicação
> Formal, informal, técnico, acessível?

Formal, Acessivel e Compreensivo 
### Exemplos de Linguagem
- Saudação: Olá! Sou a ACE. 
- Confirmação: Compreendo, verificação em instante
- Erro/Limitação: No momento não tenho/posso averigar o que me foi apresentado, no entanto posso passar para um setor de 
atendimento humano

---

## Arquitetura

### Diagrama

```mermaid
flowchart TD
    A[Usuário] -->|Gradio| (Interface Visual)]
    B --> React/Next.js + API própria
    C --> Microsoft Teams / Slack bot
    D --> LLM
    C --> E[Validação]
    E --> F[Resposta]
```

### Componentes

| Componente | Descrição                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------|
| Interface | Gradio, Next.js + API própria/Slack bot                                                                        |
| LLM | Llama                                                                                                          |
| Base de Conhecimento | Banco vetorial (Pinecone/pgvector) Documentos, SQL/PostgreSQL  Dados estruturados/APIs internas do core bancário |
| Validação | NeMo Guardrails
| Resposta| Saída final entregue ao usuário, com log de auditoria registrado

---

##  Segurançae Anti-Alucinação

### Estratégias Adotadas

- ### Estratégias Adotadas (com NeMo Guardrails)

- [ ] Agente só responde com base nos dados fornecidos — implementado via **fact-checking rail** do NeMo, que compara a resposta gerada com os documentos recuperados
- [ ] Respostas incluem a fonte da informação — configurado no fluxo de saída (output rail) para anexar metadados do documento consultado
- [ ] Quando não sabe, admite e redireciona — definido como **canonical form** no arquivo de flows (.co), ex: `bot não sabe` → aciona fallback humano
- [ ] Não faz recomendações de investimento sem perfil do cliente — **input rail** que checa se o contexto da conversa já tem o perfil de suitability antes de liberar a resposta
- [ ] Bloqueia promessas de rentabilidade — **dialogue rail** com lista de tópicos proibidos (topical rail), recusando ou reformulando se o LLM tentar gerar esse tipo de frase
- [ ] Toda resposta sensível passa por segunda validação — usando o **self-check output rail** do NeMo, que roda um segundo prompt de verificação antes de liberar a resposta final

### Limitações Declaradas
> O que o agente NÃO faz?

Validação Autônoma 
