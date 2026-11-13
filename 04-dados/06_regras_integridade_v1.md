# 06 — Regras de Integridade e Restrições de Domínio

## 1. Objetivo

Este documento define as regras de integridade, as restrições de domínio e o fluxo de status do sistema TaskFlow – Controle de Tarefas Corporativas, considerando seu modelo lógico e o dicionário de dados.

O modelo é composto por oito tabelas: SETOR, COLABORADOR, PRIORIDADE, TAREFA, TELEFONE_COLABORADOR, HISTORICO_STATUS, OBSERVACAO_TAREFA e ATRIBUICAO_TAREFA.

As restrições descritas neste documento devem ser implementadas ou verificadas no banco de dados e na aplicação, conforme a natureza de cada regra.

## 2. Estrutura e regras de integridade das tabelas

### 2.1. SETOR

Armazena os setores da organização.

- Possui `id_setor` como chave primária, com geração automática.
- `nome_setor` é obrigatório e deve ser único.
- `sigla` é obrigatória e deve ser única.
- Um setor pode estar relacionado a vários colaboradores.
- Um colaborador deve estar vinculado a um único setor, conforme a chave estrangeira `COLABORADOR.id_setor`.

### 2.2. COLABORADOR

Armazena os dados dos colaboradores.

- Possui `matricula` como chave primária, com geração automática.
- `nome` é obrigatório.
- `id_setor` é obrigatório e deve referenciar um registro existente em SETOR.
- `email` é opcional; quando informado, deve ser único.
- Um colaborador pode possuir vários telefones, atribuições de tarefas e registros de histórico sob sua responsabilidade.

### 2.3. PRIORIDADE

Armazena os níveis de prioridade disponíveis para as tarefas.

- Possui `id_prioridade` como chave primária, com geração automática.
- `nome_prioridade` é obrigatório e deve ser único.
- `descricao` é opcional.
- Uma prioridade pode estar relacionada a várias tarefas.
- Os níveis previstos na documentação atual são Baixa, Média, Alta e Crítica. A lista definitiva deve corresponder aos valores efetivamente aprovados e cadastrados pela equipe.

### 2.4. TAREFA

Armazena as tarefas do sistema.

- Possui `codigo_tarefa` como chave primária, com geração automática.
- `descricao` é obrigatória.
- `id_prioridade` é obrigatório e deve referenciar um registro existente em PRIORIDADE.
- `prazo_limite`, `status_atual` e `data_criacao` são obrigatórios, conforme o modelo lógico.
- `status_atual` deve conter exclusivamente um dos valores permitidos: Pendente, Em Andamento ou Concluída.
- Toda tarefa deve iniciar com o status Pendente.
- Uma tarefa pode possuir vários registros em HISTORICO_STATUS, OBSERVACAO_TAREFA e ATRIBUICAO_TAREFA.
- A condição `em_atraso` é um atributo derivado, não armazenado como coluna física. A tarefa é considerada atrasada quando a data atual for posterior a `prazo_limite` e seu `status_atual` for diferente de Concluída.

### 2.5. TELEFONE_COLABORADOR

Armazena os telefones relacionados aos colaboradores. Essa tabela representa o atributo multivalorado telefone da entidade COLABORADOR.

- Possui chave primária composta por `matricula_colaborador` e `numero_telefone`.
- `matricula_colaborador` também é chave estrangeira e deve referenciar `COLABORADOR.matricula`.
- `numero_telefone` é obrigatório e deve respeitar o tipo, o tamanho e o formato definidos no dicionário de dados.
- Um colaborador pode possuir zero ou vários telefones cadastrados.
- Não pode haver repetição do mesmo número para o mesmo colaborador, devido à chave primária composta.

### 2.6. HISTORICO_STATUS

Armazena as alterações de status das tarefas.

- Possui chave primária composta por `codigo_tarefa` e `data_mudanca`.
- `codigo_tarefa` também é chave estrangeira e deve referenciar uma tarefa existente em TAREFA.
- `matricula_responsavel` é obrigatório e deve referenciar um colaborador existente em COLABORADOR.
- `status_novo` é obrigatório e deve conter um status permitido.
- `status_anterior` pode ser nulo quando não houver um status anterior registrado, como no registro inicial da tarefa.
- `data_mudanca` é obrigatória e deve respeitar o tipo de data e hora definido no modelo.
- Os registros anteriores devem ser preservados para manter o histórico das alterações.
- Cada alteração de status deve identificar o colaborador responsável.

### 2.7. OBSERVACAO_TAREFA

Armazena observações relacionadas às tarefas.

- Possui `id_observacao` como chave primária, com geração automática.
- `codigo_tarefa` é chave estrangeira obrigatória e deve referenciar uma tarefa existente em TAREFA.
- `texto_observacao` e `data_registro` são obrigatórios, conforme o modelo lógico.
- Uma tarefa pode possuir zero ou várias observações.
- Cada observação pertence a uma única tarefa.

### 2.8. ATRIBUICAO_TAREFA

Relaciona tarefas e colaboradores, resolvendo o relacionamento muitos-para-muitos entre essas entidades.

- Possui `id_atribuicao` como chave primária, com geração automática.
- `codigo_tarefa` é chave estrangeira obrigatória e deve referenciar uma tarefa existente em TAREFA.
- `matricula_colaborador` é chave estrangeira obrigatória e deve referenciar um colaborador existente em COLABORADOR.
- `data_atribuicao` é obrigatória.
- `papel_na_tarefa` é opcional.
- Deve permitir diferentes registros de atribuição para a mesma tarefa e o mesmo colaborador, quando realizados em momentos distintos.
- Não deve existir restrição de unicidade sobre a combinação `codigo_tarefa` e `matricula_colaborador`, pois cada registro é identificado individualmente por `id_atribuicao`.

## 3. Integridade referencial

As chaves estrangeiras devem garantir que os relacionamentos apontem para registros existentes. Não deve ser possível inserir um registro filho que referencie um registro inexistente.

| Relacionamento | Regra de integridade |
|---|---|
| SETOR – COLABORADOR | O setor referenciado por `COLABORADOR.id_setor` deve existir em SETOR. |
| PRIORIDADE – TAREFA | A prioridade referenciada por `TAREFA.id_prioridade` deve existir em PRIORIDADE. |
| COLABORADOR – TELEFONE_COLABORADOR | O telefone deve pertencer a um colaborador existente. |
| TAREFA – HISTORICO_STATUS | O histórico deve estar associado a uma tarefa existente. |
| COLABORADOR – HISTORICO_STATUS | O responsável pela alteração deve ser um colaborador existente. |
| TAREFA – OBSERVACAO_TAREFA | A observação deve estar associada a uma tarefa existente. |
| TAREFA – ATRIBUICAO_TAREFA | A atribuição deve referenciar uma tarefa existente. |
| COLABORADOR – ATRIBUICAO_TAREFA | A atribuição deve referenciar um colaborador existente. |

As operações de atualização e exclusão não devem produzir referências inválidas. A política de `ON UPDATE` e `ON DELETE`, como restringir exclusões ou utilizar exclusão em cascata, deve ser definida de acordo com as regras de negócio aprovadas.

Enquanto essa política não estiver formalizada, não se deve presumir que a exclusão em cascata seja permitida, especialmente para registros de histórico.

## 4. Restrições de domínio

Os atributos devem respeitar os tipos de dados, tamanhos, nulabilidade e valores permitidos definidos no modelo lógico e no dicionário de dados.

| Tabela | Restrições principais |
|---|---|
| SETOR | `id_setor` como PK; `nome_setor` e `sigla` obrigatórios e únicos. |
| COLABORADOR | `matricula` como PK; `nome` obrigatório; `id_setor` obrigatório e válido; `email` opcional e único quando preenchido. |
| PRIORIDADE | `id_prioridade` como PK; `nome_prioridade` obrigatório e único. |
| TAREFA | `codigo_tarefa` como PK; `descricao`, `id_prioridade`, `prazo_limite`, `status_atual` e `data_criacao` obrigatórios; prioridade existente e status permitido. |
| TELEFONE_COLABORADOR | PK composta por `matricula_colaborador` e `numero_telefone`; colaborador existente e número obrigatório. |
| HISTORICO_STATUS | PK composta por `codigo_tarefa` e `data_mudanca`; tarefa e responsável existentes; `status_novo` obrigatório e válido; `status_anterior` opcional. |
| OBSERVACAO_TAREFA | `id_observacao` como PK; tarefa existente; `texto_observacao` e `data_registro` obrigatórios. |
| ATRIBUICAO_TAREFA | `id_atribuicao` como PK; referências válidas a tarefa e colaborador; `data_atribuicao` obrigatória; `papel_na_tarefa` opcional. |

Os atributos obrigatórios não podem receber valores nulos. Campos textuais obrigatórios também não devem aceitar conteúdo vazio ou composto apenas por espaços, caso essa validação seja implementada pela aplicação ou por restrição compatível no banco.

Os atributos opcionais podem permanecer sem preenchimento quando permitido pelo modelo. Em particular, `email`, `descricao` de PRIORIDADE, `status_anterior` e `papel_na_tarefa` são opcionais conforme a documentação atual.

A condição `em_atraso` deve ser calculada a partir de `prazo_limite` e `status_atual`, não armazenada como atributo físico.

## 5. Fluxo de status

O atributo `status_atual`, da tabela TAREFA, deve aceitar exclusivamente os seguintes valores:

| Status | Descrição |
|---|---|
| Pendente | Tarefa cadastrada, ainda não iniciada. |
| Em Andamento | Tarefa cuja execução foi iniciada. |
| Concluída | Tarefa cuja execução foi finalizada. |

### 5.1. Transições permitidas

| Status atual | Próximo status permitido |
|---|---|
| Pendente | Em Andamento |
| Em Andamento | Concluída |
| Concluída | Nenhum. É o estado final. |

### 5.2. Regras do fluxo

- Toda tarefa deve iniciar com o status Pendente.
- Uma tarefa Pendente pode passar para Em Andamento.
- Uma tarefa Em Andamento pode passar para Concluída.
- Não é permitido pular etapas.
- Não são permitidos status adicionais sem aprovação e atualização desta especificação.
- Toda alteração de status deve ser registrada em HISTORICO_STATUS.
- Cada registro de alteração deve informar `status_anterior`, `status_novo`, `data_mudanca` e `matricula_responsavel`, respeitando a possibilidade de `status_anterior` ser nulo no registro inicial.
- O valor de `TAREFA.status_atual` deve corresponder ao último status registrado no histórico.
- Os registros anteriores do histórico não devem ser sobrescritos ou excluídos durante uma alteração comum de status.

A validação da sequência de transições deve ocorrer na lógica da aplicação ou em mecanismo específico do banco de dados. Uma restrição `CHECK` que apenas limita os valores de `status_atual` não impede, sozinha, uma transição inválida.

## 6. Relação com as regras de negócio

As regras de integridade devem permanecer alinhadas ao catálogo oficial de regras de negócio do TaskFlow.

Os identificadores RN-01, RN-02 e RN-03 devem manter exatamente as definições, condições e descrições registradas no catálogo aprovado pela equipe. Este documento não atribui novas definições a esses identificadores para evitar divergências entre os artefatos do projeto.

Quando uma regra de negócio for alterada, os documentos afetados — catálogo de regras, inventário de dados, decisões de modelagem, modelo lógico, dicionário de dados e este documento — devem ser revisados para preservar a consistência da especificação.

## 7. Considerações finais

As regras apresentadas buscam garantir a integridade dos dados e a consistência dos relacionamentos entre as oito tabelas do TaskFlow.

As chaves primárias identificam os registros, enquanto as chaves estrangeiras asseguram a validade dos relacionamentos. As restrições de domínio determinam quais valores podem ser armazenados, e as regras de transição controlam a evolução do status das tarefas.

A tabela `ATRIBUICAO_TAREFA` utiliza `id_atribuicao` para identificar cada atribuição individualmente, permitindo registros distintos entre a mesma tarefa e o mesmo colaborador.

A tabela `TELEFONE_COLABORADOR` utiliza chave primária composta por `matricula_colaborador` e `numero_telefone`. A tabela `HISTORICO_STATUS` utiliza chave primária composta por `codigo_tarefa` e `data_mudanca`, além de registrar o colaborador responsável por cada alteração.

O fluxo de status deve respeitar a sequência Pendente, Em Andamento e Concluída, com registro das alterações em HISTORICO_STATUS.

Esta especificação deve permanecer compatível com o modelo lógico, o dicionário de dados e o catálogo de regras de negócio aprovados pela equipe.
