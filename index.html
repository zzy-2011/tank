(() => {
  'use strict';
  const cv = document.getElementById('game'); const ctx = cv.getContext('2d');
  const W = cv.width, H = cv.height;
  const dpr = Math.max(1, Math.min(3, window.devicePixelRatio || 1));
  cv.width = W * dpr; cv.height = H * dpr; ctx.scale(dpr, dpr);
  const scoreEl = document.getElementById('score'), livesEl = document.getElementById('lives'), levelEl = document.getElementById('level');
  const overlay = document.getElementById('overlay'), ovTitle = document.getElementById('ov-title'), ovSub = document.getElementById('ov-sub');
  const PW = 72, PH = 12, BRICKS_C = 8, BRICKS_R = 5;
  const BW = W / BRICKS_C, BH = 22;
  const COLORS = ['#ff5c7a', '#ff9f43', '#ffd23f', '#43d97a', '#4fd1ff'];
  let paddle, ball, bricks, score, lives, level, over, keys, speed;

  function resetBall() { ball = { x: paddle.x + PW / 2, y: H - PH - 14, vx: (Math.random() < 0.5 ? -1 : 1) * speed * 0.7, vy: -speed }; }
  function buildBricks() {
    bricks = [];
    for (let r = 0; r < BRICKS_R + (level > 3 ? 1 : 0); r++) for (let c = 0; c < BRICKS_C; c++) bricks.push({ x: c * BW + 2, y: 50 + r * (BH + 4), w: BW - 4, h: BH, alive: true, color: COLORS[(r + level) % COLORS.length] });
  }
  function reset() {
    paddle = { x: W / 2 - PW / 2, y: H - PH - 6 };
    score = 0; lives = 3; level = 1; over = false; speed = 4.2; keys = {};
    scoreEl.textContent = '0'; livesEl.textContent = '3'; levelEl.textContent = '1';
    buildBricks(); resetBall(); overlay.classList.add('hidden');
  }
  function update() {
    if (over) return;
    if (keys['ArrowLeft'] || keys['a']) paddle.x -= 7;
    if (keys['ArrowRight'] || keys['d']) paddle.x += 7;
    paddle.x = Math.max(0, Math.min(W - PW, paddle.x));
    ball.x += ball.vx; ball.y += ball.vy;
    if (ball.x < 8) { ball.x = 8; ball.vx *= -1; }
    if (ball.x > W - 8) { ball.x = W - 8; ball.vx *= -1; }
    if (ball.y < 8) { ball.y = 8; ball.vy *= -1; }
    if (ball.y > H - PH - 6 && ball.vy > 0 && ball.x > paddle.x && ball.x < paddle.x + PW) {
      ball.y = H - PH - 6 - 1; ball.vy = -Math.abs(ball.vy);
      ball.vx = (ball.x - (paddle.x + PW / 2)) / (PW / 2) * speed * 0.8;
      const sp = Math.hypot(ball.vx, ball.vy) || speed; ball.vx = ball.vx / sp * speed; ball.vy = ball.vy / sp * speed;
    }
    if (ball.y > H) { lives--; livesEl.textContent = lives; if (lives <= 0) { over = true; ovTitle.textContent = '游戏结束'; ovSub.textContent = '得分 ' + score; overlay.classList.remove('hidden'); } else resetBall(); }
    for (const b of bricks) {
      if (!b.alive) continue;
      if (ball.x > b.x && ball.x < b.x + b.w && ball.y > b.y && ball.y < b.y + b.h) {
        b.alive = false; score += 10; scoreEl.textContent = score;
        const fromTop = ball.y - b.y < 8, fromSide = ball.x - b.x < 8 || ball.x > b.x + b.w - 8;
        if (fromTop) ball.vy *= -1; else if (fromSide) ball.vx *= -1; else ball.vy *= -1;
        break;
      }
    }
    if (bricks.every(b => !b.alive)) { level++; levelEl.textContent = level; speed += 0.6; buildBricks(); resetBall(); }
  }
  function draw() {
    ctx.fillStyle = '#1a1c3a'; ctx.fillRect(0, 0, W, H);
    for (const b of bricks) if (b.alive) { ctx.fillStyle = b.color; ctx.fillRect(b.x, b.y, b.w, b.h); ctx.fillStyle = 'rgba(255,255,255,0.2)'; ctx.fillRect(b.x, b.y, b.w, 3); }
    ctx.fillStyle = '#6c7bff'; ctx.fillRect(paddle.x, paddle.y, PW, PH);
    ctx.fillStyle = '#fff'; ctx.beginPath(); ctx.arc(ball.x, ball.y, 8, 0, Math.PI * 2); ctx.fill();
  }
  window.addEventListener('keydown', e => { keys[e.key] = true; if (['ArrowLeft', 'ArrowRight', 'a', 'd'].includes(e.key)) e.preventDefault(); });
  window.addEventListener('keyup', e => { keys[e.key] = false; });
  function pointer(e) { const rect = cv.getBoundingClientRect(); const px = (e.clientX - rect.left) / rect.width * W; paddle.x = Math.max(0, Math.min(W - PW, px - PW / 2)); }
  cv.addEventListener('mousemove', pointer);
  cv.addEventListener('touchmove', e => { const t = e.touches[0]; const rect = cv.getBoundingClientRect(); const px = (t.clientX - rect.left) / rect.width * W; paddle.x = Math.max(0, Math.min(W - PW, px - PW / 2)); }, { passive: true });
  document.getElementById('new').addEventListener('click', reset);
  document.getElementById('ov-btn').addEventListener('click', reset);
  function loop() { update(); draw(); requestAnimationFrame(loop); }
  reset(); requestAnimationFrame(loop);
})();
