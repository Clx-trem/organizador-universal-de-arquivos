<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Organizador Universal — Login & Admin (CLX)</title>

<!-- Bibliotecas do organizador -->
<script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/xlsx/dist/xlsx.full.min.js"></script>

<style>
  /* ====== TEMA NEON AZUL + ROXO ====== */
  :root{
    --bg:#050518;
    --card:#07102a;
    --neon1:#00a8ff;
    --neon2:#7a2cff;
    --muted: #98a0b3;
    --glass: rgba(255,255,255,0.03);
  }
  *{box-sizing:border-box;font-family:Inter, system-ui, Arial, sans-serif}
  html,body{height:100%;margin:0;background:radial-gradient(circle at 10% 10%, #02031a 0%, #000 50%);color:#eaf2ff}
  .wrap{min-height:100vh;display:flex;align-items:center;justify-content:center;padding:28px;}
  .card{
    width:100%; max-width:1100px; border-radius:14px;
    background: linear-gradient(180deg, rgba(10,10,25,0.85), rgba(6,6,20,0.85));
    box-shadow:0 8px 40px rgba(0,0,0,0.6), inset 0 1px 0 rgba(255,255,255,0.02);
    overflow:hidden;
    border:1px solid rgba(255,255,255,0.03);
    display:grid; grid-template-columns: 360px 1fr;
    gap: 0;
    animation: appear .45s ease both;
  }
  @keyframes appear { from {opacity:0; transform: translateY(18px)} to {opacity:1; transform:none} }

  .side {
    padding:22px; border-right:1px solid rgba(255,255,255,0.03);
    background: linear-gradient(180deg, rgba(10,6,30,0.45), rgba(6,6,20,0.25));
    min-height:640px;
  }
  .logo {
    display:flex; gap:12px; align-items:center; margin-bottom:18px;
  }
  .logo .mark{
    width:56px;height:56px;border-radius:10px;
    background: linear-gradient(135deg,var(--neon2),var(--neon1));
    box-shadow:0 6px 28px rgba(0,168,255,0.12), 0 0 18px rgba(122,44,255,0.08) inset;
    display:flex;align-items:center;justify-content:center;font-weight:800;color:#001;
    transform:rotate(-12deg);
  }
  .logo h1{font-size:18px;margin:0;color:var(--neon1);letter-spacing:0.2px}
  label{display:block;font-size:13px;color:var(--muted);margin-top:14px}
  input, select, textarea {
    width:100%; padding:10px 12px; margin-top:8px; border-radius:10px;
    background: linear-gradient(180deg,#021026, #06122a);
    border:1px solid rgba(255,255,255,0.04); color:#e8f7ff;
    outline:none; box-shadow: 0 6px 18px rgba(0,0,0,0.5) inset;
  }
  input:focus { box-shadow:0 0 18px rgba(0,168,255,0.10); border-color:var(--neon1) }
  .btn {
    display:inline-block; width:100%; padding:11px; margin-top:16px; border-radius:10px;
    background: linear-gradient(90deg,var(--neon2),var(--neon1));
    color:#001; font-weight:700; border:none; cursor:pointer;
    box-shadow:0 6px 18px rgba(0,120,255,0.12);
    transition: transform .14s ease, box-shadow .14s;
  }
  .btn:hover { transform: translateY(-3px); box-shadow:0 12px 28px rgba(0,120,255,0.18); }
  .muted { color:var(--muted); font-size:13px; margin-top:10px }
  .small-btn {
    padding:8px 10px; border-radius:8px; border:1px solid rgba(255,255,255,0.04);
    background:transparent; color:var(--neon1); cursor:pointer; margin-top:10px;
  }
  .main {
    padding:18px; min-height:640px; overflow:auto;
    background: linear-gradient(180deg, rgba(5,7,20,0.25), rgba(2,4,12,0.15));
  }
  header.main-head { display:flex; justify-content:space-between; align-items:center; gap:12px; margin-bottom:12px }
  header.main-head h2 { margin:0; color:var(--neon1) }
  .tabs { display:flex; gap:8px; align-items:center }
  .tab-button {
    padding:8px 12px; border-radius:8px; background:transparent; border:1px solid rgba(255,255,255,0.03);
    color:var(--muted); cursor:pointer;
  }
  .tab-button.active { background: linear-gradient(90deg,#02112a, #081033); color:var(--neon1); box-shadow:0 6px 26px rgba(0,120,255,0.06) }
  .upload-box {
    border:2px dashed rgba(255,255,255,0.05); padding:22px; border-radius:10px; background:var(--glass);
    text-align:center;
  }
  table { width:100%; border-collapse:collapse; margin-top:12px; background:transparent }
  th, td { padding:8px; border-bottom:1px dashed rgba(255,255,255,0.03); color:#e6f7ff; font-size:13px; text-align:left }
  th { color:var(--neon2); font-weight:700; background:linear-gradient(90deg, rgba(255,255,255,0.02), transparent) }
  .categoria { background: linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01)); padding:10px; border-radius:8px; margin-bottom:12px; border:1px solid rgba(255,255,255,0.02) }
  .categoria-titulo { padding:6px 8px; border-radius:6px; margin-bottom:8px; display:inline-block; background:linear-gradient(90deg, rgba(0,120,255,0.05), rgba(122,44,255,0.04)); color:var(--neon1) }
  .user-row { display:flex; align-items:center; justify-content:space-between; gap:8px; padding:10px; border-radius:8px; background:linear-gradient(180deg, rgba(0,0,0,0.25), rgba(10,10,20,0.25)); margin-bottom:8px }
  .tag { padding:6px 8px; border-radius:100px; background:linear-gradient(90deg,var(--neon1),var(--neon2)); color:#001; font-weight:700; font-size:12px }
  .actions button { margin-left:8px; background:transparent; color:var(--neon1); border:1px solid rgba(255,255,255,0.03); padding:6px 8px; border-radius:8px; cursor:pointer }
  footer { text-align:center; color:var(--muted); margin-top:18px; font-size:13px }
  .row{display:flex;gap:8px;align-items:center}
  .flex{display:flex;gap:12px}
  .grow{flex:1}
  .danger{background:linear-gradient(90deg,#ff6b6b,#ff4d4d); color:#fff;border:none}
  .ok{background:linear-gradient(90deg,#00ffb3,#00a8ff); color:#001;border:none}
  @media (max-width:980px){
    .card{grid-template-columns:1fr; max-width:920px}
    .side{border-right:none}
  }
  /* admin gear floating */
  .gear {
    width:44px;height:44px;border-radius:10px;background:linear-gradient(135deg,var(--neon1),var(--neon2));
    display:flex;align-items:center;justify-content:center;color:#001;font-weight:800;cursor:pointer;
    box-shadow:0 10px 30px rgba(0,160,255,0.12);
  }
  .gear:hover { transform: rotate(12deg) scale(1.04) }
</style>
</head>
<body>
<div class="wrap">
  <div class="card" role="application" aria-label="Organizador com login">
    <!-- SIDEBAR: login / admin controls -->
    <div class="side" id="sideArea">
      <div class="logo">
        <div class="mark">CLX</div>
        <div>
          <h1>CLX — Organizador</h1>
          <div class="muted">Painel seguro</div>
        </div>
      </div>

      <!-- LOGIN FORM -->
      <div id="loginBox">
        <label for="loginUser">Usuário</label>
        <input id="loginUser" placeholder="ex: CLX" autocomplete="username" />
        <label for="loginPass">Senha</label>
        <input id="loginPass" type="password" placeholder="Senha" autocomplete="current-password" />
        <button class="btn" id="loginBtn">Entrar</button>
        <div id="loginError" class="muted" style="margin-top:10px"></div>
        <div style="margin-top:14px">
          <button class="small-btn" id="showRecoverBtn">Esqueci a senha</button>
        </div>
      </div>

      <!-- RECUPERAR SENHA -->
      <div id="recoverBox" class="hidden">
        <label for="recoverUser">Informe seu usuário</label>
        <input id="recoverUser" placeholder="Seu usuário" />
        <button class="btn" id="recoverSend">Solicitar reset (admin aprova)</button>
        <div style="margin-top:8px">
          <button class="small-btn" id="backToLogin">Voltar</button>
        </div>
        <div id="recoverMsg" class="muted" style="margin-top:8px"></div>
      </div>

      <!-- ADMIN QUICK LINKS (aparece só para admin) -->
      <div id="adminQuick" class="hidden" style="margin-top:12px">
        <div class="muted">Ações rápidas (admin)</div>
        <div style="margin-top:10px">
          <button class="small-btn" id="btnCreateUser">Criar usuário</button>
          <button class="small-btn" id="btnListUsers">Gerenciar usuários</button>
          <button class="small-btn" id="btnViewLogs">Logs</button>
        </div>
        <div style="margin-top:14px">
          <label>Procurar usuário</label>
          <input id="searchUserAdmin" placeholder="Pesquisar..." />
        </div>
      </div>

      <div id="sideContent" style="margin-top:14px"></div>

      <div style="margin-top:18px">
        <button id="logoutBtn" class="small-btn hidden">Logout</button>
      </div>

      <footer style="margin-top:28px">
        <div>Feito por <strong>CLX</strong></div>
      </footer>
    </div>

    <!-- MAIN: organizador content / admin main content -->
    <div class="main" id="mainArea">
      <header class="main-head">
        <div>
          <h2>📊 Organizador Universal de Arquivos</h2>
          <p class="muted">Carregue qualquer arquivo e organize pelos campos que desejar</p>
        </div>
        <div class="tabs" style="align-items:center">
          <button class="tab-button active" data-tab="upload">📁 Upload</button>
          <button class="tab-button" data-tab="organizacao" id="tab-organizacao" disabled>⚙️ Organização</button>
          <button class="tab-button" data-tab="visualizacao" id="tab-visualizacao" disabled>👁️ Visualização</button>
          <button class="tab-button" data-tab="exportacao" id="tab-exportacao" disabled>💾 Exportação</button>
          <!-- gear placeholder (visible only after login and only to admin) -->
          <div id="gearWrap" style="margin-left:12px"></div>
        </div>
      </header>

      <!-- ABA UPLOAD -->
      <div id="aba-upload" class="tab-content active">
          <div class="upload-box">
              <input type="file" id="fileInput" accept=".csv,.xlsx,.xls,.json,.tsv,.txt" />
              <label for="fileInput" style="display:block;cursor:pointer">
                  <div class="upload-icon" style="font-size:44px">📎</div>
                  <h3>Clique para selecionar arquivo</h3>
                  <p class="muted">Suporta: CSV, Excel (.xlsx, .xls), JSON, TSV</p>
              </label>
          </div>
          <div id="file-info" style="display:none;margin-top:14px">
              <h3>Arquivo Carregado:</h3>
              <div id="file-details"></div>
              <button class="btn" id="processBtn">Processar Arquivo</button>
          </div>
      </div>

      <!-- ABA ORGANIZAÇÃO -->
      <div id="aba-organizacao" class="tab-content" style="display:none">
          <h3>Escolha como organizar seus dados</h3>
          <label>Organizar por:</label>
          <select id="campo-organizacao">
              <option value="">Selecione um campo...</option>
          </select>
          <div id="preview-organizacao" style="margin-top:12px"></div>
          <button class="btn" id="aplicarBtn" disabled>Aplicar Organização</button>
      </div>

      <!-- ABA VISUALIZAÇÃO -->
      <div id="aba-visualizacao" class="tab-content" style="display:none">
          <input id="busca-global" type="text" placeholder="Buscar..." style="width:100%;padding:8px;border-radius:8px;border:1px solid rgba(255,255,255,0.03);background:transparent;color:#eaf2ff" />
          <div style="margin-top:12px">
            <button class="small-btn" id="expandAll">Expandir Todas</button>
            <button class="small-btn" id="collapseAll">Recolher Todas</button>
          </div>
          <div id="tabelas" style="margin-top:12px"></div>
      </div>

      <!-- ABA EXPORTACAO -->
      <div id="aba-exportacao" class="tab-content" style="display:none">
          <h3>Exportar Dados Organizados</h3>
          <div style="display:flex;gap:8px;flex-wrap:wrap">
            <button class="btn" id="exportExcelBtn">📊 Baixar Excel</button>
            <button class="btn" id="exportCSVBtn">📄 Baixar CSV</button>
            <button class="btn" id="exportJSONBtn">🔗 Baixar JSON</button>
          </div>
      </div>

    </div> <!-- main -->
  </div> <!-- card -->
</div> <!-- wrap -->

<script>
/* ========================
  STORAGE & INIT
   ======================== */
const STORAGE_USERS = 'ou_usuarios_v1';
const STORAGE_LOG = 'ou_logs_v1';
const STORAGE_CURRENT = 'ou_logado_v1';

// ensure admin exists
function initUsers(){
  let u = localStorage.getItem(STORAGE_USERS);
  if(!u){
    const admin = [
      { usuario: 'CLX', senha: '02072007', ativo: true, cargo: 'admin', nome: 'Administrador CLX' }
    ];
    localStorage.setItem(STORAGE_USERS, JSON.stringify(admin));
    logAction('system','init_admin');
    return;
  }
  let arr = JSON.parse(u);
  if(!arr.some(x=>x.usuario==='CLX')){
    arr.unshift({ usuario: 'CLX', senha: '02072007', ativo: true, cargo: 'admin', nome: 'Administrador CLX' });
    localStorage.setItem(STORAGE_USERS, JSON.stringify(arr));
    logAction('system','restore_admin');
  }
}
initUsers();

function getLogs(){ return JSON.parse(localStorage.getItem(STORAGE_LOG) || '[]'); }
function saveLogs(a){ localStorage.setItem(STORAGE_LOG, JSON.stringify(a)); }
function logAction(actor, action, details=''){ const arr = getLogs(); arr.unshift({ ts: new Date().toISOString(), actor, action, details }); if(arr.length>300) arr.length=300; saveLogs(arr); }

/* UI helpers */
const $ = id => document.getElementById(id);

/* Tabs wiring */
document.querySelectorAll('.tab-button').forEach(btn=>{
  btn.addEventListener('click', ()=> {
    document.querySelectorAll('.tab-button').forEach(b=>b.classList.remove('active'));
    btn.classList.add('active');
    const tab = btn.dataset.tab;
    document.querySelectorAll('.tab-content').forEach(c=>c.style.display='none');
    document.getElementById('aba-'+tab).style.display = 'block';
  });
});

/* quick refs */
const loginBtn = $('loginBtn');
const logoutBtn = $('logoutBtn');
const loginError = $('loginError');
const loginBox = $('loginBox');
const recoverBox = $('recoverBox');
const recoverMsg = $('recoverMsg');
const showRecoverBtn = $('showRecoverBtn');
const backToLogin = $('backToLogin');
const adminQuick = $('adminQuick');
const sideContent = $('sideContent');
const gearWrap = $('gearWrap');

/* LOGIN */
loginBtn.addEventListener('click', attemptLogin);
showRecoverBtn.addEventListener('click', ()=>{ loginBox.classList.add('hidden'); recoverBox.classList.remove('hidden'); });
backToLogin.addEventListener('click', ()=>{ recoverBox.classList.add('hidden'); loginBox.classList.remove('hidden'); });

function attemptLogin(){
  const user = $('loginUser').value.trim();
  const pass = $('loginPass').value;
  if(!user || !pass){ loginError.textContent = 'Preencha usuário e senha'; return; }
  const users = JSON.parse(localStorage.getItem(STORAGE_USERS) || '[]');
  const found = users.find(u => u.usuario === user && u.senha === pass);
  if(!found){ loginError.textContent = 'Usuário ou senha incorretos'; logAction(user,'failed_login'); return; }
  if(!found.ativo){ loginError.textContent = 'Conta desativada. Contate o administrador.'; logAction(user,'login_blocked'); return; }
  localStorage.setItem(STORAGE_CURRENT, JSON.stringify({usuario: found.usuario, cargo: found.cargo, nome: found.nome || found.usuario}));
  loginError.textContent = '';
  logAction(found.usuario,'login_success');
  $('logoutBtn').classList.remove('hidden');
  $('loginBox').classList.add('hidden');
  // show organizer UI
  if(found.cargo === 'admin') showAdminUI(found); else showUserUI(found);
}

/* LOGOUT */
$('logoutBtn').addEventListener('click', ()=>{
  localStorage.removeItem(STORAGE_CURRENT);
  location.reload();
});

/* ADMIN UI */
function showAdminUI(admin){
  adminQuick.classList.remove('hidden');
  sideContent.innerHTML = `
    <div style="margin-top:12px">
      <div style="font-size:13px;color:var(--neon1)">Logado: ${escapeHtml(admin.usuario)}</div>
      <div style="font-size:12px;color:var(--muted)">${escapeHtml(admin.nome || '')}</div>
      <div style="margin-top:10px">
        <button class="small-btn" id="openCreate">Criar usuário</button>
        <button class="small-btn" id="openList">Gerenciar</button>
        <button class="small-btn" id="openLogs">Ver logs</button>
      </div>
    </div>
  `;
  $('openCreate')?.addEventListener('click', abrirCriarUsuarioAdmin);
  $('openList')?.addEventListener('click', listarUsuariosAdmin);
  $('openLogs')?.addEventListener('click', mostrarLogsAdmin);
  // put gear in header main (admin only)
  gearWrap.innerHTML = `<div class="gear" title="Painel Admin" id="adminGear">⚙️</div>`;
  $('adminGear').addEventListener('click', ()=>{ renderAdminMain(); });
  renderAdminMain();
}

function renderAdminMain(){
  const main = $('mainArea');
  main.innerHTML = `
    <div style="padding:8px 0 18px 0;">
      <h2 style="color:var(--neon1)">Painel Administrativo</h2>
      <div style="display:flex;gap:12px;flex-wrap:wrap">
        <button class="btn" onclick="abrirCriarUsuarioAdmin()">➕ Criar usuário</button>
        <button class="btn" onclick="listarUsuariosAdmin()">📋 Gerenciar usuários</button>
        <button class="btn" onclick="mostrarLogsAdmin()">📝 Logs</button>
        <button class="btn" onclick="forcarProtecaoBanco()">🛡️ Proteger banco</button>
      </div>
      <div id="adminContent" style="margin-top:18px"></div>
    </div>
  `;
}

function abrirCriarUsuarioAdmin(){
  const c = $('adminContent');
  c.innerHTML = `
    <h3>Criar novo usuário</h3>
    <label>Nome</label><input id="adm_new_nome" placeholder="Nome (opcional)">
    <label>Usuário</label><input id="adm_new_user" placeholder="usuario123">
    <label>Senha</label><input id="adm_new_pass" type="password" placeholder="senha">
    <div style="display:flex;gap:8px;margin-top:10px">
      <button class="btn ok" onclick="adminSalvarUsuario()">Salvar</button>
      <button class="btn danger" onclick="renderAdminMain()">Cancelar</button>
    </div>
    <div id="adm_msg" style="margin-top:8px;color:var(--muted)"></div>
  `;
  $('adm_new_user').focus();
}

function adminSalvarUsuario(){
  const nome = $('adm_new_nome').value.trim();
  const usuario = $('adm_new_user').value.trim();
  const senha = $('adm_new_pass').value.trim();
  const msg = $('adm_msg');
  if(!usuario || !senha){ msg.textContent = 'Usuário e senha obrigatórios.'; return; }
  let users = JSON.parse(localStorage.getItem(STORAGE_USERS) || '[]');
  if(users.some(u => u.usuario === usuario)){ msg.textContent = 'Usuário já existe.'; return; }
  users.push({ usuario, senha, ativo: true, cargo: 'user', nome: nome || usuario });
  localStorage.setItem(STORAGE_USERS, JSON.stringify(users));
  logAction(getCurrentUser(),'admin_create_user',usuario);
  msg.textContent = 'Usuário criado com sucesso.';
  listarUsuariosAdmin();
}

function listarUsuariosAdmin(filter=''){
  let users = JSON.parse(localStorage.getItem(STORAGE_USERS) || '[]');
  if(filter) users = users.filter(u => u.usuario.toLowerCase().includes(filter.toLowerCase()) || (u.nome && u.nome.toLowerCase().includes(filter.toLowerCase())));
  const area = $('adminContent');
  let html = '<h3>Usuários</h3>';
  if(users.length===0) html += '<div class="muted">Nenhum usuário</div>';
  html += '<div style="margin-top:8px">';
  users.forEach(u=>{
    html += `
      <div class="user-row">
        <div style="display:flex;flex-direction:column">
          <strong style="color:#dfefff">${escapeHtml(u.usuario)}</strong>
          <span style="font-size:12px;color:var(--muted)">${escapeHtml(u.nome||'')}</span>
        </div>
        <div style="display:flex;align-items:center" class="actions">
          <div class="tag">${u.cargo === 'admin' ? 'ADMIN' : (u.ativo ? 'ATIVO' : 'DESATIVADO')}</div>
          <button onclick="abrirEditarUsuario('${escapeJs(u.usuario)}')">Editar</button>
          <button onclick="adminResetSenha('${escapeJs(u.usuario)}')">Redefinir senha</button>
          ${u.usuario !== 'CLX' ? `<button onclick="toggleAtivo('${escapeJs(u.usuario)}')">${u.ativo? 'Desativar' : 'Ativar'}</button>` : ''}
          ${u.usuario !== 'CLX' ? `<button onclick="adminDeleteUser('${escapeJs(u.usuario)}')" class="danger">Excluir</button>` : ''}
        </div>
      </div>
    `;
  });
  html += '</div>';
  area.innerHTML = html;
}

function abrirEditarUsuario(usuario){
  const users = JSON.parse(localStorage.getItem(STORAGE_USERS) || '[]');
  const u = users.find(x=>x.usuario === usuario);
  if(!u){ alert('Usuário não encontrado'); return; }
  const area = $('adminContent');
  area.innerHTML = `
    <h3>Editar ${escapeHtml(u.usuario)}</h3>
    <label>Nome</label><input id="edit_nome" value="${escapeAttr(u.nome||'')}" />
    <label>Usuário (fixo)</label><input value="${escapeAttr(u.usuario)}" disabled />
    <label>Nova senha (deixe em branco para manter)</label><input id="edit_pass" type="password" />
    <div style="display:flex;gap:8px;margin-top:10px">
      <button class="btn ok" onclick="adminSalvarEdicao('${escapeJs(u.usuario)}')">Salvar</button>
      <button class="btn danger" onclick="listarUsuariosAdmin()">Voltar</button>
    </div>
    <div id="edit_msg" style="margin-top:8px;color:var(--muted)"></div>
  `;
}

function adminSalvarEdicao(usuario){
  const nome = $('edit_nome').value.trim();
  const nova = $('edit_pass').value;
  const users = JSON.parse(localStorage.getItem(STORAGE_USERS) || '[]');
  const i = users.findIndex(u=>u.usuario===usuario);
  if(i===-1){ alert('Usuário não encontrado'); return; }
  users[i].nome = nome || users[i].nome;
  if(nova && nova.length>0) users[i].senha = nova;
  localStorage.setItem(STORAGE_USERS, JSON.stringify(users));
  logAction(getCurrentUser(),'admin_edit_user',usuario);
  $('edit_msg').textContent = 'Salvo!';
  listarUsuariosAdmin();
}

function adminResetSenha(usuario){
  if(usuario === 'CLX'){ alert('Não é possível resetar a senha do ADMIN via este botão.'); return; }
  const nova = prompt('Digite a nova senha para ' + usuario + ' (mínimo 4 caracteres):');
  if(!nova || nova.length < 4){ alert('Senha inválida.'); return; }
  const users = JSON.parse(localStorage.getItem(STORAGE_USERS) || '[]');
  const i = users.findIndex(u=>u.usuario===usuario);
  users[i].senha = nova;
  localStorage.setItem(STORAGE_USERS, JSON.stringify(users));
  logAction(getCurrentUser(),'admin_reset_pwd',usuario);
  alert('Senha redefinida!');
  listarUsuariosAdmin();
}

function toggleAtivo(usuario){
  if(usuario === 'CLX'){ alert('Operação não permitida no ADMIN CLX'); return; }
  const users = JSON.parse(localStorage.getItem(STORAGE_USERS) || '[]');
  const i = users.findIndex(u=>u.usuario===usuario);
  users[i].ativo = !users[i].ativo;
  localStorage.setItem(STORAGE_USERS, JSON.stringify(users));
  logAction(getCurrentUser(), users[i].ativo ? 'admin_activate' : 'admin_deactivate', usuario);
  listarUsuariosAdmin();
}

function adminDeleteUser(usuario){
  if(!confirm('Excluir usuário '+usuario+' ?')) return;
  if(usuario === 'CLX'){ alert('Não é possível excluir o ADMIN CLX!'); return; }
  let users = JSON.parse(localStorage.getItem(STORAGE_USERS) || '[]');
  users = users.filter(u=>u.usuario !== usuario);
  localStorage.setItem(STORAGE_USERS, JSON.stringify(users));
  logAction(getCurrentUser(),'admin_delete_user',usuario);
  listarUsuariosAdmin();
}

function forcarProtecaoBanco(){
  const users = JSON.parse(localStorage.getItem(STORAGE_USERS) || '[]');
  if(!users.some(u=>u.usuario==='CLX')){
    users.unshift({ usuario:'CLX', senha:'02072007', ativo:true, cargo:'admin', nome:'Administrador CLX' });
  }
  localStorage.setItem(STORAGE_USERS, JSON.stringify(users));
  localStorage.setItem('ou_users_backup', JSON.stringify(users));
  alert('Banco reforçado e backup criado.');
  logAction(getCurrentUser(),'force_protect_db');
}

function mostrarLogsAdmin(){
  const logs = getLogs();
  const area = $('adminContent');
  let html = '<h3>Últimos logs</h3>';
  html += `<div style="max-height:280px;overflow:auto;background:rgba(255,255,255,0.02);padding:8px;border-radius:8px">`;
  if(logs.length===0) html += '<div class="muted">Sem logs</div>';
  logs.forEach(l=>{
    html += `<div style="margin-bottom:8px;font-size:13px"><strong>${escapeHtml(l.actor)}</strong> — ${escapeHtml(l.action)} <div style="color:var(--muted);font-size:12px">${escapeHtml(l.details)} • ${new Date(l.ts).toLocaleString()}</div></div>`;
  });
  html += '</div>';
  area.innerHTML = html;
}

/* USER UI */
function showUserUI(user){
  renderOrganizer(); // populate organizer in main area
  sideContent.innerHTML = `
    <div style="margin-top:8px">
      <div style="font-weight:700">${escapeHtml(user.usuario)}</div>
      <div class="muted" style="font-size:13px">${escapeHtml(user.nome||'Usuário')}</div>
      <div style="margin-top:10px">
        <button class="small-btn" id="btnChangePwd">Trocar senha</button>
      </div>
    </div>
  `;
  $('btnChangePwd').addEventListener('click', ()=>{ userChangePasswordPrompt(user.usuario); });
}

/* get current user helper */
function getCurrentUser(){ const c = localStorage.getItem(STORAGE_CURRENT); return c? JSON.parse(c).usuario : null; }

/* ORGANIZER INTEGRATION (your original organizer code, adapted) */
function renderOrganizer(){
  // Re-render the original organizer UI into the main area (tabs/header already present)
  const main = $('mainArea');
  main.innerHTML = `
    <header class="main-head">
      <div>
        <h2>📊 Organizador Universal de Arquivos</h2>
        <p class="muted">Carregue qualquer arquivo e organize pelos campos que desejar</p>
      </div>
      <div class="tabs" style="align-items:center">
        <button class="tab-button active" data-tab="upload">📁 Upload</button>
        <button class="tab-button" data-tab="organizacao" id="tab-organizacao" disabled>⚙️ Organização</button>
        <button class="tab-button" data-tab="visualizacao" id="tab-visualizacao" disabled>👁️ Visualização</button>
        <button class="tab-button" data-tab="exportacao" id="tab-exportacao" disabled>💾 Exportação</button>
        <div id="gearWrapInner"></div>
      </div>
    </header>

    <!-- Tab contents (same IDs used by code) -->
    <div id="aba-upload" class="tab-content active">
      <div class="upload-box">
        <input type="file" id="fileInput" accept=".csv,.xlsx,.xls,.json,.tsv,.txt" />
        <label for="fileInput" style="display:block;cursor:pointer">
            <div class="upload-icon" style="font-size:44px">📎</div>
            <h3>Clique para selecionar arquivo</h3>
            <p class="muted">Suporta: CSV, Excel (.xlsx, .xls), JSON, TSV</p>
        </label>
      </div>
      <div id="file-info" style="display:none;margin-top:14px">
          <h3>Arquivo Carregado:</h3>
          <div id="file-details"></div>
          <button class="btn" id="processBtn">Processar Arquivo</button>
      </div>
    </div>

    <div id="aba-organizacao" class="tab-content" style="display:none">
        <h3>Escolha como organizar seus dados</h3>
        <label>Organizar por:</label>
        <select id="campo-organizacao">
            <option value="">Selecione um campo...</option>
        </select>
        <div id="preview-organizacao" style="margin-top:12px"></div>
        <button class="btn" id="aplicarBtn" disabled>Aplicar Organização</button>
    </div>

    <div id="aba-visualizacao" class="tab-content" style="display:none">
        <input id="busca-global" type="text" placeholder="Buscar..." style="width:100%;padding:8px;border-radius:8px;border:1px solid rgba(255,255,255,0.03);background:transparent;color:#eaf2ff" />
        <div style="margin-top:12px">
          <button class="small-btn" id="expandAll">Expandir Todas</button>
          <button class="small-btn" id="collapseAll">Recolher Todas</button>
        </div>
        <div id="tabelas" style="margin-top:12px"></div>
    </div>

    <div id="aba-exportacao" class="tab-content" style="display:none">
        <h3>Exportar Dados Organizados</h3>
        <div style="display:flex;gap:8px;flex-wrap:wrap">
          <button class="btn" id="exportExcelBtn">📊 Baixar Excel</button>
          <button class="btn" id="exportCSVBtn">📄 Baixar CSV</button>
          <button class="btn" id="exportJSONBtn">🔗 Baixar JSON</button>
        </div>
    </div>
  `;

  // wire tabs inside organizer
  document.querySelectorAll('#mainArea .tab-button').forEach(btn=>{
    btn.addEventListener('click', ()=> {
      document.querySelectorAll('#mainArea .tab-button').forEach(b=>b.classList.remove('active'));
      btn.classList.add('active');
      const tab = btn.dataset.tab;
      document.querySelectorAll('.tab-content').forEach(c=>c.style.display='none');
      document.getElementById('aba-'+tab).style.display = 'block';
    });
  });

  // wire organizer events (rebind)
  const fileInput = document.querySelector('#fileInput');
  const processBtn = document.querySelector('#processBtn');
  const btnAplicar = document.querySelector('#aplicarBtn');
  const campoOrganizacao = document.querySelector('#campo-organizacao');

  if(fileInput){
    fileInput.addEventListener('change', e=>{
      const file = e.target.files[0];
      if(!file) return;
      nomeArquivo = file.name;
      $('file-details').textContent = `Nome: ${file.name} (${(file.size/1024).toFixed(1)} KB)`;
      $('file-info').style.display = 'block';
    });
  }

  processBtn?.addEventListener('click', processarArquivo);
  btnAplicar?.addEventListener('click', aplicarOrganizacao);
  campoOrganizacao?.addEventListener('change', atualizarPreview);

  $('exportExcelBtn')?.addEventListener('click', ()=> {
    const wb = XLSX.utils.book_new();
    Object.keys(dadosOrganizados).forEach(cat=>{
      const ws = XLSX.utils.json_to_sheet(dadosOrganizados[cat]);
      XLSX.utils.book_append_sheet(wb, ws, cat.slice(0,31));
    });
    XLSX.writeFile(wb, `${nomeArquivo.split('.')[0] || 'dados'}_organizado.xlsx`);
  });

  $('exportCSVBtn')?.addEventListener('click', ()=> {
    const todos = Object.values(dadosOrganizados).flat();
    const csv = Papa.unparse(todos);
    const blob = new Blob([csv], { type:'text/csv;charset=utf-8;' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a'); a.href = url; a.download = `${nomeArquivo.split('.')[0] || 'dados'}_organizado.csv`; a.click();
  });

  $('exportJSONBtn')?.addEventListener('click', ()=> {
    const blob = new Blob([JSON.stringify(dadosOrganizados,null,2)], { type:'application/json' });
    const a = document.createElement('a'); a.href = URL.createObjectURL(blob); a.download = `${nomeArquivo.split('.')[0] || 'dados'}_organizado.json`; a.click();
  });

  $('busca-global')?.addEventListener('input', ()=>{
    const termo = $('busca-global').value.toLowerCase();
    document.querySelectorAll('.categoria').forEach(cat => {
      cat.style.display = cat.innerText.toLowerCase().includes(termo) ? '' : 'none';
    });
  });
  $('expandAll')?.addEventListener('click', ()=> document.querySelectorAll('.categoria table').forEach(t=>t.style.display='table'));
  $('collapseAll')?.addEventListener('click', ()=> document.querySelectorAll('.categoria table').forEach(t=>t.style.display='none'));

  // add gear inside organizer header if current user is admin (redundant but safe)
  const cur = localStorage.getItem(STORAGE_CURRENT);
  if(cur && JSON.parse(cur).cargo === 'admin'){
    const gearInner = document.getElementById('gearWrapInner');
    if(gearInner) gearInner.innerHTML = `<div class="gear" id="adminGearInner" title="Painel Admin">⚙️</div>`;
    document.getElementById('adminGearInner')?.addEventListener('click', ()=>{ renderAdminMain(); });
  }
}

/* Organizer functions (processing / preview / export) */
let dadosOriginais = [];
let dadosOrganizados = {};
let nomeArquivo = "";

function processarArquivo(){
  const file = document.querySelector('#fileInput')?.files?.[0];
  if(!file) return alert('Selecione um arquivo primeiro!');
  const extensao = file.name.split('.').pop().toLowerCase();

  if(["csv","tsv","txt"].includes(extensao)){
    Papa.parse(file, { header:true, skipEmptyLines:true, complete: results => {
      dadosOriginais = results.data;
      prepararOrganizacao();
    }});
  } else if(["xlsx","xls"].includes(extensao)){
    const reader = new FileReader();
    reader.onload = e => {
      const workbook = XLSX.read(e.target.result, { type:'binary' });
      const sheet = workbook.Sheets[workbook.SheetNames[0]];
      dadosOriginais = XLSX.utils.sheet_to_json(sheet);
      prepararOrganizacao();
    };
    reader.readAsBinaryString(file);
  } else if(extensao==='json'){
    const reader = new FileReader();
    reader.onload = e => {
      dadosOriginais = JSON.parse(e.target.result);
      prepararOrganizacao();
    };
    reader.readAsText(file);
  } else {
    alert('Tipo de arquivo não suportado!');
  }
}

function prepararOrganizacao(){
  if(!dadosOriginais || !dadosOriginais[0]) { alert('Arquivo não possui dados válidos'); return; }
  const campos = Object.keys(dadosOriginais[0]);
  const select = $('campo-organizacao');
  select.innerHTML = '<option value="">Selecione um campo...</option>';
  campos.forEach(c => { const opt = document.createElement('option'); opt.value = c; opt.textContent = c; select.appendChild(opt); });
  $('tab-organizacao').disabled = false;
  // switch to organizacao tab
  document.querySelectorAll('#mainArea .tab-button').forEach(b=>b.classList.remove('active'));
  document.querySelector('[data-tab="organizacao"]').classList.add('active');
  document.querySelectorAll('.tab-content').forEach(c=>c.style.display='none');
  $('aba-organizacao').style.display = 'block';
}

function atualizarPreview(){
  const campo = $('campo-organizacao').value;
  const previewDiv = $('preview-organizacao');
  if(!campo){ previewDiv.innerHTML = ''; $('aplicarBtn').disabled = true; return; }
  const categorias = Array.from(new Set(dadosOriginais.map(d=>d[campo])));
  previewDiv.innerHTML = `<h4 style="color:var(--neon1)">${categorias.length} categorias encontradas</h4><ul>${categorias.slice(0,200).map(c=>`<li>${escapeHtml(String(c))}</li>`).join('')}</ul>`;
  $('aplicarBtn').disabled = false;
}

function aplicarOrganizacao(){
  const campo = $('campo-organizacao').value;
  dadosOrganizados = {};
  dadosOriginais.forEach(item=>{
    const chave = item[campo] || 'Sem categoria';
    if(!dadosOrganizados[chave]) dadosOrganizados[chave] = [];
    dadosOrganizados[chave].push(item);
  });
  $('tab-visualizacao').disabled = false;
  $('tab-exportacao').disabled = false;
  // switch to visualizacao
  document.querySelectorAll('#mainArea .tab-button').forEach(b=>b.classList.remove('active'));
  document.querySelector('[data-tab="visualizacao"]').classList.add('active');
  document.querySelectorAll('.tab-content').forEach(c=>c.style.display='none');
  $('aba-visualizacao').style.display = 'block';
  renderizarVisualizacao();
}

function renderizarVisualizacao(){
  const container = $('tabelas');
  container.innerHTML = '';
  Object.keys(dadosOrganizados).forEach(categoria=>{
    const dados = dadosOrganizados[categoria];
    const colunas = Object.keys(dados[0] || {});
    const linhasHTML = dados.map(linha => `<tr>${colunas.map(c => `<td>${escapeHtml(String(linha[c] ?? ''))}</td>`).join('')}</tr>`).join('');
    const bloco = document.createElement('div');
    bloco.className = 'categoria';
    bloco.innerHTML = `
      <div class="categoria-titulo">${escapeHtml(categoria)} — ${dados.length}</div>
      <div style="overflow:auto;margin-top:8px">
        <table><thead><tr>${colunas.map(c=>`<th>${escapeHtml(c)}</th>`).join('')}</tr></thead><tbody>${linhasHTML}</tbody></table>
      </div>
    `;
    container.appendChild(bloco);
  });
}

/* Recover request (user) -> logs a request for admin to handle */
$('recoverSend')?.addEventListener('click', ()=>{
  const user = $('recoverUser').value.trim();
  if(!user){ recoverMsg.textContent = 'Informe seu usuário.'; return; }
  const arr = JSON.parse(localStorage.getItem(STORAGE_USERS) || '[]');
  if(!arr.some(u=>u.usuario===user)){ recoverMsg.textContent = 'Usuário não encontrado.'; return; }
  logAction(user, 'request_password_reset', 'request sent');
  recoverMsg.textContent = 'Pedido enviado ao admin. Peça para o admin redefinir sua senha.';
});

/* UTIL helpers */
function escapeHtml(s){ return String(s).replace(/[&<>"']/g, m => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[m])); }
function escapeAttr(s){ return String(s).replace(/"/g,'&quot;'); }
function escapeJs(s){ return String(s).replace(/'/g,"\\'").replace(/"/g,'\\"'); }

/* User self-change password */
function userChangePasswordPrompt(usuario){
  const novo = prompt('Digite nova senha (mínimo 4 caracteres):');
  if(!novo || novo.length < 4){ alert('Senha inválida'); return; }
  const users = JSON.parse(localStorage.getItem(STORAGE_USERS) || '[]');
  const i = users.findIndex(u=>u.usuario===usuario);
  if(i===-1){ alert('Usuário não encontrado'); return; }
  users[i].senha = novo;
  localStorage.setItem(STORAGE_USERS, JSON.stringify(users));
  logAction(usuario,'user_change_password');
  alert('Senha alterada com sucesso!');
}

/* sidebar quick buttons */
$('btnCreateUser')?.addEventListener('click', abrirCriarUsuarioAdmin);
$('btnListUsers')?.addEventListener('click', listarUsuariosAdmin);
$('btnViewLogs')?.addEventListener('click', mostrarLogsAdmin);

/* STARTUP: persist login */
(function startup(){
  const logged = localStorage.getItem(STORAGE_CURRENT);
  if(logged){
    const cur = JSON.parse(logged);
    const users = JSON.parse(localStorage.getItem(STORAGE_USERS) || '[]');
    const found = users.find(u=>u.usuario===cur.usuario);
    if(found && found.ativo){
      if(found.cargo === 'admin') showAdminUI(found);
      else showUserUI(found);
      $('logoutBtn').classList.remove('hidden');
    } else {
      localStorage.removeItem(STORAGE_CURRENT);
    }
  }
})();

</script>
</body>
</html>
