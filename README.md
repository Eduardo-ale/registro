
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Registro de Chamados</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.17.0/xlsx.full.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            height: 100vh;
        }
        .container {
            width: 90%;
            max-width: 1000px;
            background: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            overflow-x: auto;
        }
        h1 {
            text-align: center;
            color: #333;
        }
        .dashboard {
            display: flex;
            justify-content: space-around;
            margin-bottom: 20px;
        }
        .dashboard div {
            background: #4CAF50;
            color: white;
            padding: 15px;
            border-radius: 5px;
            text-align: center;
            width: 30%;
        }
        label {
            font-weight: bold;
            display: block;
            margin-top: 10px;
        }
        input, textarea, button {
            width: 100%;
            padding: 10px;
            margin-top: 5px;
            border: 1px solid #ddd;
            border-radius: 5px;
            transition: all 0.3s ease;
        }
        input:focus, textarea:focus {
            border-color: #4CAF50;
            box-shadow: 0 0 5px rgba(76, 175, 80, 0.5);
            outline: none;
        }
        button {
            background: #4CAF50;
            color: white;
            font-weight: bold;
            border: none;
            cursor: pointer;
        }
        button:hover {
            background: #45a049;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 10px;
            text-align: left;
        }
        th {
            background-color: #4CAF50;
            color: white;
        }
        .open { color: green; }
        .closed { color: red; }
        .actions {
            text-align: center;
        }
    </style>
</head>
<body>
    <div class="dashboard">
        <div>Total Chamados: <span id="totalChamados">0</span></div>
        <div>Abertos: <span id="chamadosAbertos">0</span></div>
        <div>Fechados: <span id="chamadosFechados">0</span></div>
    </div>
    <div class="container">
        <h1>Registro de Chamados</h1>
        <label for="titulo">Título:</label>
        <input type="text" id="titulo" placeholder="Digite o título do chamado">
        <label for="descricao">Descrição:</label>
        <textarea id="descricao" placeholder="Descreva o que foi feito"></textarea>
        <label for="url">URL do Chamado:</label>
        <input type="text" id="url" placeholder="Cole a URL do chamado">
        <button onclick="adicionarChamado()">Adicionar Chamado</button>
        <table>
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Título</th>
                    <th>Descrição</th>
                    <th>URL</th>
                    <th>Data de Abertura</th>
                    <th>Data de Encerramento</th>
                    <th>Status</th>
                    <th class="actions">Ações</th>
                </tr>
            </thead>
            <tbody id="chamados"></tbody>
        </table>
    </div>
    <script>
        let chamados = [];

        function adicionarChamado() {
            const titulo = document.getElementById("titulo").value;
            const descricao = document.getElementById("descricao").value;
            const url = document.getElementById("url").value;
            if (!titulo || !descricao || !url) return alert("Por favor, preencha todos os campos.");

            const id = chamados.length > 0 ? chamados[chamados.length - 1].id + 1 : 1;
            const dataAbertura = new Date().toLocaleString();
            const novoChamado = { id, titulo, descricao, url, dataAbertura, dataEncerramento: "-", status: "Aberto" };
            chamados.push(novoChamado);
            atualizarTabela();
        }

        function alterarStatus(id) {
            const chamado = chamados.find(ch => ch.id === id);
            if (chamado) {
                chamado.status = chamado.status === "Aberto" ? "Fechado" : "Aberto";
                chamado.dataEncerramento = chamado.status === "Fechado" ? new Date().toLocaleString() : "-";
                atualizarTabela();
            }
        }

        function atualizarTabela() {
            const tbody = document.getElementById("chamados");
            tbody.innerHTML = "";
            let abertos = 0;
            let fechados = 0;
            chamados.forEach(chamado => {
                if (chamado.status === "Aberto") abertos++;
                else fechados++;
                tbody.innerHTML += `
                    <tr>
                        <td>${chamado.id}</td>
                        <td>${chamado.titulo}</td>
                        <td>${chamado.descricao}</td>
                        <td><a href="${chamado.url}" target="_blank">Acessar</a></td>
                        <td>${chamado.dataAbertura}</td>
                        <td>${chamado.dataEncerramento}</td>
                        <td class="${chamado.status === 'Aberto' ? 'open' : 'closed'}">${chamado.status}</td>
                        <td class="actions"><button onclick="alterarStatus(${chamado.id})">Alterar Status</button></td>
                    </tr>
                `;
            });
            document.getElementById("totalChamados").textContent = chamados.length;
            document.getElementById("chamadosAbertos").textContent = abertos;
            document.getElementById("chamadosFechados").textContent = fechados;
        }
    </script>
</body>
</html>
