<style>
body{
  background:#0d1117;
  font-family:Arial;
  color:white;
  margin:0;
  padding:0;
  display:flex;
  justify-content:center;
  align-items:center;
  height:100vh;
}
.card{
  background:#161b22;
  padding:30px;
  width:350px;
  border-radius:15px;
  box-shadow:0 0 15px #00aaff;
}
input,button{
  width:100%;
  padding:12px;
  margin-top:10px;
  border-radius:8px;
  border:none;
  outline:none;
  font-size:16px;
}
button{
  background:#00aaff;
  color:white;
  cursor:pointer;
}
button:hover{ background:#0088cc; }
a{ color:#00aaff; text-decoration:none; }
a:hover{ text-decoration:underline; }
h2{text-align:center;}
</style>
";

/* ================================
   📌 TELA DE LOGIN
================================ */
if($page == "login"){
    $msg = "";

    if(isset($_POST["email"])){
        $email = $_POST["email"];
        $senha = $_POST["senha"];

        $sql = $pdo->prepare("SELECT * FROM usuarios WHERE email=?");
        $sql->execute([$email]);
        $user = $sql->fetch();

        if($user && password_verify($senha,$user["senha"])){
            $_SESSION["user"] = $user;
            header("Location: index.php?p=painel");
            exit;
        } else {
            $msg = "Email ou senha incorretos!";
        }
    }

    echo "
    <div class='card'>
        <h2>Login</h2>
        <form method='post'>
            <input type='email' name='email' placeholder='Email' required>
            <input type='password' name='senha' placeholder='Senha' required>
            <button>Entrar</button>
        </form>
        <p>$msg</p>
        <a href='?p=cadastrar'>Criar conta</a><br>
        <a href='?p=recuperar'>Esqueci a senha</a>
    </div>
    ";
    exit;
}

/* ================================
   📌 CADASTRAR
================================ */
if($page == "cadastrar"){
    $msg = "";
    if(isset($_POST["email"])){
        try{
            $nome = $_POST["nome"];
            $email = $_POST["email"];
            $senha = password_hash($_POST["senha"],PASSWORD_DEFAULT);

            $sql = $pdo->prepare("INSERT INTO usuarios (nome,email,senha) VALUES (?,?,?)");
            $sql->execute([$nome,$email,$senha]);

            header("Location: index.php?p=login");
            exit;
        } catch(Exception $e){
            $msg = "Email já cadastrado!";
        }
    }

    echo "
    <div class='card'>
        <h2>Criar Conta</h2>
        <form method='post'>
            <input name='nome' placeholder='Nome' required>
            <input name='email' type='email' placeholder='Email' required>
            <input name='senha' type='password' placeholder='Senha' required>
            <button>Cadastrar</button>
        </form>
        <p>$msg</p>
        <a href='?p=login'>Voltar</a>
    </div>
    ";
    exit;
}

/* ================================
   📌 RECUPERAR SENHA
================================ */
if($page == "recuperar"){
    $msg = "";

    if(isset($_POST["email"])){
        $email = $_POST["email"];
        $token = md5(uniqid());

        $sql = $pdo->prepare("UPDATE usuarios SET token=? WHERE email=?");
        $sql->execute([$token,$email]);

        $msg = "Link de recuperação:<br>
        <b>http://localhost/index.php?p=reset&token=$token</b>";
    }

    echo "
    <div class='card'>
        <h2>Recuperar Senha</h2>
        <form method='post'>
            <input type='email' name='email' placeholder='Seu email' required>
            <button>Enviar link</button>
        </form>
        <p>$msg</p>
        <a href='?p=login'>Voltar</a>
    </div>
    ";
    exit;
}

/* ================================
   📌 RESETAR SENHA
================================ */
if($page == "reset"){
    $token = $_GET["token"];

    if(isset($_POST["senha"])){
        $senha = password_hash($_POST["senha"],PASSWORD_DEFAULT);

        $sql = $pdo->prepare("UPDATE usuarios SET senha=?, token=NULL WHERE token=?");
        $sql->execute([$senha,$token]);

        header("Location: index.php?p=login");
        exit;
    }

    echo "
    <div class='card'>
        <h2>Nova Senha</h2>
        <form method='post'>
            <input type='password' name='senha' placeholder='Nova senha' required>
            <button>Salvar</button>
        </form>
    </div>
    ";
    exit;
}

/* ================================
   📌 PAINEL DO USUÁRIO
================================ */
if($page == "painel"){
    if(!isset($_SESSION["user"])){
        header("Location: index.php?p=login");
        exit;
    }

    $u = $_SESSION["user"];

    echo "
    <div class='card'>
        <h2>Bem-vindo, {$u["nome"]}</h2>
    ";

    if($u["tipo"] == "admin"){
        echo "<a href='?p=admin'>Painel Admin</a><br>";
    }

    echo "
        <a href='?p=logout'>Sair</a>
    </div>
    ";
    exit;
}

/* ================================
   📌 PAINEL ADMIN
================================ */
if($page == "admin"){
    if(!isset($_SESSION["user"]) || $_SESSION["user"]["tipo"] != "admin"){
        header("Location: index.php?p=login");
        exit;
    }

    $usuarios = $pdo->query("SELECT * FROM usuarios ORDER BY id DESC")->fetchAll();

    echo "<div class='card'><h2>Painel Admin</h2>";

    foreach($usuarios as $u){
        echo "
        <p>
        <b>ID:</b> {$u['id']}<br>
        <b>Nome:</b> {$u['nome']}<br>
        <b>Email:</b> {$u['email']}<br>
        <b>Tipo:</b> {$u['tipo']}<br>
        </p><hr>
        ";
    }

    echo "<a href='?p=painel'>Voltar</a></div>";
    exit;
}

/* ================================
   📌 LOGOUT
================================ */
if($page == "logout"){
    session_destroy();
    header("Location: index.php?p=login");
    exit;
}
?>
