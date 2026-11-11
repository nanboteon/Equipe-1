# 06_regras_integridade_v1.md

## 1. Objetivo

Este documento tem como objetivo especificar as regras de integridade, restrições de domínio e transições de status do sistema TaskFlow – Controle de Tarefas Corporativas.

As regras estabelecidas garantem a consistência dos dados, evitam informações inválidas e definem as condições necessárias para o funcionamento correto das tarefas dentro do sistema.

## 2. Regras de Integridade

As regras de integridade determinam condições que devem ser respeitadas para manter a confiabilidade das informações cadastradas no sistema.

- **RI01:** Toda tarefa deve possuir um identificador único.
- **RI02:** Toda tarefa deve possuir um título obrigatório.
- **RI03:** Toda tarefa deve estar associada a um usuário responsável existente no sistema.
- **RI04:** Toda tarefa deve estar vinculada a um projeto existente, quando o cadastro estiver associado a um projeto.
- **RI05:** Um usuário inativo não pode receber novas atribuições de tarefas.
- **RI06:** Uma tarefa não pode ser atribuída a um usuário inexistente.
- **RI07:** Uma tarefa concluída não pode ser considerada pendente ou em andamento sem uma alteração explícita e autorizada de seu status.
- **RI08:** A exclusão de um usuário não deve deixar tarefas com referências inválidas.
- **RI09:** Toda alteração de status deve respeitar o fluxo de transições permitido.
- **RI10:** Uma tarefa deve possuir uma data de criação válida e registrada pelo sistema.

## 3. Restrições de Domínio

As restrições de domínio definem os valores permitidos para os atributos das entidades do sistema.

### 3.1. Tarefa

| Atributo | Restrição |
|---|---|
| ID | Obrigatório e único. |
| Título | Obrigatório e não pode ser vazio. |
| Descrição | Campo opcional. |
| Data de criação | Deve ser uma data válida. |
| Data de vencimento | Deve ser uma data válida e, normalmente, posterior ou igual à data de criação. |
| Prioridade | Deve ser Baixa, Média ou Alta. |
| Status | Deve corresponder a um dos status permitidos pelo sistema. |
| Responsável | Deve corresponder a um usuário cadastrado e ativo. |

### 3.2. Usuário

| Atributo | Restrição |
|---|---|
| ID | Obrigatório e único. |
| Nome | Obrigatório e não pode ser vazio. |
| E-mail | Obrigatório e deve possuir formato válido. |
| Status do usuário | Deve ser Ativo ou Inativo. |
| Perfil | Deve corresponder a um perfil cadastrado no sistema. |

### 3.3. Projeto

| Atributo | Restrição |
|---|---|
| ID | Obrigatório e único. |
| Nome | Obrigatório e não pode ser vazio. |
| Descrição | Campo opcional. |
| Data de criação | Deve ser uma data válida. |
| Status | Deve ser Ativo ou Inativo. |

## 4. Fluxo de Status das Tarefas

O sistema TaskFlow deverá possuir um fluxo de status para controlar as etapas de execução das tarefas.

### 4.1. Status disponíveis

- **Pendente:** A tarefa foi cadastrada, mas ainda não começou a ser executada.
- **Em andamento:** A tarefa está sendo executada pelo responsável.
- **Em revisão:** A execução foi finalizada e a tarefa aguarda avaliação.
- **Concluída:** A tarefa foi revisada e considerada finalizada.
- **Cancelada:** A tarefa foi cancelada e não será mais executada.

### 4.2. Transições permitidas

| Status atual | Próximo status permitido |
|---|---|
| Pendente | Em andamento ou Cancelada |
| Em andamento | Em revisão ou Cancelada |
| Em revisão | Concluída ou Em andamento |
| Concluída | Nenhum |
| Cancelada | Nenhum |

### 4.3. Regras de transição

- **RS01:** Uma tarefa recém-criada deve iniciar com o status Pendente.
- **RS02:** Uma tarefa Pendente pode passar para Em andamento quando sua execução for iniciada.
- **RS03:** Uma tarefa Em andamento pode passar para Em revisão quando sua execução for finalizada.
- **RS04:** Uma tarefa Em revisão pode passar para Concluída após aprovação.
- **RS05:** Uma tarefa Em revisão pode retornar para Em andamento caso sejam necessárias correções.
- **RS06:** Uma tarefa Pendente ou Em andamento pode ser Cancelada mediante autorização.
- **RS07:** Uma tarefa Concluída não pode voltar automaticamente para um status anterior.
- **RS08:** Uma tarefa Cancelada não pode ser reativada diretamente. Caso necessário, deverá ser criada uma nova tarefa.
