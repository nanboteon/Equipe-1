# 00 --- Catálogo de Regras de Negócio

## Projeto TaskFlow --- Controle de Tarefas Corporativas

**Equipe 01 \| Versão 1.0 \| 06/11/2026**

### Objetivo

Este documento formaliza as regras de negócio identificadas na narrativa
do cenário TaskFlow. As regras descrevem restrições que o sistema deve
respeitar no controle de tarefas e de suas atribuições.

### RN-01 --- Registro da data de atribuição

**Descrição:** Toda vez que uma tarefa for atribuída a um colaborador, o
sistema deve registrar a data em que a atribuição foi realizada.

**Justificativa:** Permitir identificar quando cada responsabilidade foi
assumida e manter o registro das atribuições ao longo do tempo.

**Representação no modelo:** Atributo `data_atribuicao` na entidade
associativa `Atribuicao`, que relaciona `Colaborador` e `Tarefa`.

### RN-02 --- Sequência obrigatória de status

**Descrição:** O status de uma tarefa deve seguir exclusivamente a
sequência `Pendente` → `Em Andamento` → `Concluída`, sem permitir que
uma etapa seja pulada.

**Justificativa:** Padronizar o acompanhamento das tarefas e impedir
transições inválidas entre os estados.

**Representação no modelo:** Restrição de integridade sobre o atributo
`status` da tarefa. A validação deve permitir somente as transições
Pendente para Em Andamento e Em Andamento para Concluída, além de manter
o status atual quando não houver alteração.

### RN-03 --- Identificação de tarefa em atraso

**Descrição:** Uma tarefa deve ser considerada atrasada quando sua data
de prazo já tiver passado e seu status for diferente de `Concluída`.

**Justificativa:** Identificar tarefas cujo prazo foi ultrapassado e que
ainda não foram finalizadas.

**Representação no modelo:** Atributo derivado `em_atraso`, calculado a
partir dos atributos `prazo` e `status` da entidade `Tarefa`. Não é
necessário armazenar esse resultado como dado independente.

### Observações

-   As três regras acima foram extraídas diretamente da narrativa
    oficial do cenário TaskFlow.
-   O catálogo não cria regras sobre e-mail, telefone, projetos ou
    outros recursos que não estejam definidos pela equipe.
-   Qualquer regra adicional deverá ser aprovada pelo grupo e registrada
    com novo identificador (RN-04, RN-05 etc.).
