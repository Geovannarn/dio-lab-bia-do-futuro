# Base de Conhecimento

## Dados Utilizados

Descreva se usou os arquivos da pasta `data`, por exemplo:

| Arquivo | Formato | Utilização no Agente |
|---------|---------|---------------------|
| `historico_atendimento.csv` | CSV | Contextualizar interações anteriores e evitar repetição de perguntas já respondidas || `perfil_investidor.json` | JSON | Personalizar recomendações |
| `perfil_investidor.json` | JSON | Checar suitability antes de qualquer resposta sobre produtos/investimentos || `transacoes.csv` | CSV | Analisar padrão de gastos do cliente |
| `produtos_financeiros.json` | JSON | Base de grounding para recomendações — o agente só cita produtos presentes aqui |
| `transacoes.csv` | CSV | Analisar padrão de gastos e identificar contexto financeiro do cliente |

> [!TIP]
> **Quer um dataset mais robusto?** Você pode utilizar datasets públicos do [Hugging Face](https://huggingface.co/datasets) relacionados a finanças, desde que sejam adequados ao contexto do desafio.

---

## Adaptações nos Dados

> Você modificou ou expandiu os dados mockados? 
> 
Os dados mockados foram expandidos com um campo "fonte" e "data_atualizacao" em cada registro de "produtos_financeiros.json", para permitir que a camada de Validação cite a origem da informação na resposta (exigência de rastreabilidade). Também foi adicionado um campo "nivel_risco" em "perfil_investidor.json", usado como trava para bloquear recomendações incompatíveis com o perfil do cliente.

---

## Estratégia de Integração

### Como os dados são carregados?
> Descreva como seu agente acessa a base de conhecimento.

Os arquivos JSON/CSV são carregados em memória no início da sessão e indexados: dados estruturados (transações, perfil) ficam disponíveis para consulta direta por chave (ex: ID do cliente), enquanto `produtos_financeiros.json` é vetorizado para busca por similaridade (RAG), já que as descrições de produtos são mais textuais.
### Como os dados são usados no prompt?
> Os dados vão no system prompt? São consultados dinamicamente?
> 
Não vão inteiros no system prompt ,apenas o perfil do cliente e regras de compliance fixas ficam no system prompt. Os demais dados (histórico, transações, produtos relevantes) são consultados dinamicamente a cada pergunta e injetados no contexto da mensagem apenas quando relevantes, para reduzir tokens e evitar vazamento de dados de outros clientes.

---

## Exemplo de Contexto Montado

> Mostre um exemplo de como os dados são formatados para o agente.

```
Dados do Cliente:
- Nome: Geovanna Rodrigues Nascimento D'Luca
- Perfil: Agressivo
- Saldo disponível: R$ 432.000,00

Últimas transações:
- 01/11: Supermercado - R$ 450
- 01/11: Streaming - R$ 55
- 05/11: Urgência - R$ 450
- 05/11: Urgência - R$ 890
- 05/11: Urgência - R$ 123
- 05/11: Alimentação - R$ 567


...
```
