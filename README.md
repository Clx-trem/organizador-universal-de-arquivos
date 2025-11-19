<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>📊 Organizador Universal com Admin</title>
<link rel="icon" href="https://public-frontend-cos.metadl.com/mgx/img/favicon.png" type="image/png">
<script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/xlsx/dist/xlsx.full.min.js"></script>
<style>
    body { font-family: Arial; margin:0; padding:0; background:#f5f5f5; color:#000; }

    /* LOGIN FULLSCREEN */
    #login-screen {
        position: fixed; top:0; left:0; right:0; bottom:0;
        display:flex; justify-content:center; align-items:center;
        background:#000; color:#0ff; z-index:9999;
    }
    #login-box {
        background:#0a0a0a; padding:60px; border-radius:15px;
        width:90%; max-width:400px; text-align:center; box-shadow:0 0 40px #00f7ff;
    }
    #login-box h2 { margin-bottom:20px; }
    #login-box input {
        width:95%; padding:15px; margin:12px 0; border-radius:10px;
        border:none; background:#111; color:#0ff; font-size:16px;
    }
    #login-box button {
        width:95%; padding:15px; margin-top:15px; border-radius:10px;
        border:none; background:#00eaff; color:#000; cursor:pointer;
        font-weight:bold; font-size:16px; transition:0.2s;
    }
    #login-box button:hover { background:#09f; }

    /* MAIN SCREEN */
    #main-screen { display:none; }

    /* ADMIN BUTTON */
    .admin-btn {
        position: fixed; top: 20px; right:20px;
        font-size:32px; background:none; border:none; color:#00eaff;
        cursor:pointer; transition:0.3s; z-index:1000;
    }
    .admin-btn:hover { transform:rotate(15deg); color:#09f; }

    /* PAINEL ADMIN */
    #admin-panel {
        display:none; position:fixed; top:0; left:0; right:0; bottom:0;
        background: rgba(0,0,0,0.95); color:#0ff; padding:40px;
        z-index:2000; overflow-y:auto;
    }
    .admin-box { max-width:700px; margin:auto; background:#0a0a0a;
        padding:20px; border-radius:15px; box-shadow:0 0 20px #0ff;
    }
    .admin-close { float:right; cursor:pointer; font-size:25px; color:red; }
    .input-dark { width:95%; padding:10px; margin:6px 0; border:none; border-radius:8px; background:#111; color:#0ff; }
    .user-card { background:#111; padding:10px; border-radius:8px; margin:8px 0; border:1px solid #0ff; }
    .btn { padding:8px 12px; margin:4px; background:#00eaff; border:none; border-radius:6px; cursor:pointer; color:#000; font-weight:bold; }
    .btn-secondary { background:#555; color:#fff; }
</style>
</head>
<body>

<!-- LOGIN -->
<div id="login-screen">
    <div id="login-box">
        <h2>🔐 Login</h2>
        <input id="user" placeholder="Usuário">
        <input id="pass" type="password" placeholder="Senha">
        <button onclick="login()">Entrar</button>
    </div>
</div>

<!-- ADMIN BUTTON -->
<button class="admin-btn" onclick="abrirAdmin()" id="admin-btn" style="display:none;">⚙️</button>

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

<!-- CONTEÚDO ORIGINAL DO ORGANIZADOR -->
<div id="main-screen">
    <div class="container">
        <header>
            <h1>📊 Organizador Universal de Arquivos</h1>
            <p>Carregue qualquer arquivo e organize pelos campos que desejar</p>
        </header>

        <!-- Navegação -->
        <nav class="tabs">
            <button class="tab-button active" onclick="mostrarAba('upload')">📁 Upload</button>
            <button class="tab-button" onclick="mostrarAba('organizacao')" id="tab-organizacao" disabled>⚙️ Organização</button>
            <button class="tab-button" onclick="mostrarAba('visualizacao')" id="tab-visualizacao" disabled>👁️ Visualização</button>
            <button class="tab-button" onclick="mostrarAba('exportacao')" id="tab-exportacao" disabled>💾 Exportação</button>
        </nav>

        <!-- Aba Upload -->
        <div id="aba-upload" class="tab-content active">
            <div class="upload-box">
                <input type="file" id="fileInput" accept=".csv,.xlsx,.xls,.json,.tsv,.txt">
                <label for="fileInput">
                    <div class="upload-icon">📎</div>
                    <h3>Clique para selecionar arquivo</h3>
                    <p>Suporta: CSV, Excel (.xlsx, .xls), JSON, TSV</p>
                </label>
            </div>
            <div id="file-info" style="display: none; margin-top: 15px;">
                <h3>Arquivo Carregado:</h3>
                <div id="file-details"></div>
                <button onclick="processarArquivo()" class="btn-primary">Processar Arquivo</button>
            </div>
        </div>

        <!-- Aba Organização -->
        <div id="aba-organizacao" class="tab-content">
            <h2>Escolha como organizar seus dados</h2>
            <label for="campo-organizacao">Organizar por:</label>
            <select id="campo-organizacao" onchange="atualizarPreview()">
                <option value="">Selecione um campo...</option>
            </select>
            <div id="preview-organizacao" style="margin-top: 15px;"></div>
            <button onclick="aplicarOrganizacao()" class="btn-primary" id="btn-aplicar" disabled>Aplicar Organização</button>
        </div>

        <!-- Aba Visualização -->
        <div id="aba-visualizacao" class="tab-content">
            <input type="text" id="busca-global" placeholder="Buscar..." oninput="buscarGlobal()" style="width:100%;padding:8px;margin-bottom:10px;">
            <button onclick="expandirTodas()" class="btn-secondary">Expandir Todas</button>
            <button onclick="recolherTodas()" class="btn-secondary">Recolher Todas</button>
            <div id="tabelas" style="margin-top:15px;"></div>
        </div>

        <!-- Aba Exportação -->
        <div id="aba-exportacao" class="tab-content">
            <h2>Exportar Dados Organizados</h2>
            <div>
                <button onclick="exportarExcel()" class="btn-primary">📊 Baixar Excel</button>
                <button onclick="exportarCSV()" class="btn-primary">📄 Baixar CSV</button>
                <button onclick="exportarJSON()" class="btn-primary">🔗 Baixar JSON</button>
            </div>
        </div>
    </div>
</div>

<script>
/* ====== LOGIN ====== */
if(!localStorage.getItem("usuarios")) {
    localStorage.setItem("usuarios", JSON.stringify({"CLX": {senha:"02072007", bloqueado:false}}));
}
function login(){
    const u=document.getElementById("user").value;
    const p=document.getElementById("pass").value;
    const banco=JSON.parse(localStorage.getItem("usuarios"));
    if(!banco[u]) return alert("Usuário não existe!");
    if(banco[u].bloqueado) return alert("Usuário bloqueado!");
    if(banco[u].senha!==p) return alert("Senha incorreta!");
    document.getElementById("login-screen").style.display="none";
    document.getElementById("main-screen").style.display="block";
    if(u==="CLX") document.getElementById("admin-btn").style.display="block";
}

/* ====== ADMIN ====== */
function abrirAdmin(){ atualizarListaUsuarios(); document.getElementById("admin-panel").style.display="block"; }
function fecharAdmin(){ document.getElementById("admin-panel").style.display="none"; }

function atualizarListaUsuarios(){
    const banco=JSON.parse(localStorage.getItem("usuarios"));
    const div=document.getElementById("lista-usuarios");
    div.innerHTML="";
    Object.keys(banco).forEach(u=>{
        const bloqueado=banco[u].bloqueado?"(Bloqueado)":"";
        div.innerHTML+=`
            <div class="user-card">
                <b>${u}</b> ${bloqueado}<br>
                <button class="btn" onclick="trocarSenha('${u}')">Trocar Senha</button>
                <button class="btn" onclick="editarUsuario('${u}')">Editar Nome</button>
                <button class="btn" onclick="bloquearUsuario('${u}')">${banco[u].bloqueado?'Desbloquear':'Bloquear'}</button>
                <button class="btn" onclick="excluirUsuario('${u}')">Excluir</button>
            </div>
        `;
    });
}

function criarUsuario(){
    const u=document.getElementById("novoUser").value;
    const p=document.getElementById("novoPass").value;
    if(u.length<3 || p.length<3) return alert("Usuário/Senha muito curto!");
    const banco=JSON.parse(localStorage.getItem("usuarios"));
    if(banco[u]) return alert("Usuário já existe!");
    banco[u]={senha:p, bloqueado:false};
    localStorage.setItem("usuarios", JSON.stringify(banco));
    atualizarListaUsuarios();
    alert("Usuário criado!");
}

function trocarSenha(u){
    const nova=prompt("Nova senha para "+u);
    if(!nova || nova.length<3) return alert("Senha inválida!");
    const banco=JSON.parse(localStorage.getItem("usuarios"));
    banco[u].senha=nova;
    localStorage.setItem("usuarios", JSON.stringify(banco));
    atualizarListaUsuarios();
    alert("Senha atualizada!");
}

function editarUsuario(u){
    const novoNome=prompt("Novo nome para "+u);
    if(!novoNome || novoNome.length<3) return alert("Nome inválido!");
    const banco=JSON.parse(localStorage.getItem("usuarios"));
    if(banco[novoNome]) return alert("Usuário já existe!");
    banco[novoNome]=banco[u];
    delete banco[u];
    localStorage.setItem("usuarios", JSON.stringify(banco));
    atualizarListaUsuarios();
}

function bloquearUsuario(u){
    const banco=JSON.parse(localStorage.getItem("usuarios"));
    banco[u].bloqueado=!banco[u].bloqueado;
    localStorage.setItem("usuarios", JSON.stringify(banco));
    atualizarListaUsuarios();
}

function excluirUsuario(u){
    if(!confirm("Tem certeza que deseja excluir "+u+"?")) return;
    const banco=JSON.parse(localStorage.getItem("usuarios"));
    delete banco[u];
    localStorage.setItem("usuarios", JSON.stringify(banco));
    atualizarListaUsuarios();
}

/* ====== ORGANIZADOR ====== */
let dadosOriginais=[]; let dadosOrganizados={}; let nomeArquivo="";
function mostrarAba(aba){document.querySelectorAll(".tab-button").forEach(btn=>btn.classList.remove("active"));document.querySelectorAll(".tab-content").forEach(tab=>tab.classList.remove("active"));document.querySelector(`[onclick="mostrarAba('${aba}')"]`).classList.add("active");document.querySelector(`#aba-${aba}`).classList.add("active");}
document.getElementById("fileInput").addEventListener("change",e=>{const file=e.target.files[0]; if(!file)return; nomeArquivo=file.name; document.getElementById("file-details").innerText=`Nome: ${file.name} (${(file.size/1024).toFixed(1)} KB)`; document.getElementById("file-info").style.display="block"; });
function processarArquivo(){const file=document.getElementById("fileInput").files[0]; if(!file)return alert("Selecione um arquivo primeiro!"); const ext=file.name.split(".").pop().toLowerCase(); if(["csv","tsv","txt"].includes(ext)){Papa.parse(file,{header:true,skipEmptyLines:true,complete: results=>{dadosOriginais=results.data; prepararOrganizacao();}});}else if(["xlsx","xls"].includes(ext)){const reader=new FileReader();reader.onload=e=>{const workbook=XLSX.read(e.target.result,{type:"binary"});const sheet=workbook.Sheets[workbook.SheetNames[0]];dadosOriginais=XLSX.utils.sheet_to_json(sheet);prepararOrganizacao();};reader.readAsBinaryString(file);}else if(ext==="json"){const reader=new FileReader();reader.onload=e=>{dadosOriginais=JSON.parse(e.target.result);prepararOrganizacao();};reader.readAsText(file);}else{alert("Tipo de arquivo não suportado!");}}
function prepararOrganizacao(){const campos=Object.keys(dadosOriginais[0]);const select=document.getElementById("campo-organizacao");select.innerHTML='<option value="">Selecione um campo...</option>';campos.forEach(campo=>{const opt=document.createElement("option");opt.value=campo;opt.textContent=campo;select.appendChild(opt);});document.getElementById("tab-organizacao").disabled=false;mostrarAba("organizacao");}
function atualizarPreview(){const campo=document.getElementById("campo-organizacao").value;const previewDiv=document.getElementById("preview-organizacao");if(!campo){previewDiv.innerHTML="";document.getElementById("btn-aplicar").disabled=true;return;}const categorias=[...new Set(dadosOriginais.map(d=>d[campo]))];previewDiv.innerHTML=`<h3>Encontradas ${categorias.length} categorias:</h3><ul>${categorias.map(c=>`<li>${c}</li>`).join("")}</ul>`;document.getElementById("btn-aplicar").disabled=false;}
function aplicarOrganizacao(){const campo=document.getElementById("campo-organizacao").value;dadosOrganizados={};dadosOriginais.forEach(item=>{const chave=item[campo]||"Sem categoria";if(!dadosOrganizados[chave])dadosOrganizados[chave]=[];dadosOrganizados[chave].push(item);});document.getElementById("tab-visualizacao").disabled=false;document.getElementById("tab-exportacao").disabled=false;mostrarAba("visualizacao");renderizarVisualizacao();}
function renderizarVisualizacao(){const container=document.getElementById("tabelas");container.innerHTML="";Object.keys(dadosOrganizados).forEach(categoria=>{const tabela=document.createElement("div");tabela.className="categoria";const dados=dadosOrganizados[categoria];const colunas=Object.keys(dados[0]);const linhasHTML=dados.map(linha=>`<tr>${colunas.map(c=>`<td>${linha[c]??""}</td>`).join("")}</tr>`).join("");tabela.innerHTML=`<h3 class="categoria-titulo">${categoria} (${dados.length})</h3><table><thead><tr>${colunas.map(c=>`<th>${c}</th>`).join("")}</tr></thead><tbody>${linhasHTML}</tbody></table>`;container.appendChild(tabela);});}
function buscarGlobal(){const termo=document.getElementById("busca-global").value.toLowerCase();document.querySelectorAll(".categoria").forEach(cat=>{cat.style.display=cat.innerText.toLowerCase().includes(termo)?"":"none";});}
function expandirTodas(){document.querySelectorAll(".categoria table").forEach(tbl=>tbl.style.display="table");}
function recolherTodas(){document.querySelectorAll(".categoria table").forEach(tbl=>tbl.style.display="none");}
function exportarExcel(){const wb=XLSX.utils.book_new();Object.keys(dadosOrganizados).forEach(cat=>{const ws=XLSX.utils.json_to_sheet(dadosOrganizados[cat]);XLSX.utils.book_append_sheet(wb,ws,cat.slice(0,31));});XLSX.writeFile(wb,`${nomeArquivo.split('.')[0]}_organizado.xlsx`);}
function exportarCSV(){const todos=Object.values(dadosOrganizados).flat();const csv=Papa.unparse(todos);const blob=new Blob([csv],{type:"text/csv;charset=utf-8;"});const url=URL.createObjectURL(blob);const a=document.createElement("a");a.href=url;a.download=`${nomeArquivo.split('.')[0]}_organizado.csv`;a.click();}
function exportarJSON(){const blob=new Blob([JSON.stringify(dadosOrganizados,null,2)],{type:"application/json"});const url=URL.createObjectURL(blob);const a=document.createElement("a");a.href=url;a.download=`${nomeArquivo.split('.')[0]}_organizado.json`;a.click();}
</script>
</body>
</html>
