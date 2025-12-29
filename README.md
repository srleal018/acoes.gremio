<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Minha Loja Online</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        .modal { display: none; position: fixed; z-index: 50; left: 0; top: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); }
    </style>
</head>
<body class="bg-gray-100 font-sans">

    <nav class="bg-blue-600 p-4 text-white flex justify-between items-center sticky top-0 shadow-lg">
        <h1 class="text-2xl font-bold cursor-pointer" onclick="showSection('loja')">TechStore</h1>
        <div class="space-x-4">
            <button onclick="showSection('loja')" class="hover:underline">Loja</button>
            <button onclick="toggleCart()" class="hover:underline">Carrinho (<span id="cart-count">0</span>)</button>
            <button onclick="showSection('admin')" class="bg-blue-800 px-3 py-1 rounded hover:bg-blue-900">Adm</button>
        </div>
    </nav>

    <section id="secao-loja" class="container mx-auto p-6">
        <h2 class="text-3xl font-semibold mb-6">Nossos Produtos</h2>
        <div id="lista-produtos" class="grid grid-cols-1 md:grid-cols-3 gap-6">
            </div>
    </section>

    <section id="secao-admin" class="container mx-auto p-6 hidden">
        <h2 class="text-3xl font-semibold mb-6">Painel Administrativo</h2>
        <div class="bg-white p-6 rounded shadow-md mb-8">
            <h3 class="text-xl mb-4 font-bold">Adicionar Novo Produto</h3>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                <input type="text" id="adm-nome" placeholder="Nome do Produto" class="border p-2 rounded">
                <input type="number" id="adm-preco" placeholder="Preço (R$)" class="border p-2 rounded">
                <button onclick="adicionarProduto()" class="bg-green-500 text-white p-2 rounded hover:bg-green-600">Cadastrar</button>
            </div>
        </div>
        <div class="bg-white p-6 rounded shadow-md">
            <h3 class="text-xl mb-4 font-bold">Vendas Realizadas</h3>
            <ul id="lista-vendas" class="divide-y">
                <li class="py-2 text-gray-500">Nenhuma venda registrada ainda.</li>
            </ul>
        </div>
    </section>

    <div id="modal-carrinho" class="modal flex items-center justify-center">
        <div class="bg-white p-8 rounded-lg w-full max-w-md">
            <h2 class="text-2xl font-bold mb-4">Seu Carrinho</h2>
            <div id="itens-carrinho" class="mb-4 max-h-60 overflow-y-auto">
                </div>
            <div class="border-t pt-4 text-xl font-bold mb-4">
                Total: R$ <span id="total-carrinho">0.00</span>
            </div>
            <div class="flex justify-between">
                <button onclick="toggleCart()" class="text-red-500">Fechar</button>
                <button onclick="finalizarCompra()" class="bg-blue-600 text-white px-6 py-2 rounded">Finalizar Compra</button>
            </div>
        </div>
    </div>

    <script>
        // Dados iniciais (Simulando um banco de dados)
        let produtos = [
            { id: 1, nome: "Smartphone Pro", preco: 2500.00 },
            { id: 2, nome: "Notebook Air", preco: 4500.00 },
            { id: 3, nome: "Fone Bluetooth", preco: 150.00 }
        ];
        let carrinho = [];
        let vendas = [];

        // Funções de Interface
        function showSection(section) {
            document.getElementById('secao-loja').classList.toggle('hidden', section !== 'loja');
            document.getElementById('secao-admin').classList.toggle('hidden', section !== 'admin');
        }

        function toggleCart() {
            const modal = document.getElementById('modal-carrinho');
            modal.style.display = modal.style.display === 'flex' ? 'none' : 'flex';
            renderCarrinho();
        }

        // Lógica de Produtos
        function renderProdutos() {
            const container = document.getElementById('lista-produtos');
            container.innerHTML = '';
            produtos.forEach(p => {
                container.innerHTML += `
                    <div class="bg-white p-4 rounded shadow hover:shadow-xl transition">
                        <div class="h-40 bg-gray-200 mb-4 rounded"></div>
                        <h3 class="text-lg font-bold">${p.nome}</h3>
                        <p class="text-blue-600 font-semibold mb-4">R$ ${p.preco.toFixed(2)}</p>
                        <button onclick="addToCart(${p.id})" class="w-full bg-blue-600 text-white py-2 rounded hover:bg-blue-700">Adicionar ao Carrinho</button>
                    </div>
                `;
            });
        }

        function adicionarProduto() {
            const nome = document.getElementById('adm-nome').value;
            const preco = parseFloat(document.getElementById('adm-preco').value);
            if (nome && preco) {
                produtos.push({ id: Date.now(), nome, preco });
                renderProdutos();
                alert("Produto cadastrado!");
            }
        }

        // Lógica do Carrinho
        function addToCart(id) {
            const produto = produtos.find(p => p.id === id);
            carrinho.push(produto);
            document.getElementById('cart-count').innerText = carrinho.length;
            alert(`${produto.nome} adicionado!`);
        }

        function renderCarrinho() {
            const container = document.getElementById('itens-carrinho');
            const totalEl = document.getElementById('total-carrinho');
            container.innerHTML = carrinho.length === 0 ? '<p>Carrinho vazio</p>' : '';
            let total = 0;
            
            carrinho.forEach((item, index) => {
                total += item.preco;
                container.innerHTML += `
                    <div class="flex justify-between mb-2">
                        <span>${item.nome}</span>
                        <span>R$ ${item.preco.toFixed(2)}</span>
                    </div>
                `;
            });
            totalEl.innerText = total.toFixed(2);
        }

        function finalizarCompra() {
            if (carrinho.length === 0) return;
            const total = carrinho.reduce((sum, item) => sum + item.preco, 0);
            vendas.push({ data: new Date().toLocaleString(), total });
            
            // Atualiza painel adm
            const listaVendas = document.getElementById('lista-vendas');
            if(vendas.length === 1) listaVendas.innerHTML = '';
            listaVendas.innerHTML += `<li class="py-2">Venda em ${new Date().toLocaleString()} - Total: R$ ${total.toFixed(2)}</li>`;
            
            carrinho = [];
            document.getElementById('cart-count').innerText = 0;
            toggleCart();
            alert("Compra realizada com sucesso!");
        }

        // Inicialização
        renderProdutos();
    </script>
</body>
</html>
