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
```
```
```
```
```