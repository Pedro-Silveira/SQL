# SQL
A SQL test.

## CREATE TABLES
```
CREATE TABLE empresa(
  id_empresa SERIAL PRIMARY KEY, 
  razao_social VARCHAR(255),
  inativo BOOLEAN DEFAULT FALSE
);
```

```
CREATE TABLE produtos(
  id_produto SERIAL PRIMARY KEY,
  descricao VARCHAR(255),
  inativo BOOLEAN DEFAULT FALSE
);
```

```
CREATE TABLE vendedores(
  id_vendedor SERIAL PRIMARY KEY,
  nome VARCHAR(255),
  cargo VARCHAR(100),
  salario NUMERIC,
  data_admissao DATE,
  inativo BOOLEAN DEFAULT FALSE
);
```

```
CREATE TABLE config_preco_produto(
  id_config_preco_produto SERIAL PRIMARY KEY,
  id_vendedor INT REFERENCES vendedores(id_vendedor),
  id_empresa INT REFERENCES empresa(id_empresa),
  id_produto INT REFERENCES produtos(id_produto),
  preco_minimo NUMERIC,
  preco_maximo NUMERIC
);
```

```
CREATE TABLE clientes(
  id_cliente SERIAL PRIMARY KEY,
  razao_social VARCHAR(255),
  data_cadastro DATE,
  id_vendedor INT REFERENCES vendedores(id_vendedor),
  id_empresa INT REFERENCES empresa(id_empresa),
  inativo BOOLEAN DEFAULT FALSE
);
```

```
CREATE TABLE pedido(
  id_pedido SERIAL PRIMARY KEY,
  id_empresa INT REFERENCES empresa(id_empresa),
  id_cliente INT REFERENCES clientes(id_cliente),
  valor_total NUMERIC,
  data_emissao DATE,
  situacao CHAR(1)
);
```

```
CREATE TABLE itens_pedido(
  id_item_pedido SERIAL PRIMARY KEY,
  id_pedido INT REFERENCES pedido(id_pedido),
  id_produto INT REFERENCES produtos(id_produto),
  preco_praticado NUMERIC,
  quantidade INT
);
```

## INSERTS
```
INSERT INTO empresa (razao_social, inativo) VALUES
('VersoTech', FALSE),
('Verso', FALSE),
('Tech', TRUE);
```

```
INSERT INTO produtos (descricao, inativo) VALUES
('Software', FALSE),
('Hardware', FALSE),
('Alienware', TRUE);
```

```
INSERT INTO vendedores (nome, cargo, salario, data_admissao, inativo) VALUES
('Pedro', 'Backend', 3500.00, '2024-01-24', FALSE),
('Henrique', 'Fullstack', 4000.00, '2014-03-20', FALSE),
('Silveira', 'Frontend', 3000.00, '2021-07-10', TRUE);
```

```
INSERT INTO CONFIG_PRECO_PRODUTO (id_vendedor, id_empresa, id_produto, preco_minimo, preco_maximo) VALUES
(1, 1, 1, 45.00, 55.00),
(2, 1, 2, 90.00, 110.00),
(3, 2, 1, 70.00, 80.00);
```

```
INSERT INTO clientes (razao_social, data_cadastro, id_vendedor, id_empresa, inativo) VALUES
('Chaves', '2021-05-22', 1, 1, FALSE),
('Quico', '2020-11-13', 2, 1, FALSE),
('Chiquinha', '2022-02-01', 1, 2, TRUE);
```

```
INSERT INTO pedido (id_empresa, id_cliente, valor_total, data_emissao, situacao) VALUES
(1, 1, 100.00, '2023-03-01', 'C'),
(1, 2, 200.00, '2023-03-05', 'P'),
(2, 3, 150.00, '2023-03-10', 'X');
```

```
INSERT INTO itens_pedido (id_pedido, id_produto, preco_praticado, quantidade) VALUES
(1, 1, 50.00, 2),
(2, 2, 100.00, 2),
(3, 1, 75.00, 2);
```

## QUERYS

```
SELECT * FROM vendedores ORDER BY salario DESC;
```

```
SELECT * FROM pedido ORDER BY data_emissao DESC;
```

```
SELECT id_cliente, sum(valor_total) FROM pedido GROUP BY id_cliente;
```

```
SELECT id_empresa, sum(valor_total) FROM pedido GROUP BY id_empresa;
```

```
SELECT c.id_vendedor, sum(p.valor_total) FROM pedido AS p, clientes AS c WHERE p.id_cliente = c.id_cliente GROUP BY c.id_vendedor;
```

```
SELECT 
    P.id_produto,
    P.descricao AS descricao_produto,
    C.id_cliente,
    C.razao_social AS razao_social_cliente,
    CP.id_empresa,
    E.razao_social AS razao_social_empresa,
    C.id_vendedor,
    V.nome AS nome_vendedor,
    CP.preco_minimo,
    CP.preco_maximo,
    COALESCE(MAX(IP.preco_praticado), CP.preco_minimo) AS preco_base
FROM 
    PRODUTOS AS P
JOIN 
    CONFIG_PRECO_PRODUTO AS CP ON P.id_produto = CP.id_produto
JOIN 
    CLIENTES AS C ON CP.id_empresa = C.id_empresa
JOIN 
    PEDIDO AS PD ON PD.id_cliente = C.id_cliente
JOIN 
    ITENS_PEDIDO AS IP ON PD.id_pedido = IP.id_pedido AND P.id_produto = IP.id_produto
JOIN 
    EMPRESA AS E ON CP.id_empresa = E.id_empresa
JOIN 
    VENDEDORES AS V ON C.id_vendedor = V.id_vendedor;
```

```
```

```
```

```
```

```
```

```
```

```
```