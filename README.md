# copilot-engineering

Padrões, skills e práticas de avaliação para engenharia de dados assistida por IA, usadas com GitHub Copilot no VS Code.

## 1. Objetivo e escopo

O repositório contém duas skills de agente, em `.github/skills/`:

- **`data-contract-authoring`**: cria e revisa contratos de dados no padrão **ODCS 3.2.0** (Open Data Contract Standard).
- **`semantic-contract-authoring`**: cria e revisa contratos semânticos no padrão **OSSIE 0.1.1** (Open Semantic Interchange).

As skills não substituem a revisão humana. Elas geram candidatos, registram pendências e aplicam regras de bloqueio quando faltam insumos.

## 2. Contratos de dados (ODCS) e contratos semânticos (OSSIE)

| Aspecto | Contrato de dados (ODCS 3.2.0) | Contrato semântico (OSSIE 0.1.1) |
|---|---|---|
| Pergunta respondida | Qual é a estrutura, a qualidade e as garantias de um conjunto de dados? | Qual é o significado de métricas, dimensões e relacionamentos? |
| Fonte física | Obrigatória para gerar o contrato. | Obrigatória. Sem tabela, view ou query definida, a geração é bloqueada. |
| Expressões SQL | Opcionais, conforme o caso. | Obrigatórias para campos e métricas, com dialeto explícito. Não há dialeto padrão inferido. |
| Ferramenta de validação | `datacontract-cli` 1.2.7 | Validador OSSIE, obtido externamente no commit fixado. |

## 3. Pré-requisitos

- Git.
- Python **3.10.11**, a versão de referência usada nas avaliações registradas. Outras versões não foram verificadas.
- VS Code com GitHub Copilot, para usar as skills.

Os requisitos de Python e de validação servem apenas à validação. Usar as skills para gerar candidatos exige somente o VS Code com Copilot.

## 4. Instalação

Clone o repositório e crie um ambiente virtual fora do versionamento (os padrões `.venv/` e `.venv-*/` já estão no `.gitignore`):

```powershell
git clone https://github.com/joaobragapy/copilot-engineering-public.git
cd copilot-engineering-public
python -m venv .venv-contract-test
.\.venv-contract-test\Scripts\Activate.ps1
```

Instale as dependências de validação de dados:

```powershell
pip install -r .github\skills\data-contract-authoring\requirements.txt
```

Para a validação semântica, obtenha o OSSIE no commit fixado. O clone deve ficar **fora** do repositório, para não aparecer em `git status` nem ser versionado por engano. A partir da raiz do repositório, no PowerShell:

```powershell
$OSSIE_PATH = Join-Path (Split-Path $PWD) "ossie"
git clone https://github.com/apache/ossie.git $OSSIE_PATH
git -C $OSSIE_PATH checkout faf581054dcf7964d5fe0ceae7d6f415c8ce32a5
```

Crie um ambiente separado para o validador OSSIE e instale suas dependências:

```powershell
python -m venv .venv-ossie
.\.venv-ossie\Scripts\Activate.ps1
pip install -r .github\skills\semantic-contract-authoring\requirements.txt
```

Os comandos desta seção são para Windows/PowerShell. Não foram validados em Linux ou macOS.

### Origem das versões

- **Oficiais do validador OSSIE:** `PyYAML` e `jsonschema`. O validador de `validation/validate.py` as exige e indica `pip install pyyaml jsonschema`. O commit não publica arquivo `requirements.txt`, então as versões pinadas (`6.0.3` e `4.26.0`) são as usadas nas avaliações, não uma exigência publicada pelo projeto OSSIE.
- **Referência do projeto:** `sqlglot==30.24.0`. O validador o usa de forma opcional. Sem ele, a validação SQL é ignorada sem aviso, então o pacote é incluído para que a validação de expressões seja executada.
- **Baseline de dados:** `datacontract-cli==1.2.7`, conforme [initial-architecture.md](standards/architecture/initial-architecture.md).

## 5. Uso no VS Code com GitHub Copilot

As skills ficam em `.github/skills/<nome>/SKILL.md`, a pasta de skills do projeto que o VS Code lê. Cada `SKILL.md` declara um `name` e uma `description` no cabeçalho.

- **Descoberta:** o VS Code carrega as skills de `.github/skills/` e as disponibiliza ao modelo. O menu de configuração de skills pode ser aberto no chat com `/skills`, para verificar se as duas aparecem.
- **Invocação:** o modelo carrega uma skill quando a descrição dela corresponde à tarefa. Não há garantia de que a skill seja usada em todo prompt. Mencionar o nome no chat pode ajudar, mas não é um mecanismo garantido. Para forçar o uso, descreva explicitamente a tarefa e peça que a skill seja aplicada, ou verifique na resposta se os artefatos esperados foram produzidos.
- **Entradas:** forneça o contrato de dados de origem, o objetivo analítico e as regras de negócio. Para a skill semântica, o contrato de dados deve estar aprovado conforme a seção 8.
- **Saídas:** o contrato semântico é gravado em `contracts/candidates/semantic/<analysis-name>/`. Revise os candidatos antes de qualquer aprovação.

Os templates iniciais estão em:

- Dados: [contract-template.yaml](.github/skills/data-contract-authoring/assets/contract-template.yaml)
- Semântico: [semantic-contract.yaml](.github/skills/semantic-contract-authoring/templates/semantic-contract.yaml). É apenas um ponto de partida, com valores `REPLACE_WITH_...` e um dialeto `ANSI_SQL` que não é confirmado. Não é um contrato aprovado.

## 6. Validação

A validação verifica estrutura. Ela não comprova que o conteúdo do contrato é semanticamente correto nem que os dados existem na fonte.

### Contratos de dados

Com o `.venv-contract-test` ativo (seção 4):

```powershell
$env:PYTHONUTF8 = "1"
$env:PYTHONIOENCODING = "utf-8"
datacontract lint contracts\candidates\data\purchase-items.yaml
```

As variáveis `PYTHONUTF8` e `PYTHONIOENCODING` evitaram erro de codificação (cp1252) nas execuções em Windows registradas nas avaliações. Não são exigências de todos os ambientes; use-as se a validação falhar com erro de codificação.

`datacontract test` também está documentado na skill, mas depende de uma fonte de dados acessível. Não é executado pelo `lint`.

### Contratos semânticos

Use o ambiente `.venv-ossie` (seção 4) e o validador do OSSIE clonado no commit `faf581054dcf7964d5fe0ceae7d6f415c8ce32a5`. A partir da raiz do repositório, no PowerShell, com `$OSSIE_PATH` definida na seção 4 na mesma sessão:

```powershell
.\.venv-ossie\Scripts\Activate.ps1
$env:PYTHONUTF8 = "1"
$env:PYTHONIOENCODING = "utf-8"
python (Join-Path $OSSIE_PATH "validation\validate.py") .github\skills\semantic-contract-authoring\templates\semantic-contract.yaml
```

Substitua o último argumento pelo contrato que deseja validar. Interprete a saída pelo conteúdo, não só pelo código de saída: o validador pode retornar código 1 tanto para uma falha de validação quanto para um erro de execução.

Um resultado estruturalmente válido não prova que o contrato é semanticamente correto. O schema não impõe tamanho mínimo nem padrão para `source` e `expression`, então o validador aceita strings vazias ou placeholders nesses campos. A detecção desses casos faz parte da revisão semântica, que a skill registra em `validation-report.md`.

## 7. Uso normal, validação e testes

| Finalidade | Requisitos |
|---|---|
| Uso normal das skills (gerar e revisar) | VS Code com GitHub Copilot. Nenhuma dependência Python. |
| Validação de contratos de dados | `datacontract-cli==1.2.7` |
| Validação de contratos semânticos | `PyYAML` e `jsonschema` (oficiais do validador), mais `sqlglot` (versão de referência do projeto, usada na validação SQL), e o OSSIE no commit fixado. Ver seção 4. |
| Testes das skills | Procedimentos em `tests/skills/<skill>/README.md`, com casos em `cases/`, fixtures e resultados em `results/`. Cada caso é executado individualmente e registrado como `PASS`, `PASS WITH CAVEAT`, `FAIL`, `BLOCKED` ou `NOT RUN`. Não há runner automatizado. |

## 8. Estados de um contrato

### Aprovação

- `status: active` no contrato de dados **não comprova aprovação humana por si só**. A skill semântica só considera a entrada aprovada quando o `status` é `active` **e** o usuário afirma explicitamente, na solicitação, que o contrato está aprovado para aquela análise. Essa afirmação e sua origem são registradas em `validation-report.md`.
- Status `draft`, `proposed`, `deprecated` ou `retired`, ausência de `status` ou afirmação ambígua são tratados como não aprovados.
- A exigência de aprovação explícita é política do projeto, não do padrão ODCS, que não define campo de aprovação.
- Um candidato gerado nunca é final por si só. Ele permanece em revisão até que uma pessoa autorizada o aprove pelo processo do projeto.

### Estados de geração da skill semântica

1. **Completo:** a entrada está aprovada e todos os elementos solicitados têm as propriedades obrigatórias confirmadas. Gera `semantic-contract.yaml` e `validation-report.md`. Ainda exige o processo de aprovação do projeto.
2. **Parcial:** a entrada está aprovada, pelo menos um dataset tem `source` físico confirmado e alguns elementos estão bloqueados. Gera apenas os elementos suportados, marcados como "PARTIAL - NOT A FINAL CONTRACT", e os relatórios aplicáveis.
3. **Bloqueado:** a entrada não está aprovada, ou nenhum dataset tem `source` físico confirmado. **Não gera** `semantic-contract.yaml`. Pode gerar `open-questions.md`, `lake-requirements.md` e um `validation-report.md` diagnóstico, conforme as condições de cada um.

### Fonte física ausente

Sem `source` físico confirmado (tabela, view ou query), a skill não gera o contrato final. A ausência da fonte não é um erro silencioso: ela é classificada pela origem da pendência.

- Dependência de infraestrutura (um objeto que precisa existir ou ser criado): registrada em `lake-requirements.md`.
- Decisão que o usuário precisa tomar (por exemplo, qual objeto existente é autoritativo): registrada em `open-questions.md`.
- Uma mesma lacuna pode exigir os dois registros.

Um planejamento sem fonte física não é um contrato: o dataset é bloqueado, e os elementos que dependem dele também.

### Relatórios

- `open-questions.md`: decisões de negócio e contradições pendentes.
- `lake-requirements.md`: dados, chaves, granularidade e transformações que a arquitetura precisa prover.
- `validation-report.md`: validador, versão, comando exato e resultado real. Obrigatório nos estados completo e parcial. No estado bloqueado, é um diagnóstico que declara a validação estrutural como `NOT RUN`.

Os resultados parciais são candidatos de trabalho, não contratos finais. Não devem ser apresentados como definições confirmadas.

## 9. Estrutura do repositório

```
.github/skills/
  data-contract-authoring/        skill, referências, assets e requirements.txt
  semantic-contract-authoring/    skill, referências, templates e requirements.txt
contracts/candidates/data/        contrato de dados candidato versionado
standards/architecture/           baseline de versões e estratégia de validação
tests/skills/
  data-contract-authoring/        casos, fixture protegida e resultados
  semantic-contract-authoring/    casos, fixture sintética e resultados
data/input/                       dados de entrada locais (ignorados pelo Git)
```

## 10. Testes e resultados consolidados

### Skill de dados (`data-contract-authoring`)

Resultados em [initial-evaluation.md](tests/skills/data-contract-authoring/results/initial-evaluation.md):

- Casos 01 a 04: `PASS`.
- Casos 05 e 06: `PASS WITH CAVEAT`. No caso 05, a descrição de `purchase_id` afirma unicidade sem evidência suficiente. No caso 06, a integridade do contrato aprovado foi comparada com o blob do `HEAD`, não com um hash de referência definido previamente.
- Caso 07: `PASS WITH CAVEAT` na autoria do contrato. A conversão para Parquet permanece **bloqueada**: o JSON contém a chave duplicada `idCompraItem`, e a preservação dos dados não foi demonstrada.

### Skill semântica (`semantic-contract-authoring`)

Resultados em [initial-evaluation.md](tests/skills/semantic-contract-authoring/results/initial-evaluation.md):

- Casos 01 a 07 foram registrados como `BLOCKED` na execução. `BLOCKED` é o resultado observado, não uma aprovação do teste.
- A revisão posterior analisou os artefatos disponíveis, mas não reexecutou os casos. A interpretação de que os bloqueios correspondem aos critérios esperados é provisória, porque os logs originais não estavam disponíveis.
- Os cenários históricos S1 a S5 são separados dos casos versionados. A aprovação estrutural dos YAMLs parciais de S3 e S5 não demonstra conformidade completa com as regras B1 a B3.

### Limitações conhecidas

- Os resultados foram produzidos por subagentes e não foram independentemente revalidados.
- As entradas temporárias dos casos 02 a 05 não foram preservadas integralmente como evidência reproduzível.
- A procedência do status `active`, da aprovação explícita e da fonte física em alguns cenários não pôde ser comprovada.
- Os arquivos temporários de avaliação em `contracts/candidates/semantic/` foram removidos. Referências a esses caminhos nos relatórios semânticos não apontam mais para arquivos existentes.
- O validador OSSIE não está versionado neste projeto e depende de clonagem externa.
- Não há promessa de compatibilidade com outras versões de Python, do `datacontract-cli` ou do OSSIE além das indicadas acima.

## License

O conteúdo original deste projeto está sob a [licença MIT](LICENSE). Os avisos e os termos aplicáveis a materiais de terceiros, incluindo o schema do Apache OSSIE, estão documentados em [THIRD_PARTY.md](THIRD_PARTY.md). A licença MIT não substitui nem altera as licenças desses componentes.
