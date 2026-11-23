-- ============================================================
-- Exercício 6 - Sprint de Refatoração | Cenário: App de Delivery
-- Rodar em SQLite, de cima para baixo, em uma única execução
-- ============================================================

-- ------------------------------------------------------------
-- PARTE: 01_legado_ddl_dml.sql
-- ------------------------------------------------------------
-- ESTADO ANTES (legado): tudo na tabela Pedidos (aqui chamada pedidos_legado)
DROP TABLE IF EXISTS pedidos_legado;
CREATE TABLE pedidos_legado (
    id               INTEGER PRIMARY KEY,
    cliente_nome     TEXT,
    cliente_telefone TEXT,
    cliente_endereco TEXT,
    restaurante_nome TEXT,
    lista_produtos   TEXT,      -- ex.: '2x Pizza Calabresa, 1x Refrigerante'
    total            REAL,
    status           TEXT,
    data_pedido      TEXT
);

INSERT INTO pedidos_legado VALUES
(1,'João Silva','11999990001','Rua A, 10','Pizzaria Bella','2x Pizza Calabresa, 1x Refrigerante',90.00,'entregue','2026-11-01'),
(2,'Maria Souza','11999990002','Rua B, 20','Burger House','1x X-Burger, 1x Batata Frita, 2x Refrigerante',57.00,'entregue','2026-11-02'),
(3,'João Silva','11999990001','Rua A, 10','Burger House','2x X-Burger',56.00,'entregue','2026-11-03'),
(4,'Ana Lima','11999990003','Rua C, 30','Pizzaria Bella','1x Pizza Calabresa, 1x Pizza Mussarela',80.00,'cancelado','2026-11-03'),
(5,'Maria Souza','11999990002','Rua B, 20','Pizzaria Bella','1x Pizza Mussarela, 2x Refrigerante',56.00,'entregue','2026-11-04'),
(6,'Carlos Pereira','11999990004','Rua das Flores, 341','Pizzaria Bella','1x Pizza Mussarela, 3x Pizza Calabresa, 3x Refrigerante',184.00,'entregue','2026-11-05'),
(7,'Fernanda Costa','11999990005','Av. Paulista, 164','Pizzaria Bella','1x Refrigerante, 1x Pizza Mussarela, 3x Pizza Calabresa',168.00,'entregue','2026-11-06'),
(8,'Lucas Oliveira','11999990006','Rua Augusta, 414','Pizzaria Bella','3x Pizza Mussarela, 1x Pizza Calabresa',160.00,'entregue','2026-11-07'),
(9,'Juliana Rocha','11999990007','Rua da Consolação, 676','Burger House','3x Refrigerante, 3x X-Burger, 3x Batata Frita',150.00,'cancelado','2026-11-08'),
(10,'Pedro Santos','11999990008','Av. Brasil, 59','Pizzaria Bella','3x Pizza Calabresa, 1x Refrigerante',128.00,'entregue','2026-11-09'),
(11,'Camila Alves','11999990009','Rua Oscar Freire, 84','Pizzaria Bella','2x Refrigerante, 2x Pizza Mussarela',96.00,'entregue','2026-11-10'),
(12,'Rafael Martins','11999990010','Rua Haddock Lobo, 850','Burger House','1x Batata Frita, 3x X-Burger',99.00,'entregue','2026-11-11'),
(13,'Beatriz Ferreira','11999990011','Av. Rebouças, 558','Pizzaria Bella','2x Refrigerante',16.00,'entregue','2026-11-12'),
(14,'Gustavo Ribeiro','11999990012','Rua Vergueiro, 106','Burger House','2x Refrigerante, 3x Batata Frita',59.00,'entregue','2026-11-13'),
(15,'Larissa Gomes','11999990013','Rua Domingos de Morais, 384','Pizzaria Bella','2x Refrigerante',16.00,'entregue','2026-11-14'),
(16,'Thiago Carvalho','11999990014','Rua Pamplona, 606','Pizzaria Bella','2x Pizza Calabresa, 1x Pizza Mussarela',120.00,'entregue','2026-11-15'),
(17,'Patrícia Nunes','11999990015','Rua Bela Cintra, 69','Pizzaria Bella','3x Refrigerante, 2x Pizza Mussarela, 3x Pizza Calabresa',224.00,'cancelado','2026-11-16'),
(18,'Bruno Araújo','11999990016','Av. Ipiranga, 529','Burger House','2x Batata Frita, 2x X-Burger, 3x Refrigerante',107.00,'entregue','2026-11-17'),
(19,'Aline Barbosa','11999990017','Rua Teodoro Sampaio, 229','Pizzaria Bella','3x Refrigerante',24.00,'entregue','2026-11-18'),
(20,'Diego Teixeira','11999990018','Rua dos Pinheiros, 48','Burger House','3x Refrigerante, 2x Batata Frita, 3x X-Burger',135.00,'entregue','2026-11-19'),
(21,'Ana Lima','11999990003','Rua C, 30','Burger House','2x Batata Frita',30.00,'entregue','2026-11-20'),
(22,'Thiago Carvalho','11999990014','Rua Pamplona, 606','Pizzaria Bella','1x Pizza Calabresa, 2x Pizza Mussarela, 1x Refrigerante',128.00,'entregue','2026-11-21'),
(23,'Thiago Carvalho','11999990014','Rua Pamplona, 606','Pizzaria Bella','1x Pizza Mussarela, 1x Refrigerante',48.00,'entregue','2026-11-22'),
(24,'Ana Lima','11999990003','Rua C, 30','Burger House','1x Refrigerante, 2x Batata Frita',37.00,'entregue','2026-11-23'),
(25,'Pedro Santos','11999990008','Av. Brasil, 59','Burger House','1x Batata Frita, 1x Refrigerante, 1x X-Burger',50.00,'entregue','2026-11-24');

-- ------------------------------------------------------------
-- PARTE: 02_refatoracao_ddl.sql
-- ------------------------------------------------------------
-- ESTADO DEPOIS: modelo normalizado (3FN)
PRAGMA foreign_keys = OFF;
-- Limpa execuções anteriores (permite rodar o script mais de uma vez)
DROP TABLE IF EXISTS itens_pedido;
DROP TABLE IF EXISTS pedidos;
DROP TABLE IF EXISTS produtos;
DROP TABLE IF EXISTS restaurantes;
DROP TABLE IF EXISTS clientes;
PRAGMA foreign_keys = ON;

CREATE TABLE clientes (
    id        INTEGER PRIMARY KEY,
    nome      TEXT NOT NULL,
    telefone  TEXT NOT NULL UNIQUE,
    endereco  TEXT NOT NULL
);

CREATE TABLE restaurantes (
    id    INTEGER PRIMARY KEY,
    nome  TEXT NOT NULL UNIQUE
);

CREATE TABLE produtos (
    id              INTEGER PRIMARY KEY,
    restaurante_id  INTEGER NOT NULL REFERENCES restaurantes(id),
    nome            TEXT NOT NULL,
    preco           REAL,
    UNIQUE (restaurante_id, nome)
);

CREATE TABLE pedidos (
    id              INTEGER PRIMARY KEY,
    cliente_id      INTEGER NOT NULL REFERENCES clientes(id),
    restaurante_id  INTEGER NOT NULL REFERENCES restaurantes(id),
    status          TEXT NOT NULL CHECK (status IN ('entregue','cancelado','em_preparo')),
    data_pedido     TEXT NOT NULL
);

CREATE TABLE itens_pedido (
    pedido_id       INTEGER NOT NULL REFERENCES pedidos(id),
    produto_id      INTEGER NOT NULL REFERENCES produtos(id),
    quantidade      INTEGER NOT NULL CHECK (quantidade > 0),
    preco_unitario  REAL NOT NULL,
    PRIMARY KEY (pedido_id, produto_id)
);

-- Índices para as consultas mais comuns
CREATE INDEX idx_pedidos_cliente ON pedidos(cliente_id);
CREATE INDEX idx_pedidos_status_data ON pedidos(status, data_pedido);
CREATE INDEX idx_itens_produto ON itens_pedido(produto_id);

-- ------------------------------------------------------------
-- PARTE: 03_migracao_dml.sql
-- ------------------------------------------------------------
-- Migra os dados do legado para o modelo novo
PRAGMA foreign_keys = ON;

INSERT INTO clientes (nome, telefone, endereco)
SELECT DISTINCT cliente_nome, cliente_telefone, cliente_endereco FROM pedidos_legado;

INSERT INTO restaurantes (nome)
SELECT DISTINCT restaurante_nome FROM pedidos_legado;

INSERT INTO pedidos (id, cliente_id, restaurante_id, status, data_pedido)
SELECT p.id, c.id, r.id, p.status, p.data_pedido
FROM pedidos_legado p
JOIN clientes c ON c.telefone = p.cliente_telefone
JOIN restaurantes r ON r.nome = p.restaurante_nome;

-- "Explode" a lista separada por vírgula em uma linha por item
DROP TABLE IF EXISTS itens_tmp;
CREATE TEMP TABLE itens_tmp AS
WITH RECURSIVE quebra(pedido_id, item, resto) AS (
    SELECT id, '', lista_produtos || ',' FROM pedidos_legado
    UNION ALL
    SELECT pedido_id,
           trim(substr(resto, 1, instr(resto, ',') - 1)),
           substr(resto, instr(resto, ',') + 1)
    FROM quebra WHERE resto <> ''
)
SELECT pedido_id,
       CAST(substr(item, 1, instr(item, 'x ') - 1) AS INTEGER) AS quantidade,
       trim(substr(item, instr(item, 'x ') + 2))               AS produto_nome
FROM quebra WHERE item <> '';

INSERT INTO produtos (restaurante_id, nome)
SELECT DISTINCT p.restaurante_id, t.produto_nome
FROM itens_tmp t JOIN pedidos p ON p.id = t.pedido_id;

-- O legado não guardava preço unitário: definimos o catálogo (decisão da dupla)
UPDATE produtos SET preco = CASE nome
    WHEN 'Pizza Calabresa' THEN 40.00
    WHEN 'Pizza Mussarela' THEN 40.00
    WHEN 'X-Burger'        THEN 28.00
    WHEN 'Batata Frita'    THEN 15.00
    WHEN 'Refrigerante'    THEN 7.00 END;
-- refrigerante da Pizzaria Bella custa 8,00 (para o total histórico do pedido 5 bater)
UPDATE produtos SET preco = 8.00
WHERE nome = 'Refrigerante' AND restaurante_id = (SELECT id FROM restaurantes WHERE nome='Pizzaria Bella');
-- OBS: pedido 1 soma 88,00 nos itens contra 90,00 no legado (diferença de 2,00 = taxa de entrega nunca registrada; ver relatório)

INSERT INTO itens_pedido (pedido_id, produto_id, quantidade, preco_unitario)
SELECT t.pedido_id, pr.id, t.quantidade, pr.preco
FROM itens_tmp t
JOIN pedidos p  ON p.id = t.pedido_id
JOIN produtos pr ON pr.restaurante_id = p.restaurante_id AND pr.nome = t.produto_nome;

DROP TABLE itens_tmp;

-- ------------------------------------------------------------
-- PARTE: 04_validacao_consultas.sql
-- ------------------------------------------------------------
-- Relatório antes impossível: pratos mais vendidos
SELECT pr.nome AS prato, SUM(i.quantidade) AS total_vendido
FROM itens_pedido i
JOIN produtos pr ON pr.id = i.produto_id
JOIN pedidos p   ON p.id = i.pedido_id
WHERE p.status = 'entregue'
GROUP BY pr.nome
ORDER BY total_vendido DESC;

-- Contagens (devem bater com o legado)
SELECT (SELECT COUNT(*) FROM pedidos_legado) AS pedidos_legado,
       (SELECT COUNT(*) FROM pedidos) AS pedidos_novo,
       (SELECT COUNT(*) FROM clientes) AS clientes,
       (SELECT COUNT(*) FROM itens_pedido) AS itens;

-- Totais: legado x recalculado
SELECT l.id, l.total AS total_legado,
       SUM(i.quantidade * i.preco_unitario) AS total_novo
FROM pedidos_legado l JOIN itens_pedido i ON i.pedido_id = l.id
GROUP BY l.id;

-- Plano de execução usando índice
EXPLAIN QUERY PLAN
SELECT * FROM pedidos WHERE status='entregue' AND data_pedido >= '2026-11-01';
