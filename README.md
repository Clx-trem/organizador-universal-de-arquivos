<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>📊 Organizador Universal de Arquivos + Login</title>
    <link rel="icon" href="https://public-frontend-cos.metadl.com/mgx/img/favicon.png" type="image/png">
    <script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/xlsx/dist/xlsx.full.min.js"></script>

    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f5f5f5;
            margin: 0;
            padding: 0;
        }
        /* LOGIN */
        #login {
            max-width: 400px;
            margin: 80px auto;
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0,0,0,0.2);
        }
        #login input {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border-radius: 6px;
            border: 1px solid #ccc;
        }
        #login button {
            width: 100%;
            background: #007bff;
            padding: 10px;
            border-radius: 6px;
            border: none;
            color: #fff;
            cursor: pointer;
            font-size: 16px;
        }

        /* APP */
        #app { display: none; }
        .container {
            max-width: 1200px;
            margin: auto;
            padding: 20px;
        }
        header {
            text-align: center;
            margin-bottom: 20px;
        }
        .tabs {
            display: flex;
            justify-content: center;
            flex-wrap: wrap;
        }
        .tab-button {
            background: #ddd;
            padding: 10px 20px;
            margin: 5px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
        }
        .tab-button.active { background: #007bff; color: white; }
        .tab-content { display: none; }
        .tab-content.active { display: block; }

        .upload-box {
            border: 2px dashed #999;
            background: white;
            padding: 30px;
            border-radius: 10px;
            text-align: center;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 10px;
        }
        th, td {
            border: 1px solid #aaa;
            padding: 6px;
        }
        .categoria {
            background: white;
            padding: 10px;
            border-radius: 8px;
            margin-bottom: 10px;
            border: 1px solid #ccc;
        }
    </style>
</head>
<body>

<!-- LOGIN -->
<div id="login">
    <h2>Login</h2>
    <input type="text" id="usuario" placeholder="Usuário">
    <input type="password" id="senha" placeholder="Senha">
    <button onclick="entrar()">Entrar</button>
</div>

<!-- APP -->
<div id="app">

    <div class="container">
        <header>
            <h1>📊 Organizador Universal de Arquivos</h1>
            <p>Carregue qualquer arquivo e organize automaticamente</p>
        </header>

        <nav class="tabs">
            <button class="tab-button active" onclick="mostrarAba('upload')">📁 Upload</button>
            <button class="tab-button" onclick="mostrarAba('organizacao')" id="tab-organizacao" disabled>⚙️ Organização</button>
            <button class="tab-button" onclick="mostrarAba('visualizacao')" id="tab-visualizacao" disabled>👁️ Visualização</button>
            <button class="tab-button" onclick="mostrarAba('exportacao')" id="tab-exportacao" disabled>💾 Exportação</button>
        </nav>

        <!-- UPLOAD -->
        <div id="aba-upload" class="tab-content active">
            <div class="upload-box">
                <input type="file" id="fileInput" accept=".csv,.xlsx,.xls,.json,.tsv,.txt">
                <label for="fileInput">
                    <div style="font-size:40px">📎</div>
                    <h3>Clique para selecionar arquivo</h3>
                </label>
            </div>
            <div id="file-info" style="display:none;margin-top:15px;">
                <h3>Arquivo Carregado:</h3>
                <div id="file-details"></div>
                <button onclick="processarArquivo()" class="btn-primary">Processar Arquivo</button>
            </div>
        </div>

        <!-- ORGANIZAÇÃO -->
        <div id="aba-organizacao" class="tab-content">
            <h2>Escolha o campo de organização</h2>
            <select id="campo-organizacao" onchange="atualizarPreview()"></select>
            <div id="preview-organizacao" style="margin-top:15px;"></div>
            <button onclick="aplicarOrganizacao()" id="btn-aplicar" disabled>Aplicar</button>
        </div>

        <!-- VISUALIZAÇÃO -->
        <div id="aba-visualizacao" class="tab-content">
            <input type="text" id="busca-global" placeholder="Buscar..." oninput="buscarGlobal()" style="width:100%;padding:10px;">
            <div id="tabelas" style="margin-top:15px;"></div>
        </div>

        <!-- EXPORTAÇÃO -->
        <div id="aba-exportacao" class="tab-content">
            <h2>Exportar Dados</h2>
            <button onclick="exportarExcel()">📊 Excel</button>
            <button onclick="exportarCSV()">📄 CSV</button>
            <button onclick="exportarJSON()">🔗 JSON</button>
        </div>

    </div>
</div>

<script>
// LOGIN
function entrar(){
    const u=document.getElementById('usuario').value;
    const s=document.getElementById('senha').value;
    if(u==="admin" && s==="123"){
        document.getElementById('login').style.display="none";
        document.getElementById('app').style.display="block";
    } else {
        alert("Usuário ou senha incorretos!");
    }
}

// SISTEMA PRINCIPAL
let dadosOriginais = [];
let dadosOrganizados = {};
let nomeArquivo = "";

function mostrarAba(aba){
    document.querySelectorAll('.tab-button').forEach(b => b.classList.remove('active'));
    document.querySelector(`[onclick="mostrarAba('${aba}')"]`).classList.add('active');

    document.querySelectorAll('.tab-content').forEach(t => t.classList.remove('active'));
    document.getElementById(`aba-${aba}`).classList.add('active');
}

document.getElementById("fileInput").addEventListener("change", e => {
    const file = e.target.files[0];
    if(!file) return;
    nomeArquivo = file.name;
    document.getElementById("file-details").innerText = file.name;
    document.getElementById("file-info").style.display = "block";
});

function processarArquivo(){
    const file = document.getElementById("fileInput").files[0];
    if(!file) return alert("Selecione um arquivo!");

    const ext = file.name.split('.').pop().toLowerCase();

    if(["csv","tsv","txt"].includes(ext)){
        Papa.parse(file,{
            header:true,
            complete:r=>{dadosOriginais=r.data;prepararOrganizacao();}
        });
    }
    else if(["xlsx","xls"].includes(ext)){
        const reader = new FileReader();
        reader.onload = e =>{
            const wb = XLSX.read(e.target.result,{type:'binary'});
            const sheet = wb.Sheets[wb.SheetNames[0]];
            dadosOriginais = XLSX.utils.sheet_to_json(sheet);
            prepararOrganizacao();
        };
        reader.readAsBinaryString(file);
    }
    else if(ext==="json"){
        const reader = new FileReader();
        reader.onload = e =>{
            dadosOriginais = JSON.parse(e.target.result);
            prepararOrganizacao();
        };
        reader.readAsText(file);
    }
}

function prepararOrganizacao(){
    const campos = Object.keys(dadosOriginais[0]);
    const select = document.getElementById("campo-organizacao");
    select.innerHTML = '<option value="">Selecione...</option>';

    campos.forEach(c=>{
        const o=document.createElement("option");
        o.value=c;
        o.textContent=c;
        select.appendChild(o);
    });

    document.getElementById("tab-organizacao").disabled = false;
    mostrarAba('organizacao');
}

function atualizarPreview(){
    const campo=document.getElementById("campo-organizacao").value;
    if(!campo){document.getElementById("btn-aplicar").disabled=true;return;}

    document.getElement
