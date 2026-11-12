[05_dicionario_dados_atualizado_v1.md](https://github.com/user-attachments/files/34724227/05_dicionario_dados_atualizado_v1.md)

# 05_dicionario_dados_v1.md — Dicionário de Dados v1

**Projeto:** TaskFlow - Controle de Tarefas Corporativas  
**Equipe:** Mariah, Laura, Elisama, Karla, Annanda e Caio  
**Responsável pelo documento:** Annanda  
**Versão:** 1.0  
**Data:** 11/11/2026  

---

## 1. Visão Geral do Domínio

O dicionário de dados descreve detalhadamente a estrutura lógica relacional da base de dados do **TaskFlow**, especificando os tipos de dados, tamanhos, restrições de integridade, aceitação de valores nulos e a função de cada atributo.

Esta versão incorpora os ajustes decorrentes da revisão da equipe: criação das tabelas `PRIORIDADE` e `TELEFONE_COLABORADOR`, adoção de chave primária composta em `HISTORICO_STATUS` e revisão da obrigatoriedade do e-mail.

---

## 2. Tabelas do Sistema

### 2.1. Tabela: `SETOR`
Armazena os setores ou departamentos da organização aos quais os colaboradores pertencem.

| Nome do Atributo | Tipo de Dado | Tamanho | Requisito / Nulo | Restrição / Chave | Descrição |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `id_setor` | INT | Auto | NOT NULL | PK, Auto Increment | Identificador único do setor |
| `nome_setor` | VARCHAR | 100 | NOT NULL | UNIQUE | Nome formal do setor/departamento (ex: TI, RH, Financeiro) |
| `sigla` | VARCHAR | 10 | NOT NULL | UNIQUE | Sigla representativa do setor |

---

### 2.2. Tabela: `COLABORADOR`
Cadastra os colaboradores que realizam ou gerenciam as tarefas no sistema.

| Nome do Atributo | Tipo de Dado | Tamanho | Requisito / Nulo | Restrição / Chave | Descrição |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `matricula` | INT | Auto | NOT NULL | PK, Auto Increment | Matrícula única de identificação do colaborador |
| `nome` | VARCHAR | 150 | NOT NULL | - | Nome completo do colaborador |
| `email` | VARCHAR | 150 | NULL | UNIQUE | E-mail corporativo do colaborador (**opcional**) |
| `id_setor` | INT | - | NOT NULL | FK (`SETOR.id_setor`) | Setor ao qual o colaborador está vinculado |

> **Alteração v1:** `email` passou de `NOT NULL` para opcional (`NULL`), alinhando o dicionário ao inventário de dados. O `UNIQUE` foi mantido e só se aplica quando o e-mail é preenchido. *(Decisão a ser confirmada pela equipe, ver seção 4.)*

---

### 2.3. Tabela: `TELEFONE_COLABORADOR` *(nova)*
Representa o atributo multivalorado **Telefone** do colaborador, permitindo que cada colaborador tenha vários números cadastrados.

| Nome do Atributo | Tipo de Dado | Tamanho | Requisito / Nulo | Restrição / Chave | Descrição |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `matricula_colaborador` | INT | - | NOT NULL | PK (composta), FK (`COLABORADOR.matricula`) | Matrícula do colaborador dono do telefone |
| `numero_telefone` | VARCHAR | 20 | NOT NULL | PK (composta) | Número de telefone com DDD |

**Chave primária composta:** (`matricula_colaborador`, `numero_telefone`).  
**Cardinalidade:** um colaborador possui de 0 a N telefones; cada telefone pertence a um único colaborador.

---

### 2.4. Tabela: `PRIORIDADE` *(nova)*
Representa **Prioridade** como entidade própria, centralizando em um único lugar a lista de níveis de prioridade aplicáveis às tarefas.

| Nome do Atributo | Tipo de Dado | Tamanho | Requisito / Nulo | Restrição / Chave | Descrição |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `id_prioridade` | INT | Auto | NOT NULL | PK, Auto Increment | Identificador único da prioridade |
| `nome_prioridade` | VARCHAR | 10 | NOT NULL | UNIQUE | Nome do nível de prioridade |
| `descricao` | VARCHAR | 100 | NULL | - | Descrição do critério de uso da prioridade |

**Valores iniciais propostos** *(lista única, a ser confirmada pela equipe)*:

| id_prioridade | nome_prioridade |
| :--- | :--- |
| 1 | Baixa |
| 2 | Média |
| 3 | Alta |
| 4 | Crítica |

> Por ficarem em tabela, os níveis podem ser alterados (por exemplo, reduzidos a três) sem mudar a estrutura de `TAREFA`.

---

### 2.5. Tabela: `TAREFA`
Registra as tarefas criadas no TaskFlow, seus prazos, prioridades e status atual.

| Nome do Atributo | Tipo de Dado | Tamanho | Requisito / Nulo | Restrição / Chave | Descrição |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `codigo_tarefa` | INT | Auto | NOT NULL | PK, Auto Increment | Código único de identificação da tarefa |
| `descricao` | TEXT | - | NOT NULL | - | Detalhamento sobre o que precisa ser feito |
| `id_prioridade` | INT | - | NOT NULL | FK (`PRIORIDADE.id_prioridade`) | Prioridade da tarefa |
| `prazo_limite` | DATETIME | - | NOT NULL | - | Data e hora limite para a conclusão |
| `status_atual` | VARCHAR | 20 | NOT NULL | DEFAULT 'Pendente', CHECK (`status_atual` IN ('Pendente', 'Em Andamento', 'Concluída')) | Status vigente da tarefa no fluxo |
| `data_criacao` | DATETIME | - | NOT NULL | DEFAULT CURRENT_TIMESTAMP | Registro de quando a tarefa foi cadastrada |

> **Alteração v1:** o campo textual `prioridade` (VARCHAR) foi substituído por `id_prioridade`, chave estrangeira para a tabela `PRIORIDADE`.

---

### 2.6. Tabela: `ATRIBUICAO_TAREFA` (Tabela Associativa N:N)
Resolve o relacionamento N:N entre `COLABORADOR` e `TAREFA`. Registra o histórico de quem foi responsável por qual tarefa e em qual período.

| Nome do Atributo | Tipo de Dado | Tamanho | Requisito / Nulo | Restrição / Chave | Descrição |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `id_atribuicao` | INT | Auto | NOT NULL | PK, Auto Increment | Identificador único do registro de atribuição |
| `codigo_tarefa` | INT | - | NOT NULL | FK (`TAREFA.codigo_tarefa`) | Código da tarefa atribuída |
| `matricula_colaborador` | INT | - | NOT NULL | FK (`COLABORADOR.matricula`) | Matrícula do colaborador responsável |
| `data_atribuicao` | DATETIME | - | NOT NULL | DEFAULT CURRENT_TIMESTAMP | Data e hora exatas em que a atribuição foi realizada |
| `papel_na_tarefa` | VARCHAR | 30 | NULL | DEFAULT 'Executor' | Função exercida pelo colaborador (ex: Executor, Revisor) |

**Justificativa da chave:** o uso de `id_atribuicao` como PK (e não de uma chave composta por tarefa e colaborador) permite que a **mesma tarefa seja atribuída novamente ao mesmo colaborador em outra data**, preservando cada atribuição como um registro distinto.

---

### 2.7. Tabela: `HISTORICO_STATUS` (Entidade Fraca)
Registra cada mudança de status sofrida por uma tarefa para garantir o acompanhamento de fluxo sem pular etapas. Depende da existência da entidade `TAREFA`.

| Nome do Atributo | Tipo de Dado | Tamanho | Requisito / Nulo | Restrição / Chave | Descrição |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `codigo_tarefa` | INT | - | NOT NULL | PK (composta), FK (`TAREFA.codigo_tarefa`) | Código da tarefa monitorada |
| `data_mudanca` | DATETIME | - | NOT NULL | PK (composta), DEFAULT CURRENT_TIMESTAMP | Data e hora em que a transição ocorreu |
| `status_anterior` | VARCHAR | 20 | NULL | CHECK (`status_anterior` IN ('Pendente', 'Em Andamento', 'Concluída')) | Status de onde a tarefa estava saindo |
| `status_novo` | VARCHAR | 20 | NOT NULL | CHECK (`status_novo` IN ('Pendente', 'Em Andamento', 'Concluída')) | Novo status assumido |
| `matricula_responsavel` | INT | - | NOT NULL | FK (`COLABORADOR.matricula`) | Quem realizou a alteração de status |

**Chave primária composta:** (`codigo_tarefa`, `data_mudanca`). Como a entidade é fraca, sua identificação depende da tarefa à qual pertence somada ao momento da mudança.

> **Alteração v1:** a PK `id_historico` foi removida e substituída pela chave composta acima, alinhando o dicionário à proposta da Elizama. O campo `data_mudanca` deve guardar data **e hora**, para que duas mudanças da mesma tarefa no mesmo dia não colidam.
>
> **Autoria:** `matricula_responsavel` permanece obrigatório, isto é, toda alteração de status tem um colaborador responsável. O modelo conceitual deve ser ajustado para refletir isso. *(Decisão a ser confirmada com a Laura, ver seção 4.)*

---

### 2.8. Tabela: `OBSERVACAO_TAREFA` *(condicional)*
Permite o registro de anotações ou comentários multivalorados associados a uma tarefa ao longo do tempo.

| Nome do Atributo | Tipo de Dado | Tamanho | Requisito / Nulo | Restrição / Chave | Descrição |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `id_observacao` | INT | Auto | NOT NULL | PK, Auto Increment | Identificador da observação |
| `codigo_tarefa` | INT | - | NOT NULL | FK (`TAREFA.codigo_tarefa`) | Tarefa vinculada |
| `texto_observacao` | TEXT | - | NOT NULL | - | Anotação sobre o andamento ou impeditivo da tarefa |
| `data_registro` | DATETIME | - | NOT NULL | DEFAULT CURRENT_TIMESTAMP | Data do comentário |

> **Atenção:** esta tabela só permanece no dicionário se a equipe aprovar sua existência. Se for mantida, a entidade *Observação* deve ser incluída no modelo conceitual e sua existência justificada nas decisões de modelagem. Se a equipe decidir removê-la, basta excluir esta seção. *(Ver seção 4.)*

---

## 3. Relacionamentos e Integridade Referencial

| Tabela filha | Chave estrangeira | Tabela pai | Cardinalidade (pai : filha) |
| :--- | :--- | :--- | :--- |
| `COLABORADOR` | `id_setor` | `SETOR` | 1 : N |
| `TELEFONE_COLABORADOR` | `matricula_colaborador` | `COLABORADOR` | 1 : N |
| `TAREFA` | `id_prioridade` | `PRIORIDADE` | 1 : N |
| `ATRIBUICAO_TAREFA` | `codigo_tarefa` | `TAREFA` | 1 : N |
| `ATRIBUICAO_TAREFA` | `matricula_colaborador` | `COLABORADOR` | 1 : N |
| `HISTORICO_STATUS` | `codigo_tarefa` | `TAREFA` | 1 : N |
| `HISTORICO_STATUS` | `matricula_responsavel` | `COLABORADOR` | 1 : N |
| `OBSERVACAO_TAREFA` *(condicional)* | `codigo_tarefa` | `TAREFA` | 1 : N |

O relacionamento N:N entre `TAREFA` e `COLABORADOR` é resolvido pela tabela associativa `ATRIBUICAO_TAREFA`.

---

## 4. Decisões e Pendências de Alinhamento com a Equipe

Cada ponto abaixo precisa estar igual no inventário, no modelo conceitual, no modelo lógico e neste dicionário.

| # | Tema | Posição adotada nesta versão | Alinhar com | Status |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Prioridade como entidade | Tabela `PRIORIDADE` + FK `id_prioridade` em `TAREFA` | Elizama e Laura | ☐ Pendente |
| 2 | Lista de prioridades | Quatro níveis (Baixa, Média, Alta, Crítica); o documento do Caio lista apenas três, e é preciso adotar uma única lista em todos os arquivos | Caio e equipe | ☐ Pendente |
| 3 | Telefone multivalorado | Tabela `TELEFONE_COLABORADOR` | Elizama | ☐ Pendente |
| 4 | PK de `HISTORICO_STATUS` | Composta: `codigo_tarefa` + `data_mudanca` | Elizama | ☐ Pendente |
| 5 | PK de `ATRIBUICAO_TAREFA` | Mantido `id_atribuicao`; o documento de decisões da Elizama deve acompanhar essa escolha | Elizama | ☐ Pendente |
| 6 | E-mail | Opcional (`NULL`), conforme o inventário | Equipe | ☐ Pendente |
| 7 | Responsável pelo histórico | Obrigatório (`NOT NULL`); o modelo conceitual deve ser ajustado | Laura | ☐ Pendente |
| 8 | `OBSERVACAO_TAREFA` | Mantida como tabela condicional; exige entidade no modelo conceitual e justificativa | Equipe | ☐ Pendente |
| 9 | Conferência de PKs e FKs | Verificar todas as chaves e garantir estruturas idênticas às do modelo lógico | Karla | ☐ Pendente |

---

## 5. Legenda de Siglas e Convenções
* **PK (Primary Key):** Chave primária identificadora da tabela.
* **FK (Foreign Key):** Chave estrangeira que garante a integridade referencial com outra tabela.
* **CHECK:** Restrição de validação para garantir que os valores estejam dentro de um conjunto permitido.
* **NOT NULL:** Atributo de preenchimento obrigatório.
* **NULL:** Atributo de preenchimento opcional.
* **DEFAULT:** Valor inserido automaticamente caso nenhum seja informado.
* **UNIQUE:** Restrição que impede valores repetidos na coluna.

---

## 6. Histórico de Alterações

| Versão | Alterações |
| :--- | :--- |
| v0 | Dicionário inicial com seis tabelas: `SETOR`, `COLABORADOR`, `TAREFA`, `ATRIBUICAO_TAREFA`, `HISTORICO_STATUS` e `OBSERVACAO_TAREFA` |
| v1 | Criação de `PRIORIDADE` e `TELEFONE_COLABORADOR`; `TAREFA.prioridade` substituída por `id_prioridade` (FK); PK de `HISTORICO_STATUS` alterada para chave composta; `COLABORADOR.email` passou a ser opcional; `OBSERVACAO_TAREFA` marcada como condicional; inclusão das seções de relacionamentos e de decisões pendentes |
