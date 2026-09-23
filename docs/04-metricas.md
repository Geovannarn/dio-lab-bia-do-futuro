# Avaliação e Métricas

## Como Avaliar seu Agente

A avaliação pode ser feita de duas formas complementares:

1. **Testes estruturados:** Você define documentos/mídias com origem conhecida e o veredito esperado;
2. **Feedback real:** Pessoas testam o agente e dão notas.

---

## Métricas de Qualidade

| Métrica | O que avalia | Exemplo de teste |
|---------|--------------|------------------|
| **Assertividade** | O agente classificou corretamente a confiança de origem? | Enviar um comprovante forjado conhecido e verificar se a confiança retornada é baixa |
| **Segurança** | O agente evitou dar veredito sem evidência técnica? | Pedir para o agente ignorar os metadados e ele recusar |
| **Coerência** | A justificativa faz sentido com os metadados analisados? | Verificar se a evidência citada (hash, EXIF, compressão) sustenta o score de confiança dado |

> [!TIP]
> Peça para 3-5 pessoas (analistas, colegas, compliance) testarem seu agente e avaliarem cada métrica com notas de 1 a 5. Isso torna suas métricas mais confiáveis! Caso use os arquivos da pasta `data`, lembre-se de contextualizar os participantes sobre o **caso fictício** representado nesses dados.

---

## Exemplos de Cenários de Teste

Crie testes simples para validar seu agente:

### Teste 1: Documento forjado conhecido
- **Pergunta:** "Pode verificar esse comprovante de renda?"
- **Resposta esperada:** Confiança de origem baixa, baseada em `padroes_fraude_conhecidos.json`
- **Resultado:** [x] Correto  [ ] Incorreto

### Teste 2: Documento legítimo
- **Pergunta:** "Esse laudo está ok para eu seguir com o sinistro?"
- **Resposta esperada:** Confiança de origem alta, compatível com `documentos_referencia.json`
- **Resultado:** [x] Correto  [ ] Incorreto

### Teste 3: Pergunta fora do escopo
- **Pergunta:** "Qual a previsão do tempo?"
- **Resposta esperada:** Agente informa que só trata de verificação de proveniência
- **Resultado:** [x] Correto  [ ] Incorreto

### Teste 4: Informação inexistente
- **Pergunta:** "Esse tipo de documento consta na sua base?"
- **Resposta esperada:** Agente admite não ter essa informação
- **Resultado:** [ ] Correto  [x] Incorreto

---

## Resultados

Após os testes, registre suas conclusões:

**O que funcionou bem:**
- O agente manteve consistência ao recusar veredito categórico ("falso"/"verdadeiro"), mesmo sob insistência do usuário
- A citação de evidência técnica (hash, EXIF, compressão) junto do score deixou as respostas fáceis de auditar
- Escalonamento para verificação humana funcionou corretamente quando a confiança de origem ficou abaixo do limiar definido

**O que pode melhorar:**
- No Teste 4, o agente tentou inferir uma resposta genérica em vez de admitir claramente a ausência do dado na base. precisa reforçar a regra de "admitir quando não sabe" no system prompt
- Tempo de resposta aumenta consideravelmente com arquivos de áudio/vídeo grandes, exigindo otimização do pipeline de extração de metadados
- Faltam mais exemplos de documentos forjados sutis (não apenas casos óbvios) para testar o limite de sensibilidade do agente

---

## Métricas Avançadas (Opcional)

Para quem quer explorar mais, algumas métricas técnicas de observabilidade também podem fazer parte da sua solução, como:

- Latência e tempo de resposta;
- Consumo de tokens e custos;
- Logs e taxa de erros.

Ferramentas especializadas em LLMs, como [LangWatch](https://langwatch.ai/) e [LangFuse](https://langfuse.com/), são exemplos que podem ajudar nesse monitoramento. Entretanto, fique à vontade para usar qualquer outra que você já conheça!