# 01 --- Inventário de Dados

## Projeto TaskFlow --- Controle de Tarefas Corporativas

**Equipe 01 \| Versão 1.0 \| 06/11/2026**

### 1. Objetivo

Identificar os dados necessários para representar o cenário TaskFlow,
separando os dados de colaboradores, tarefas e atribuições. Este
inventário é a base para a elaboração do modelo conceitual e deverá ser
revisado em conjunto pela equipe.

### 2. Dados identificados na narrativa

#### 2.1 Colaborador

Representa uma pessoa cadastrada que pode receber tarefas.

  -----------------------------------------------------------------------
  Dado                    Descrição               Observação
  ----------------------- ----------------------- -----------------------
  matrícula               Identificador do        Identificador único
                          colaborador             

  nome                    Nome do colaborador     Dado cadastral

  setor                   Setor ao qual pertence  Pode ser atributo
                                                  textual nesta primeira
                                                  versão
  -----------------------------------------------------------------------

#### 2.2 Tarefa

Representa uma atividade que precisa ser acompanhada pela equipe.

  -----------------------------------------------------------------------
  Dado                    Descrição               Observação
  ----------------------- ----------------------- -----------------------
  código                  Identificador da tarefa Identificador único

  descrição               Descrição da atividade  Dado descritivo

  prioridade              Grau de prioridade      Domínio/valores a
                                                  definir pela equipe

  prazo                   Data limite para        Usado na identificação
                          conclusão               de atraso

  status                  Situação atual da       Pendente, Em Andamento
                          tarefa                  ou Concluída

  em_atraso               Indica se a tarefa está Atributo derivado de
                          atrasada                prazo e status
  -----------------------------------------------------------------------

#### 2.3 Atribuição

Representa a associação entre um colaborador e uma tarefa. É necessária
porque um colaborador pode acumular várias tarefas e uma tarefa pode ser
atribuída a mais de um colaborador ao longo do tempo.

  -----------------------------------------------------------------------
  Dado                    Descrição               Observação
  ----------------------- ----------------------- -----------------------
  data_atribuicao         Data em que a           Exigida pela RN-01
                          atribuição foi          
                          realizada               

  matrícula_colaborador   Referência ao           Chave estrangeira no
                          colaborador             modelo lógico

  código_tarefa           Referência à tarefa     Chave estrangeira no
                                                  modelo lógico
  -----------------------------------------------------------------------

### 3. Relacionamentos identificados

-   Um colaborador pode participar de várias atribuições.
-   Uma tarefa pode possuir várias atribuições ao longo do tempo.
-   Cada atribuição relaciona um colaborador a uma tarefa e registra a
    data correspondente.
-   Portanto, existe um relacionamento N:N entre `Colaborador` e
    `Tarefa`, resolvido pela entidade associativa `Atribuição`.

### 4. Dados derivados e regras relacionadas

-   `em_atraso` não é informado manualmente: é calculado comparando o
    prazo com a data atual e verificando se o status é diferente de
    Concluída.
-   O status deve respeitar a sequência definida na RN-02.
-   A data da atribuição deve ser registrada conforme a RN-01.

### 5. Decisões que precisam de validação da equipe

O edital exige pelo menos cinco entidades no modelo conceitual (sem
contar a entidade associativa), uma entidade dependente/fraca e um
atributo multivalorado. A narrativa fornece diretamente os dados de
Colaborador e Tarefa e o relacionamento N:N resolvido por Atribuição,
mas não especifica todos esses elementos adicionais.

  -----------------------------------------------------------------------
  Ponto                   Situação nesta versão   Próxima decisão
  ----------------------- ----------------------- -----------------------
  E-mail do colaborador   Considerar no máximo um Confirmar se será
                          e-mail, conforme        incluído no escopo
                          decisão discutida pelo  
                          grupo; não é dado da    
                          narrativa original      

  Atributo multivalorado  Ainda não identificado  Grupo deve escolher um
                          na narrativa            dado que realmente
                                                  possa ter vários
                                                  valores e justificar
                                                  sua necessidade

  Entidade                Ainda precisa ser       Grupo deve identificar
  fraca/dependente        definida                um registro cuja
                                                  existência dependa de
                                                  outra entidade

  Quinta entidade ou      Ainda não definidas     Grupo deve decidir
  outras entidades        pela narrativa          quais entidades
                                                  adicionais fazem
                                                  sentido para o domínio

  Valores de prioridade   Não detalhados no       Definir os valores
                          cenário                 aceitos, se necessário
  -----------------------------------------------------------------------

**Importante:** não foram inventados dados adicionais como projetos,
telefones ou múltiplos e-mails. Os itens pendentes devem ser decididos
pelo grupo antes do fechamento do modelo conceitual.

### 6. Referência

LOPES, Edson Vaz. *Edital do Projeto --- Unidade 3: Requisitos,
Modelagem e Análise de Dados*. 2026/2. Cenário Grupo 01 --- TaskFlow.
