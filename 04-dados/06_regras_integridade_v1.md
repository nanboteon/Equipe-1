# 06 — Especificação das Regras de Integridade e Restrições de Domínio

## 1. Objetivo

Este documento apresenta as regras de integridade, as restrições de domínio e o fluxo de status do sistema **TaskFlow – Controle de Tarefas Corporativas**.

A especificação considera as entidades **Tarefa** e **Colaborador**, além da entidade associativa **Atribuição**, responsável por representar o relacionamento entre tarefas e colaboradores.

## 2. Regras de Integridade

As regras de integridade definem as condições que devem ser respeitadas para garantir a consistência dos dados armazenados no sistema.

| Código | Regra |
|---|---|
| RI-01 | Cada Tarefa deve possuir um identificador único (`id_tarefa`), utilizado como chave primária. |
| RI-02 | Cada Colaborador deve possuir uma matrícula única (`matricula`), utilizada como identificador. |
| RI-03 | Os atributos obrigatórios de Tarefa e Colaborador devem ser preenchidos antes do cadastro. |
| RI-04 | Cada registro de Atribuição deve referenciar uma Tarefa e um Colaborador existentes. |
| RI-05 | O relacionamento entre Tarefa e Colaborador deve ser representado pela entidade associativa Atribuição, permitindo que uma tarefa seja relacionada a um ou mais colaboradores. |
| RI-06 | Não deve existir mais de uma associação entre a mesma Tarefa e o mesmo Colaborador. |
| RI-07 | Toda alteração de status de uma Tarefa deve respeitar o fluxo de transições definido neste documento. |

## 3. Restrições de Domínio

As restrições de domínio determinam quais valores são permitidos para os atributos das entidades do sistema.

### 3.1. Entidade Tarefa

| Atributo | Restrição |
|---|---|
| `id_tarefa` | Obrigatório e único. |
| `descricao` | Obrigatória. Deve descrever a tarefa. |
| `status` | Deve aceitar exclusivamente: Pendente, Em Andamento ou Concluída. |
| `prioridade` | Deve aceitar: Baixa, Média, Alta ou Crítica, sujeito à confirmação da equipe. |
| `data_criacao` | Deve representar uma data válida, quando utilizada. |

### 3.2. Entidade Colaborador

| Atributo | Restrição |
|---|---|
| `matricula` | Obrigatória e única. |
| `nome` | Obrigatório. |
| `setor` | Deve identificar o setor ao qual o colaborador pertence. |

### 3.3. Entidade Atribuição

A entidade Atribuição representa o relacionamento entre Tarefa e Colaborador.

| Atributo | Restrição |
|---|---|
| `id_tarefa` | Deve referenciar uma Tarefa existente, por meio de chave estrangeira. |
| `matricula` | Deve referenciar um Colaborador existente, por meio de chave estrangeira. |
| Combinação dos atributos | A combinação `id_tarefa` + `matricula` deve ser única, evitando associações duplicadas. |

### 3.4. Restrições relacionadas às datas

As datas registradas pelo sistema devem possuir valores válidos.

Não será imposta uma regra que obrigue a data de vencimento a ser posterior ou igual à data de criação sem que essa condição seja aprovada como regra de negócio pela equipe.

## 4. Fluxo de Status das Tarefas

O sistema deve possuir exclusivamente três status para representar a situação de uma tarefa:

| Status | Descrição |
|---|---|
| **Pendente** | A tarefa foi cadastrada, mas sua execução ainda não foi iniciada. |
| **Em Andamento** | A execução da tarefa foi iniciada e está em progresso. |
| **Concluída** | A execução da tarefa foi finalizada. |

### 4.1. Transições permitidas

As mudanças de status devem seguir uma sequência definida, sem permitir que etapas sejam ignoradas.

| Status atual | Próximo status permitido |
|---|---|
| Pendente | Em Andamento |
| Em Andamento | Concluída |
| Concluída | Nenhum |

### 4.2. Regras do fluxo de status

- **RS-01:** Toda tarefa deve ser iniciada com o status Pendente.
- **RS-02:** Uma tarefa Pendente pode passar para Em Andamento.
- **RS-03:** Uma tarefa Em Andamento pode passar para Concluída.
- **RS-04:** Não é permitido pular etapas do fluxo de status.
- **RS-05:** Não estão previstos os status Em Revisão ou Cancelada na especificação atual.

## 5. Relação com as Regras de Negócio

As regras de integridade e as restrições de domínio devem estar alinhadas às regras de negócio do projeto.

A correspondência com as regras RN-01, RN-02 e RN-03 deve ser conferida com a documentação oficial da equipe, pois seus conteúdos precisam ser considerados antes de estabelecer uma relação definitiva.

| Regra de negócio | Validação necessária |
|---|---|
| RN-01 | Conferir o conteúdo e verificar sua relação com as regras de integridade. |
| RN-02 | Conferir o conteúdo e verificar sua relação com as restrições de domínio. |
| RN-03 | Conferir o conteúdo e verificar sua relação com o fluxo de status. |

## 6. Pontos para Validação com a Equipe

Antes da finalização da documentação, os seguintes pontos devem ser confirmados:

1. **Prioridade:** confirmar com Annanda e Elisama se os valores Baixa, Média, Alta e Crítica serão utilizados definitivamente.
2. **Atributos:** confirmar os nomes e a estrutura dos atributos das entidades Tarefa e Colaborador.
3. **Atribuição:** validar a estrutura da entidade associativa e suas chaves.
4. **Regras de negócio:** conferir o conteúdo oficial de RN-01, RN-02 e RN-03.

## 7. Considerações Finais

A especificação estabelece as condições necessárias para manter a integridade e a consistência das informações do TaskFlow.

O relacionamento entre tarefas e colaboradores é representado pela entidade associativa Atribuição, sem exigir um responsável único por tarefa.

O fluxo de status utiliza exclusivamente Pendente, Em Andamento e Concluída, respeitando a sequência de transições definida.

As regras apresentadas devem ser mantidas alinhadas à documentação oficial do projeto e às decisões aprovadas pela equipe.
