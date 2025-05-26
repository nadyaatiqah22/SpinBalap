<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Balapan Kelompok</title>
<style>
  body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #87cefa, #ff8fe1);
    margin: 0;
    padding: 30px 10px;
    text-align: center;
    user-select: none;
  }

  h1 {
    color: #2c3e50;
    margin-bottom: 5px;
  }

  #namesInput {
    width: 80%;
    max-width: 600px;
    padding: 12px 15px;
    font-size: 18px;
    border: 2px solid #2980b9;
    border-radius: 8px;
    margin-bottom: 15px;
    box-sizing: border-box;
  }

  #startBtn {
    background-color: #560464;
    border: none;
    padding: 14px 30px;
    font-size: 20px;
    color: white;
    border-radius: 50px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    margin-bottom: 30px;
  }

  #startBtn:hover:not(:disabled) {
    background-color: #1abc9c;
  }

  #startBtn:disabled {
    background-color: #7f8c8d;
    cursor: not-allowed;
  }

  #raceTrack {
    width: 90%;
    max-width: 700px;
    margin: 0 auto 40px;
    position: relative;
    background: repeating-linear-gradient(
      to bottom,
      #95a5a6,
      #95a5a6 5px,
      #ecf0f1 5px,
      #ecf0f1 25px
    );
    border-radius: 15px;
    padding: 10px 0;
    box-shadow: 0 5px 15px rgba(0,0,0,0.1);
  }

  .track {
    position: relative;
    height: 60px;
    margin: 15px 0;
    background: #330221;
    border-radius: 30px;
    overflow: hidden;
    box-shadow: inset 0 0 10px #fba6ff;
    border: 2px solid #bdc3c7;
  }

  /* garis lintasan */
  .track::before {
    content: "";
    position: absolute;
    top: 50%;
    left: 0;
    right: 0;
    border-top: 2px dashed #7f8c8d;
    transform: translateY(-50%);
    pointer-events: none;
  }

  .runner {
    position: absolute;
    top: 10px;
    left: 0;
    height: 40px;
    width: 80px;
    background-color: #3498db;
    border-radius: 20px;
    color: white;
    font-weight: 700;
    font-size: 16px;
    line-height: 40px;
    text-align: center;
    box-shadow: 0 3px 8px rgba(0,0,0,0.3);
    display: flex;
    align-items: center;
    justify-content: center;
    user-select: none;
    animation-name: runAnim;
    animation-timing-function: linear;
    animation-iteration-count: infinite;
    animation-duration: 0.7s;
  }

  /* Animasi kaki berlari sederhana */
  @keyframes runAnim {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-6px); }
  }

  /* finish line */
  #finishLine {
    position: absolute;
    top: 0;
    bottom: 0;
    right: 0;
    width: 12px;
    background:
      linear-gradient(45deg, black 25%, white 25%, white 50%, black 50%, black 75%, white 75%, white 100%);
    background-size: 24px 24px;
    border-left: 3px solid #e74c3c;
    border-radius: 0 15px 15px 0;
  }

  #winner {
    font-size: 28px;
    font-weight: 900;
    color: #44022a;
    text-shadow: 1px 1px 3px #00000080;
    min-height: 50px;
    margin-top: 15px;
    user-select: none;
  }
</style>
</head>
<body>

  <h1>🏃‍♀️ Balapan Kelompok</h1>
  <p>Masukkan daftar nama, pisahkan dengan koma</p>
  <input type="text" id="namesInput" placeholder="Contoh: Aldi, Budi, Citra, Dini" />
  <br />
  <button id="startBtn">Mulai Balapan!</button>

  <div id="raceTrack"></div>

  <div id="winner"></div>

  <script>
    const startBtn = document.getElementById('startBtn');
    const raceTrack = document.getElementById('raceTrack');
    const winnerDiv = document.getElementById('winner');

    function getColor(i) {
      const colors = ['#3498db', '#e67e22', '#9b59b6', '#2ecc71', '#f1c40f', '#1abc9c', '#e74c3c', '#34495e'];
      return colors[i % colors.length];
    }

    function createTrack(name, index) {
      const track = document.createElement('div');
      track.classList.add('track');

      const runner = document.createElement('div');
      runner.classList.add('runner');
      runner.style.backgroundColor = getColor(index);
      runner.textContent = name;

      track.appendChild(runner);

      const finishLine = document.createElement('div');
      finishLine.id = 'finishLine';
      track.appendChild(finishLine);

      raceTrack.appendChild(track);

      return { track, runner, position: 0, name };
    }

    startBtn.addEventListener('click', () => {
      const inputVal = document.getElementById('namesInput').value.trim();
      if (!inputVal) {
        alert('Masukkan setidaknya 2 nama.');
        return;
      }

      const names = inputVal.split(',').map(n => n.trim()).filter(n => n !== '');
      if (names.length < 2) {
        alert('Masukkan minimal 2 nama untuk balapan.');
        return;
      }

      // Reset UI
      raceTrack.innerHTML = '';
      winnerDiv.textContent = '';
      startBtn.disabled = true;

      const runners = names.map((name, i) => createTrack(name, i));

      let winnerFound = false;

      // Mulai animasi balapan
      const raceInterval = setInterval(() => {
        for (const runner of runners) {
          if (winnerFound) break;

          // Tambah posisi acak tapi makin kecil tiap interval (seperti lari menurun)
          const step = Math.random() * 20 + 5;
          runner.position += step;

          // Batasi posisi supaya gak melebihi track width minus lebar runner
          const maxPos = raceTrack.clientWidth - 100;
          if (runner.position > maxPos) runner.position = maxPos;

          runner.runner.style.left = runner.position + 'px';

          if (runner.position >= maxPos) {
            winnerFound = true;
            winnerDiv.textContent = `🏆 Terpilih: ${runner.name}! Selamat! 🎉`;
            clearInterval(raceInterval);
            startBtn.disabled = false;
            break;
          }
        }
      }, 100);
    });
  </script>
</body>
</html>
