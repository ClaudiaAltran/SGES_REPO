# Regras de constru��o de sql

Regras de constru��o de ficheiros sql, pl/sql. 
Para todas as altera��es, deve-se criar um novo ficheiro, ou continuar o ultimo, pois os utilizadores que n�o v�em essa altera��o podem n�o ter em conta esses dados.
Esse ficheiro deve seguir a ordem anterior, para isso, deve-se efetuar o update. 
As ordens devem ser do tipo: 01, 02, 03, 04 (...), 10, 11, e o script deve ter como nome: <ordem>_<nome_descritivo_dado>

# Tabelas

Todas os nomes das tabelas come�am por uma abreviatura:
* **GEN** tabelas genericas
* **PRM** tabelas de parametros
* **EP** tabelas relacionadas com epis�dio
* **MAP** tabelas relacionado com mapeamento

## Cria��o de tabelas

Quase todas as tabelas cont�m: 

* **Id_<nome_tabela>** id do nome da tabela, este id � �nico, e chave prim�ria, mas  pode ser diferente nos diferentes ambientes (DEV,QUALIDADE e PRODUCAO).
* **update_date** data de atualizacao da linha da tabela, este dado � importante para gest�o de hist�rico
* **updated_by** nome do utilizador que alterou
* **created_date** data de cria��o
* **created_by** nome do utilizador que criou
* **state_id** 1- ativo, 2- desativo; Estes estados simulam o "eliminar", mas n�o s�o retirados da base de dados para efeitos de hist�rico

As tabelas de parametros contem:
* **codigo** codigo do valor, utilizado para pesquisas, e convem que seja unico. Como � sempre o mesmo nos ambientes, � este que � utilizado nas pesquisas.

### Ao criar uma tabela tamb�m se deve criar.

Ao criar uma nova tabela, ser� necess�rio criar novos objetos para fim de organizar melhor os dados e para efeitos de hist�rico:

* **tabela historico** - com o nome: *<nome_tabela>_HIST * , de igual constru��o que a tabela, mas como chaves primarias: o *id* e o *update_date *.
* **trigger historico** - com o nome *TRG_HT_<nome_tabela> * , para antes do update � tabela copiar todos os dados para a tabela de hist�rio
* **sequencia** - com o nome *SEQ_<nome_tabela>, utilizado para inserir novos valores na tabela, o id da tabela deve sempre seguir a ordem da sequencia.

Ao criar o trigger ou procedures, deve-se colocar no fim: END;/ para os comandos seguintes continuarem.

### Adicionar chaves estrangeiras

O nome da chave estrangeira de ter como nome, algo preceptivel que nos indique que tabelas essa "constraint" relaciona. Como por exemplo,
* **<nome_tabela>_<nome_tabela_estangeira>_FK**- O nome das tabelas deve ser reduzido de forma a ter no m�ximo 30 caracteres.

## Altera��o das tabelas

### Adicionar e remover colunas

Ao adicionar e remover as colunas, � necess�rio: 

* verificar se estas n�o interferem com outras tabelas
* remover/adicionar o nome ao trigger.

## Adicionar dados
Ao adicionar dados preencher as linhas sempre com estes dados:

* **Id_<nome_tabela>** SEQ_<nome_tabela>.nextval
* **update_date** sysdate
* **updated_by** nome da origem, por exemplo: migracao_webgdh, instalacao ,migracao.sges, etc;
* **created_date** sysdate
* **created_by** nome da origem, por exemplo: migracao_webgdh, instalacao ,migracao.sges, etc;
* **state_id** 1- ativo, 2- desativo; o desativo pode ser utilizado aqui, mas em raros casos.

Se houver codigo, tamb�m se deve preencher esse campo. Se n�o houver dados para preencher esse codigo, usar: 100, 200, 300, 400 (ou de 10 em 10).

Ao adicionar os dados � necess�rio colocar COMMIT; no fim.