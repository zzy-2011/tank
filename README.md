(() => {
  'use strict';
  const cv = document.getElementById('game'); const ctx = cv.getContext('2d');
  const W = cv.width, H = cv.height;
  const dpr = Math.max(1, Math.min(3, window.devicePixelRatio || 1));
  cv.width = W * dpr; cv.height = H * dpr; ctx.scale(dpr, dpr);
  const scoreEl = document.getElementById('score'), livesEl = document.getElementById('lives'), levelEl = document.getElementById('level');
  const overlay = document.getElementById('overlay'), ovTitle = document.getElementById('ov-title'), ovSub = document.getElementById('ov-sub');
  const GRID = 13, T = W / GRID, SZ = 28;
  const DV = [[0, -1], [1, 0], [0, 1], [-1, 0]];
  let wall, player, enemies, bullets, score, lives, level, over, keys, spawnTimer, total, spawned;

  function buildWalls() {
    wall = Array.from({ length: GRID }, () => Array(GRID).fill(0));
    for (let i = 0; i < GRID; i++) { wall[0][i] = 2; wall[GRID - 1][i] = 2; wall[i][0] = 2; wall[i][GRID - 1] = 2; }
    for (let n = 0; n < 14 + level * 2; n++) { const r = 2 + Math.floor(Math.random() * (GRID - 4)), c = 2 + Math.floor(Math.random() * (GRID - 4)); if (wall[r][c] === 0) wall[r][c] = 1; }
  }
  function overlap(ax, ay, bx, by) { return ax < bx + SZ && ax + SZ > bx && ay < by + SZ && ay + SZ > by; }
  function canMove(x, y, self) {
    if (x < 0 || y < 0 || x + SZ > W || y + SZ > H) return false;
    const c0 = Math.floor(x / T), c1 = Math.floor((x + SZ - 1) / T), r0 = Math.floor(y / T), r1 = Math.floor((y + SZ - 1) / T);
    for (let r = r0; r <= r1; r++) for (let c = c0; c <= c1; c++) if (wall[r][c]) return false;
    const list = [player, ...enemies];
    for (const t of list) if (t !== self && t.alive && overlap(x, y, t.x, t.y)) return false;
    return true;
  }
  function resetPlayer() { player = { x: 6 * T + 2, y: 11 * T + 2, dir: 0, alive: true, cd: 0 }; }
  function reset(full) {
    if (full) { score = 0; lives = 3; level = 1; }
    buildWalls(); resetPlayer(); enemies = []; bullets = []; over = false; keys = {};
    spawned = 0; total = 3 + level; spawnTimer = 0;
    scoreEl.textContent = score; livesEl.textContent = lives; levelEl.textContent = level;
    overlay.classList.add('hidden');
  }
  function spawnEnemy() {
    if (enemies.filter(e => e.alive).length >= 4 || spawned >= total) return;
    const c = 1 + Math.floor(Math.random() * (GRID - 2));
    const x = c * T + 2, y = 1 * T + 2;
    if (overlap(x, y, player.x, player.y)) return;
    enemies.push({ x, y, dir: 2, alive: true, cd: 0, t: Math.floor(Math.random() * 60) });
    spawned++;
  }
  function fire(t, owner) {
    if (t.cd > 0) return;
    t.cd = 18;
    const [dx, dy] = DV[t.dir];
    bullets.push({ x: t.x + SZ / 2 - 3 + dx * (SZ / 2), y: t.y + SZ / 2 - 3 + dy * (SZ / 2), dir: t.dir, owner, sp: 5 });
  }
  function moveTank(t, dir) { t.dir = dir; const [dx, dy] = DV[dir]; const nx = t.x + dx * 2.4, ny = t.y + dy * 2.4; if (canMove(nx, ny, t)) { t.x = nx; t.y = ny; } }
  function update() {
    if (over) return;
    if (keys['ArrowUp'] || keys['w']) moveTank(player, 0);
    if (keys['ArrowRight'] || keys['d']) moveTank(player, 1);
    if (keys['ArrowDown'] || keys['s']) moveTank(player, 2);
    if (keys['ArrowLeft'] || keys['a']) moveTank(player, 3);
    if (keys[' '] || keys['j']) fire(player, 'p');
    if (player.cd > 0) player.cd--;
    spawnTimer++; if (spawnTimer > 70) { spawnTimer = 0; spawnEnemy(); }
    for (const e of enemies) {
      if (!e.alive) continue;
      e.t--; if (e.t <= 0) { e.dir = Math.floor(Math.random() * 4); e.t = 30 + Math.floor(Math.random() * 50); }
      if (!moveTank(e, e.dir)) e.dir = Math.floor(Math.random() * 4);
      if (e.cd > 0) e.cd--; if (Math.random() < 0.02) fire(e, 'e');
    }
    for (const b of bullets) {
      const [dx, dy] = DV[b.dir]; b.x += dx * b.sp; b.y += dy * b.sp;
      const cc = Math.floor((b.y + 3) / T), cr = Math.floor((b.x + 3) / T);
      if (b.x < 0 || b.y < 0 || b.x > W || b.y > H) { b.dead = true; continue; }
      if (cc >= 0 && cc < GRID && cr >= 0 && cr < GRID && wall[cc][cr]) { if (wall[cc][cr] === 1) wall[cc][cr] = 0; b.dead = true; continue; }
      if (b.owner === 'p') { for (const e of enemies) if (e.alive && overlap(b.x, b.y, e.x, e.y)) { e.alive = false; score += 100; scoreEl.textContent = score; b.dead = true; break; } }
      else { if (player.alive && overlap(b.x, b.y, player.x, player.y)) { player.alive = false; b.dead = true; lives--; livesEl.textContent = lives; if (lives <= 0) { over = true; ovTitle.textContent = '游戏结束'; ovSub.textContent = '得分 ' + score; overlay.classList.remove('hidden'); } else resetPlayer(); } }
    }
    bullets = bullets.filter(b => !b.dead);
    if (spawned >= total && enemies.every(e => !e.alive)) { level++; levelEl.textContent = level; total = 3 + level; spawned = 0; buildWalls(); resetPlayer(); enemies = []; bullets = []; }
  }
  function drawTank(t, color) {
    ctx.fillStyle = color; ctx.fillRect(t.x, t.y, SZ, SZ);
    const [dx, dy] = DV[t.dir]; ctx.fillStyle = '#10122a';
    ctx.fillRect(t.x + SZ / 2 - 3 + dx * 8, t.y + SZ / 2 - 3 + dy * 8, 6, 6);
  }
  function draw() {
    ctx.fillStyle = '#1a1c3a'; ctx.fillRect(0, 0, W, H);
    for (let r = 0; r < GRID; r++) for (let c = 0; c < GRID; c++) { if (wall[r][c] === 1) { ctx.fillStyle = '#a0522d'; ctx.fillRect(c * T + 1, r * T + 1, T - 2, T - 2); ctx.fillStyle = '#7a3b1d'; ctx.fillRect(c * T + 1, r * T + 1, T - 2, 3); } else if (wall[r][c] === 2) { ctx.fillStyle = '#9aa0c0'; ctx.fillRect(c * T + 1, r * T + 1, T - 2, T - 2); } }
    if (player.alive) drawTank(player, '#43d97a');
    for (const e of enemies) if (e.alive) drawTank(e, '#ff5c7a');
    ctx.fillStyle = '#ffd23f'; for (const b of bullets) ctx.fillRect(b.x, b.y, 6, 6);
  }
  window.addEventListener('keydown', e => { keys[e.key] = true; if (['ArrowUp', 'ArrowDown', 'ArrowLeft', 'ArrowRight', ' ', 'w', 'a', 's', 'd', 'j'].includes(e.key)) e.preventDefault(); });
  window.addEventListener('keyup', e => { keys[e.key] = false; });
  cv.addEventListener('touchstart', e => { e.preventDefault(); const t = e.changedTouches[0]; const rect = cv.getBoundingClientRect(); const px = (t.clientX - rect.left) / rect.width * W, py = (t.clientY - rect.top) / rect.height * H; if (py < H / 2) moveTank(player, 0); else if (px < W / 2) moveTank(player, 3); else moveTank(player, 1); fire(player, 'p'); }, { passive: false });
  document.getElementById('new').addEventListener('click', () => reset(false));
  document.getElementById('ov-btn').addEventListener('click', () => reset(true));
  function loop() { update(); draw(); requestAnimationFrame(loop); }
  reset(true); requestAnimationFrame(loop);
})();
