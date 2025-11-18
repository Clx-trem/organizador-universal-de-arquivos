<!-- COLOQUE ESTE ARQUIVO COMO login.html -->
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Login</title>
  <style>
    body{
      background:#0d1117;
      font-family:Arial;
      color:white;
      display:flex;
      justify-content:center;
      align-items:center;
      height:100vh;
      margin:0;
    }
    .box{
      background:#161b22;
      padding:30px;
      width:320px;
      border-radius:10px;
      box-shadow:0 0 10px #0004;
    }
    input{
      width:100%;
      padding:12px;
      margin:8px 0;
      border:none;
      border-radius:6px;
      background:#21262d;
      color:white;
    }
    button{
      width:100%;
      padding:12px;
      border:none;
      background:#238636;
      color:white;
      border-radius:6px;
      cursor:pointer;
      font-size:16px;
      margin-top:10px;
    }
  </style>
</head>
<body>
  <div class="box">
    <h2>Login</h2>
    <input id="loginUser" placeholder="Usuário" />
    <input id="loginPass" type="password" placeholder="Senha" />
    <button id="loginBtn">Entrar</button>
  </div>

<script>
  document.getElementById("loginBtn").addEventListener("click", () => {
      const u = document.getElementById("loginUser").value.trim();
      const p = document.getElementById("loginPass").value.trim();

      const userCorreto = "admin";
      const passCorreta = "1234";

      if(u === userCorreto && p === passCorreta){
          localStorage.setItem("logado", "sim");
          window.location.href = "index.html";
      } else {
          alert("Usuário ou senha incorretos!");
      }
  });
</script>
</body>
</html>



<!-- COLOQUE ESTE ARQUIVO COMO index.html -->
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Sistema CLX</title>
  <style>
    body{
      font-family:Arial;
      background:#0d1117;
      color:white;
      margin:0;
      padding:20px;
    }
    header{
      padding:15px;
      background:#161b22;
      border-radius:10px;
      margin-bottom:20px;
    }
    button{
      padding:10px 16px;
      background:#238636;
      border:none;
      border-radius:6px;
      color:white;
      cursor:pointer;
    }
  </style>
</head>
<body>

<script>
  if(localStorage.getItem("logado") !== "sim"){
    window.location.href = "login.html";
  }

  function sair(){
    localStorage.removeItem("logado");
    window.location.href = "login.html";
  }
</script>

<header>
  <h2>Bem-vindo ao Sistema</h2>
  <button onclick="sair()">Sair</button>
</header>

<p>Aqui vai o conteúdo principal do seu sistema...</p>

</body>
</html>
