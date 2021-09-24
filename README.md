# HW-6.4-PostgreSQL

##  Задача 1

Подготовка образа

    C:\Users\Lenovo>cd c:/oleg
    
    c:\Oleg>docker ps -a
    CONTAINER ID   IMAGE                    COMMAND                  CREATED        STATUS                    PORTS                                       NAMES
    2a212bfab9b3   mysql                    "docker-entrypoint.s…"   19 hours ago   Up 19 hours               3306/tcp, 33060/tcp                         oleg-mysql
    23e96795e959   postgres                 "docker-entrypoint.s…"   5 days ago     Up 5 days                 0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   pg-test1
    06382f9d4a43   postgres                 "docker-entrypoint.s…"   5 days ago     Exited (0) 5 days ago                                                 pg-test
    0bb3ac6f8d19   test                     "/usr/bin/ponysay /b…"   10 days ago    Exited (0) 10 days ago                                                pony1
    ba7167c6c0d3   archlinux                "/usr/bin/bash"          12 days ago    Exited (255) 9 days ago                                               arch
    64327697e578   docker/getting-started   "/docker-entrypoint.…"   12 days ago    Exited (255) 9 days ago   0.0.0.0:80->80/tcp, :::80->80/tcp           musing_boyd
    
    c:\Oleg>docker exec -it pg-test1 psql -U oleg -d test_db
    psql (13.4 (Debian 13.4-1.pgdg100+1))
    Type "help" for help.
    
    test_db=#


вывода списка БД

     \l[+]   [PATTERN]      list databases
    
    
    test_db=#  \l
                                List of databases
       Name    | Owner | Encoding |  Collate   |   Ctype    | Access privileges
    -----------+-------+----------+------------+------------+-------------------
     postgres  | oleg  | UTF8     | en_US.utf8 | en_US.utf8 |
     template0 | oleg  | UTF8     | en_US.utf8 | en_US.utf8 | =c/oleg          +
               |       |          |            |            | oleg=CTc/oleg
     template1 | oleg  | UTF8     | en_US.utf8 | en_US.utf8 | =c/oleg          +
               |       |          |            |            | oleg=CTc/oleg
     test_db   | oleg  | UTF8     | en_US.utf8 | en_US.utf8 |
    (4 rows)
  
подключения к БД
  
    Connection
      \c[onnect] {[DBNAME|- USER|- HOST|- PORT|-] | conninfo}
                             connect to new database (currently "test_db")

вывода списка таблиц

    \dt[S+] [PATTERN]      list tables

    test_db=# \dt
             List of relations
     Schema |   Name   | Type  | Owner
    --------+----------+-------+-------
     public | clients  | table | oleg
     public | clients1 | table | oleg
     public | orders   | table | oleg
    (3 rows)

вывода описания содержимого таблиц

    \dp     [PATTERN]      list table, view, and sequence access privileges
    
    test_db=# \dp
                                       Access privileges
     Schema |      Name       |   Type   | Access privileges | Column privileges | Policies
    --------+-----------------+----------+-------------------+-------------------+----------
     public | clients         | table    |                   |                   |
     public | clients1        | table    |                   |                   |
     public | clients1_id_seq | sequence |                   |                   |
     public | clients_id_seq  | sequence |                   |                   |
     public | orders          | table    |                   |                   |
     public | orders_id_seq   | sequence |                   |                   |
    (6 rows)
    
    test_db=#
    
выхода из psql

     \q                     quit psql

##  Задача 2

    test_db=# create database test_database;
    CREATE DATABASE
    test_db=#  \l
                                  List of databases
         Name      | Owner | Encoding |  Collate   |   Ctype    | Access privileges
    ---------------+-------+----------+------------+------------+-------------------
     postgres      | oleg  | UTF8     | en_US.utf8 | en_US.utf8 |
      emplate0     | oleg  | UTF8     | en_US.utf8 | en_US.utf8 | =c/oleg          +
                   |       |          |            |            | oleg=CTc/oleg
     template1     | oleg  | UTF8     | en_US.utf8 | en_US.utf8 | =c/oleg          +
                   |       |          |            |            | oleg=CTc/oleg
     test_database | oleg  | UTF8     | en_US.utf8 | en_US.utf8 |
     test_db       | oleg  | UTF8     | en_US.utf8 | en_US.utf8 |
    (5 rows)

    
    test_db=# \c test_database;
    You are now connected to database "test_database" as user "oleg".
    test_database=#
    
    test_database=#
    test_database=# \dt
    Did not find any relations.
    test_database=# \dp
                                Access privileges
     Schema | Name | Type | Access privileges | Column privileges | Policies
    --------+------+------+-------------------+-------------------+----------
    (0 rows)
    
    test_database=#


восстанавливаем из бекапа, используя вольюм для бекапа с хостовой машины
создавался при запуске контейнера

    -v c:\oleg\backup:/etc/postgresql/
    
    psql -U oleg test_database < test_dump.sql
    
    root@23e96795e959:/etc/postgresql# dir
    test_db.dump  test_dump.sql  test_dump.sql.txt
    root@23e96795e959:/etc/postgresql# psql -U oleg test_database < test_dump.sql
    SET
    SET
    SET
    SET
    SET
     set_config
    ------------
    
    (1 row)
    
    SET
    SET
    SET
    SET
    SET
    SET
    CREATE TABLE
    ERROR:  role "postgres" does not exist
    CREATE SEQUENCE
    ERROR:  role "postgres" does not exist
    ALTER SEQUENCE
    ALTER TABLE
    COPY 8
     setval
    --------
          8
    (1 row)
    
    ALTER TABLE
    root@23e96795e959:/etc/postgresql#
    root@23e96795e959:/etc/postgresql#

Проверяем ДБ

    test_database=# \dt
           List of relations
     Schema |  Name  | Type  | Owner
    --------+--------+-------+-------
     public | orders | table | oleg
    (1 row)
    
    test_database=# \dp
                                     Access privileges
     Schema |     Name      |   Type   | Access privileges | Column privileges | Policies
    --------+---------------+----------+-------------------+-------------------+----------
     public | orders        | table    |                   |                   |
     public | orders_id_seq | sequence |                   |                   |
    (2 rows)
    
    test_database=# select * from orders;
     id |        title         | price
    ----+----------------------+-------
      1 | War and peace        |   100
      2 | My little database   |   500
      3 | Adventure psql time  |   300
      4 | Server gravity falls |   300
      5 | Log gossips          |   123
      6 | WAL never lies       |   900
      7 | Me and my bash-pet   |   499
      8 | Dbiezdmin            |   501
    (8 rows)
    
    test_database=#

операци ANALYZE для сбора статистики по таблице.

    test_database=# analyze verbose orders;
    INFO:  analyzing "public.orders"
    INFO:  "orders": scanned 1 of 1 pages, containing 8 live rows and 0 dead rows; 8 rows in sample, 8 estimated total rows
    ANALYZE
    test_database=#

 столбец таблицы orders с наибольшим средним значением размера элементов в байтах.

Через два запроса:

    test_database=# select max(avg_width) from pg_stats where tablename = 'orders';
    -[ RECORD 1 ]
    max | 16

    test_database=#
    select * from pg_stats where tablename = 'orders' and avg_width = 16;
    -[ RECORD 1 ]----------+--------------------------------------------------------------------------------------------------------------------------------------------------
    schemaname             | public
    tablename              | orders
    attname                | title
    inherited              | f
    null_frac              | 0
    avg_width              | 16
    n_distinct             | -1
    most_common_vals       |
    most_common_freqs      |
    histogram_bounds       | {"Adventure psql time",Dbiezdmin,"Log gossips","Me and my bash-pet","My little database","Server gravity falls","WAL never lies","War and peace"}
    correlation            | -0.3809524
    most_common_elems      |
    most_common_elem_freqs |
    elem_count_histogram   |


Или через один

    test_database=# select * from pg_stats where tablename = 'orders' and avg_width = (select max(avg_width) from pg_stats where tablename = 'orders');
    -[ RECORD 1 ]----------+--------------------------------------------------------------------------------------------------------------------------------------------------
    schemaname             | public
    tablename              | orders
    attname                | title
    inherited              | f
    null_frac              | 0
    avg_width              | 16
    n_distinct             | -1
    most_common_vals       |
    most_common_freqs      |
    histogram_bounds       | {"Adventure psql time",Dbiezdmin,"Log gossips","Me and my bash-pet","My little database","Server gravity falls","WAL never lies","War and peace"}
    correlation            | -0.3809524
    most_common_elems      |
    most_common_elem_freqs |
    elem_count_histogram   |
    
    test_database=#

##  Задача 3

Предложите SQL-транзакцию для проведения данной операции.

    begin;
    
    create table orders_1 ( check (price > 499)) inherit (orders);
    
    
    create table orders_2 ( check (price <= 499)) inherit (orders);
    
    commit;


Можно ли было изначально исключить "ручное" разбиение при проектировании таблицы orders?

Можно было при создании таблицы наложить необходимые ограничения.

## Задача 4


    pg_dump -U oleg test_database > test_database.dump

выполнен бекап, файл бекапа:

    --------------------------------------------
    --
    -- PostgreSQL database dump
    --
    
    -- Dumped from database version 13.4 (Debian 13.4-1.pgdg100+1)
    -- Dumped by pg_dump version 13.4 (Debian 13.4-1.pgdg100+1)
    
    SET statement_timeout = 0;
    SET lock_timeout = 0;
    SET idle_in_transaction_session_timeout = 0;
    SET client_encoding = 'UTF8';
    SET standard_conforming_strings = on;
    SELECT pg_catalog.set_config('search_path', '', false);
    SET check_function_bodies = false;
    SET xmloption = content;
    SET client_min_messages = warning;
    SET row_security = off;
    
    SET default_tablespace = '';
    
    SET default_table_access_method = heap;
    
    --
    -- Name: orders; Type: TABLE; Schema: public; Owner: oleg
    --
    
    CREATE TABLE public.orders (
       id integer NOT NULL,
       title character varying(80) NOT NULL,
       price integer DEFAULT 0
    );
    
    
    ALTER TABLE public.orders OWNER TO oleg;
    
    --
    -- Name: orders_id_seq; Type: SEQUENCE; Schema: public; Owner: oleg
    --
    
    CREATE SEQUENCE public.orders_id_seq
        AS integer
        START WITH 1
        INCREMENT BY 1
        NO MINVALUE
        NO MAXVALUE
        CACHE 1;
    
    
    ALTER TABLE public.orders_id_seq OWNER TO oleg;
    
    --
    -- Name: orders_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: oleg
    --
    
    ALTER SEQUENCE public.orders_id_seq OWNED BY public.orders.id;
    
    
    --
    -- Name: orders id; Type: DEFAULT; Schema: public; Owner: oleg
    --
    
    ALTER TABLE ONLY public.orders ALTER COLUMN id SET DEFAULT nextval('public.orders_id_seq'::regclass);
    
    
    --
    -- Data for Name: orders; Type: TABLE DATA; Schema: public; Owner: oleg
    --
    
    COPY public.orders (id, title, price) FROM stdin;
    1	War and peace	100
    2	My little database	500
    3	Adventure psql time	300
    4	Server gravity falls	300
    5	Log gossips	123
    6	WAL never lies	900
    7	Me and my bash-pet	499
    8	Dbiezdmin	501
    \.
    
    
    --
    -- Name: orders_id_seq; Type: SEQUENCE SET; Schema: public; Owner: oleg
    --
    
    SELECT pg_catalog.setval('public.orders_id_seq', 8, true);
    
    
    --
    -- Name: orders orders_pkey; Type: CONSTRAINT; Schema: public; Owner: oleg
    --
    
    ALTER TABLE ONLY public.orders
       ADD CONSTRAINT orders_pkey PRIMARY KEY (id);
    
    
    --
    -- PostgreSQL database dump complete
    --
    
    --------------------------------------------
Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца title для таблиц test_database?


Добавить


    ALTER TABLE ONLY public.orders
        ADD CONSTRAINT title_unique UNIQUE (title);





