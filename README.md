# primeiro-reposit-rio.
Estou fazendo meu primeiro projeto de aprendizagem em python sobre clientes atacadista e seus produtos de limpezas usados no dia a dia.
import datetime

class ProdutoLimpeza:
    """
    Classe para representar um produto de limpeza.

    Atributos:
        id_produto (int): Identificador único do produto.
        nome (str): Nome do produto.
        categoria (str): Categoria do produto (ex: desinfetante, detergente).
        descricao (str): Descrição detalhada do produto.
        unidade_medida (str): Unidade de medida (ex: litro, kg, unidade).
        preco_unitario (float): Preço por unidade de medida.
        conexoes (list): Lista de outros produtos frequentemente comprados em conjunto.
    """
    def __init__(self, id_produto, nome, categoria, descricao, unidade_medida, preco_unitario):
        self.id_produto = id_produto
        self.nome = nome
        self.categoria = categoria
        self.descricao = descricao
        self.unidade_medida = unidade_medida
        self.preco_unitario = preco_unitario
        self.conexoes = []  # Lista para armazenar produtos relacionados

    def adicionar_conexao(self, produto):
        """Adiciona um produto à lista de conexões."""
        if produto not in self.conexoes:
            self.conexoes.append(produto)

    def __str__(self):
        return f"{self.nome} ({self.unidade_medida}) - R${self.preco_unitario:.2f}"

class ClienteAtacadista:
    """
    Classe para representar um cliente atacadista.

    Atributos:
        id_cliente (int): Identificador único do cliente.
        nome (str): Nome ou razão social do cliente.
        cnpj (str): CNPJ do cliente.
        endereco (str): Endereço do cliente.
        segmento (str): Segmento de atuação do cliente (ex: restaurante, hotel, empresa de limpeza).
    """
    def __init__(self, id_cliente, nome, cnpj, endereco, segmento):
        self.id_cliente = id_cliente
        self.nome = nome
        self.cnpj = cnpj
        self.endereco = endereco
        self.segmento = segmento

    def __str__(self):
        return f"{self.nome} ({self.cnpj})"

class Transacao:
    """
    Classe para representar uma transação de compra.

    Atributos:
        id_transacao (int): Identificador único da transação.
        cliente (ClienteAtacadista): Cliente que realizou a compra.
        data (datetime): Data e hora da transação.
        itens (dict): Dicionário contendo os produtos e suas quantidades (produto: quantidade).
    """
    def __init__(self, id_transacao, cliente, data):
        self.id_transacao = id_transacao
        self.cliente = cliente
        self.data = data
        self.itens = {}  # Dicionário para armazenar os itens da transação (produto: quantidade)

    def adicionar_item(self, produto, quantidade):
        """Adiciona um item à transação."""
        if isinstance(produto, ProdutoLimpeza):
            if produto in self.itens:
                self.itens[produto] += quantidade
            else:
                self.itens[produto] = quantidade
        else:
            print("Erro: Item adicionado não é um produto de limpeza válido.")

    def calcular_total(self):
        """Calcula o valor total da transação."""
        total = 0
        for produto, quantidade in self.itens.items():
            total += produto.preco_unitario * quantidade
        return total

    def __str__(self):
         itens_str = "\n".join([f"  - {produto.nome}: {quantidade} {produto.unidade_medida}" for produto, quantidade in self.itens.items()])
         return (f"Transação #{self.id_transacao}\n"
                f"Cliente: {self.cliente.nome}\n"
                f"Data: {self.data.strftime('%Y-%m-%d %H:%M:%S')}\n"
                f"Itens:\n{itens_str}\n"
                f"Total: R${self.calcular_total():.2f}")



# --- Exemplos de uso e demonstração ---

# Criando produtos
desinfetante_pinho = ProdutoLimpeza(1, "Desinfetante Pinho Sol", "Desinfetante", "Desinfetante bactericida", "litro", 8.50)
detergente_limpol = ProdutoLimpeza(2, "Detergente Limpol", "Detergente", "Detergente neutro", "ml", 2.20)
agua_sanitaria = ProdutoLimpeza(3, "Água Sanitária Qboa", "Alvejante", "Alvejante para limpeza geral", "litro", 4.00)
limpador_multiuso = ProdutoLimpeza(4, "Limpador Multiuso Veja", "Limpeza Geral", "Limpa e remove manchas", "litro", 6.00)

# Definindo conexões entre produtos (ex: comprados juntos frequentemente)
desinfetante_pinho.adicionar_conexao(detergente_limpol)
detergente_limpol.adicionar_conexao(desinfetante_pinho)
agua_sanitaria.adicionar_conexao(desinfetante_pinho)


# Criando clientes
restaurante_sabores = ClienteAtacadista(101, "Restaurante Sabores Ltda", "12.345.678/0001-90", "Rua Principal, 123", "Restaurante")
hotel_estrelas = ClienteAtacadista(102, "Hotel Estrelas S.A.", "98.765.432/0001-21", "Avenida Beira Mar, 456", "Hotel")

# Criando transações
transacao1 = Transacao(201, restaurante_sabores, datetime.datetime(2023, 10, 27, 10, 30, 0))
transacao2 = Transacao(202, hotel_estrelas, datetime.datetime(2023, 10, 27, 14, 15, 0))

# Adicionando itens às transações
transacao1.adicionar_item(desinfetante_pinho, 5) # 5 litros
transacao1.adicionar_item(detergente_limpol, 10) # 10 unidades (ml no caso, considerar ajustar)
transacao2.adicionar_item(agua_sanitaria, 10) # 10 litros
transacao2.adicionar_item(limpador_multiuso, 3) # 3 litros

# Imprimindo informações das transações
print(transacao1)
print("\n" + "=" * 20 + "\n")
print(transacao2)

# Acessando informações e relações
print(f"\nProdutos frequentemente comprados com {desinfetante_pinho.nome}:")
for produto in desinfetante_pinho.conexoes:
    print(f"- {produto.nome}")


# --- Análise e Consultas (Exemplos) ---

def produtos_mais_vendidos(transacoes):
    """Retorna um dicionário com os produtos mais vendidos e suas quantidades."""
    contagem_produtos = {}
    for transacao in transacoes:
        for produto, quantidade in transacao.itens.items():
            if produto in contagem_produtos:
                contagem_produtos[produto] += quantidade
            else:
                contagem_produtos[produto] = quantidade

    # Ordenar por quantidade (decrescente)
    produtos_ordenados = sorted(contagem_produtos.items(), key=lambda item: item[1], reverse=True)
    return produtos_ordenados

def gasto_total_por_cliente(transacoes):
  """Retorna um dicionário com o gasto total de cada cliente."""
  gasto_clientes = {}
  for transacao in transacoes:
    cliente = transacao.cliente
    total = transacao.calcular_total()
    if cliente in gasto_clientes:
      gasto_clientes[cliente] += total
    else:
      gasto_clientes[cliente] = total
  return gasto_clientes


# Exemplo de uso das funções de análise
transacoes = [transacao1, transacao2]
produtos_mais_vendidos_lista = produtos_mais_vendidos(transacoes)
print("\nProdutos mais vendidos:")
for produto, quantidade in produtos_mais_vendidos_lista:
  print(f"- {produto.nome}: {quantidade} {produto.unidade_medida}")

gastos_por_cliente = gasto_total_por_cliente(transacoes)
print("\nGasto total por cliente:")
for cliente, gasto in gastos_por_cliente.items():
  print(f"- {cliente.nome}: R${gasto:.2f}")
