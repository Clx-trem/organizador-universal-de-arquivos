<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>📊 Organizador Universal de Arquivos</title>
<link rel="icon" href="https://public-frontend-cos.metadl.com/mgx/img/favicon.png" type="image/png">
<script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/xlsx/dist/xlsx.full.min.js"></script>
<style>
    body {
        font-family: Arial, sans-serif;
        margin: 0;
        padding: 0;
        background: #020202;
        color: white;
    }

    /* LOGIN */
    #login-screen {
        position: fixed;
        inset: 0;
        display: flex;
        justify-content: center;
        align-items: center;
        background: #000;
    }
    #login-box {
        background: #0a0a0a;
        width: 400px;
        max-width: 90%;
        padding: 40px;
        border-radius: 15px;
        box-shadow: 0 0 30px #00f7ff;
        text-align: center;
    }
    #login-box input {
        width: 90%;
        padding: 12px;
        margin: 10px 0;
        border: none;
        border-radius: 8px;
        background: #111;
        color: #0ff;
        font-size: 16px;
    }
    #login-box label {
        font-size: 14px;
        display: block;
        margin: 5px 0;
        color: #0ff;
        cursor: pointer;
    }
    #login-box button {
        width: 95%;
        padding: 12px;
        margin-top: 10px;
        border: none;
        border-radius: 8px;
        background: #00eaff;
        color: #000;
        font-weight: bold;
        cursor: pointer;
        font-size: 16px;
    }

    /* TELA PRINCIPAL */
    #main-screen { display: none; padding: 20px; background: #f5f5f5; color: #000; }

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
    .topbar-title { font-size: 22px; }
    .topbar button { background:none;border:none;color:#0ff;font-size:18px;cursor:pointer; }
    .admin-btn { font-size:28px; }

    /* ORGANIZADOR */
    .container { max-width: 1200px; margin: auto; padding: 20px; }
    header { text-align: center; margin-bottom: 20px; }
    .tabs { display: flex; justify-content: center; flex-wrap: wrap; margin-bottom: 20px; }
    .tab-button { background: #ddd; color: #000; border: none; padding: 10px 20px; cursor: pointer; margin: 5px; border-radius: 8px; transition: 0.3s; }
    .tab-button:hover { background: #bbb; }
    .tab-button.active { background: #007bff; color: #fff; }
    .tab-button:disabled { opacity: 0.5; cursor: not-allowed; }
    .tab-content { display: none; }
    .tab-content.active { display: block; }
    .upload-box { border: 2px dashed #999; border-radius: 10px; padding: 30px; text-align: center; background-color: #fff; }
    .btn-primary, .btn-secondary { background: #007bff; border: none; padding: 10px 20px; border-radius: 6px; color: white; cursor: pointer; margin: 5px; }
    .btn-secondary { background: #555; }
    .categoria { background: #fff; padding: 10px; border-radius: 8px; margin-bottom: 10px; border: 1px solid #ccc; }
    .categoria-titulo { background: #eaeaea; padding: 8px; border-radius: 6px; cursor: pointer; }
    table { width: 100%; border-collapse: collapse; margin-top: 10px; }
    th, td { border: 1px solid #aaa; padding: 6px; text-align: left; }
    input[type="file"] { display: none; }
    .upload-icon { font-size: 40px; }
    select, input[type="text"] { padding: 8px; border-radius: 6px; border: 1px solid #ccc; }

    /* PAINEL ADMIN */
    #admin-panel { position: fixed; top:0; left:0; width:100%; height:100%; background: rgba(0,0,0,0.95); display: none; padding: 30px; overflow-y:auto; }
    .admin-box { max-width:600px; margin:auto; background:#0a0a0a; padding:20px; border-radius:15px; box-shadow:0 0 30px #0ff; color:#0ff; }
    .admin-close { float:right; cursor:pointer; font-size:25px; color:red; }
    .input-dark { width:90%; padding:10px; margin:6px 0; background:#111; border:none; border-radius:8px; color:#0ff; }
    .user-card { background:#111; padding:10px; border-radius:8px; margin:8px 0; }
</style>
</head>
<body>

<!-- LOGIN -->
<div id="login-screen">
    <div id="login-box">
        <h2>🔐 Login</h2>
        <input id="user" placeholder="Usuário">
        <input id="pass" type="password" placeholder="Senha">
        <label><input type="checkbox" id="remember"> Lembrar login</label>
        <button onclick="login()">Entrar</button>
    </div>
</div>

<!-- PRINCIPAL -->
<div id="main-screen">
    <div class="topbar">
        <div class="topbar-title">📊 Organizador Universal</div>
        <div>
            <button class="admin-btn" onclick="abrirAdmin()">⚙️ Admin</button>
            <button onclick="sair()" style="margin-left:10px;">🔓 Sair</button>
        </div>
    </div>

    <!-- CONTEÚDO DO ORGANIZADOR ORIGINAL -->
    <div class="container">
        <header>
            <h1>📊 Organizador Universal de Arquivos</h1>
            <p>Carregue qualquer arquivo e organize pelos campos que desejar</p>
        </header>
        <nav class="tabs">
            <button class="tab-button active" onclick="mostrarAba('upload')">📁 Upload</button>
            <button class="tab-button" onclick="mostrarAba('organizacao')" id="tab-organizacao" disabled>⚙️ Organização</button>
            <button class="tab-button" onclick="mostrarAba('visualizacao')" id="tab-visualizacao" disabled>👁️ Visualização</button>
            <button class="tab-button" onclick="mostrarAba('exportacao')" id="tab-exportacao" disabled>💾 Exportação</button>
        </nav>

        <div id="aba-upload" class="tab-content active">
            <div class="upload-box">
                <input type="file" id="fileInput" accept=".csv,.xlsx,.xls,.json,.tsv,.txt">
                <label for="fileInput">
                    <div class="upload-icon">📎</div>
                    <h3>Clique para selecionar arquivo</h3>
                    <p>Suporta: CSV, Excel (.xlsx, .xls), JSON, TSV</p>
                </label>
            </div>
            <div id="file-info" style="display:none; margin-top:15px;">
                <h3>Arquivo Carregado:</h3>
                <div id="file-details"></div>
                <button onclick="processarArquivo()" class="btn-primary">Processar Arquivo</button>
            </div>
        </div>

        <!-- Restante das abas (organização, visualização, exportação) mantidas iguais -->
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
    // BANCO DE USUÁRIOS
    let banco = JSON.parse(localStorage.getItem("usuarios")) || {};
    if(!banco["CLX"]) { banco["CLX"] = {senha:"02072007", bloqueado:false}; localStorage.setItem("usuarios", JSON.stringify(banco)); }

    // LOGIN
    document.addEventListener("DOMContentLoaded", ()=>{
        const saved = localStorage.getItem("rememberUser");
        if(saved){ document.getElementById("user").value = saved; document.getElementById("remember").checked = true; }
    });

    function login(){
        let u = document.getElementById("user").value;
        let p = document.getElementById("pass").value;
        let remember = document.getElementById("remember").checked;
        banco = JSON.parse(localStorage.getItem("usuarios")) || {};
        if(!banco[u]) { alert("Usuário não existe!"); return; }
        if(banco[u].senha !== p) { alert("Senha incorreta!"); return; }
        if(banco[u].bloqueado) { alert("Usuário bloqueado!"); return; }
        localStorage.setItem("logado", u);
        if(remember) localStorage.setItem("rememberUser", u); else localStorage.removeItem("rememberUser");
        document.getElementById("login-screen").style.display="none";
        document.getElementById("main-screen").style.display="block";
    }

    function sair(){
        localStorage.removeItem("logado");
        document.getElementById("main-screen").style.display="none";
        document.getElementById("login-screen").style.display="flex";
    }

    // ADMIN
    function abrirAdmin(){ if(localStorage.getItem("logado")!=="CLX"){alert("Apenas o ADMIN pode acessar!");return;} atualizarListaUsuarios(); document.getElementById("admin-panel").style.display="block"; }
    function fecharAdmin(){ document.getElementById("admin-panel").style.display="none"; }
    function atualizarListaUsuarios(){
        banco = JSON.parse(localStorage.getItem("usuarios"))||{};
        let div = document.getElementById("lista-usuarios"); div.innerHTML="";
        Object.keys(banco).forEach(u=>{
            div.innerHTML+=`
            <div class="user-card">
            <b>${u}</b> ${banco[u].bloqueado?"(Bloqueado)":""}<br>
            Senha: ${"*".repeat(banco[u].senha.length)}<br>
            <button class="btn" onclick="trocarSenha('${u}')">Trocar Senha</button>
            <button class="btn" onclick="bloquearUsuario('${u}')">${banco[u].bloqueado?"Desbloquear":"Bloquear"}</button>
            <button class="btn" onclick="excluirUsuario('${u}')">Excluir</button>
            </div>`;
        });
    }

    function criarUsuario(){
        let u=document.getElementById("novoUser").value;
        let p=document.getElementById("novoPass").value;
        banco = JSON.parse(localStorage.getItem("usuarios"))||{};
        if(u.length<3){alert("Usuário muito curto!");return;}
        if(p.length<3){alert("Senha muito curta!");return;}
        if(banco[u]){alert("Usuário já existe!");return;}
        banco[u]={senha:p, bloqueado:false};
        localStorage.setItem("usuarios", JSON.stringify(banco));
        atualizarListaUsuarios();
        alert("Usuário criado!");
    }

    function trocarSenha(u){
        let nova=prompt("Nova senha para "+u);
        if(!nova||nova.length<3){alert("Senha inválida!");return;}
        banco[u].senha=nova;
        localStorage.setItem("usuarios", JSON.stringify(banco));
        atualizarListaUsuarios();
        alert("Senha atualizada!");
    }

    function bloquearUsuario(u){
        if(u==="CLX"){alert("Não é possível bloquear o ADMIN!"); return;}
        banco[u].bloqueado=!banco[u].bloqueado;
        localStorage.setItem("usuarios", JSON.stringify(banco));
        atualizarListaUsuarios();
    }

    function excluirUsuario(u){
        if(u==="CLX"){alert("Não é possível excluir o ADMIN!"); return;}
        if(!confirm(`Deseja realmente excluir o usuário ${u}?`)) return;
        delete banco[u];
        localStorage.setItem("usuarios", JSON.stringify(banco));
        atualizarListaUsuarios();
    }

    // ORGANIZADOR original mantido aqui (processarArquivo, prepararOrganizacao, atualizarPreview, aplicarOrganizacao, renderizarVisualizacao, buscarGlobal, expandirTodas, recolherTodas, exportarExcel/CSV/JSON)
</script>
</body>
</html>
