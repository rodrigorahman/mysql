# Curso Mysql #

### Conectando no mysql pela linha de comando ###
```
mysql -u root -h localhost -p
```

**Descrição dos Parametros:**

* -u: Passando o nome do usuário

* -h: IP do servidor onde você gostaria de logar

* -p: Será solicitado o password


### Criando um novo usuário ###

```
create user usermysql@'%' identified by 'cursomysql';
```

**Descrição dos Parametros:**

* **usermysql**: Nome do usuário que será criado

* **%**: Indica que nosso usuário poderá acessar o banco a partir de qualquer host, caso queira limitar o acesso apenas para o host **localhost** basta substituir **%** por **localhost**

### Concedendo privilegios totais para o usuário ###

```
grant all privileges on *.* to usermysql@'%' with grant option;
```

**Concedendo permissão apenas para um banco de dados:**
```
grant all to comercial.* to usermysql@localhost
```

**Concedendo permissão apenas para uma tabela banco de dados:**
```
grant all on comercial.nome_da_tabela;
```

**Concedendo acesso a stored procedure**

***Routine está ligada a todas as ações que um usuário pode realizar em um storedProcedure***

```
grant routine on comercial.* to usermysql@localhost;
```

***Grant de execução em procedure***

```
grant execute on procedure comercial.nome_procedure to usermysql@localhost;
```


### Revogando acesso ao usuário ###
```
revoke all on *.* from usermysql;
```

**Revogando permissão apenas para um banco de dados:**
```
revoke all on comercial.*;
```

**Revogando permissão apenas para uma tabela do banco de dados:**
```
revoke all on comercial.nome_da_tabela;
```

# Querys #

### Funções###

***Concatenação***

>concateWS

A função acima faz a concatenação utilizando um caractere ex:

```
select concat_ws(";", "Rodrigo", "Luana")
```

Resultado:
>Rodrigo;Luana

***Date***

>datediff

Retorna a diferença em dias entre duas datas ex:

```
select datediff('2016-03-01', '2016-02-01')
```

Resultado:
>29 -- Dias

**Extraindo uma data**

>extract

```
select extract(year from '2016-11-19');
select extract(month from '2016-11-19');
select extract(day from '2016-11-19');

```

Resultado:
>2016
>11
>19


**Formatando datas**
>date_format

```
select DATE_FORMAT(now(), '%d/%m/%Y');
```


# Event Schedule #

Podemos criar eventos no mysql para ser executado de tempos em tempos.

ex:

```
delimiter $$
create event processar_comissao
  on schedule every 1 week starts '2016-11-01 23:38:00'
  do
    begin
      call processar_comissao();
    end
  $$
  delimiter;
```

Com o código acima será criado um evento para rodar uma vez por semana comecando no dia 01/11/2016 as 23:38:00

**Habilitando e desabilitando jobs**


```
alter event processar_comissao disable;
alter event processar_comissao enable;
```

# Triggers #

Criando uma trigger

>new: Para valores novos
>old: para valores antigos

```
delimiter $$
create trigger tri_vendas
  before insert on comvenda
  for each row

  begin

  end
$$
delimiter ;
```


# Mysql Avançado #

Existem diversas variaveis que são importantes para o mysql para visualiza-las utilize o comando abaixo:

```
show variables;
```

***Variaveis relacionadas a tabela***

```
show variables like '%table%';
```

***lower_case_table_names***
Esse valor define se o banco tratará case sensitive ou não abaixo os valores possíveis:
>0: case sentive
>1: case insensitive
>2: case insensitive


### Visualizando conexões ativas ###

```
show processlist;
```

***Matando conexões ativas***

```
kill numero_id;
```


### Exportando resultado para .csv ou .txt ###

Ex:

```
select * from tabelas_x
  into outfile '/path_exportacao/tabelax.txt'
  fields terminated by ','
  enclosed by '''';
```


### Importando dados de um txt ###
```
load data infile '/path_arquivo/arquivo.txt'
  into table tabelas_x
  fields terminated by ','
  enclosed by '''';
```

### Fazendo select no metadata do banco(information_schema) ###

Encontrando qual tabela que tem o campo X

```
select table_schema banco_dados,
  table_name tabela
from information_schema.columns
where table_schema = 'comercial' -- nome do banco de dados
and column_name = 'id_qualquercoisa';
```


***Criando variaveis para otimizar consultas***

Mudamos a query acima colocando duas variaveis @banco @coluna assim quando executarmos o banco pedirá esses valores.

```
set @banco = 'comercial';
set @coluna = 'cd_tabela';

select table_schema banco_dados,
  table_name tabela
from information_schema.columns
where table_schema = @banco
and column_name = @coluna;
```
