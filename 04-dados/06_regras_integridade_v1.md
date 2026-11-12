# 06 — Regras de Integridade e Restrições de Domínio

## 1. Objetivo

Este documento define as regras de integridade, as restrições de domínio e o fluxo de status do sistema **TaskFlow – Controle de Tarefas Corporativas**, considerando seu modelo atual de banco de dados.

O modelo é composto por oito tabelas: `SETOR`, `COLABORADOR`, `PRIORIDADE`, `TAREFA`, `TELEFONE_COLABORADOR`, `HISTORICO_STATUS`, `OBSERVACAO_TAREFA` e `ATRIBUICAO_TAREFA`.

## 2. Estrutura e regras de integridade das tabelas

### 2.1. SETOR

Armazena os setores da organização.

- Deve possuir uma chave primária única, conforme definida no modelo.
- Os atributos obrigatórios devem ser preenchidos.
- Um setor pode estar relacionado a vários colaboradores.

### 2.2. COLABORADOR

Armazena os dados dos colaboradores.

- Deve possuir uma chave primária única, conforme definida no modelo.
- Os dados obrigatórios do colaborador devem ser preenchidos.
- Deve possuir referência válida a um registro de `SETOR`.
- Pode estar relacionado a vários telefones e atribuições, conforme o modelo.

### 2.3. PRIORIDADE

Armazena os níveis de prioridade disponíveis.

- Possui `id_prioridade` como chave primária.
- Cada identificador deve ser único.
- Os registros devem representar prioridades válidas.
- Uma prioridade pode estar relacionada a várias tarefas.

Os níveis previstos são Baixa, Média, Alta e Crítica, sujeitos à confirmação dos valores cadastrados no modelo atual.

### 2.4. TAREFA

Armazena as tarefas do sistema.

- Possui `codigo_tarefa` como chave primária.
- O atributo `descricao` deve ser preenchido.
- O atributo `status_atual` deve conter um status permitido.
- O atributo `id_prioridade` deve referenciar um registro existente em `PRIORIDADE`.
- Uma tarefa pode possuir históricos, observações e atribuições.

### 2.5. TELEFONE_COLABORADOR

Armazena os telefones relacionados aos colaboradores.

- Deve possuir uma chave primária definida no modelo.
- Deve possuir uma chave estrangeira que referencie um colaborador existente.
- O número deve respeitar o formato e as restrições definidos para o atributo.
- Um colaborador pode possuir mais de um registro de telefone, caso permitido pelo modelo.

### 2.6. HISTORICO_STATUS

Armazena as alterações de status das tarefas.

- Deve possuir uma chave primária própria.
- Deve possuir uma chave estrangeira que referencie uma tarefa existente.
- Deve registrar um status válido.
- Os campos de data e horário devem respeitar os tipos definidos no modelo.
- Os registros anteriores devem ser preservados para manter o histórico das alterações.

### 2.7. OBSERVACAO_TAREFA

Armazena observações relacionadas às tarefas.

- Deve possuir uma chave primária própria.
- Deve possuir uma chave estrangeira que referencie uma tarefa existente.
- O conteúdo deve ser preenchido quando definido como obrigatório.
- Uma tarefa pode possuir várias observações.

### 2.8. ATRIBUICAO_TAREFA

Relaciona tarefas e colaboradores.

- Possui `id_atribuicao` como chave primária.
- Deve possuir uma chave estrangeira que referencie uma tarefa existente.
- Deve possuir uma chave estrangeira que referencie um colaborador existente.
- Deve permitir diferentes registros de atribuição para a mesma tarefa e o mesmo colaborador.
- Não deve existir restrição de unicidade sobre a combinação entre tarefa e colaborador, pois cada atribuição é identificada individualmente por `id_atribuicao`.

## 3. Integridade referencial

As chaves estrangeiras devem garantir que os relacionamentos apontem para registros existentes.

| Relacionamento | Regra |
|---|---|
| SETOR – COLABORADOR | O setor referenciado pelo colaborador deve existir. |
| PRIORIDADE – TAREFA | A prioridade da tarefa deve existir na tabela PRIORIDADE. |
| COLABORADOR – TELEFONE_COLABORADOR | O telefone deve pertencer a um colaborador existente. |
| TAREFA – HISTORICO_STATUS | O histórico deve estar associado a uma tarefa existente. |
| TAREFA – OBSERVACAO_TAREFA | A observação deve estar associada a uma tarefa existente. |
| TAREFA – ATRIBUICAO_TAREFA | A atribuição deve referenciar uma tarefa existente. |
| COLABORADOR – ATRIBUICAO_TAREFA | A atribuição deve referenciar um colaborador existente. |

As operações de exclusão e atualização não devem gerar referências inválidas entre as tabelas. A política específica deve seguir o modelo e as regras de negócio aprovadas.

## 4. Restrições de domínio

Os atributos devem respeitar os tipos de dados e os valores permitidos definidos no modelo.

| Tabela | Restrições principais |
|---|---|
| SETOR | Identificador único e dados obrigatórios preenchidos. |
| COLABORADOR | Identificador único, dados obrigatórios e setor válido. |
| PRIORIDADE | `id_prioridade` único e identificação válida da prioridade. |
| TAREFA | `codigo_tarefa` único, `descricao` preenchida, `status_atual` válido e `id_prioridade` existente. |
| TELEFONE_COLABORADOR | Identificador único, colaborador existente e número válido. |
| HISTORICO_STATUS | Identificador único, tarefa existente, status válido e data válida. |
| OBSERVACAO_TAREFA | Identificador único, tarefa existente e conteúdo conforme obrigatoriedade definida. |
| ATRIBUICAO_TAREFA | `id_atribuicao` único e referências válidas a tarefa e colaborador. |

Os atributos obrigatórios não podem ser nulos ou vazios. Os opcionais podem permanecer sem preenchimento quando permitido pelo modelo.

A lista definitiva de atributos obrigatórios e opcionais deve corresponder ao dicionário de dados oficial.

## 5. Fluxo de status

O atributo `status_atual`, da tabela `TAREFA`, deve aceitar exclusivamente os seguintes valores:

| Status | Descrição |
|---|---|
| Pendente | Tarefa cadastrada, ainda não iniciada. |
| Em Andamento | Tarefa cuja execução foi iniciada. |
| Concluída | Tarefa cuja execução foi finalizada. |

### 5.1. Transições permitidas

| Status atual | Próximo status |
|---|---|
| Pendente | Em Andamento |
| Em Andamento | Concluída |
| Concluída | Nenhum. É o estado final. |

### 5.2. Regras do fluxo

- Toda tarefa deve iniciar como Pendente.
- Uma tarefa Pendente pode passar para Em Andamento.
- Uma tarefa Em Andamento pode passar para Concluída.
- Não é permitido pular etapas.
- Não são permitidos status adicionais sem aprovação e atualização da especificação.

Toda alteração deve ser registrada em `HISTORICO_STATUS`, preservando os registros anteriores e mantendo a consistência com `status_atual`.

## 6. Relação com as regras de negócio

As regras de integridade devem estar alinhadas ao catálogo oficial do TaskFlow.

As regras RN-01, RN-02 e RN-03 devem ser conferidas em sua versão atual para garantir que não existam divergências entre as regras de negócio, as restrições do banco de dados e os relacionamentos.

Não devem ser atribuídas definições presumidas a essas regras sem consultar o catálogo oficial.

## 7. Considerações finais

As regras apresentadas buscam garantir a integridade dos dados e a consistência dos relacionamentos entre as oito tabelas do TaskFlow.

As chaves primárias identificam individualmente os registros, enquanto as chaves estrangeiras asseguram a validade dos relacionamentos.

A tabela `ATRIBUICAO_TAREFA` utiliza `id_atribuicao` para identificar cada atribuição, permitindo registros distintos entre a mesma tarefa e o mesmo colaborador.

O fluxo de status deve respeitar a sequência Pendente, Em Andamento e Concluída, com registro das alterações em `HISTORICO_STATUS`.

A especificação deve permanecer compatível com o modelo lógico, o dicionário de dados e o catálogo de regras de negócio aprovados pela equipe.
