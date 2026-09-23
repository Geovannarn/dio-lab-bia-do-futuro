# Base de Conhecimento

## Dados Utilizados

Descreva se usou os arquivos da pasta `data`, por exemplo:

| Arquivo | Formato | Utilização no Agente |
|---------|---------|---------------------|
| `metadados_captura.json` | JSON | Armazenar metadados originais de captura (device, geolocalização, timestamp, hash) para comparação com o arquivo recebido |
| `padroes_fraude_conhecidos.json` | JSON | Assinaturas técnicas de documentos/áudios já identificados como gerados por IA |
| `historico_verificacoes.csv` | CSV | Registrar verificações anteriores e detectar reenvio de material já sinalizado |
| `documentos_referencia.json` | JSON | Modelos legítimos de comprovantes/contratos usados como baseline de comparação |

> [!TIP]
> **Quer um dataset mais robusto?** Você pode utilizar datasets públicos do [Hugging Face](https://huggingface.co/datasets) relacionados a detecção de deepfake e forgery de documentos, desde que sejam adequados ao contexto do desafio.

---

## Adaptações nos Dados

> Você modificou ou expandiu os dados mockados? Descreva aqui.

Foi adicionado o campo `confianca_origem` (score de 0 a 1) em `metadados_captura.json`, e o campo `tipo_artefato` (ex: "compressão inconsistente", "ausência de ruído de sensor", "espectro de áudio sintético") em `padroes_fraude_conhecidos.json`. Isso permite que a camada de Validação use critérios técnicos objetivos, em vez de depender apenas de um julgamento textual do LLM.

---

## Estratégia de Integração

### Como os dados são carregados?
> Descreva como seu agente acessa a base de conhecimento.

Os metadados do arquivo recebido são extraídos no momento do upload (EXIF, hash, formato de compressão) e comparados contra `documentos_referencia.json` (padrão esperado de um documento legítimo) e `padroes_fraude_conhecidos.json` (padrão esperado de uma falsificação conhecida), antes de qualquer chamada ao LLM.

### Como os dados são usados no prompt?
> Os dados vão no system prompt? São consultados dinamicamente?

Não vão inteiros no system prompt — apenas as regras fixas de classificação (limiares de confiança, política de resposta) ficam no system prompt. O resultado da checagem técnica de cada documento (metadados, hash, comparação com padrões) é consultado dinamicamente e injetado no contexto a cada verificação, já que cada arquivo analisado é único.

---

## Exemplo de Contexto Montado

> Mostre um exemplo de como os dados são formatados para o agente.

```
Documento analisado: comprovante_pix_0472.jpg

Metadados extraídos:
- Hash: 8f3a...c21
- Dispositivo de origem: não identificado (metadados EXIF ausentes)
- Compressão: dupla compressão JPEG detectada (indício de edição)

Comparação com padrões conhecidos:
- Similaridade com padrão de fraude "template_pix_editado_v2": 87%

Resultado da checagem técnica:
- Confiança de origem: 0.21 (baixa)
- Classificação preliminar: suspeito
```