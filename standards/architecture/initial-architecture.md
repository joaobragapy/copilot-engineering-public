# Arquitetura inicial — Copilot Engineering

## 1. Objetivo

Manter padrões, skills e práticas de avaliação reutilizáveis para desenvolvimento assistido por IA em projetos de engenharia e análise de dados.

O repositório deve favorecer modularidade, rastreabilidade, validação e controle humano sobre alterações relevantes.

## 2. Escopo

### Incluído
- Instruções reutilizáveis para GitHub Copilot.
- Skills baseadas no padrão Agent Skills.
- Padrões de engenharia de contexto.
- Referências técnicas versionadas.
- Casos de teste e critérios de aceitação das skills.

### Fora do escopo inicial
- Orquestração multiagente.
- Infraestrutura própria de execução de agentes.
- Publicação automática de contratos.
- Distribuição automatizada de skills para outros repositórios.

## 3. Primeira skill

**Nome:** `data-contract-authoring`

**Responsabilidade:** produzir um rascunho de contrato de dados a partir dos requisitos fornecidos, consultando o ODCS, as decisões arquiteturais e as convenções internas.

A skill não deve:
- Inventar requisitos de negócio ausentes.
- Resolver contradições sem explicitar a incerteza.
- Sobrescrever contratos aprovados sem autorização.
- Declarar um contrato válido sem executar as validações aplicáveis.

## 4. Padrão e ferramenta de validação

| Componente | Versão inicial |
|---|---|
| Open Data Contract Standard (ODCS) | `3.2.0` |
| `datacontract-cli` | `1.2.7` |

A combinação acima constitui o baseline inicial do laboratório.

A ferramenta deverá ser executada em ambiente Python isolado, com suas dependências controladas e documentadas.

A validação estrutural utilizará o comando `datacontract lint`, verificando a conformidade do contrato com o padrão aplicável.

Quando houver dados e regras de qualidade apropriados, poderão ser utilizados testes adicionais por meio de `datacontract test`.

O baseline somente será considerado validado após a execução bem-sucedida de testes reais no ambiente definido.

## 5. Estratégia de validação

A validação será dividida em três dimensões:

1. **Estrutural:** conformidade com o schema ODCS correspondente à versão declarada.
2. **Semântica:** coerência com os requisitos de negócio e as decisões arquiteturais fornecidas.
3. **Operacional:** execução correta da skill no ambiente GitHub Copilot utilizado.

A aprovação em uma dimensão não implica aprovação nas demais.

## 6. Controle humano

O fluxo padrão será:

1. Receber os requisitos.
2. Identificar lacunas e contradições.
3. Gerar um rascunho.
4. Executar as validações disponíveis.
5. Apresentar resultados, limitações e pendências.
6. Solicitar aprovação humana antes de promover o contrato a aprovado.

Instruções textuais não substituem controles de permissões e proteção de arquivos no ambiente de execução.

## 7. Testes

Os casos iniciais devem cobrir:
- Requisitos completos.
- Requisitos incompletos.
- Requisitos contraditórios.
- Contrato estruturalmente inválido.
- Contrato válido estruturalmente, mas inconsistente com os requisitos.
- Tentativa de sobrescrever um contrato aprovado.

Os resultados esperados devem ser documentados e versionados.

Os testes devem distinguir falhas da skill, falhas do validador e problemas nos próprios requisitos de entrada.

## 8. Versionamento e atualização

O repositório utilizará Git para registrar alterações nas skills, referências, templates, padrões e testes.

Atualizações do ODCS ou do `datacontract-cli` exigem:
1. Revisão deliberada da versão proposta.
2. Verificação de compatibilidade.
3. Execução dos testes de regressão.
4. Registro da alteração e de suas justificativas.

A atualização de uma dependência não deverá alterar silenciosamente o baseline aprovado.

## 9. Distribuição das skills

Inicialmente, a reutilização entre repositórios será avaliada com um workspace do VS Code contendo o repositório de padrões e o projeto consumidor.

A descoberta e a invocação das skills deverão ser testadas no ambiente real. A existência de arquivos em um repositório não garante que o Copilot os carregue automaticamente em outro.

Não será implementado mecanismo de distribuição automatizada nesta fase.

## 10. Evolução

A arquitetura será expandida somente quando houver necessidade demonstrada.

Novas skills, agentes, mecanismos de distribuição e orquestração deverão ter responsabilidades explícitas e critérios de avaliação próprios.