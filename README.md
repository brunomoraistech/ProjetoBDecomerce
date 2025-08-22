# ProjetoBDecomerce
 Refinando um Projeto Conceitual de Banco de Dados – E-COMMERCE

# 1. Registro das Entidades e seus Significados
•	Cliente: Usuário que realiza compras no e-commerce. Armazena dados pessoais para contato e entrega.
•	Produto: Itens disponíveis para venda, com detalhes como descrição, preço e estoque.
•	Pedido: Registro da compra efetuada pelo cliente com data e status.
•	ItemPedido: Produtos específicos que compõem cada pedido, incluindo quantidade e preço unitário.
•	Pagamento: Representa a transação financeira do pedido, com valor, forma e status do pagamento.
 
# 2. Descrição dos Relacionamentos
•	Um Cliente pode realizar muitos Pedidos (1 cliente para muitos pedidos).
•	Cada Pedido tem vários ItensPedido (1 pedido para vários itens).
•	Cada ItemPedido está associado a um único Produto.
•	Cada Pedido possui exatamente um Pagamento vinculado.
 
# 3. Principais Regras de Negócio
•	Cadastro do cliente é obrigatório para efetuaro pedido.
•	Pedido só pode ser registrado se houver estoque suficiente dos produtos.
•	Estoque dos produtos deve ser atualizado reduzindo a quantidade vendida ao confirmar o pedido.
•	Pagamento está sempre vinculado a um pedido específico e deve ser confirmado para liberar o envio.
•	O status do pedido deve refletir as etapas do processo (ex: Em processamento, Enviado, Concluído).
•	Um produto não pode ter estoque negativo.
 
# 4. Scripts SQL para Criação e Inserção
Criação das tabelas:
CREATE TABLE Cliente (
    cliente_id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(70) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    telefone VARCHAR(15),
    endereco TEXT
);

CREATE TABLE Produto (
    produto_id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(100) NOT NULL,
    descricao TEXT,
    preco DECIMAL(10, 2) NOT NULL,
    estoque INT NOT NULL CHECK (estoque >= 0)
);

CREATE TABLE Pedido (
    pedido_id INT PRIMARY KEY AUTO_INCREMENT,
    data_pedido DATETIME NOT NULL,
    cliente_id INT NOT NULL,
    status_pedido VARCHAR(20) NOT NULL,
    FOREIGN KEY (cliente_id) REFERENCES Cliente(cliente_id)
);

CREATE TABLE ItemPedido (
    item_pedido_id INT PRIMARY KEY AUTO_INCREMENT,
    pedido_id INT NOT NULL,
    produto_id INT NOT NULL,
    quantidade INT NOT NULL CHECK (quantidade > 0),
    preco_unitario DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (pedido_id) REFERENCES Pedido(pedido_id),
    FOREIGN KEY (produto_id) REFERENCES Produto(produto_id)
);

CREATE TABLE Pagamento (
    pagamento_id INT PRIMARY KEY AUTO_INCREMENT,
    pedido_id INT UNIQUE NOT NULL,
    valor DECIMAL(10, 2) NOT NULL,
    forma_pagamento VARCHAR(30) NOT NULL,
    status_pagamento VARCHAR(20) NOT NULL,
    FOREIGN KEY (pedido_id) REFERENCES Pedido(pedido_id)
);

# Inserção de dados de exemplo:
INSERT INTO Cliente (nome, email, telefone, endereco) VALUES ('Ana Silva', 'ana@email.com', '123456789', 'Rua A, 123');

INSERT INTO Produto (nome, descricao, preco, estoque) VALUES ('Smartphone XYZ', 'Smartphone com 64GB de armazenamento', 1500.00, 100);

INSERT INTO Pedido (data_pedido, cliente_id, status_pedido) VALUES (NOW(), 1, 'Em processamento');

INSERT INTO ItemPedido (pedido_id, produto_id, quantidade, preco_unitario) VALUES (1, 1, 2, 1500.00);

INSERT INTO Pagamento (pedido_id, valor, forma_pagamento, status_pagamento) VALUES (1, 3000.00, 'Cartão de Crédito', 'Pendente');

 
# 5. Explicação do Fluxo de Dados
1.	O cliente faz cadastro fornecendo seus dados pessoais.
2.	Os produtos são cadastrados no sistema, com preço e estoque definidos.
3.	O cliente realiza um pedido selecionando produtos e suas quantidades.
4.	O sistema verifica o estoque dos produtos; se disponível, confirma o pedido.
5.	São criados os registros na tabela ItemPedido para cada produto no pedido.
6.	O estoque do produto é atualizado, subtraindo as quantidades vendidas.
7.	O cliente efetua o pagamento, que fica vinculado ao pedido.
8.	O status do pedido é atualizado conforme processamento: em processamento, enviado, concluído.
