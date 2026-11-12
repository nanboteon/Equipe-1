
# 00 --- Catálogo de Regras de Negócio

## Projeto TaskFlow --- Controle de Tarefas Corporativas

**Equipe 01 | Versão 1.1 | 12/11/2026**

### Objetivo

Este documento formaliza as regras de negócio identificadas na narrativa do cenário TaskFlow e alinhadas ao dicionário de dados atualizado. As regras descrevem restrições que o sistema deve respeitar no controle de tarefas, de suas atribuições e do acompanhamento das mudanças de status.

### RN-01 --- Registro da data de atribuição

**Descrição:** Toda vez que uma tarefa for atribuída a um colaborador, o sistema deve registrar a data e a hora em que a atribuição foi realizada.

**Justificativa:** Permitir identificar quando cada responsabilidade foi assumida e manter o registro das atribuições ao longo do tempo.

**Representação no modelo:** Atributo `data_atribuicao` na entidade associativa `ATRIBUICAO_TAREFA`, que relaciona `COLABORADOR` e `TAREFA`.

### RN-02 --- Sequência obrigatória de status

**Descrição:** O status de uma tarefa deve seguir exclusivamente a sequência `Pendente` → `Em Andamento` → `Concluída`, sem permitir que uma etapa seja pulada.

**Justificativa:** Padronizar o acompanhamento das tarefas e impedir transições inválidas entre os estados.

**Representação no modelo:** Restrição de integridade sobre o atributo `status_atual` da tabela `TAREFA`. A validação deve permitir somente as transições Pendente para Em Andamento e Em Andamento para Concluída, além de manter o status atual quando não houver alteração. As mudanças realizadas também devem ser registradas na tabela `HISTORICO_STATUS`, por meio dos atributos `status_anterior` e `status_novo`.

### RN-03 --- Identificação de tarefa em atraso

**Descrição:** Uma tarefa deve ser considerada atrasada quando sua data e hora de prazo já tiverem passado e seu status for diferente de `Concluída`.

**Justificativa:** Identificar tarefas cujo prazo foi ultrapassado e que ainda não foram finalizadas.

**Representação no modelo:** Atributo derivado `em_atraso`, calculado a partir dos atributos `prazo_limite` e `status_atual` da tabela `TAREFA`. Não é necessário armazenar esse resultado como dado independente.

### RN-04 --- Registro das alterações de status

**Descrição:** Toda alteração de status de uma tarefa deve ser registrada no histórico, informando o status anterior, o novo status, a data e a hora da mudança e o colaborador responsável pela alteração.

**Justificativa:** Manter a rastreabilidade das mudanças de status, permitindo identificar o que foi alterado, quando ocorreu e quem realizou a alteração.

**Representação no modelo:** Tabela `HISTORICO_STATUS`, vinculada à tabela `TAREFA` por meio de `codigo_tarefa` e à tabela `COLABORADOR` por meio de `matricula_responsavel`. A identificação do registro utiliza a chave primária composta por `codigo_tarefa` e `data_mudanca`. O responsável pela alteração é obrigatório.

### Observações

- As regras RN-01, RN-02 e RN-03 foram mantidas a partir do catálogo anterior, com ajustes nos nomes e nas representações para acompanhar o dicionário de dados atualizado.
- A RN-04 foi acrescentada para contemplar o registro das mudanças de status e a identificação obrigatória do colaborador responsável, conforme definido no dicionário de dados.
- A tabela `PRIORIDADE` e a tabela `TELEFONE_COLABORADOR` representam decisões de estrutura dos dados. Por si só, não determinam novas regras de negócio neste catálogo.
- O e-mail do colaborador permanece opcional (`NULL`), conforme o dicionário de dados. Essa definição é uma restrição de preenchimento, não uma nova regra de negócio.
- A inclusão de `OBSERVACAO_TAREFA` permanece condicionada à aprovação da equipe. Caso seja aprovada, sua inclusão deverá ser refletida também no modelo conceitual e nos demais documentos relacionados.
- A lista de prioridades deverá seguir a definição alinhada pela equipe nos documentos do projeto.
- Qualquer regra adicional deverá ser aprovada pelo grupo e registrada com novo identificador (RN-05, RN-06 etc.).
