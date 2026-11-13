Aqui está o conteúdo completo e atualizado. É só copiar e colar em um arquivo `.md` no VS Code:

````markdown
# TaskFlow – Decisões do Modelo Conceitual e Lógico

## 1. Entidades do Sistema

Para atender ao escopo mínimo de cinco entidades no modelo conceitual, desconsiderando a tabela associativa, o domínio do TaskFlow é representado pelas cinco entidades/tabelas principais descritas no Dicionário de Dados v1: **SETOR**, **COLABORADOR**, **TAREFA**, **HISTORICO_STATUS** e **OBSERVACAO_TAREFA**. A tabela **ATRIBUICAO_TAREFA** é tratada separadamente por ser associativa.

- **Colaborador (Entidade Forte):** entidade principal para o cadastro dos funcionários. Sua identificação é feita por `matricula`, chave primária com incremento automático. Possui nome, e-mail e vínculo obrigatório com um setor.
- **Tarefa (Entidade Forte):** entidade principal que armazena os registros das atividades do sistema, identificada por `codigo_tarefa`. Contém descrição, prioridade, prazo limite, status atual e data de criação.
- **Setor (Entidade Forte):** representa os setores ou departamentos da empresa, como TI, RH e Financeiro. É identificado por `id_setor` e possui `nome_setor` e `sigla`, ambos obrigatórios e com restrição `UNIQUE`. Permite vincular os colaboradores aos seus respectivos setores.
- **Histórico de Status (Entidade Dependente / Fraca):** registra as mudanças de status de cada tarefa, permitindo acompanhar sua evolução. No modelo lógico, possui `id_historico` como chave primária e `codigo_tarefa` como chave estrangeira para TAREFA.
- **Observação da Tarefa (Entidade Dependente / Fraca):** armazena anotações e comentários associados a uma tarefa ao longo do tempo. É identificada por `id_observacao` e possui `codigo_tarefa` como chave estrangeira, além de `texto_observacao` e `data_registro`, ambos obrigatórios (`data_registro` com valor padrão `CURRENT_TIMESTAMP`).

**Decisão sobre PRIORIDADE:** no Dicionário de Dados v1, PRIORIDADE não é uma entidade independente. Ela é um atributo obrigatório da entidade TAREFA, armazenado em `prioridade`, com valores permitidos Baixa, Média, Alta e Crítica, controlados por uma restrição `CHECK`.

## 2. Identificação de Entidades Dependentes (Fracas)

Neste documento, os termos "dependente" e "fraca" são usados como sinônimos, em correspondência com o Dicionário de Dados v1, que classifica `HISTORICO_STATUS` como Entidade Fraca.

- **Histórico de Status:** é dependente da entidade TAREFA, pois um registro de mudança de status está relacionado a uma tarefa específica e não tem significado no contexto do sistema sem esse vínculo. No modelo lógico definido pelo dicionário, `id_historico` é a chave primária simples, com incremento automático, enquanto `codigo_tarefa` é uma chave estrangeira que referencia `TAREFA.codigo_tarefa`. Portanto, a chave primária não é composta.
- **Observação da Tarefa:** também é dependente de TAREFA, pois cada observação pertence a uma tarefa. No modelo lógico, `id_observacao` é a chave primária simples e `codigo_tarefa` é a chave estrangeira que estabelece o vínculo. O dicionário descreve as anotações como "multivalorados" no sentido de que uma tarefa pode ter várias observações ao longo do tempo; no modelo, isso é resolvido por uma entidade dependente própria (relação 1:N com TAREFA), e não por um atributo multivalorado de TAREFA.

## 3. Atributos Especiais (Multivalorados e Derivados)

- **Atributo Multivalorado — Telefone:** o documento de decisões original considerava telefone como atributo multivalorado de COLABORADOR, pois uma pessoa poderia possuir mais de um contato. Entretanto, o Dicionário de Dados v1 não apresenta o atributo TELEFONE nem uma tabela específica para armazenar telefones. Para manter a coerência entre os documentos, telefone não faz parte da estrutura lógica atualmente documentada. Sua inclusão dependerá de decisão da equipe e de atualização prévia do dicionário.
- **Atributo Derivado — Status de Atraso:** a decisão original considerava um indicador calculado a partir do prazo final e do status da tarefa: a tarefa estaria atrasada quando a data atual ultrapassasse `prazo_limite` e o `status_atual` fosse diferente de Concluída. O Dicionário de Dados v1 contém `prazo_limite` e `status_atual`, mas não define formalmente esse atributo derivado nem sua regra de cálculo. Assim, a regra é mantida neste documento como uma proposta/decisão conceitual a validar, não como coluna armazenada na tabela TAREFA.

## 4. Resolução do Relacionamento N:N (Muitos para Muitos)

- **Relacionamento Colaborador x Tarefa:** uma tarefa pode ser atribuída a colaboradores, e um colaborador pode receber várias tarefas. Esse relacionamento muitos-para-muitos (N:N) é resolvido pela tabela associativa ATRIBUICAO_TAREFA.
- **Resolução técnico-relacional:** conforme o Dicionário de Dados v1, ATRIBUICAO_TAREFA possui `id_atribuicao` como chave primária simples, auto incremento. Os campos `codigo_tarefa` e `matricula_colaborador` são chaves estrangeiras para TAREFA e COLABORADOR, respectivamente; eles não compõem a chave primária. A tabela também possui `data_atribuicao`, obrigatória, com valor padrão `CURRENT_TIMESTAMP`, e `papel_na_tarefa`, opcional, com valor padrão Executor.

A utilização de `id_atribuicao` permite identificar cada registro de atribuição individualmente. Dessa forma, podem ser registrados diferentes eventos de atribuição envolvendo o mesmo colaborador e a mesma tarefa, em datas distintas.

## 5. Demais Relacionamentos e Regras de Integridade

- **Setor x Colaborador:** `COLABORADOR.id_setor` é chave estrangeira que referencia `SETOR.id_setor`. O vínculo do colaborador com o setor é obrigatório.
- **Tarefa x Histórico de Status:** `HISTORICO_STATUS.codigo_tarefa` referencia `TAREFA.codigo_tarefa`. Cada registro do histórico também identifica o colaborador responsável pela alteração por meio de `matricula_responsavel`, chave estrangeira obrigatória para COLABORADOR.
- **Tarefa x Observação:** `OBSERVACAO_TAREFA.codigo_tarefa` referencia `TAREFA.codigo_tarefa`, vinculando cada comentário à tarefa correspondente.
- **E-mail do colaborador:** `COLABORADOR.email` é obrigatório e possui restrição `UNIQUE`, garantindo que não haja e-mails repetidos.
- **Prioridade e status:** `TAREFA.prioridade` aceita Baixa, Média, Alta e Crítica. `TAREFA.status_atual` aceita Pendente, Em Andamento e Concluída e possui valor padrão Pendente.
- **Histórico:** `status_anterior` pode ser nulo; `status_novo`, `data_mudanca` e `matricula_responsavel` são obrigatórios, conforme o dicionário. `data_mudanca` possui valor padrão `CURRENT_TIMESTAMP`. Tanto `status_anterior` quanto `status_novo` são controlados por `CHECK`, aceitando apenas Pendente, Em Andamento e Concluída, os mesmos valores de `TAREFA.status_atual`.

## 6. Resumo das Chaves do Modelo Lógico

| Tabela | Chave primária (PK) | Chaves estrangeiras (FK) |
|---|---|---|
| SETOR | `id_setor` | — |
| COLABORADOR | `matricula` | `id_setor` → `SETOR.id_setor` |
| TAREFA | `codigo_tarefa` | — |
| ATRIBUICAO_TAREFA | `id_atribuicao` | `codigo_tarefa` → `TAREFA.codigo_tarefa`; `matricula_colaborador` → `COLABORADOR.matricula` |
| HISTORICO_STATUS | `id_historico` | `codigo_tarefa` → `TAREFA.codigo_tarefa`; `matricula_responsavel` → `COLABORADOR.matricula` |
| OBSERVACAO_TAREFA | `id_observacao` | `codigo_tarefa` → `TAREFA.codigo_tarefa` |

## 7. Considerações Finais

As decisões deste documento foram revisadas para manter correspondência com o Dicionário de Dados v1. Foram preservados os temas do documento original — entidades, entidades dependentes, atributos especiais e relacionamento N:N — e acrescentadas as decisões necessárias para contemplar as tabelas e restrições descritas no dicionário.

Quando uma ideia presente no documento original não está representada no Dicionário de Dados v1, como TELEFONE multivalorado e o indicador derivado de atraso, ela foi mantida como ponto a validar, sem ser apresentada como estrutura já implementada no modelo lógico. Alterações futuras deverão ser refletidas nos dois documentos.
````

Para ver a prévia formatada no VS Code, use `Ctrl+Shift+V` (ou `Cmd+Shift+V` no Mac).
