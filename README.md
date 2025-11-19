<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Organizador Universal + Login + Admin</title>
    <script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/xlsx/dist/xlsx.full.min.js"></script>

    <style>
        body {
            margin: 0;
            font-family: Arial;
            background: #020202;
            color: white;
        }

        /* TELA DE LOGIN */
        #login-screen {
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            background: #000;
        }

        #login-box {
            background: #0a0a0a;
            padding: 40px;
            border-radius: 15px;
            width: 320px;
            box-shadow: 0 0 20px #00f7ff;
            text-align: center;
        }

        #login-box input {
            width: 95%;
            padding: 10px;
            margin: 8px 0;
            border: none;
            border-radius: 8px;
            background: #111;
            color: #0ff;
        }

        #login-box button {
            width: 100%;
            padding: 12px;
            margin-top: 10px;
            border: none;
            border-radius: 8px;
            background: #00eaff;
            color: #000;
            cursor: pointer;
            font-weight: bold;
        }

        /* CONTEÚDO PRINCIPAL */
        #main-screen {
            display: none;
            padding: 20px;
        }

        /* TOPBAR */
        .topbar {
            width: 100%;
            background: #000;
            color: #0ff;
            padding: 12px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 2px solid #0ff;
        }

        .topbar-title {
            font-size: 22px;
        }

        .admin-btn {
            font-size: 28px;
            background: none;
            border: none;
            color: #0ff;
            cursor: pointer;
            transition: 0.2s;
        }

        .admin-btn:hover {
            transform: rotate(15deg);
            color: #09f;
        }

        /* ORGANIZADOR */
        .container {
            max-width: 1100px;
            margin: auto;
        }

        .upload-box {
            border: 2px dashed #0ff;
            padding: 30px;
            border-radius: 15px;
            text-align: center;
            background: #0a0a0a;
            margin-top: 20px;
        }

        .btn {
            padding: 10px 20px;
            background: #00eaff;
            border: none;
            color: #000;
            border-radius: 8px;
            cursor: pointer;
            margin: 5px;
        }

        /* PAINEL ADMIN */
        #admin-panel {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,.9);
            display: none;
            padding: 30px;
        }

        .admin-box {
            max-width: 600px;
            margin: auto;
            background: #0a0a0a;
            padding: 20px;
            border-radius: 15px;
            box-shadow: 0 0 20px #0ff;
        }

        .admin-close {
            float: right;
            cursor: pointer;
            font-size: 25px;
            color: red;
        }

        .input-dark {
            width: 95%;
            padding: 10px;
            margin: 6px 0;
            background: #111;
            border: none;
            border-radius: 8px;
            color: #0ff;
        }

        .user-card {
            background: #111;
            padding: 10px;
            border-radius: 8px;
            margin: 8px 0;
        }

    </style>
</head>
<body>

<!-- TELA DE LOGIN -->
<div id="login-screen">
    <div id="login-box">
        <h2>🔐 Login</h2>

        <input id="user" placeholder="Usuário">
        <input id="pass" type="password" placeholder="Senha">

        <button onclick="login()">Entrar</button>
        <p style="margin-top:12px;color:#0ff;font-size:14px;">
            Usuário padrão: <b>CLX</b> — Senha: <b>02072007</b>
        </p>
    </div>
</div>


<!-- TELA PRINCIPAL (ORGANIZADOR ORIGINAL) -->
<div id="main-screen">

    <!-- TOPBAR -->
    <div class="topbar">
        <div class="topbar-title">📊 Organizador Universal</div>
        <button class="admin-btn" onclick="abrirAdmin()">⚙️</button>
    </div>

    <!-- ORGANIZADOR -->
    <div class="container">
        <div class="upload-box">
            <input type="file" id="fileInput" accept=".csv,.xlsx,.xls,.json,.txt,.tsv">
            <label>
                <h2>📎 Clique para selecionar o arquivo</h2>
                <p>CSV, Excel, JSON, TXT, TSV</p>
            </label>

            <button class="btn" onclick="processarArquivo()">Processar</button>
        </div>

        <div id="resultado"></div>
    </div>
</div>


<!-- PAINEL ADMIN -->
<div id="admin-panel">
    <div class="admin-box">
        <span class="admin-close" onclick="fecharAdmin()">✖</span>
        <h2>⚙️ Painel do Administrador</h2>

        <h3>Usuários Cadastrados:</h3>
        <div id="lista-usuarios"></div>

        <h3>Criar Novo Usuário</h3>
        <input id="novoUser" class="input-dark" placeholder="Nome de usuário">
        <input id="novoPass" class="input-dark" placeholder="Senha">

        <button class="btn" onclick="criarUsuario()">Criar Usuário</button>
    </div>
</div>



<script>
    /* ---------- BANCO LOCAL ---------- */
    if (!localStorage.getItem("usuarios")) {
        localStorage.setItem("usuarios", JSON.stringify({
            "CLX": "02072007"
        }));
    }

    /* ---------- LOGIN ---------- */
    function login() {
        let u = document.getElementById("user").value;
        let p = document.getElementById("pass").value;

        let banco = JSON.parse(localStorage.getItem("usuarios"));

        if (!banco[u]) {
            alert("Usuário não existe!");
            return;
        }

        if (banco[u] !== p) {
            alert("Senha incorreta!");
            return;
        }

        localStorage.setItem("logado", u);
        document.getElementById("login-screen").style.display = "none";
        document.getElementById("main-screen").style.display = "block";
    }

    /* ---------- ADMIN ---------- */
    function abrirAdmin() {
        let user = localStorage.getItem("logado");
        if (user !== "CLX") {
            alert("Apenas o ADMIN pode acessar!");
            return;
        }
        atualizarListaUsuarios();
        document.getElementById("admin-panel").style.display = "block";
    }

    function fecharAdmin() {
        document.getElementById("admin-panel").style.display = "none";
    }

    function atualizarListaUsuarios() {
        let banco = JSON.parse(localStorage.getItem("usuarios"));
        let div = document.getElementById("lista-usuarios");
        div.innerHTML = "";

        Object.keys(banco).forEach(u => {
            div.innerHTML += `
                <div class="user-card">
                    <b>${u}</b>
                    <br>Senha: ${"*".repeat(banco[u].length)}
                    <br><button class="btn" onclick="trocarSenha('${u}')">Trocar Senha</button>
                </div>
            `;
        });
    }

    function criarUsuario() {
        let u = document.getElementById("novoUser").value;
        let p = document.getElementById("novoPass").value;

        if (u.length < 3) return alert("Usuário muito curto!");
        if (p.length < 3) return alert("Senha muito curta!");

        let banco = JSON.parse(localStorage.getItem("usuarios"));
        if (banco[u]) return alert("Usuário já existe!");

        banco[u] = p;
        localStorage.setItem("usuarios", JSON.stringify(banco));

        atualizarListaUsuarios();
        alert("Usuário criado!");
    }

    function trocarSenha(user) {
        let nova = prompt("Nova senha para " + user);
        if (!nova || nova.length < 3) return alert("Senha inválida!");

        let banco = JSON.parse(localStorage.getItem("usuarios"));
        banco[user] = nova;
        localStorage.setItem("usuarios", JSON.stringify(banco));

        atualizarListaUsuarios();
        alert("Senha atualizada!");
    }

    /* ---------- ORGANIZAR ARQUIVOS ---------- */
    function processarArquivo() {
        const file = document.getElementById("fileInput").files[0];
        if (!file) return alert("Selecione um arquivo!");

        document.getElementById("resultado").innerHTML = `
            <h2 style='color:#0ff'>Arquivo carregado com sucesso!</h2>
            <p>${file.name}</p>
        `;
    }
</script>

</body>
</html>
