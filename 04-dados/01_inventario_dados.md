
# 01 --- Inventário de Dados

## Projeto TaskFlow --- Controle de Tarefas Corporativas

**Equipe 01 | Versão 1.1 | 12/11/2026**

### 1. Objetivo

Identificar e organizar os dados necessários para representar o cenário TaskFlow, considerando as entidades, seus atributos, relacionamentos, dados derivados e características específicas do domínio. Este inventário serve como base para a elaboração dos modelos conceitual e lógico, devendo manter consistência com o dicionário de dados do projeto.

### 2. Dados identificados na narrativa

#### 2.1 SETOR

Representa os setores ou departamentos da organização aos quais os colaboradores pertencem.

| Dado | Descrição | Observação |
|---|---|---|
| `id_setor` | Identificador do setor | Identificador único, gerado automaticamente |
| `nome_setor` | Nome formal do setor | Obrigatório e único |
| `sigla` | Sigla representativa do setor | Obrigatória e única |

#### 2.2 COLABORADOR

Representa uma pessoa cadastrada que pode receber tarefas ou realizar alterações no sistema.

| Dado | Descrição | Observação |
|---|---|---|
| `matricula` | Identificador do colaborador | Identificador único, gerado automaticamente |
| `nome` | Nome completo do colaborador | Dado cadastral obrigatório |
| `email` | E-mail corporativo do colaborador | Opcional; quando informado, deve ser único |
| `id_setor` | Setor ao qual pertence | Referência à entidade SETOR |

#### 2.3 TELEFONE_COLABORADOR

Representa os números de telefone associados a um colaborador. É utilizada para organizar o atributo multivalorado telefone, permitindo que um colaborador possua vários números cadastrados.

| Dado | Descrição | Observação |
|---|---|---|
| `matricula_colaborador` | Referência ao colaborador | Chave estrangeira para COLABORADOR e parte da chave primária composta |
| `numero_telefone` | Número de telefone com DDD | Obrigatório e parte da chave primária composta |

**Chave primária composta:** `matricula_colaborador` + `numero_telefone`.

#### 2.4 PRIORIDADE

Representa os níveis de prioridade disponíveis para classificação das tarefas, mantendo os valores organizados em uma entidade própria.

| Dado | Descrição | Observação |
|---|---|---|
| `id_prioridade` | Identificador da prioridade | Identificador único, gerado automaticamente |
| `nome_prioridade` | Nome do nível de prioridade | Obrigatório e único |
| `descricao` | Descrição do critério de uso | Opcional |

**Valores iniciais propostos:**

- Baixa;
- Média;
- Alta;
- Crítica.

#### 2.5 TAREFA

Representa uma atividade cadastrada no TaskFlow que precisa ser acompanhada pela equipe.

| Dado | Descrição | Observação |
|---|---|---|
| `codigo_tarefa` | Identificador da tarefa | Identificador único, gerado automaticamente |
| `descricao` | Detalhamento da atividade | Dado descritivo obrigatório |
| `id_prioridade` | Prioridade da tarefa | Chave estrangeira para PRIORIDADE |
| `prazo_limite` | Data e hora limite para conclusão | Utilizado na identificação de atraso |
| `status_atual` | Situação atual da tarefa | Pendente, Em Andamento ou Concluída |
| `data_criacao` | Data e hora de cadastro da tarefa | Preenchida automaticamente |

**Dados derivados:**

- `em_atraso`: indica se a tarefa está atrasada. É calculado a partir de `prazo_limite` e `status_atual`, não sendo necessário armazená-lo como dado independente.

#### 2.6 ATRIBUICAO_TAREFA

Representa a associação entre colaboradores e tarefas. É necessária porque um colaborador pode participar de várias tarefas e uma tarefa pode possuir vários colaboradores associados ao longo do tempo.

| Dado | Descrição | Observação |
|---|---|---|
| `id_atribuicao` | Identificador da atribuição | Identificador único, gerado automaticamente |
| `codigo_tarefa` | Referência à tarefa | Chave estrangeira para TAREFA |
| `matricula_colaborador` | Referência ao colaborador | Chave estrangeira para COLABORADOR |
| `data_atribuicao` | Data e hora em que a atribuição foi realizada | Preenchida automaticamente |
| `papel_na_tarefa` | Função exercida pelo colaborador | Opcional; pode representar Executor ou Revisor |

**Identificação:** a atribuição possui identificador próprio (`id_atribuicao`), permitindo registrar novamente a mesma tarefa para o mesmo colaborador em outra data, sem perder o histórico anterior.

#### 2.7 HISTORICO_STATUS

Representa os registros das mudanças de status realizadas nas tarefas. Depende da existência de uma tarefa e permite acompanhar as alterações ao longo do tempo.

| Dado | Descrição | Observação |
|---|---|---|
| `codigo_tarefa` | Referência à tarefa | Chave estrangeira para TAREFA e parte da chave primária composta |
| `data_mudanca` | Data e hora da alteração | Obrigatória e parte da chave primária composta |
| `status_anterior` | Status anterior da tarefa | Opcional; pode não existir no primeiro registro |
| `status_novo` | Novo status assumido pela tarefa | Obrigatório |
| `matricula_responsavel` | Colaborador que realizou a alteração | Chave estrangeira para COLABORADOR e obrigatória |

**Chave primária composta:** `codigo_tarefa` + `data_mudanca`.

**Observação:** cada registro deve identificar a tarefa, o momento da alteração, o status anterior, o novo status e o colaborador responsável.

#### 2.8 OBSERVACAO_TAREFA

Representa as anotações ou comentários associados a uma tarefa, permitindo registrar informações sobre seu andamento ou possíveis impedimentos.

| Dado | Descrição | Observação |
|---|---|---|
| `id_observacao` | Identificador da observação | Identificador único, gerado automaticamente |
| `codigo_tarefa` | Referência à tarefa | Chave estrangeira para TAREFA |
| `texto_observacao` | Conteúdo da anotação | Texto obrigatório |
| `data_registro` | Data e hora do comentário | Preenchida automaticamente |

### 3. Relacionamentos identificados

- Um setor pode possuir vários colaboradores, enquanto cada colaborador pertence a um único setor.
- Um colaborador pode possuir nenhum ou vários telefones cadastrados, enquanto cada telefone pertence a um único colaborador.
- Uma prioridade pode estar associada a várias tarefas, enquanto cada tarefa possui uma única prioridade.
- Um colaborador pode participar de várias atribuições.
- Uma tarefa pode possuir várias atribuições ao longo do tempo.
- Cada atribuição relaciona um único colaborador a uma única tarefa e registra a data correspondente.
- Portanto, existe um relacionamento N:N entre `COLABORADOR` e `TAREFA`, resolvido pela entidade associativa `ATRIBUICAO_TAREFA`.
- Uma tarefa pode possuir vários registros de histórico de status, enquanto cada registro de histórico pertence a uma única tarefa.
- Um colaborador pode ser responsável por várias alterações de status, enquanto cada registro de histórico possui um único colaborador responsável.
- Uma tarefa pode possuir várias observações, enquanto cada observação pertence a uma única tarefa.

### 4. Dados derivados e regras relacionadas

- `em_atraso` não é informado manualmente: é calculado comparando `prazo_limite` com a data e hora atual e verificando se `status_atual` é diferente de `Concluída`.
- O status da tarefa deve respeitar a sequência definida na RN-02: `Pendente` → `Em Andamento` → `Concluída`.
- A data e a hora da atribuição devem ser registradas conforme a RN-01, por meio do atributo `data_atribuicao` em `ATRIBUICAO_TAREFA`.
- As alterações de status devem ser registradas em `HISTORICO_STATUS`, incluindo o status anterior, o novo status, a data e hora da mudança e o colaborador responsável, conforme a RN-04.
- O e-mail do colaborador é opcional e, quando informado, não pode ser repetido.
- O telefone é um atributo multivalorado, representado pela entidade `TELEFONE_COLABORADOR`.
- A prioridade é representada por uma entidade própria, relacionada à tarefa por meio de `id_prioridade`.

### 5. Características e decisões da estrutura de dados

O inventário considera as seguintes características para manter a organização e a integridade dos dados:

| Característica | Representação no projeto |
|---|---|
| Entidades principais | SETOR, COLABORADOR, PRIORIDADE e TAREFA |
| Entidade associativa | ATRIBUICAO_TAREFA |
| Entidades dependentes | TELEFONE_COLABORADOR, HISTORICO_STATUS e OBSERVACAO_TAREFA |
| Atributo multivalorado | Telefone do colaborador, representado por TELEFONE_COLABORADOR |
| Atributo derivado | `em_atraso`, calculado a partir do prazo e do status |
| Identificação de atribuição | `id_atribuicao`, permitindo preservar atribuições realizadas em momentos diferentes |
| Identificação do histórico | Chave primária composta por `codigo_tarefa` e `data_mudanca` |
| E-mail | Opcional (`NULL`) e único quando preenchido |
| Prioridade | Entidade independente relacionada à TAREFA |

### 6. Referência

LOPES, Edson Vaz. *Edital do Projeto --- Unidade 3: Requisitos, Modelagem e Análise de Dados*. 2026/2. Cenário Grupo 01 --- TaskFlow.
