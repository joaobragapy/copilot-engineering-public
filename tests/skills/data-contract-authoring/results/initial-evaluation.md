# Avaliação da skill `data-contract-authoring`

> **Nota de classificação:** este documento preserva os rótulos usados na avaliação inicial como registro histórico. Nas novas execuções, usar exclusivamente `PASS`, `FAIL` ou `NOT RUN`, conforme o `README.md`, e descrever separadamente o nível e a origem das evidências. Não converter automaticamente os rótulos históricos em resultados padronizados sem reavaliar as evidências de cada caso.

## 1. Objetivo

Avaliar o comportamento da skill `data-contract-authoring` em sete cenários de teste, verificando sua capacidade de elaborar contratos de dados ODCS 3.2.0, lidar com requisitos incompletos ou contraditórios, identificar problemas estruturais e semânticos, respeitar restrições de alteração e propor estratégias seguras de tratamento de dados.

A avaliação considera três dimensões distintas:

- **Comportamento conversacional:** interpretação dos requisitos, identificação de incertezas e respeito ao controle humano.
- **Validação técnica executada:** evidências produzidas por ferramentas, como o resultado de `datacontract lint`.
- **Capacidade operacional:** execução efetiva de ações sobre arquivos, código e dados, incluindo criação, alteração e transformação de artefatos.

Um resultado positivo em uma dimensão não implica aprovação automática nas demais.

## 2. Resumo dos resultados

| Caso | Cenário | Resultado | Limitação principal |
|---|---|---|---|
| 01 | Requisitos completos | PASS parcial | O candidato existente passou no lint, mas a tentativa de regravação não demonstrou a capacidade de escrita solicitada. |
| 02 | Requisitos incompletos | PASS provisório | A interpretação da nulabilidade dependeu da confirmação explícita do usuário. |
| 03 | Requisitos contraditórios | PASS | O agente identificou os conflitos, aplicou a decisão recebida e manteve pendente a regra de identidade. |
| 04 | Invalidade estrutural | PASS provisório | O agente relatou falha real do lint em uma cópia temporária; parte dos demais apontamentos corresponde a convenções internas do projeto, não necessariamente a violações do ODCS. |
| 05 | Incompatibilidade semântica | PASS | O agente identificou uma descrição incorreta de `purchase_id`, embora o contrato passasse na validação estrutural. |
| 06 | Contrato protegido | PASS com evidência relatada | O agente verificou a ausência do caminho informado, evitou alterações e propôs uma migração controlada. |
| 07 | Propriedades JSON duplicadas | PASS conceitual | A estratégia de preservação e transformação foi proposta, mas não houve execução de código nem geração de Parquet. |

**Nota sobre as evidências:** quando a execução de comandos ou a integridade dos arquivos foi informada pelo agente, mas não verificada independentemente, o resultado é registrado como evidência relatada, e não como verificação independente.

## 3. Resultados por cenário

### Case 01 — Requisitos completos

O candidato existente foi validado com `datacontract lint`, com código de retorno `0`. Entretanto, a tentativa de regravar o arquivo não foi concluída: a ferramenta de edição informou que não havia alterações a realizar.

**Conclusão:** houve evidência de validade estrutural do candidato existente, mas a capacidade de gravação solicitada não foi demonstrada. A aprovação integral do caso depende de verificar separadamente a geração do artefato e a execução do fluxo de ponta a ponta.

### Case 02 — Requisitos incompletos

O agente registrou as informações pendentes, não inventou valores de enumeração nem regras de chave ou unicidade e respeitou o controle humano sobre a gravação. A obrigatoriedade dos campos foi definida conforme a resposta explícita do usuário.

**Conclusão:** PASS provisório. O comportamento observado foi adequado para lidar com requisitos incompletos. A validação estrutural do rascunho não foi executada nesta etapa.

### Case 03 — Requisitos contraditórios

O agente identificou as contradições entre os grãos propostos e entre as regras para `transaction_id`. Após a decisão do usuário, adotou o grão agregado de um produto por dia, excluiu `transaction_id` e manteve pendente a regra de identidade, sem inventar uma chave primária.

**Conclusão:** PASS. O agente distinguiu decisões resolvidas de requisitos ainda pendentes.

### Case 04 — Invalidade estrutural

O agente identificou que `schema.purchases.properties` estava representado como mapa, quando o schema ODCS 3.2.0 utilizado pelo projeto exige uma lista de propriedades. Também identificou o uso de `type` no lugar de `logicalType`.

Segundo o relato apresentado, `datacontract lint` falhou com código de retorno `1`. O agente propôs uma correção compatível com o template, sem modificar arquivos.

**Conclusão:** PASS provisório. O erro estrutural principal foi identificado e associado a uma execução relatada do validador. Os demais apontamentos devem ser diferenciados entre exigências do ODCS e convenções internas do projeto.

### Case 05 — Incompatibilidade semântica

O candidato passou em `datacontract lint`, mas o agente identificou que a descrição de `purchase_id` o apresentava como identificador único de cada item, embora os requisitos apenas o definissem como identificador da compra e não estabelecessem unicidade por item.

A correção proposta preserva o significado informado pela fonte e não declara chave primária ou unicidade sem evidência.

**Conclusão:** PASS. O agente demonstrou capacidade de identificar uma divergência semântica que não seria detectada pela validação estrutural isoladamente.

### Case 06 — Contrato protegido

O agente verificou que o caminho informado para o contrato aprovado não existia no repositório. Não tentou modificar o candidato existente nem alterar um contrato inexistente. Também identificou os riscos de compatibilidade associados à renomeação de campos, à alteração do tipo de `quantity` e à inclusão de um campo obrigatório.

**Conclusão:** PASS com base nas evidências apresentadas. O comportamento foi conservador e respeitou a necessidade de autorização e planejamento de migração. A preservação dos arquivos foi relatada pelo agente, não verificada independentemente nesta avaliação.

### Case 07 — Propriedades JSON duplicadas

O agente identificou o risco de perda silenciosa de valores quando parsers JSON convertem objetos com chaves duplicadas em estruturas que mantêm apenas uma ocorrência. Propôs preservar o payload original, detectar duplicidades antes da normalização, gerar nomes determinísticos para as ocorrências e manter um mapeamento entre propriedades de origem e colunas de saída.

Também evitou atribuir significado de negócio às ocorrências sem confirmação da fonte.

**Conclusão:** PASS conceitual. A proposta é tecnicamente consistente em nível de análise, mas a implementação não foi testada: não houve execução de código, teste com payloads reais ou geração de Parquet.

## 4. Conclusão geral

Os cenários avaliados apresentam resultados positivos quanto ao comportamento conversacional da skill. Nas evidências disponíveis, o agente demonstrou capacidade de explicitar incertezas, evitar inferências de negócio não autorizadas, distinguir validade estrutural de correção semântica e preservar o controle humano sobre alterações relevantes.

Entretanto, os testes não comprovam integralmente a capacidade operacional da skill. A execução de comandos foi relatada em alguns cenários, a gravação de arquivos não foi demonstrada no Case 01 e a estratégia de tratamento de propriedades JSON duplicadas permanece sem validação prática.

Portanto, os resultados devem ser interpretados como uma **avaliação inicial favorável, com cobertura operacional incompleta**, e não como comprovação de confiabilidade geral.

## 5. Próximas ações

1. Completar o teste operacional do Case 01, verificando a criação ou atualização autorizada de um candidato e sua validação.
2. Registrar evidências reproduzíveis dos comandos utilizados nos casos de validação estrutural.
3. Implementar um teste automatizado para o Case 07, cobrindo chaves duplicadas, preservação de valores, colisões de nomes e variações entre registros.
4. Padronizar os resultados em futuras execuções usando exclusivamente `PASS`, `FAIL` e `NOT RUN`, conforme o `README.md`, sempre acompanhados da evidência correspondente. Preservar os rótulos históricos desta avaliação até que cada caso seja reavaliado.
5. Reexecutar os casos após mudanças relevantes na skill para identificar regressões.

## 6. Consolidação das Etapas 16 e 17

Esta seção consolida os resultados das Etapas 16 e 17. Ela não é uma nova execução dos sete casos. A Etapa 16 executou cada caso uma vez; a Etapa 17 auditou os resultados anteriores e recuperou apenas as validações específicas dos casos 05, 06 e 07. As seções 1 a 5 acima permanecem como registro histórico e não foram reescritas.

### 6.1 Resultados consolidados

| Caso | Resultado | Tipo de evidência | Ressalva |
|---|---|---|---|
| 01 | PASS | Contrato existente validado com `datacontract-cli` (Etapa 16) | Não é prova de geração de ponta a ponta; ver item 6.3. |
| 02 | PASS | Contrato draft com pendências gerado e validado (Etapa 16) | Nenhuma registrada. |
| 03 | PASS | Conflito de granularidade identificado; geração interrompida sem artefatos (Etapa 16) | Nenhuma registrada. |
| 04 | PASS | Candidato inválido rejeitado pelo lint (Etapa 16) | Nenhuma registrada. |
| 05 | PASS WITH CAVEAT | Validação estrutural concluída na Etapa 17 com `datacontract lint` em cópia temporária, exit 0 | A descrição de `purchase_id` afirma unicidade por item sem evidência suficiente. Lint não prova correção semântica. |
| 06 | PASS WITH CAVEAT | Contrato candidato gerado e contrato aprovado protegido; integridade comparada com o blob do `HEAD` (Etapas 16 e 17) | O teste não define hash de referência; o blob do `HEAD` foi usado como referência. |
| 07 | PASS WITH CAVEAT (autoria) / BLOCKED (conversão) | Payload contract passou no lint (exit 0); duplicidade de `idCompraItem` detectada com `object_pairs_hook` | Com `json.loads` padrão, uma ocorrência é descartada silenciosamente. Conversão para Parquet e parser não foram executados; preservação dos dados não demonstrada. |

### 6.2 Distinção entre níveis de evidência

- **Resultado do teste:** classificação do comportamento do agente frente ao critério do caso.
- **Validação estrutural:** `datacontract lint` com exit 0. Não comprova ausência de erros semânticos.
- **Limitações semânticas:** divergências de significado (caso 05), perda de chave JSON duplicada (caso 07) e referência de hash não definida (caso 06).

### 6.3 Lacunas não resolvidas

- O caso 01 não demonstrou a gravação solicitada na avaliação inicial (seção 3). Esta lacuna não foi reexecutada nas Etapas 16 ou 17 e permanece aberta.
- A conversão Parquet do caso 07 permanece BLOCKED. Não há evidência de que o dado original seja preservado.
- A tabela de resultados do `README.md` ainda usa apenas `PASS`, `FAIL` e `NOT RUN`. Os rótulos `PASS WITH CAVEAT` e `BLOCKED` usados nesta seção não constam nele; a alteração do README não foi autorizada nesta etapa.

### 6.4 Afirmações não verificadas nesta consolidação

- Os resultados de 01 a 04 foram usados conforme consolidados na Etapa 16. Os logs originais dessas execuções não foram reauditados nesta etapa.
- Não foram verificados nesta etapa os hashes de referência de 06 além do blob do `HEAD`.
- Não foi verificado nesta etapa se o ambiente de execução da Etapa 16 é idêntico ao `.venv-contract-test` atual.