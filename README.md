# SQL: Um Teste de SQL

Este é um teste de SQL de nível intermediário, desenvolvido para a empresa VersoTech.

## Estrutura do Banco

Copie as instruções abaixo e cole no seu ambiente PostgreSQL para a criação das tabelas.

**Tabela Empresa:**
```
CREATE TABLE empresa(
  id_empresa SERIAL PRIMARY KEY, 
  razao_social VARCHAR(255),
  inativo BOOLEAN DEFAULT FALSE
);
```
**Tabela Produtos:**
```
CREATE TABLE produtos(
  id_produto SERIAL PRIMARY KEY,
  descricao VARCHAR(255),
  inativo BOOLEAN DEFAULT FALSE
);
```
**Tabela Vendedores:**
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
**Tabela Config_Preco_Produto:**
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
**Tabela Clientes:**
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
**Tabela Pedido:**
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
**Tabela Itens_Pedido:**
```
CREATE TABLE itens_pedido(
  id_item_pedido SERIAL PRIMARY KEY,
  id_pedido INT REFERENCES pedido(id_pedido),
  id_produto INT REFERENCES produtos(id_produto),
  preco_praticado NUMERIC,
  quantidade INT
);
```

## Alimentação das Tabelas

Copie as instruções abaixo e cole no seu ambiente PostgreSQL para alimentar as tabelas.

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
INSERT INTO config_preco_produto (id_vendedor, id_empresa, id_produto, preco_minimo, preco_maximo) VALUES
(1, 1, 1, 10.00, 20.00),
(1, 1, 2, 25.00, 35.00),
(2, 2, 3, 40.00, 60.00);
```
```
INSERT INTO clientes (razao_social, data_cadastro, id_vendedor, id_empresa, inativo) VALUES
('Chaves', '2020-01-01', 1, 1, FALSE),
('Quico', '2020-01-01', 1, 1, FALSE),
('Chiquinha', '2020-01-02', 2, 2, FALSE);
```
```
INSERT INTO pedido (id_empresa, id_cliente, valor_total, data_emissao, situacao) VALUES
(1, 1, 60.00, '2023-01-01', 'C'),
(1, 2, 300.00, '2023-01-02', 'P'),
(2, 3, 100.00, '2023-01-03', 'X');
```
```
INSERT INTO itens_pedido (id_pedido, id_produto, preco_praticado, quantidade) VALUES
(1, 1, 15.00, 4),
(2, 2, 30.00, 10),
(3, 3, 50.00, 2);
```

## Consultas

Abaixo, seguem as instruções para cumprir com o que foi solicitado pelos testes, bem como a explicação do que foi executado.

### Lista de funcionários ordenando pelo salário decrescente.
```
SELECT * FROM vendedores ORDER BY salario DESC;
```
Enquanto o símbolo `*` se encarrega de retornar todos os dados da tabela, a inclusão de `DESC` na ordenação pelo salário se encarrega de executar a ordenação começando pelo maior valor e, consequentemente, encerrando pelo menor.

### Lista de pedidos de vendas ordenado por data de emissão.
```
SELECT * FROM pedido ORDER BY data_emissao;
```
Segue a mesma lógica da consulta anterior. Vale ressaltar que nesse caso não é necessário especificar a ordenação diretamente na instrução, pois por padrão é aplicado a ordem crescente.

### Valor de faturamento por cliente.
```
SELECT id_cliente, SUM(valor_total) as faturamento FROM pedido GROUP BY id_cliente;
```
Para atingir o objeto, utiliza-se a função `SUM()` para somar os valores de faturamento do cliente, e isso apenas é possível porque a instrução agrupa os dados pelo ID do cliente.

### Valor de faturamento por empresa.
```
SELECT id_empresa, SUM(valor_total) as faturamento FROM pedido GROUP BY id_empresa;
```
Segue a mesma lógica da instrução anterior.

### Valor de faturamento por vendedor.
```
SELECT c.id_vendedor, SUM(p.valor_total) AS faturamento FROM pedido AS p, clientes AS c WHERE p.id_cliente = c.id_cliente GROUP BY c.id_vendedor;
```
Também segue a mesma lógica das duas instruções anteriores. Porém, tendo em vista que o atributo `id_vendedor` (necessário para distinguir entre os vendedores) não está na mesma tabela que o atributo `valor_total` (necessário para calcular o faturamento), é preciso utilizar a cláusula `WHERE` para relacionar as chaves estrangeiras e identificar qual venda é referente a qual vendedor.

### Consulta de Junção
```
SELECT
  prod.id_produto,
  prod.descricao,
  ped.id_cliente,
  c.razao_social AS razao_social_cliente,
  ped.id_empresa,
  e.razao_social AS razao_social_empresa,
  c.id_vendedor,
  v.nome AS nome_vendedor,
  cpp.preco_minimo,
  cpp.preco_maximo,
  COALESCE(
    (
      SELECT
        ip2.preco_praticado 
      FROM
        itens_pedido ip2 
      JOIN 
        pedido ped2 ON ped2.id_pedido = ip2.id_pedido
      WHERE
        ip2.id_produto = prod.id_produto
      AND
        ped2.id_cliente = ped.id_cliente
      AND
        ped2.id_empresa = ped.id_empresa
      AND
        ip2.preco_praticado BETWEEN cpp.preco_minimo AND cpp.preco_maximo
      ORDER BY
        ped2.data_emissao DESC
      LIMIT 1
    ), cpp.preco_minimo
  ) AS preco_base
FROM
  produtos AS prod
JOIN
  config_preco_produto AS cpp ON cpp.id_produto = prod.id_produto
JOIN
  clientes AS c ON cpp.id_empresa = c.id_empresa
JOIN
  pedido AS ped ON ped.id_cliente = c.id_cliente AND ped.id_empresa = cpp.id_empresa
JOIN
  itens_pedido AS ip ON ped.id_pedido = ip.id_pedido AND prod.id_produto = ip.id_produto
JOIN
  empresa AS e ON cpp.id_empresa = e.id_empresa
JOIN
  vendedores AS v ON c.id_vendedor = v.id_vendedor;
```
Por fim, a instrução mais complexa de todas, afinal é preciso unir a listagem de produtos com a listagem de clientes e procurar pelo último preço praticado nesse cliente com esse produto, formulando o preço base do produto. Para atingir o objetivo, inicialmente foram explicitados todos os campos que deveriam ser retornados pela instrução. Um desses campos é o `preco_base`, e para retorná-lo foi construída uma subquery que seleciona todos os registros de `preco_praticado` do produto para o cliente, verifica se esse preço está respeitando a configuração de preço imposta pela tabela `CONFIG_PRECO_PRODUTO`, ordena todos esses registros pela data de emissão em ordem decrescente, e então limita o retorno da instrução para um valor, garantindo que apenas o último registro de preço seja fornecido, assim como é definido pelo teste. Essa subquery está envolvida pela função `COALESCE`, a qual recebe outro parâmetro além da própria subquery: o preço mínimo. Sendo assim, a função irá retornar o primeiro valor que não seja nulo entre eles, começando logicamente pela subquery. Dessa forma, caso ela não retorne nenhum valor, o preço mínimo será considerado como preço base. Por fim, são executadas todas as cláusulas `JOIN` para garantir o correto relacionamento entre todas as chaves estrangeiras da consulta.

*OBS: Para testar o correto funcionamento dessa instrução, basta alterar dois registros e garantir que os atributos `id_cliente` e `id_cliente` sejam os mesmos. Após isso, você irá notar que o campo de preço base irá variar conforme a alteração na data de emissão da compra. Outro teste pode ser realizado ao mudar o atributo `preco_praticado` na compra de um cliente para um valor que não esteja de acordo com a configuração de preço do produto ou para nulo, pois a consulta irá retornar o preço mínimo nesse caso.*
