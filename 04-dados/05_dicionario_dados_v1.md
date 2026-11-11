# 05_dicionario_dados_v1.md — Dicionário de Dados v1

**Projeto:** TaskFlow - Controle de Tarefas Corporativas  
**Equipe:** Mariah, Laura, Elizama, Karla, Annanda e Caio  
**Responsável pelo documento:** Annanda  
**Versão:** 1.0  
**Data:** 06/11/2026  

---

## 1. Visão Geral do Domínio

O dicionário de dados descreve detalhadamente a estrutura lógica relacional da base de dados do **TaskFlow**, especificando os tipos de dados, tamanhos, restrições de integridade, aceitação de valores nulos e a função de cada atributo.

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
| `email` | VARCHAR | 150 | NOT NULL | UNIQUE | E-mail corporativo do colaborador |
| `id_setor` | INT | Auto | NOT NULL | FK (`SETOR.id_setor`) | Setor ao qual o colaborador está vinculado |

---

### 2.3. Tabela: `TAREFA`
Registra as tarefas criadas no TaskFlow, seus prazos, prioridades e status atual.

| Nome do Atributo | Tipo de Dado | Tamanho | Requisito / Nulo | Restrição / Chave | Descrição |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `codigo_tarefa` | INT | Auto | NOT NULL | PK, Auto Increment | Código único de identificação da tarefa |
| `descricao` | TEXT | - | NOT NULL | - | Detalhamento sobre o que precisa ser feito |
| `prioridade` | VARCHAR | 10 | NOT NULL | CHECK (`prioridade` IN ('Baixa', 'Média', 'Alta', 'Crítica')) | Nível de prioridade da tarefa |
| `prazo_limite` | DATETIME | - | NOT NULL | - | Data e hora limite para a conclusão |
| `status_atual` | VARCHAR | 20 | NOT NULL | DEFAULT 'Pendente', CHECK (`status_atual` IN ('Pendente', 'Em Andamento', 'Concluída')) | Status vigente da tarefa no fluxo |
| `data_criacao` | DATETIME | - | NOT NULL | DEFAULT CURRENT_TIMESTAMP | Registro de quando a tarefa foi cadastrada |

---

### 2.4. Tabela: `ATRIBUICAO_TAREFA` (Tabela Associativa N:N)
Resolve o relacionamento N:N entre `COLABORADOR` e `TAREFA`. Registra o histórico de quem foi responsável por qual tarefa e em qual período.

| Nome do Atributo | Tipo de Dado | Tamanho | Requisito / Nulo | Restrição / Chave | Descrição |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `id_atribuicao` | INT | Auto | NOT NULL | PK, Auto Increment | Identificador único do registro de atribuição |
| `codigo_tarefa` | INT | Auto | NOT NULL | FK (`TAREFA.codigo_tarefa`) | Código da tarefa atribuída |
| `matricula_colaborador` | INT | Auto | NOT NULL | FK (`COLABORADOR.matricula`) | Matrícula do colaborador responsável |
| `data_atribuicao` | DATETIME | - | NOT NULL | DEFAULT CURRENT_TIMESTAMP | Data e hora exatas em que a atribuição foi realizada |
| `papel_na_tarefa` | VARCHAR | 30 | NULL | DEFAULT 'Executor' | Função exercida pelo colaborador (ex: Executor, Revisor) |

---

### 2.5. Tabela: `HISTORICO_STATUS` (Entidade Fraca)
Registra cada mudança de status sofrida por uma tarefa para garantir o acompanhamento de fluxo sem pular etapas. Depende da existência da entidade `TAREFA`.

| Nome do Atributo | Tipo de Dado | Tamanho | Requisito / Nulo | Restrição / Chave | Descrição |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `id_historico` | INT | Auto | NOT NULL | PK, Auto Increment | Identificador único do histórico de transição |
| `codigo_tarefa` | INT | Auto | NOT NULL | FK (`TAREFA.codigo_tarefa`) | Código da tarefa monitorada |
| `status_anterior` | VARCHAR | 20 | NULL | CHECK (`status_anterior` IN ('Pendente', 'Em Andamento', 'Concluída')) | Status de onde a tarefa estava saindo |
| `status_novo` | VARCHAR | 20 | NOT NULL | CHECK (`status_novo` IN ('Pendente', 'Em Andamento', 'Concluída')) | Novo status assumido |
| `data_mudanca` | DATETIME | - | NOT NULL | DEFAULT CURRENT_TIMESTAMP | Data e hora em que a transição ocorreu |
| `matricula_responsavel` | INT | Auto | NOT NULL | FK (`COLABORADOR.matricula`) | Quem realizou a alteração de status |

---

### 2.6. Tabela: `OBSERVACAO_TAREFA`
Permite o registro de anotações ou comentários multivalorados associados a uma tarefa ao longo do tempo.

| Nome do Atributo | Tipo de Dado | Tamanho | Requisito / Nulo | Restrição / Chave | Descrição |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `id_observacao` | INT | Auto | NOT NULL | PK, Auto Increment | Identificador da observação |
| `codigo_tarefa` | INT | Auto | NOT NULL | FK (`TAREFA.codigo_tarefa`) | Tarefa vinculada |
| `texto_observacao` | TEXT | - | NOT NULL | - | Anotação sobre o andamento ou impeditivo da tarefa |
| `data_registro` | DATETIME | - | NOT NULL | DEFAULT CURRENT_TIMESTAMP | Data do comentário |

---

## 3. Legenda de Siglas e Convenções
* **PK (Primary Key):** Chave primária identificadora da tabela.
* **FK (Foreign Key):** Chave estrangeira que garante a integridade referencial com outra tabela.
* **CHECK:** Restrição de validação para garantir que os valores estejam dentro de um conjunto permitido.
* **NOT NULL:** Atributo de preenchimento obrigatório.
* **DEFAULT:** Valor inserido automaticamente caso nenhum seja informado.
