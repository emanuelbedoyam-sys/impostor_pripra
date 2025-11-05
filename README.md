<?php
session_start();

if ($_SERVER["REQUEST_METHOD"] == "POST" && isset($_POST["num_players"])) {
    $numPlayers = intval($_POST["num_players"]);
    $_SESSION["num_players"] = $numPlayers;
    $_SESSION["current_player"] = 1;
    $_SESSION["impostor"] = rand(1, $numPlayers);
    header("Location: impostor_game.php");
    exit;
}

if (isset($_SESSION["num_players"])) {
    $numPlayers = $_SESSION["num_players"];
    $currentPlayer = $_SESSION["current_player"];
    $impostor = $_SESSION["impostor"];

    if (isset($_POST["next"])) {
        $_SESSION["current_player"]++;
        $currentPlayer++;
        if ($currentPlayer > $numPlayers) {
            session_destroy();
            header("Location: impostor_game.php?end=true");
            exit;
        }
    }
}
?>
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Juego del Impostor</title>
<style>
    body {
        font-family: 'Poppins', sans-serif;
        background-color: #121212;
        color: #fff;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        flex-direction: column;
        margin: 0;
    }
    .card {
        background: #1e1e1e;
        padding: 30px;
        border-radius: 20px;
        box-shadow: 0 0 15px rgba(255,255,255,0.1);
        text-align: center;
        width: 300px;
    }
    input, button {
        width: 80%;
        padding: 10px;
        margin: 10px 0;
        border-radius: 10px;
        border: none;
        outline: none;
        font-size: 16px;
    }
    button {
        background: #00adb5;
        color: #fff;
        cursor: pointer;
        transition: 0.3s;
    }
    button:hover {
        background: #03c4cf;
    }
    .role {
        font-size: 1.5em;
        margin-bottom: 15px;
    }
</style>
</head>
<body>
<div class="card">
<?php if (!isset($_SESSION["num_players"]) && !isset($_GET["end"])): ?>
    <h2>🎮 Juego del Impostor</h2>
    <form method="post">
        <input type="number" name="num_players" placeholder="Número de jugadores" required min="3">
        <button type="submit">Iniciar</button>
    </form>

<?php elseif (isset($_SESSION["num_players"])): ?>
    <h2>Jugador <?php echo $currentPlayer; ?></h2>
    <?php if (isset($_POST["show"])): ?>
        <div class="role">
            <?php
            if ($currentPlayer == $impostor) {
                echo "👀 Eres el <b>IMPOSTOR</b>";
            } else {
                echo "😇 Eres un <b>Jugador Normal</b>";
            }
            ?>
        </div>
        <form method="post">
            <button type="submit" name="next">Siguiente jugador</button>
        </form>
    <?php else: ?>
        <form method="post">
            <button type="submit" name="show">Ver tu rol</button>
        </form>
    <?php endif; ?>

<?php elseif (isset($_GET["end"])): ?>
    <h2>✅ Todos los jugadores ya vieron su rol</h2>
    <p>¡Ahora intenten descubrir quién es el impostor!</p>
    <form method="get" action="impostor_game.php">
        <button type="submit">Volver a jugar</button>
    </form>
<?php endif; ?>
</div>
</body>
</html>
