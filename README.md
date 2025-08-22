<?php
// tic-tac-toe.php
session_start();

// Initialize board
if (!isset($_SESSION['board'])) {
    $_SESSION['board'] = array_fill(0, 9, '');
    $_SESSION['turn'] = 'X';
    $_SESSION['winner'] = null;
}

// Handle moves
if ($_SERVER['REQUEST_METHOD'] === 'POST' && isset($_POST['cell'])) {
    $cell = (int)$_POST['cell'];

    if ($_SESSION['board'][$cell] === '' && !$_SESSION['winner']) {
        $_SESSION['board'][$cell] = $_SESSION['turn'];
        $_SESSION['turn'] = ($_SESSION['turn'] === 'X') ? 'O' : 'X';
    }

    // Check winner
    $winningCombos = [
        [0,1,2],[3,4,5],[6,7,8], // rows
        [0,3,6],[1,4,7],[2,5,8], // cols
        [0,4,8],[2,4,6]          // diagonals
    ];
    foreach ($winningCombos as $combo) {
        [$a,$b,$c] = $combo;
        if ($_SESSION['board'][$a] &&
            $_SESSION['board'][$a] === $_SESSION['board'][$b] &&
            $_SESSION['board'][$a] === $_SESSION['board'][$c]) {
            $_SESSION['winner'] = $_SESSION['board'][$a];
        }
    }
}

// Reset game
if (isset($_POST['reset'])) {
    unset($_SESSION['board'], $_SESSION['turn'], $_SESSION['winner']);
    header("Location: tic-tac-toe.php");
    exit;
}
?>
<!DOCTYPE html>
<html>
<head>
    <title>PHP Tic-Tac-Toe</title>
    <style>
        body { font-family: Arial, sans-serif; background: #f4f4f4; text-align: center; padding: 40px; }
        h1 { color: #333; }
        .board { display: grid; grid-template-columns: repeat(3, 100px); gap: 5px; margin: 20px auto; }
        .cell { width: 100px; height: 100px; font-size: 32px; display: flex; align-items: center; justify-content: center; background: #fff; border: 2px solid #333; cursor: pointer; }
        button { width: 100%; height: 100%; font-size: 32px; border: none; background: transparent; cursor: pointer; }
        .winner { font-size: 22px; margin: 20px; font-weight: bold; }
    </style>
</head>
<body>
    <h1>🎮 PHP Tic-Tac-Toe</h1>
    <form method="POST" class="board">
        <?php foreach ($_SESSION['board'] as $i => $mark): ?>
            <div class="cell">
                <?php if ($mark): ?>
                    <?= htmlspecialchars($mark) ?>
                <?php elseif (!$_SESSION['winner']): ?>
                    <button type="submit" name="cell" value="<?= $i ?>"></button>
                <?php endif; ?>
            </div>
        <?php endforeach; ?>
    </form>
    <?php if ($_SESSION['winner']): ?>
        <p class="winner">🏆 Player <?= $_SESSION['winner'] ?> wins!</p>
    <?php elseif (!in_array('', $_SESSION['board'])): ?>
        <p class="winner">🤝 It's a draw!</p>
    <?php else: ?>
        <p class="winner">Turn: <?= $_SESSION['turn'] ?></p>
    <?php endif; ?>
    <form method="POST">
        <button type="submit" name="reset">🔄 Restart Game</button>
    </form>
</body>
</html>
