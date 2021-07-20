# Exercícios

## 1. Explique o conceito de normalização e para que é usado.

Refere-se à aplicação de um conjuto de regras que visam reduzir redundâncias e
melhorar a integridade dos dados. Através do uso das formas normais espera-se
que o sistema gerenciador de banco de dados possa garantir a integridade das
referências, restrições e dependências entre os dados.

## 2. Adicione um filme à tabela de filmes.

```
INSERT INTO movies (title, rating, release_date, length) VALUES ('The Room', 3.7, '2003-06-27 00:00:00', 99);
```

## 3. Adicione um gênero à tabela de gêneros.

```
INSERT INTO genres (name, ranking) VALUES ('Vergonha alheia', 13);
```

## 4. Associe o filme do Ex 2. ao gênero criado no Ex. 3.

```
UPDATE movies
SET genre_id = (SELECT id FROM genres WHERE name = 'Vergonha alheia')
WHERE title = 'The Room';
```

## 5. Modifique a tabela de atores para que pelo menos um ator adicione como favorito o filme adicionado no Ex. 2.

```
UPDATE actors
SET favorite_movie_id = (SELECT id FROM movies WHERE title = 'The Room')
WHERE first_name = 'Kit' AND last_name = 'Harington';
```

## 6. Crie uma cópia temporária da tabela de filmes.

```
CREATE TEMPORARY TABLE IF NOT EXISTS movies_tmp AS (SELECT * FROM movies);
```

## 7. Elimine desta tabela temporária todos os filmes que ganharam menos de 5 prêmios.

```
DELETE
FROM movies_tmp
WHERE awards < 5;
```

## 8. Obtenha a lista de todos os gêneros que possuem pelo menos um filme.

```
SELECT g.id, g.name, g.ranking, COUNT(g.id) AS count
FROM genres AS g
    INNER JOIN movies AS m ON m.genre_id = g.id
WHERE g.active = 1
GROUP BY g.id;
```

## 9. Obtenha a lista de atores cujo filme favorito ganhou mais de 3 prêmios.

```
SELECT a.id, a.first_name, a.last_name, a.rating, m.title, m.awards
FROM actors AS a
    INNER JOIN movies AS m ON m.id = a.favorite_movie_id
WHERE m.awards > 3;
```

## 10. Use o plano de explicação para analisar as consultas nos Ex. 6 e 7.


### Exercício 6:

```
EXPLAIN ANALYZE CREATE TEMPORARY TABLE IF NOT EXISTS movies_tmp AS (SELECT * FROM movies);
```

> ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'CREATE TEMPORARY TABLE IF NOT EXISTS movies_tmp AS (SELECT * FROM movies)' at line 1

### Exercício 7:

```
EXPLAIN ANALYZE DELETE FROM movies_tmp WHERE awards < 5;
```

> +----------------------------------------+
> | EXPLAIN                                |
> +----------------------------------------+
> | <not executable by iterator executor>
>  |
> +----------------------------------------+
> 1 row in set (0.00 sec)

### 11. O que são os índices? Para que servem?

É um mecanismo de otimização de consultas ao banco de dados através da crianção
de uma estrutura de dados cujas chaves de comparação pertencem à coluna sendo
indexada.

### 12. Crie um índice sobre o nome na tabela de filmes.

```
CREATE INDEX movies_title_idx
ON movies (title);
```

### 13. Verifique se o índice foi criado corretamente.

```
SHOW INDEX FROM movies;
```

> +--------+------------+-------------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
> | Table  | Non_unique | Key_name                | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment | Visible | Expression |
> +--------+------------+-------------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
> | movies |          0 | PRIMARY                 |            1 | id          | A         |          21 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
> | movies |          1 | movies_genre_id_foreign |            1 | genre_id    | A         |           8 |     NULL |   NULL | YES  | BTREE      |         |               | YES     | NULL       |
> | movies |          1 | movies_title_idx        |            1 | title       | A         |          22 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
> +--------+------------+-------------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
> 3 rows in set (0.00 sec)
