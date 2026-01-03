# Jogo_memoriaa

<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <title>Jogo da Memória</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(135deg, #0f172a, #1e293b);
      color: #fff;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
    }
    .game {
      background: #020617;
      padding: 15px;
      border-radius: 12px;
      width: 100%;
      max-width: 380px;
      box-shadow: 0 10px 30px rgba(0,0,0,0.4);
    }
    .top {
      display: flex;
      justify-content: space-between;
      margin-bottom: 10px;
      font-size: 14px;
    }
    .grid {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 8px;
      margin-top: 10px;
    }
    .card {
      background: #1e293b;
      height: 70px;
      border-radius: 8px;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 28px;
      cursor: pointer;
      user-select: none;
      transition: 0.2s;
    }
    .card.open, .card.matched {
      background: #22c55e;
      cursor: default;
    }
    button {
      width: 100%;
      margin-top: 10px;
      padding: 10px;
      border: none;
      border-radius: 8px;
      background: #3b82f6;
      color: #fff;
      font-size: 16px;
      cursor: pointer;
    }
    button.secondary {
      background: #f59e0b;
    }
    .ad {
      margin-top: 10px;
      background: #111827;
      text-align: center;
      padding: 10px;
      border-radius: 8px;
      font-size: 13px;
    }
  </style>
</head>
<body>
  <div class="game">
    <div class="top">
      <div>🪙 Moedas: <span id="coins">0</span></div>
      <div>Jogadas: <span id="moves">0</span></div>
    </div>

    <div class="grid" id="grid"></div>

    <button onclick="startGame()">🔄 Novo jogo (10 moedas)</button>
    <button class="secondary" onclick="watchAd()">📺 Assistir anúncio +20 moedas</button>

    <div class="ad">📢 Espaço do anúncio</div>
  </div>

  <script>
    const emojis = ['🍎','🍌','🍇','🍓','🍍','🥝','🍑','🍒'];
    let cards = [];
    let openCards = [];
    let moves = 0;
    let coins = Number(localStorage.getItem('coins')) || 100;

    document.getElementById('coins').innerText = coins;

    function startGame() {
      if (coins < 10) {
        alert('Sem moedas 😢 Assiste um anúncio pra ganhar mais!');
        return;
      }

      coins -= 10;
      saveCoins();

      const grid = document.getElementById('grid');
      grid.innerHTML = '';
      moves = 0;
      document.getElementById('moves').innerText = moves;
      openCards = [];

      cards = [...emojis, ...emojis]
        .sort(() => Math.random() - 0.5)
        .map(emoji => ({ emoji, matched: false }));

      cards.forEach(card => {
        const div = document.createElement('div');
        div.className = 'card';
        div.onclick = () => flipCard(div, card);
        grid.appendChild(div);
      });
    }

    function flipCard(div, card) {
      if (card.matched || openCards.length === 2) return;
      if (openCards.includes(card)) return;

      div.innerText = card.emoji;
      div.classList.add('open');
      openCards.push(card);

      if (openCards.length === 2) {
        moves++;
        document.getElementById('moves').innerText = moves;
        setTimeout(checkMatch, 500);
      }
    }

    function checkMatch() {
      const [c1, c2] = openCards;
      const openDivs = document.querySelectorAll('.card.open');

      if (c1.emoji === c2.emoji) {
        c1.matched = c2.matched = true;
        openDivs.forEach(d => d.classList.add('matched'));

        if (cards.every(c => c.matched)) {
          coins += 20;
          saveCoins();
          alert('Boa! Você ganhou +20 moedas 🪙');
        }
      } else {
        openDivs.forEach(d => {
          d.innerText = '';
          d.classList.remove('open');
        });
      }
      openCards = [];
    }

    function watchAd() {
      alert('Simulando anúncio...');
      setTimeout(() => {
        coins += 20;
        saveCoins();
        alert('Valeu! +20 moedas 🪙');
      }, 1500);
    }

    function saveCoins() {
      localStorage.setItem('coins', coins);
      document.getElementById('coins').innerText = coins;
    }
  </script>
</body>
</html>
