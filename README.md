<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no, viewport-fit=cover">
<title>転がりチーズ オンライン＆レース</title>
<style>
  :root{
    --bg:#a9cdea; --fg:#1d2b1d; --panel:#1f3524; --panel-fg:#fff; --accent:#ffd23f; --accent-fg:#1f3524;
    --ok:#69db7c; --danger:#ff6b6b; --muted:rgba(255,255,255,.14);
    --sky:#a9cdea; --grass:#5f8f3e; --grass-dark:#4c7731; --fence:#7a5a3a;
    --cheese-rind:#e3a03a; --cheese-face:#f3cf72; --trail:#fff4d6;
    --rock:#8c8a82; --bark:#6b4a2f; --wood:#c9a574; --straw:#d8b55a;
    --gorse:#34502a; --gorse-light:#6f8f3c; --gorse-flower:#efc72c; --board:#d69e56; --board-line:#7a4e24;
    --skins:#f1c9a5,#e0ac85,#c68a62,#8d5a3b;
    --shirts:#f2f2f2,#c93a3a,#2f5fbf,#3a8a4a,#262626,#e0b52e,#7a3fae,#e07a2c,#4fa3c7,#b8b8b8;
    --pants:#2f3b55,#3a3a3a,#5a6b7d,#4b3a2a,#22304a,#6b6b52;
    --hair:#2a1d14,#5a3a22,#a87a4a,#161616,#cdb27a; --shoes:#eee,#2e2e2e,#7a3b2a,#3a4a6a;
  }
  *{box-sizing:border-box;-webkit-tap-highlight-color:transparent}
  html,body{margin:0;height:100%;overflow:hidden}
  body{background:var(--bg);color:var(--fg);font-family:"Hiragino Sans","Yu Gothic","Noto Sans JP",system-ui,sans-serif;
    touch-action:manipulation;-webkit-user-select:none;user-select:none;-webkit-touch-callout:none}
  #game{position:fixed;inset:0}
  canvas{display:block;touch-action:none}
  .hidden{display:none !important}
  #joy{position:absolute;left:0;top:0;width:120px;height:120px;margin:-60px 0 0 -60px;border-radius:50%;
    background:rgba(255,255,255,.22);border:2px solid rgba(255,255,255,.7);pointer-events:none}
  #knob{position:absolute;left:50%;top:50%;width:52px;height:52px;margin:-26px 0 0 -26px;border-radius:50%;background:rgba(255,255,255,.9)}
  #hudWrap{position:absolute;left:0;right:0;top:calc(env(safe-area-inset-top,0px) + 10px);
    display:flex;flex-direction:column;align-items:center;pointer-events:none}
  #hud{font-size:28px;font-weight:800;font-variant-numeric:tabular-nums;color:#162618;text-shadow:0 2px 6px rgba(255,255,255,.8)}
  #hudSub{margin-top:3px;font-size:12px;font-weight:700;font-variant-numeric:tabular-nums;
    background:rgba(31,53,36,.85);color:#ffd23f;padding:3px 12px;border-radius:999px}
  #menuBtn{position:absolute;left:12px;top:calc(env(safe-area-inset-top,0px) + 12px);border:none;border-radius:10px;
    padding:9px 13px;font-size:12px;font-weight:700;cursor:pointer;background:rgba(31,53,36,.85);color:#fff;z-index:5}
  #diveBtn{position:absolute;right:18px;bottom:calc(env(safe-area-inset-bottom,0px) + 24px);width:102px;height:102px;
    border-radius:50%;border:3px solid rgba(255,255,255,.85);background:rgba(255,210,63,.92);color:#1f3524;
    font-size:15px;font-weight:900;box-shadow:0 8px 20px rgba(0,0,0,.3);cursor:pointer;z-index:5;
    display:flex;flex-direction:column;align-items:center;justify-content:center;touch-action:none}
  #diveBtn small{font-size:10px;opacity:.75;margin-top:2px}
  #diveBtn.disabled{opacity:.45;transform:scale(.95)}
  .overlay{position:fixed;inset:0;display:flex;align-items:center;justify-content:center;padding:10px;
    background:rgba(15,28,18,.52);backdrop-filter:blur(4px);z-index:10}
  .card{width:100%;max-width:460px;max-height:94vh;overflow-y:auto;background:var(--panel);color:var(--panel-fg);
    border-radius:18px;padding:16px 15px;box-shadow:0 16px 40px rgba(0,0,0,.45)}
  .card h1{margin:0 0 10px;text-align:center;font-size:21px}
  .modes{display:grid;grid-template-columns:repeat(3,1fr);gap:6px;margin-bottom:9px}
  .mode-btn{border:2px solid rgba(255,255,255,.18);border-radius:10px;padding:7px 4px;cursor:pointer;
    background:rgba(255,255,255,.06);color:#fff;text-align:center}
  .mode-btn strong{display:block;font-size:12px;margin-bottom:2px}
  .mode-btn span{display:block;font-size:9.5px;opacity:.8;line-height:1.2}
  .mode-btn.active{border-color:var(--accent);background:rgba(255,210,63,.2);color:var(--accent)}
  .mode-btn.wide{grid-column:span 2}
  .mode-hint{font-size:11.5px;line-height:1.45;color:rgba(255,255,255,.9);background:rgba(255,255,255,.07);
    border-radius:9px;padding:7px 10px;margin-bottom:9px}
  .best-box{display:flex;justify-content:space-between;align-items:center;background:rgba(0,0,0,.25);
    border-radius:10px;padding:8px 12px;margin-bottom:9px;font-size:13px;font-weight:700}
  .best-box .val{color:var(--accent);font-size:17px;font-variant-numeric:tabular-nums}
  .online-box{background:rgba(0,0,0,.25);border:1px solid rgba(255,255,255,.14);border-radius:12px;padding:10px;margin-bottom:10px}
  .sub-tabs,.toggle-row{display:grid;grid-template-columns:1fr 1fr;gap:6px}
  .sub-tabs{margin-bottom:8px}
  .sub-tab,.seg-btn{border:1.5px solid rgba(255,255,255,.2);border-radius:8px;padding:7px 5px;cursor:pointer;
    background:rgba(255,255,255,.06);color:#fff;font-size:12px;font-weight:700}
  .sub-tab.active{border-color:var(--accent);background:var(--accent);color:var(--accent-fg)}
  .seg-btn.active{border-color:var(--accent);background:rgba(255,210,63,.22);color:var(--accent)}
  .sub-tab:disabled,.seg-btn:disabled{opacity:.45;cursor:not-allowed}
  .status-line{display:flex;align-items:center;gap:8px;font-size:12px;font-weight:700;background:rgba(255,255,255,.06);
    border-radius:8px;padding:7px 9px;margin-bottom:8px;line-height:1.35}
  .dot{width:10px;height:10px;border-radius:50%;background:#adb5bd;flex-shrink:0}
  .dot.wait{background:var(--accent);animation:pulse 1s infinite}
  .dot.ok{background:var(--ok)}
  .dot.err{background:var(--danger)}
  @keyframes pulse{0%,100%{opacity:1}50%{opacity:.35}}
  .chips{display:flex;flex-wrap:wrap;gap:5px;margin:6px 0 8px}
  .chip{font-size:11px;font-weight:700;padding:3px 9px;border-radius:999px;background:rgba(255,255,255,.12)}
  .chip.me{background:var(--accent);color:var(--accent-fg)}
  .room-row{display:flex;gap:6px;margin-bottom:8px}
  .room-input{flex:1;min-width:0;border:1px solid rgba(255,255,255,.25);border-radius:8px;background:rgba(255,255,255,.1);
    color:#fff;padding:8px 10px;font-size:14px;font-weight:700;-webkit-user-select:text;user-select:text;outline:none}
  .mini-btn{border:none;border-radius:8px;padding:0 10px;font-size:12px;font-weight:700;cursor:pointer;background:var(--muted);color:#fff}
  .toggle-group{margin-top:8px}
  .toggle-label{font-size:11px;font-weight:700;opacity:.85;margin-bottom:4px;display:block}
  .settings{display:flex;flex-direction:column;gap:7px;margin-bottom:12px}
  .setting-row{background:rgba(255,255,255,.06);border-radius:9px;padding:6px 10px}
  .setting-head{display:flex;justify-content:space-between;margin-bottom:3px;font-size:12px;font-weight:700}
  .setting-val{color:var(--accent);font-variant-numeric:tabular-nums}
  input[type=range]{width:100%;accent-color:var(--accent);cursor:pointer;margin:0}
  .btn-row{display:flex;gap:7px;flex-wrap:wrap}
  .btn{flex:1;min-width:100px;border:none;border-radius:10px;padding:11px 10px;font-size:13.5px;font-weight:800;cursor:pointer}
  .btn-primary{background:var(--accent);color:var(--accent-fg)}
  .btn-sub{background:var(--muted);color:#fff}
  .btn-danger{background:rgba(255,107,107,.24);color:#ffb3b3}
  .btn:disabled{opacity:.42;cursor:not-allowed}
  .result-card{text-align:center;max-width:370px}
  .result-mode{font-size:12px;opacity:.82;margin:0 0 5px;font-weight:700}
  .result-title{font-size:21px;font-weight:900;margin:0 0 6px;color:var(--accent)}
  .result-title.fail{color:#ff8a80}
  .result-value{font-size:38px;font-weight:900;margin:0 0 6px;font-variant-numeric:tabular-nums}
  .result-best{font-size:13px;font-weight:700;margin:0 0 12px;color:rgba(255,255,255,.88)}
  .new-best{display:inline-block;background:var(--accent);color:var(--accent-fg);font-size:11.5px;font-weight:900;
    padding:3px 10px;border-radius:999px;margin-bottom:6px}
  .rank-list{list-style:none;margin:0 0 14px;padding:0;text-align:left;display:flex;flex-direction:column;gap:5px}
  .rank-item{display:flex;justify-content:space-between;align-items:center;padding:6px 10px;border-radius:8px;
    background:rgba(255,255,255,.08);font-size:13px;font-weight:700}
  .rank-item.me{background:rgba(255,210,63,.22);border:1px solid var(--accent);color:var(--accent)}
  .rank-dot{display:inline-block;width:10px;height:10px;border-radius:50%;margin-right:7px;vertical-align:middle}
</style>
</head>
<body>
<div id="game">
  <button id="menuBtn" type="button" class="hidden">設定 / 退出</button>
  <div id="hudWrap" class="hidden">
    <div id="hud">0 m</div>
    <div id="hudSub">BEST 0 m</div>
  </div>
  <button id="diveBtn" type="button" class="hidden">飛びつく!<small>Space</small></button>
  <div id="joy" class="hidden" aria-hidden="true"><div id="knob"></div></div>

  <!-- タイトル＆設定 -->
  <div id="menuOverlay" class="overlay">
    <div class="card">
      <h1>転がりチーズ</h1>
      <div class="modes">
        <button type="button" class="mode-btn active" data-mode="normal"><strong>通常モード</strong><span>3000mで勝利</span></button>
        <button type="button" class="mode-btn" data-mode="board"><strong>板よけモード</strong><span>板を高速回避</span></button>
        <button type="button" class="mode-btn" data-mode="human"><strong>人間モード</strong><span>障害物すり抜け</span></button>
        <button type="button" class="mode-btn" data-mode="race"><strong>チーズレース</strong><span>ゴール競走</span></button>
        <button type="button" class="mode-btn wide" data-mode="online"><strong>オンライン対戦（最大6人）</strong><span>1vs最大5人鬼ごっこ / レース</span></button>
      </div>

      <div class="mode-hint" id="modeHint"></div>

      <div class="best-box" id="bestBox">
        <span id="menuBestLabel">ベスト</span>
        <span class="val" id="menuBestVal">---</span>
      </div>

      <!-- オフラインレース用コース選択 -->
      <div class="toggle-group hidden" id="raceCourseBox" style="margin:0 0 10px">
        <span class="toggle-label">レースコース</span>
        <div class="toggle-row">
          <button type="button" class="seg-btn active" data-rcourse="normal">通常コース</button>
          <button type="button" class="seg-btn" data-rcourse="board">板よけコース</button>
        </div>
      </div>

      <!-- オンラインロビー -->
      <div class="online-box hidden" id="onlineBox">
        <div class="sub-tabs">
          <button type="button" class="sub-tab active" data-match="free">全国フリーマッチ</button>
          <button type="button" class="sub-tab" data-match="room">合言葉マッチ</button>
        </div>
        <div class="status-line"><span class="dot" id="netDot"></span><span id="netStatus">ルールを選んで開始してください</span></div>
        <div class="chips hidden" id="playerChips"></div>

        <div class="toggle-group" style="margin-top:0">
          <span class="toggle-label">対戦ルール</span>
          <div class="toggle-row">
            <button type="button" class="seg-btn active" data-otype="chase">鬼ごっこ（1vs最大5）</button>
            <button type="button" class="seg-btn" data-otype="race">チーズレース（最大6）</button>
          </div>
        </div>
        <div class="toggle-group" style="margin-bottom:9px">
          <span class="toggle-label">コース</span>
          <div class="toggle-row">
            <button type="button" class="seg-btn active" data-course="normal">通常コース</button>
            <button type="button" class="seg-btn" data-course="board">板よけコース</button>
          </div>
        </div>

        <div id="freeMatchUI">
          <div class="btn-row">
            <button id="freeSearchBtn" type="button" class="btn btn-primary">全国フリーマッチ開始</button>
            <button id="freeStartNowBtn" type="button" class="btn btn-primary hidden">今すぐ開始</button>
            <button id="freeCancelBtn" type="button" class="btn btn-danger hidden">中止</button>
          </div>
        </div>

        <div id="roomMatchUI" class="hidden">
          <div id="connectForm">
            <div class="room-row">
              <input id="roomCode" class="room-input" type="text" maxlength="20" placeholder="合言葉（例: 4821）" autocomplete="off">
              <button id="randCodeBtn" type="button" class="mini-btn">ランダム</button>
            </div>
            <div class="btn-row">
              <button id="hostBtn" type="button" class="btn btn-primary">部屋を作る</button>
              <button id="joinBtn" type="button" class="btn btn-sub">部屋に入る</button>
            </div>
          </div>
          <div id="connectedControls" class="hidden">
            <div class="btn-row" style="margin-bottom:7px">
              <button id="roomStartBtn" type="button" class="btn btn-primary hidden">対戦スタート</button>
              <button id="copyInviteBtn" type="button" class="btn btn-sub hidden">招待URLコピー</button>
              <button id="disconnectBtn" type="button" class="btn btn-danger">接続を切る</button>
            </div>
          </div>
          <div class="toggle-group" id="roleToggleBox">
            <span class="toggle-label" id="roleToggleLabel">ホストの役割（他は人間）</span>
            <div class="toggle-row">
              <button type="button" class="seg-btn active" data-role="cheese">チーズ役（逃げる）</button>
              <button type="button" class="seg-btn" data-role="human">人間役（追う）</button>
            </div>
          </div>
        </div>
      </div>

      <!-- スライダー設定 -->
      <div class="settings" id="settingsArea">
        <div class="setting-row">
          <div class="setting-head"><label for="optGoal">ゴール距離（最大3000m）</label><span class="setting-val" id="valGoal">3000 m</span></div>
          <input id="optGoal" type="range" min="300" max="3000" step="100">
        </div>
        <div class="setting-row">
          <div class="setting-head"><label for="optSpeed">チーズの速さ</label><span class="setting-val" id="valSpeed">9.5 m/s</span></div>
          <input id="optSpeed" type="range" min="5" max="120" step="0.5">
        </div>
        <div class="setting-row">
          <div class="setting-head"><label for="optVx" id="lblVx">横移動の速さ</label><span class="setting-val" id="valVx">4.5 m/s</span></div>
          <input id="optVx" type="range" min="3" max="60" step="0.5">
        </div>
        <div class="setting-row">
          <div class="setting-head"><label for="optChasers" id="lblChasers">追っ手の人数</label><span class="setting-val" id="valChasers">6 人</span></div>
          <input id="optChasers" type="range" min="1" max="300" step="1">
        </div>
        <div class="setting-row" id="rowChSpeed">
          <div class="setting-head"><label for="optChSpeed" id="lblChSpeed">追っ手の速さ</label><span class="setting-val" id="valChSpeed">1.0 倍</span></div>
          <input id="optChSpeed" type="range" min="0.5" max="1.8" step="0.05">
        </div>
        <div class="setting-row hidden" id="rowBoardGap">
          <div class="setting-head"><label for="optBoardGap">板の間隔</label><span class="setting-val" id="valBoardGap">25 m</span></div>
          <input id="optBoardGap" type="range" min="10" max="60" step="1">
        </div>
        <div class="setting-row">
          <div class="setting-head"><label for="optCamUp">カメラの高さ</label><span class="setting-val" id="valCamUp">5 m</span></div>
          <input id="optCamUp" type="range" min="3" max="70" step="1">
        </div>
      </div>

      <div class="btn-row" id="bottomStartRow">
        <button id="presetResetBtn" type="button" class="btn btn-sub">初期値</button>
        <button id="startBtn" type="button" class="btn btn-primary">スタート</button>
      </div>
    </div>
  </div>

  <!-- 結果画面 -->
  <div id="resultOverlay" class="overlay hidden">
    <div class="card result-card">
      <p class="result-mode" id="resMode"></p>
      <p class="result-title hidden" id="resTitle"></p>
      <div class="new-best hidden" id="resNewBest">NEW BEST!</div>
      <p class="result-value" id="dist">0 m</p>
      <p class="result-best" id="resBest"></p>
      <ul class="rank-list hidden" id="rankList"></ul>
      <div class="btn-row">
        <button id="toMenuBtn" type="button" class="btn btn-sub">モード・設定</button>
        <button id="nextFreeBtn" type="button" class="btn btn-primary hidden">次の相手を探す</button>
        <button id="swapRetryBtn" type="button" class="btn btn-sub hidden">役割交代で再戦</button>
        <button id="retryBtn" type="button" class="btn btn-primary">もう一回</button>
      </div>
    </div>
  </div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script src="https://unpkg.com/peerjs@1.5.4/dist/peerjs.min.js"></script>
<script>
(() => {
  'use strict';
  const $ = id => document.getElementById(id);
  const css = n => getComputedStyle(document.documentElement).getPropertyValue(n).trim();
  const list = n => css(n).split(',').map(s => s.trim());
  const clamp = (v, a, b) => v < a ? a : v > b ? b : v;
  const lerp = (a, b, t) => a + (b - a) * t;
  const frac = x => x - Math.floor(x);
  const smooth = t => t * t * (3 - 2 * t);
  const TAU = Math.PI * 2;

  const MAX_GOAL = 3000;
  const MAX_ONLINE_PLAYERS = 6; // チーズ1 + 人間最大5 / レース最大6チーズ
  const MAX_CHEESES = 6;
  const RACER_COLORS = ['#ffd23f', '#ff595e', '#38bdf8', '#4ade80', '#c084fc', '#fb923c'];

  const ROOM_PREFIX = 'kcheese-r3-';
  const FREE_PREFIX = 'kcheese-f3-';
  const FREE_SLOTS = 12;
  const PEER_OPTS = {
    debug: 0,
    config: {
      iceServers: [
        { urls:'stun:stun.l.google.com:19302' },
        { urls:'stun:stun1.l.google.com:19302' },
        { urls:'stun:stun.cloudflare.com:3478' },
      ],
    },
  };

  const PRESETS = {
    normal: { label:'通常モード', hint:'3000mゴールを超えたらチーズの勝ち！人間側は障害物をすり抜けて追ってきます。', goalDist:3000, speed:14, vxMax:6, chasers:6, chSpeed:1.0, boardGap:25, camUp:6 },
    board:  { label:'板よけモード', hint:'左右の板を高速で避けて3000mゴールを目指せ！人間側は板をすり抜けます。', goalDist:3000, speed:75, vxMax:32, chasers:60, chSpeed:1.0, boardGap:25, camUp:48 },
    human:  { label:'人間モード（追っ手）', hint:'あなたは障害物をすり抜けられます！矢印/WASDで走り、スペースか右下ボタンで飛びついて3000m前に捕まえよう。', goalDist:3000, speed:12, vxMax:7, chasers:6, chSpeed:1.05, boardGap:25, camUp:6 },
    race:   { label:'チーズレース', hint:'複数のチーズがゴール（最大3000m）を目指してぶつかり合いながら競走！1位を狙おう。', goalDist:3000, speed:24, vxMax:11, chasers:6, chSpeed:1.0, boardGap:28, camUp:10 },
    online: { label:'オンライン対戦', hint:'最大6人（チーズ1人 vs 人間最大5人）の鬼ごっこ、または最大6人のチーズレースで対戦！', goalDist:3000, speed:16, vxMax:8, chasers:0, chSpeed:1.05, boardGap:28, camUp:8 },
  };

  const FREE_RULES = {
    chase_normal: { onlineGame:'chase', onlineCourse:'normal', goalDist:3000, speed:16, vxMax:8,  chasers:0, chSpeed:1.05, boardGap:28, camUp:8 },
    chase_board:  { onlineGame:'chase', onlineCourse:'board',  goalDist:3000, speed:48, vxMax:28, chasers:0, chSpeed:1.05, boardGap:28, camUp:38 },
    race_normal:  { onlineGame:'race',  onlineCourse:'normal', goalDist:3000, speed:24, vxMax:11, chasers:6, chSpeed:1.0,  boardGap:28, camUp:10 },
    race_board:   { onlineGame:'race',  onlineCourse:'board',  goalDist:3000, speed:52, vxMax:30, chasers:6, chSpeed:1.0,  boardGap:28, camUp:38 },
  };

  const userCfg = { mode:'normal', raceCourse:'normal', onlineType:'free', onlineGame:'chase', onlineCourse:'normal', hostRole:'cheese', ...PRESETS.normal };

  const cfg = {
    mode:'normal', onlineType:'room', isOnline:false, isRace:false, course:'normal', goalDist:3000,
    myRole:'cheese', myIndex:0, humanPlayerCount:0,
    halfW:8, xlim:7.8, cheeseR:0.32, cheeseW:0.2, gFall:9.8, gAlong:6.556, drag:0.073, turnCost:0.1,
    vxMax:4.5, latAcc:22, bounce:0.3, bounceMin:1.2, airControl:0.6,
    chStart:6, chMax:12, chAddEvery:100, startDelay:1.0, startGrace:2.0, chSpeedMult:1.0, runAcc:7, chVx:3.2,
    boardGap:25, boardW:20, boardH:3.2, boardThick:1.6, thigh:0.43, shin:0.43,
    camBack:8, camUp:5, camFollowX:0.75, camVfovMax:65, targetSpeed:14,
  };

  const net = {
    peer:null, hostConn:null, clients:new Map(), roster:[], myId:'host',
    role:'none', matchKind:'room', searching:false, connected:false, room:'',
    searchGen:0, startTimer:null, joinTimer:null, helloTimer:null, lastSend:0, snap:null,
  };

  // ---------- 固定定数 ----------
  const SLOPE = 0.9, CH_ALLOC = 300;
  const TILT_KICK = 0.35, TILT_SPRING = 30, TILT_DAMP = 4, TILT_DRIFT = 3;
  const JOY_R = 50, JOY_DEAD = 0.12, PUSH_ACC = 0.8, BRAKE_ACC = 6;
  const CATCHUP_DIST = 10, CATCHUP = 3.5, SPEED_VAR = 0.3;
  const DIVE_RANGE = 3.0, DIVE_BACK = 0.5, DIVE_DX = 2.2, WINDUP = 0.2;
  const DIVE_T = 0.42, DIVE_REACH = 0.9, DIVE_SPIN = 2.2, AIM = 0.7;
  const CATCH_WINDOW = DIVE_T + 0.35, CATCH_HEIGHT = 0.95;
  const RAG_MIN = 1.0, RAG_MAX = 2.6, RAG_STILL = 2.5, GETUP_T = 0.6, COOLDOWN = 0.8;
  const RESPAWN_BEHIND = 30, SPAWN_BEHIND = 20, OVER_DELAY = 1.9, SLOW_SCALE = 0.25;
  const ZOOM_SPEED = 3, ZOOM_BACK = 3.0, ZOOM_UP = 1.8, ZOOM_SIDE = 1.3;
  const RAG_ITER = 8, RAG_DAMP = 0.999, RAG_MU = 0.85, RUN_DUTY = 0.28, UPARM = 0.29, FOREARM = 0.27, FOOT_H = 0.07;
  const OB_BAND = 4, OB_START = 25, OB_RATE0 = 0.6, OB_RATE_GROW = 1/500, OB_RATE_MAX = 1, OB_SECOND = 1/600;
  const OB_KIND = [['rock', 0.3], ['bush', 0.25], ['log', 0.25], ['bale', 0.2]];
  const OB_AHEAD = 160, OB_BEHIND = 30, ROCK_VARIANTS = 5, BUSH_VARIANTS = 3, BUSH_SPIKES = 220, BUSH_FLOWERS = 40;
  const OB_BOUNCE = 0.35, OB_LOSS = 0.8, BUSH_DRAG = 6;
  const STEP = 1 / 120, SYNC_MS = 33;
  const TRAIL_FADE = 0.6, TRAIL_N = 32, TRAIL_STEP = 0.16, TRAIL_ALPHA = 0.55, TRAIL_TAPER = 0.6;
  const CHUNK_LEN = 25, CHUNK_SEG_S = 44, TERRAIN_HALF = 28, TERRAIN_SEG_X = 68;
  const OUTSIDE_SHADE = 0.55, CHUNK_COUNT = 9, CHUNK_BEHIND = 20, GRASS_TILE = 2, POST_GAP = 2.5;
  const FOG_NEAR = 55, FOG_FAR = 165, SHADOW_SIZE = 14, CAM_LOOK = -1.5, CAM_HFOV = 60;

  // ---------- ベストスコア ----------
  const bestKey = m => `runawayCheese.v3.best.${m}`;
  function getBest(m){
    if (m === 'online') return null;
    try { const n = parseFloat(localStorage.getItem(bestKey(m))); return Number.isFinite(n) && n > 0 ? n : null; }
    catch { return null; }
  }
  function saveBest(m, val){
    if (m === 'online' || val <= 0) return false;
    const prev = getBest(m), lower = (m === 'human' || m === 'race');
    if (prev === null || (lower ? val < prev : val > prev)){
      try { localStorage.setItem(bestKey(m), String(val)); } catch {}
      return true;
    }
    return false;
  }
  function formatBest(m, v){
    if (v === null) return '---';
    if (m === 'race') return `最速 ${v.toFixed(1)} 秒`;
    return m === 'human' ? `最短 ${Math.round(v)} m` : `${Math.round(v)} m`;
  }

  // ---------- 地形 ----------
  function bump(x, s){
    return 0.5 * Math.sin(0.31 * s + 1.4 * Math.sin(0.043 * s)) * (0.75 + 0.25 * Math.sin(0.37 * x + 0.11 * s))
         + 0.14 * Math.sin(0.83 * s + 0.6 * x + 2.0) + 0.08 * Math.sin(1.37 * s - 0.9 * x + 0.5)
         + 0.12 * Math.sin(0.23 * x - 0.17 * s) + 0.03 * Math.sin(2.1 * s + 1.7 * x);
  }
  const ground = (x, s) => -SLOPE * s + bump(x, s);
  const EPS = 0.04;
  const slopeS = (x, s) => (bump(x, s + EPS) - bump(x, s - EPS)) / (2 * EPS) - SLOPE;
  const slopeX = (x, s) => (bump(x + EPS, s) - bump(x - EPS, s)) / (2 * EPS);

  // ---------- 状態 ----------
  let game = 'menu', outcome = 'caught', winnerIdx = -1, S0 = 0, overT = 0, playTime = 0;
  let activeN = 0, cheeseN = 1, shownHudKey = '', currentBest = null, maxDist = 0, diveRequested = false;
  let raceOrder = [], raceFinishTimer = 0;
  const stick = { x:0, y:0 }, keys = { left:false, right:false, up:false, down:false };
  const cheeses = Array.from({ length:MAX_CHEESES }, (_, i) => ({
    idx:i, s:0, x:0, y:0, v:0, vx:0, vy:0, rot:0, tilt:0, tiltV:0, grounded:true,
    held:null, hand:0, finished:false, finishTime:0, ctrlId:'ai', label:`P${i + 1}`,
    color:RACER_COLORS[i], aiBias:0, aiSpeedK:1,
  }));
  const mainCheese = () => cheeses[0];

  // ---------- three.js ----------
  const renderer = new THREE.WebGLRenderer({ antialias:true });
  renderer.setPixelRatio(Math.min(window.devicePixelRatio || 1, 2));
  renderer.outputEncoding = THREE.sRGBEncoding;
  renderer.shadowMap.enabled = true;
  renderer.shadowMap.type = THREE.PCFSoftShadowMap;
  $('game').prepend(renderer.domElement);
  const cv = renderer.domElement;
  const lin = c => new THREE.Color(c).convertSRGBToLinear();
  const scene = new THREE.Scene();
  scene.background = new THREE.Color(css('--sky'));
  scene.fog = new THREE.Fog(new THREE.Color(css('--sky')), FOG_NEAR, FOG_FAR);
  const camera = new THREE.PerspectiveCamera(60, 1, 0.1, 400);
  scene.add(new THREE.HemisphereLight(lin('#d6e8ff'), lin('#55703a'), 0.75));
  const sun = new THREE.DirectionalLight(lin('#fff3e0'), 1.5);
  sun.castShadow = true; sun.shadow.mapSize.set(2048, 2048);
  Object.assign(sun.shadow.camera, { left:-SHADOW_SIZE, right:SHADOW_SIZE, top:SHADOW_SIZE, bottom:-SHADOW_SIZE, near:1, far:60 });
  sun.shadow.bias = -0.0004; sun.shadow.normalBias = 0.03;
  scene.add(sun, sun.target);

  const UP = new THREE.Vector3(0, 1, 0);
  const rel = (out, s, x, y) => out.set(x, y + SLOPE * S0, -(s - S0));

  // ---------- テクスチャ ----------
  function rand(seed){ let t = seed >>> 0; return () => { t = (t + 0x6D2B79F5) >>> 0; let r = Math.imul(t ^ (t >>> 15), 1 | t); r ^= r + Math.imul(r ^ (r >>> 7), 61 | r); return ((r ^ (r >>> 14)) >>> 0) / 4294967296; }; }
  function tileNoise(S, cells, rnd){
    const g = new Float32Array(cells * cells).map(() => rnd()), out = new Float32Array(S * S);
    for (let y = 0; y < S; y++) for (let x = 0; x < S; x++){
      const fx = x / S * cells, fy = y / S * cells, x0 = Math.floor(fx), y0 = Math.floor(fy), tx = smooth(fx - x0), ty = smooth(fy - y0);
      const a = g[(y0 % cells) * cells + x0 % cells], b = g[(y0 % cells) * cells + (x0 + 1) % cells];
      const c = g[((y0 + 1) % cells) * cells + x0 % cells], d = g[((y0 + 1) % cells) * cells + (x0 + 1) % cells];
      out[y * S + x] = lerp(lerp(a, b, tx), lerp(c, d, tx), ty);
    }
    return out;
  }
  function canvasTex(S, H, fn){
    const c = document.createElement('canvas'); c.width = S; c.height = H;
    const g = c.getContext('2d'), img = g.createImageData(S, H);
    fn(img.data); g.putImageData(img, 0, 0);
    const t = new THREE.CanvasTexture(c); t.encoding = THREE.sRGBEncoding; return t;
  }
  function grassTexture(){
    const S = 256, rnd = rand(7), base = new THREE.Color(css('--grass')), dark = new THREE.Color(css('--grass-dark'));
    const n1 = tileNoise(S, 4, rnd), n2 = tileNoise(S, 16, rnd), lum = new Float32Array(S * S);
    for (let i = 0; i < S * S; i++) lum[i] = 0.55 * n1[i] + 0.45 * n2[i] + (rnd() - 0.5) * 0.25;
    for (let k = 0; k < 6000; k++){
      const x0 = Math.floor(rnd() * S), y0 = Math.floor(rnd() * S), len = 3 + Math.floor(rnd() * 8), sl = (rnd() - 0.5) * 0.6, sh = (rnd() - 0.45) * 0.7;
      for (let t = 0; t < len; t++) lum[((y0 - t + S) % S) * S + ((x0 + Math.round(t * sl) + S) % S)] += sh * (1 - t / len);
    }
    const t = canvasTex(S, S, d => {
      for (let i = 0; i < S * S; i++){
        const v = clamp(lum[i], 0, 1);
        d[i*4] = Math.round(255 * lerp(dark.r, base.r * 1.15, v)); d[i*4+1] = Math.round(255 * lerp(dark.g, base.g * 1.12, v));
        d[i*4+2] = Math.round(255 * lerp(dark.b, base.b * 1.05, v)); d[i*4+3] = 255;
      }
    });
    t.wrapS = t.wrapT = THREE.RepeatWrapping; t.anisotropy = renderer.capabilities.getMaxAnisotropy(); return t;
  }
  function rindTexture(tintHex){
    const S = 128, H = 32, rnd = rand(11), c = new THREE.Color(tintHex || css('--cheese-rind')), n = tileNoise(S, 16, rnd);
    return canvasTex(S, H, d => {
      for (let y = 0; y < H; y++) for (let x = 0; x < S; x++){
        const k = 0.84 + 0.22 * n[(y * 4 % S) * S + x] + (rnd() - 0.5) * 0.08, i = (y * S + x) * 4;
        d[i] = Math.round(255 * clamp(c.r * k, 0, 1)); d[i+1] = Math.round(255 * clamp(c.g * k, 0, 1));
        d[i+2] = Math.round(255 * clamp(c.b * k, 0, 1)); d[i+3] = 255;
      }
    });
  }
  function faceTexture(tintHex){
    const S = 128, rnd = rand(13), face = new THREE.Color(css('--cheese-face')), rind = new THREE.Color(tintHex || css('--cheese-rind')), n = tileNoise(S, 16, rnd);
    return canvasTex(S, S, d => {
      for (let y = 0; y < S; y++) for (let x = 0; x < S; x++){
        const dx = (x + 0.5) / S * 2 - 1, dy = (y + 0.5) / S * 2 - 1, r = Math.hypot(dx, dy);
        const base = r > 0.82 ? rind : face, k = 0.9 + 0.14 * n[y * S + x], i = (y * S + x) * 4;
        d[i] = Math.round(255 * clamp(base.r * k, 0, 1)); d[i+1] = Math.round(255 * clamp(base.g * k, 0, 1));
        d[i+2] = Math.round(255 * clamp(base.b * k, 0, 1)); d[i+3] = 255;
      }
    });
  }
  function boardTexture(){
    const S = 128, H = 64, rnd = rand(31), c = new THREE.Color(css('--board')), line = new THREE.Color(css('--board-line')), n = tileNoise(S, 16, rnd);
    return canvasTex(S, H, d => {
      for (let y = 0; y < H; y++) for (let x = 0; x < S; x++){
        const base = (x < 4 || x >= S - 4 || y < 4 || y >= H - 4 || y % 16 < 2) ? line : c;
        const g = 0.88 + 0.18 * n[(y * 2 % S) * S + x], i = (y * S + x) * 4;
        d[i] = Math.round(255 * clamp(base.r * g, 0, 1)); d[i+1] = Math.round(255 * clamp(base.g * g, 0, 1));
        d[i+2] = Math.round(255 * clamp(base.b * g, 0, 1)); d[i+3] = 255;
      }
    });
  }
  function checkerTexture(){
    const c = document.createElement('canvas'); c.width = 256; c.height = 64;
    const g = c.getContext('2d');
    for (let y = 0; y < 4; y++) for (let x = 0; x < 16; x++){
      g.fillStyle = (x + y) % 2 === 0 ? '#ffffff' : '#181818';
      g.fillRect(x * 16, y * 16, 16, 16);
    }
    const t = new THREE.CanvasTexture(c); t.encoding = THREE.sRGBEncoding; return t;
  }

  // ---------- 地形＆柵＆ゴールゲート ----------
  const NX = TERRAIN_SEG_X, NS = CHUNK_SEG_S, VN = (NX + 1) * (NS + 1), terrainIdx = [];
  for (let j = 0; j < NS; j++) for (let i = 0; i < NX; i++){
    const a = j * (NX + 1) + i, b = a + NX + 1;
    terrainIdx.push(a, a + 1, b, a + 1, b + 1, b);
  }
  const terrainMat = new THREE.MeshStandardMaterial({ map:grassTexture(), vertexColors:true, roughness:1, metalness:0 });
  const chunks = [];
  for (let k = 0; k < CHUNK_COUNT; k++){
    const geo = new THREE.BufferGeometry();
    geo.setAttribute('position', new THREE.BufferAttribute(new Float32Array(VN * 3), 3));
    geo.setAttribute('normal', new THREE.BufferAttribute(new Float32Array(VN * 3), 3));
    geo.setAttribute('color', new THREE.BufferAttribute(new Float32Array(VN * 3), 3));
    geo.setAttribute('uv', new THREE.BufferAttribute(new Float32Array(VN * 2), 2));
    geo.setIndex(terrainIdx);
    const mesh = new THREE.Mesh(geo, terrainMat);
    mesh.frustumCulled = false; mesh.receiveShadow = true; scene.add(mesh);
    chunks.push({ mesh, k:null });
  }
  function buildChunk(ch, k){
    ch.k = k; const s0 = k * CHUNK_LEN, uv0 = (((s0 % 100) + 100) % 100) / GRASS_TILE;
    const geo = ch.mesh.geometry, p = geo.attributes.position.array, n = geo.attributes.normal.array, col = geo.attributes.color.array, uv = geo.attributes.uv.array;
    let q = 0, u = 0;
    for (let j = 0; j <= NS; j++){
      const s = s0 + j * CHUNK_LEN / NS;
      for (let i = 0; i <= NX; i++){
        const x = -TERRAIN_HALF + i * 2 * TERRAIN_HALF / NX;
        p[q] = x; p[q+1] = bump(x, s) - SLOPE * (s - s0); p[q+2] = -(s - s0);
        const gx = slopeX(x, s), gs = slopeS(x, s), L = Math.hypot(gx, 1, gs);
        n[q] = -gx / L; n[q+1] = 1 / L; n[q+2] = gs / L;
        const shade = (Math.abs(x) > cfg.halfW ? OUTSIDE_SHADE : 1) * (0.9 + 0.1 * Math.sin(x * 0.7 + s * 0.31) * Math.sin(s * 0.13 - x * 0.4));
        col[q] = col[q+1] = col[q+2] = shade;
        uv[u] = x / GRASS_TILE; uv[u+1] = uv0 + (s - s0) / GRASS_TILE;
        q += 3; u += 2;
      }
    }
    for (const a of ['position', 'normal', 'color', 'uv']) geo.attributes[a].needsUpdate = true;
  }
  function updateChunks(){
    const k0 = Math.floor((S0 - CHUNK_BEHIND) / CHUNK_LEN);
    for (const ch of chunks) if (ch.k === null || ch.k < k0 || ch.k >= k0 + CHUNK_COUNT) ch.k = null;
    for (let k = k0; k < k0 + CHUNK_COUNT; k++) if (!chunks.some(ch => ch.k === k)) buildChunk(chunks.find(ch => ch.k === null), k);
    for (const ch of chunks){ const s0 = ch.k * CHUNK_LEN; ch.mesh.position.set(0, -SLOPE * (s0 - S0), -(s0 - S0)); }
  }

  const NPOST = Math.ceil(CHUNK_COUNT * CHUNK_LEN / POST_GAP) + 1;
  const fenceMat = new THREE.MeshStandardMaterial({ color:lin(css('--fence')), roughness:0.9 });
  const posts = new THREE.InstancedMesh(new THREE.BoxGeometry(0.1, 1.1, 0.1), fenceMat, NPOST * 2);
  const rails = new THREE.InstancedMesh(new THREE.BoxGeometry(0.05, 0.09, 1), fenceMat, NPOST * 4);
  for (const m of [posts, rails]){ m.frustumCulled = false; m.castShadow = m.receiveShadow = true; }
  scene.add(posts, rails);
  const dummy = new THREE.Object3D();
  function updateFence(){
    const n0 = Math.floor((S0 - CHUNK_BEHIND) / POST_GAP);
    let pi = 0, ri = 0;
    for (let n = n0; n < n0 + NPOST; n++){
      const s1 = n * POST_GAP, s2 = s1 + POST_GAP;
      for (const side of [-1, 1]){
        const x = side * (cfg.halfW + 0.1), y1 = ground(x, s1), y2 = ground(x, s2);
        rel(dummy.position, s1, x, y1 + 0.5); dummy.rotation.set(0, 0, 0); dummy.scale.set(1, 1, 1); dummy.updateMatrix();
        posts.setMatrixAt(pi++, dummy.matrix);
        for (const h of [0.45, 0.9]){
          rel(dummy.position, (s1 + s2) / 2, x, (y1 + y2) / 2 + h);
          dummy.rotation.set(Math.atan2(y2 - y1, s2 - s1), 0, 0); dummy.scale.set(1, 1, Math.hypot(POST_GAP, y2 - y1)); dummy.updateMatrix();
          rails.setMatrixAt(ri++, dummy.matrix);
        }
      }
    }
    posts.instanceMatrix.needsUpdate = rails.instanceMatrix.needsUpdate = true;
  }

  // ゴールゲート
  const goalGroup = new THREE.Group();
  const goalMat = new THREE.MeshStandardMaterial({ map:checkerTexture(), roughness:0.6 });
  const goalPoleMat = new THREE.MeshStandardMaterial({ color:lin('#e63946'), roughness:0.5 });
  const goalL = new THREE.Mesh(new THREE.CylinderGeometry(0.22, 0.22, 7.5, 12), goalPoleMat);
  const goalR = new THREE.Mesh(new THREE.CylinderGeometry(0.22, 0.22, 7.5, 12), goalPoleMat);
  const goalBanner = new THREE.Mesh(new THREE.BoxGeometry(1, 1.8, 0.28), goalMat);
  const goalLine = new THREE.Mesh(new THREE.PlaneGeometry(1, 1.4).rotateX(-Math.PI / 2), new THREE.MeshBasicMaterial({ map:checkerTexture(), side:THREE.DoubleSide }));
  goalGroup.add(goalL, goalR, goalBanner, goalLine);
  scene.add(goalGroup);
  function updateGoalGate(){
    const d = Math.abs(cfg.goalDist - S0);
    goalGroup.visible = d < 200;
    if (!goalGroup.visible) return;
    const w = cfg.halfW * 2, gy = ground(0, cfg.goalDist);
    rel(goalGroup.position, cfg.goalDist, 0, gy);
    goalL.position.set(-cfg.halfW, 3.6, 0);
    goalR.position.set(cfg.halfW, 3.6, 0);
    goalBanner.scale.set(w + 0.6, 1, 1); goalBanner.position.set(0, 6.4, 0);
    goalLine.scale.set(w, 1, 1); goalLine.position.set(0, 0.14, 0);
  }

  // ---------- チーズ（最大6個）＆頭上マーカー ----------
  const cheeseGeo = new THREE.CylinderGeometry(1, 1, 1, 36).rotateZ(Math.PI / 2);
  const cheeseVisuals = [];
  const markers = [];
  for (let i = 0; i < MAX_CHEESES; i++){
    const grp = new THREE.Group(); grp.rotation.order = 'YXZ';
    const spin = new THREE.Group(); grp.add(spin);
    const tint = i === 0 ? null : RACER_COLORS[i];
    const rMat = new THREE.MeshStandardMaterial({ map:rindTexture(tint), roughness:0.75 });
    const fMat = new THREE.MeshStandardMaterial({ map:faceTexture(tint), roughness:0.8 });
    const mesh = new THREE.Mesh(cheeseGeo, [rMat, fMat, fMat]);
    mesh.castShadow = true; spin.add(mesh); scene.add(grp);
    cheeseVisuals.push({ grp, spin, mesh });
    const mk = new THREE.Mesh(new THREE.ConeGeometry(0.24, 0.45, 4).rotateX(Math.PI), new THREE.MeshBasicMaterial({ color:RACER_COLORS[i] }));
    mk.visible = false; scene.add(mk); markers.push(mk);
  }

  // ---------- チーズの軌跡 ----------
  const trail = []; let trailK = 0;
  const trailGeo = new THREE.BufferGeometry();
  trailGeo.setAttribute('position', new THREE.BufferAttribute(new Float32Array(TRAIL_N * 2 * 3), 3));
  trailGeo.setAttribute('alpha', new THREE.BufferAttribute(new Float32Array(TRAIL_N * 2), 1));
  {
    const idx = [];
    for (let i = 0; i < TRAIL_N - 1; i++){ const a = i * 2; idx.push(a, a+1, a+2, a+1, a+3, a+2); }
    trailGeo.setIndex(idx);
  }
  const trailMesh = new THREE.Mesh(trailGeo, new THREE.ShaderMaterial({
    uniforms:{ color:{ value:new THREE.Color(css('--trail')) } },
    vertexShader:'attribute float alpha; varying float vA; void main(){ vA = alpha; gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0); }',
    fragmentShader:'uniform vec3 color; varying float vA; void main(){ gl_FragColor = vec4(color, vA); }',
    transparent:true, depthWrite:false, side:THREE.DoubleSide,
  }));
  trailMesh.frustumCulled = false; scene.add(trailMesh);
  const focusCheese = () => cfg.isRace ? cheeses[clamp(cfg.myIndex, 0, cheeseN - 1)] : cheeses[0];
  function recordTrail(){
    const fc = focusCheese();
    if (fc.held) return;
    const t = trail[0];
    if (t && Math.hypot(fc.s - t.s, fc.x - t.x, fc.y - t.y) < TRAIL_STEP) return;
    trail.unshift({ s:fc.s, x:fc.x, y:fc.y });
    if (trail.length > TRAIL_N) trail.pop();
  }
  const _tp = Array.from({ length:TRAIL_N }, () => new THREE.Vector3()), _tt = new THREE.Vector3(), _ts = new THREE.Vector3();
  function drawTrail(dt){
    const fc = focusCheese(), want = fc.held ? 0 : clamp((fc.v - Math.max(8, cfg.targetSpeed * 0.92)) / TRAIL_FADE, 0, 1);
    trailK += (want - trailK) * Math.min(1, dt * 6);
    const n = trail.length;
    trailMesh.visible = trailK > 0.01 && n > 1;
    if (!trailMesh.visible) return;
    rel(_tp[0], fc.s, fc.x, fc.y);
    for (let i = 1; i < TRAIL_N; i++){ const p = trail[Math.min(i, n - 1)]; rel(_tp[i], p.s, p.x, p.y); }
    const pos = trailGeo.attributes.position.array, al = trailGeo.attributes.alpha.array;
    for (let i = 0; i < TRAIL_N; i++){
      _tt.subVectors(_tp[Math.max(i - 1, 0)], _tp[Math.min(i + 1, TRAIL_N - 1)]);
      _ts.crossVectors(_tt, UP);
      const u = i / (TRAIL_N - 1);
      if (_ts.lengthSq() > 1e-10) _ts.normalize().multiplyScalar(cfg.cheeseW / 2 * (1 - TRAIL_TAPER * u)); else _ts.set(0, 0, 0);
      pos[i*6] = _tp[i].x + _ts.x; pos[i*6+1] = _tp[i].y + _ts.y; pos[i*6+2] = _tp[i].z + _ts.z;
      pos[i*6+3] = _tp[i].x - _ts.x; pos[i*6+4] = _tp[i].y - _ts.y; pos[i*6+5] = _tp[i].z - _ts.z;
      al[i*2] = al[i*2+1] = i < n ? trailK * TRAIL_ALPHA * Math.pow(1 - u, 1.5) : 0;
    }
    trailGeo.attributes.position.needsUpdate = trailGeo.attributes.alpha.needsUpdate = true;
  }

  // ---------- 障害物（チーズのみ衝突・人間側は完全すり抜け） ----------
  const obstacles = []; let obSeed = 1, obNextBand = 0;
  function rockTex(){ const S = 64, rnd = rand(21), c = new THREE.Color(css('--rock')), n = tileNoise(S, 8, rnd);
    return canvasTex(S, S, d => { for (let i = 0; i < S*S; i++){ const k = 0.72 + 0.4 * n[i]; d[i*4]=Math.round(255*clamp(c.r*k,0,1)); d[i*4+1]=Math.round(255*clamp(c.g*k,0,1)); d[i*4+2]=Math.round(255*clamp(c.b*k,0,1)); d[i*4+3]=255; } }); }
  function barkTex(){ const S = 64, rnd = rand(23), c = new THREE.Color(css('--bark')), st = new Float32Array(S).map(() => rnd());
    return canvasTex(S, S, d => { for (let y=0;y<S;y++) for (let x=0;x<S;x++){ const k = 0.65 + 0.4 * st[x], i=(y*S+x)*4; d[i]=Math.round(255*clamp(c.r*k,0,1)); d[i+1]=Math.round(255*clamp(c.g*k,0,1)); d[i+2]=Math.round(255*clamp(c.b*k,0,1)); d[i+3]=255; } }); }
  function ringTex(){ const S = 64, w = new THREE.Color(css('--wood')), b = new THREE.Color(css('--bark'));
    return canvasTex(S, S, d => { for (let y=0;y<S;y++) for (let x=0;x<S;x++){ const r = Math.hypot((x+.5)/S*2-1,(y+.5)/S*2-1), base = r>.86?b:w, k = r>.86?1:.88+.12*Math.cos(r*45), i=(y*S+x)*4; d[i]=Math.round(255*clamp(base.r*k,0,1)); d[i+1]=Math.round(255*clamp(base.g*k,0,1)); d[i+2]=Math.round(255*clamp(base.b*k,0,1)); d[i+3]=255; } }); }
  function strawTex(){ const S = 64, rnd = rand(27), c = new THREE.Color(css('--straw'));
    return canvasTex(S, S, d => { for (let i=0;i<S*S;i++){ const k = 0.78 + rnd()*0.3; d[i*4]=Math.round(255*clamp(c.r*k,0,1)); d[i*4+1]=Math.round(255*clamp(c.g*k,0,1)); d[i*4+2]=Math.round(255*clamp(c.b*k,0,1)); d[i*4+3]=255; } }); }
  const rockMat = new THREE.MeshStandardMaterial({ map:rockTex(), roughness:0.95, flatShading:true });
  const barkMat = new THREE.MeshStandardMaterial({ map:barkTex(), roughness:0.95 });
  const ringMat = new THREE.MeshStandardMaterial({ map:ringTex(), roughness:0.9 });
  const strawMat = new THREE.MeshStandardMaterial({ map:strawTex(), roughness:1 });
  const bushMat = new THREE.MeshStandardMaterial({ vertexColors:true, roughness:1, flatShading:true });
  const boardMat = new THREE.MeshStandardMaterial({ map:boardTexture(), roughness:0.85 });

  const rockGeos = Array.from({ length:ROCK_VARIANTS }, (_, v) => {
    const rnd = rand(101 + v), g = new THREE.IcosahedronGeometry(1, 2), pos = g.attributes.position, ph = Array.from({ length:5 }, () => rnd() * TAU);
    for (let i = 0; i < pos.count; i++){
      const x = pos.getX(i), y = pos.getY(i), z = pos.getZ(i);
      const k = 1 + 0.16 * Math.sin(x * 2.1 + ph[0]) * Math.sin(y * 1.7 + ph[1]) + 0.12 * Math.sin(z * 2.6 + ph[2] + x);
      pos.setXYZ(i, x * k, y * k, z * k);
    }
    g.computeVertexNormals(); return g;
  });
  const logGeo = new THREE.CylinderGeometry(1, 1, 1, 14).rotateZ(Math.PI / 2);
  const baleGeo = new THREE.CylinderGeometry(1, 1, 1, 24).translate(0, 0.5, 0);
  const boardGeo = new THREE.BoxGeometry(1, 1, 1).translate(0, 0.5, 0);

  function bushGeo(seed){
    const rnd = rand(seed), leaf = lin(css('--gorse')), light = lin(css('--gorse-light')), flw = lin(css('--gorse-flower'));
    const parts = [];
    const mound = new THREE.SphereGeometry(0.85, 12, 6, 0, TAU, 0, Math.PI / 2).scale(1, 0.6, 1).toNonIndexed();
    parts.push({ g:mound, c:leaf });
    const cone = new THREE.ConeGeometry(1, 1, 4).translate(0, 0.5, 0).toNonIndexed();
    const bud = new THREE.IcosahedronGeometry(1, 0), m = new THREE.Matrix4(), q = new THREE.Quaternion(), sc = new THREE.Vector3(), dir = new THREE.Vector3();
    for (let i = 0; i < BUSH_SPIKES; i++){
      const a = rnd() * TAU, y = Math.pow(rnd(), 0.7) * 0.95 + 0.05, h = Math.sqrt(1 - y * y);
      const p = new THREE.Vector3(Math.cos(a) * h * 0.85, y * 0.52, Math.sin(a) * h * 0.85);
      dir.set(Math.cos(a) * h + (rnd() - .5) * .5, y * 1.3 + .2, Math.sin(a) * h + (rnd() - .5) * .5).normalize();
      q.setFromUnitVectors(UP, dir); sc.set(0.03, 0.2 + rnd() * 0.2, 0.03); m.compose(p, q, sc);
      parts.push({ g:cone.clone().applyMatrix4(m), c:leaf.clone().lerp(light, rnd()).multiplyScalar(0.65 + 0.45 * y) });
    }
    for (let i = 0; i < BUSH_FLOWERS; i++){
      const a = rnd() * TAU, y = Math.pow(rnd(), 0.7) * 0.95 + 0.05, h = Math.sqrt(1 - y * y), r = 0.025 + rnd() * 0.015;
      m.compose(new THREE.Vector3(Math.cos(a) * h * 0.98, y * 0.62, Math.sin(a) * h * 0.98), q.identity(), sc.set(r, r, r));
      parts.push({ g:bud.clone().applyMatrix4(m), c:flw });
    }
    let n = 0; for (const p of parts) n += p.g.attributes.position.count;
    const pos = new Float32Array(n * 3), nor = new Float32Array(n * 3), col = new Float32Array(n * 3);
    let o = 0;
    for (const { g, c } of parts){
      const gp = g.attributes.position, gn = g.attributes.normal;
      for (let i = 0; i < gp.count; i++){
        const k = (o + i) * 3;
        pos[k]=gp.getX(i); pos[k+1]=gp.getY(i); pos[k+2]=gp.getZ(i);
        nor[k]=gn.getX(i); nor[k+1]=gn.getY(i); nor[k+2]=gn.getZ(i);
        col[k]=c.r; col[k+1]=c.g; col[k+2]=c.b;
      }
      o += gp.count;
    }
    const g = new THREE.BufferGeometry();
    g.setAttribute('position', new THREE.BufferAttribute(pos, 3));
    g.setAttribute('normal', new THREE.BufferAttribute(nor, 3));
    g.setAttribute('color', new THREE.BufferAttribute(col, 3));
    return g;
  }
  const bushGeos = Array.from({ length:BUSH_VARIANTS }, (_, v) => bushGeo(201 + v));
  const pools = { rock:[], bush:[], log:[], bale:[], board:[] };
  function takeMesh(o){
    let m = pools[o.kind].pop();
    if (!m){
      if (o.kind === 'rock') m = new THREE.Mesh(rockGeos[0], rockMat);
      if (o.kind === 'log') m = new THREE.Mesh(logGeo, [barkMat, ringMat, ringMat]);
      if (o.kind === 'bale') m = new THREE.Mesh(baleGeo, strawMat);
      if (o.kind === 'bush') m = new THREE.Mesh(bushGeos[0], bushMat);
      if (o.kind === 'board') m = new THREE.Mesh(boardGeo, boardMat);
      m.castShadow = m.receiveShadow = true; scene.add(m);
    }
    m.visible = true; m.rotation.set(0, o.yaw || 0, 0);
    if (o.kind === 'rock'){ m.geometry = rockGeos[o.shape]; m.scale.set(o.r, o.r * 0.8, o.r); }
    if (o.kind === 'bush'){ m.geometry = bushGeos[o.shape]; m.scale.set(o.r, o.h, o.r); }
    if (o.kind === 'log') m.scale.set(o.len, o.r, o.r);
    if (o.kind === 'bale') m.scale.set(o.r, o.h, o.r);
    if (o.kind === 'board') m.scale.set(o.len, o.h + 1.0, o.thick);
    return m;
  }
  const releaseMesh = o => { o.mesh.visible = false; pools[o.kind].push(o.mesh); };

  function genBand(b){
    if (cfg.course === 'board'){
      const s0 = b * cfg.boardGap;
      if (s0 < Math.max(25, cfg.boardGap) || s0 > cfg.goalDist - 12) return;
      const rnd = rand(obSeed * 100003 + b * 7919), side = rnd() < 0.5 ? -1 : 1;
      const o = { kind:'board', s:s0, x:side * (cfg.halfW - cfg.boardW / 2), len:cfg.boardW, h:cfg.boardH, thick:cfg.boardThick, id:b, yaw:0 };
      o.mesh = takeMesh(o); obstacles.push(o); return;
    }
    const s0 = b * OB_BAND;
    if (s0 < OB_START || s0 > cfg.goalDist - 10) return;
    const rnd = rand(obSeed * 100003 + b * 7919), rate = Math.min(OB_RATE_MAX, OB_RATE0 + s0 * OB_RATE_GROW);
    let n = rnd() < rate ? 1 : 0; if (n && rnd() < s0 * OB_SECOND) n = 2;
    for (let i = 0; i < n; i++){
      let u = rnd(), kind = OB_KIND[0][0];
      for (const [k, w] of OB_KIND){ if (u < w){ kind = k; break; } u -= w; }
      const o = { kind, s:s0 + rnd() * OB_BAND, id:b * 4 + i, r:0, len:0, h:0, yaw:rnd() * TAU };
      if (kind === 'rock'){ o.r = 0.3 + rnd() * 0.4; o.shape = Math.floor(rnd() * ROCK_VARIANTS); }
      if (kind === 'bush'){ o.r = 0.7 + rnd() * 0.5; o.h = 0.9 + rnd() * 0.5; o.shape = Math.floor(rnd() * BUSH_VARIANTS); }
      if (kind === 'log'){ o.r = 0.2 + rnd() * 0.1; o.len = 2 + rnd() * 2.5; o.yaw = 0; }
      if (kind === 'bale'){ o.r = 0.65; o.h = 1.2; }
      const half = (kind === 'log' ? o.len / 2 : o.r) + 0.3;
      o.x = -cfg.halfW + half + rnd() * 2 * (cfg.halfW - half);
      o.mesh = takeMesh(o); obstacles.push(o);
    }
  }
  function updateObstacles(){
    const bandLen = cfg.course === 'board' ? cfg.boardGap : OB_BAND;
    let aheadRef = S0, behindRef = S0;
    for (let i = 0; i < cheeseN; i++){ aheadRef = Math.max(aheadRef, cheeses[i].s); behindRef = Math.min(behindRef, cheeses[i].s); }
    while (obNextBand * bandLen < Math.min(aheadRef + OB_AHEAD, cfg.goalDist + 20)) genBand(obNextBand++);
    for (let i = obstacles.length - 1; i >= 0; i--) if (obstacles[i].s < behindRef - OB_BEHIND){ releaseMesh(obstacles[i]); obstacles.splice(i, 1); }
  }
  function clearObstacles(fixedSeed){
    for (const o of obstacles) releaseMesh(o);
    obstacles.length = 0; obNextBand = 0; obSeed = fixedSeed !== undefined ? fixedSeed : Math.floor(Math.random() * 1e6);
  }
  function drawObstacles(){
    for (const o of obstacles){
      const m = o.mesh;
      if (o.kind === 'rock') rel(m.position, o.s, o.x, ground(o.x, o.s) + o.r * 0.25);
      if (o.kind === 'bush') rel(m.position, o.s, o.x, ground(o.x, o.s) - 0.08);
      if (o.kind === 'bale') rel(m.position, o.s, o.x, ground(o.x, o.s) - 0.06);
      if (o.kind === 'board') rel(m.position, o.s, o.x, ground(o.x, o.s) - 1.0);
      if (o.kind === 'log'){
        const g1 = ground(o.x - o.len / 2, o.s), g2 = ground(o.x + o.len / 2, o.s);
        rel(m.position, o.s, o.x, (g1 + g2) / 2 + o.r * 0.85); m.rotation.set(0, 0, Math.atan2(g2 - g1, o.len));
      }
    }
  }

  const _n = [0, 0, 0];
  function obstacleHit(o, x, y, s, pr){
    if (o.kind === 'board'){
      const minX = o.x - o.len / 2, maxX = o.x + o.len / 2, minS = o.s - o.thick / 2, maxS = o.s + o.thick / 2;
      const gy = ground(clamp(x, minX, maxX), o.s);
      const cx = clamp(x, minX, maxX), cy = clamp(y, gy - 1.5, gy + o.h), cs = clamp(s, minS, maxS);
      const dx = x - cx, dy = y - cy, ds = s - cs, d = Math.hypot(dx, dy, ds);
      if (d >= pr) return 0;
      if (d < 1e-6){ _n[0]=0; _n[1]=0; _n[2]=-1; return (s - minS) + pr; }
      _n[0]=dx/d; _n[1]=dy/d; _n[2]=ds/d; return pr - d;
    }
    if (o.kind === 'rock' || o.kind === 'log'){
      let cx = o.x, cy, R;
      if (o.kind === 'rock'){ cy = ground(o.x, o.s) + o.r * 0.25; R = o.r * 0.9 + pr; }
      else { const x1 = o.x - o.len / 2, t = clamp((x - x1) / o.len, 0, 1); cx = x1 + t * o.len; cy = lerp(ground(x1, o.s), ground(x1 + o.len, o.s), t) + o.r * 0.85; R = o.r + pr; }
      const dx = x - cx, dy = y - cy, ds = s - o.s, d = Math.hypot(dx, dy, ds);
      if (d >= R || d < 1e-6) return 0;
      _n[0]=dx/d; _n[1]=dy/d; _n[2]=ds/d; return R - d;
    }
    if (o.kind === 'bale'){
      const gy = ground(o.x, o.s), dx = x - o.x, ds = s - o.s, d = Math.hypot(dx, ds) || 1e-6;
      const side = o.r + pr - d, up = gy + o.h + pr - y;
      if (side <= 0 || up <= 0 || y < gy - pr) return 0;
      if (up < side){ _n[0]=0; _n[1]=1; _n[2]=0; return up; }
      _n[0]=dx/d; _n[1]=0; _n[2]=ds/d; return side;
    }
    return 0;
  }
  const hard = o => o.kind !== 'bush';
  const halfWidth = o => (o.kind === 'log' || o.kind === 'board') ? o.len / 2 : o.r;
  const inBush = (x, s) => cfg.course !== 'board' && obstacles.some(o => o.kind === 'bush' && Math.hypot(x - o.x, s - o.s) < o.r * 0.9);
  function cheeseObstacles(c){
    for (const o of obstacles){
      const sLim = o.kind === 'board' ? o.thick / 2 + 2.5 : 3.5;
      if (!hard(o) || Math.abs(o.s - c.s) > sLim) continue;
      const d = obstacleHit(o, c.x, c.y, c.s, cfg.cheeseR * 0.92);
      if (d <= 0) continue;
      c.x += _n[0] * d; c.y += _n[1] * d; c.s += _n[2] * d;
      const vn = c.vx * _n[0] + c.vy * _n[1] + c.v * _n[2];
      if (vn < 0){
        if (o.kind === 'board' && _n[2] < -0.5){ c.v = -2.5; c.vx *= 0.2; c.vy = Math.max(0, c.vy); }
        else { c.vx -= (1 + OB_BOUNCE) * vn * _n[0]; c.vy -= (1 + OB_BOUNCE) * vn * _n[1]; c.v -= (1 + OB_BOUNCE) * vn * _n[2]; c.vx *= OB_LOSS; c.v *= OB_LOSS; c.grounded = false; }
        c.tiltV += (Math.random() - 0.5) * Math.min(20, Math.abs(vn)) * TILT_KICK * 3;
      }
    }
  }

  // ---------- 人の体（人間側は障害物をすべてすり抜ける） ----------
  const HEAD=0, NECK=1, PELVIS=2, SHL=3, SHR=4, ELL=5, ELR=6, HNL=7, HNR=8, HPL=9, HPR=10, KNL=11, KNR=12, FTL=13, FTR=14, NP=15;
  const PART_R = [0.11,0.06,0.1,0.06,0.06,0.05,0.05,0.05,0.05,0.07,0.07,0.06,0.06,0.05,0.05];
  const INV_M = [1/4,1/5,1/12,1/4,1/4,1/1.6,1/1.6,1/0.6,1/0.6,1/6,1/6,1/4,1/4,1/1.5,1/1.5];
  const TORSO = [NECK, SHL, SHR, PELVIS, HPL, HPR];
  let DIST = [];
  const MIN_DIST = [[HPL,FTL,0.33],[HPR,FTR,0.33],[SHL,HNL,0.2],[SHR,HNR,0.2]];

  function writePose(c, o, mode, k){
    const sy = Math.sin(c.yaw), cy = Math.cos(c.yaw), Fx = sy, Fs = cy, Rx = cy, Rs = -sy;
    const set = (i, x, y, s) => { o[i*3]=x; o[i*3+1]=y; o[i*3+2]=s; };
    const ph = c.phase, v = mode === 'run' ? c.v : 0, vr = Math.min(1, v / 3);
    let lean = 0, hipY = 0.93, twist = 0, armL = 0.1, armR = 0.1, bend = 0.3;
    const feet = [[0,0],[0,0]];
    if (mode === 'run'){
      lean = 0.18 * Math.min(1, v / 5);
      hipY = 0.885 - 0.035 * vr * Math.cos(4 * Math.PI * (ph - RUN_DUTY / 2));
      twist = 0.15 * vr * Math.cos(TAU * ph);
      armR = 0.1 + 0.75 * vr * Math.cos(TAU * ph); armL = 0.1 - 0.75 * vr * Math.cos(TAU * ph); bend = 0.3 + 1.2 * vr;
      const a = clamp(v / Math.max(0.5, c.freq) * RUN_DUTY / 2, 0, 0.45);
      for (let side = 0; side < 2; side++){
        const r = frac(ph + side * 0.5);
        if (r < RUN_DUTY) feet[side][0] = a * (1 - 2 * r / RUN_DUTY);
        else { const u = (r - RUN_DUTY) / (1 - RUN_DUTY); feet[side][0] = -a + 2 * a * smooth(u) - 0.3 * vr * Math.sin(Math.PI * u); feet[side][1] = (0.08 + 0.27 * Math.min(1, v / 6)) * Math.sin(Math.PI * u); }
      }
    } else if (mode === 'crouch'){ lean = -0.55 * k; hipY = 0.88 - 0.18 * k; armL = armR = -0.9 * k; bend = 0.6; feet[0][0] = 0.25; feet[1][0] = -0.2; }
    else if (mode === 'reach'){ lean = -1.0; hipY = 0.78; armL = armR = 2.7; bend = 0.15; feet[0][0] = -0.45; feet[1][0] = -0.25; feet[1][1] = 0.2; }
    else if (mode === 'ready'){ lean = -0.35; hipY = 0.86; armL = -0.3; armR = 0.5; bend = 1.2; feet[0][0] = 0.2; feet[1][0] = -0.25; }
    const gy = ground(c.x, c.s), px = c.x, py = gy + hipY, ps = c.s;
    const cl = Math.cos(lean), sl = Math.sin(lean), Tx = -Fx * sl, Ty = cl, Ts = -Fs * sl, Px = Fx * cl, Py = sl, Ps = Fs * cl;
    const ct = Math.cos(twist), st = Math.sin(twist), RhX = Rx * ct - Fx * st, RhS = Rs * ct - Fs * st, RsX = Rx * ct + Fx * st, RsS = Rs * ct + Fs * st;
    set(PELVIS, px, py, ps); set(NECK, px + Tx * 0.52, py + Ty * 0.52, ps + Ts * 0.52);
    set(HEAD, px + Tx * 0.7 + Px * 0.03, py + Ty * 0.7 + Py * 0.03, ps + Ts * 0.7 + Ps * 0.03);
    const thigh = cfg.thigh, shin = cfg.shin;
    for (let side = 0; side < 2; side++){
      const sg = side ? 1 : -1, S = side ? SHR : SHL, E = side ? ELR : ELL, Hn = side ? HNR : HNL;
      const sx = px + Tx * 0.47 + RsX * 0.19 * sg, syy = py + Ty * 0.47, ss = ps + Ts * 0.47 + RsS * 0.19 * sg;
      set(S, sx, syy, ss);
      const th = side ? armR : armL, c1 = Math.cos(th), s1 = Math.sin(th), c2 = Math.cos(th + bend), s2 = Math.sin(th + bend);
      const ex = sx + (-Tx * c1 + Px * s1) * UPARM + Rx * 0.03 * sg, ey = syy + (-Ty * c1 + Py * s1) * UPARM, es = ss + (-Ts * c1 + Ps * s1) * UPARM + Rs * 0.03 * sg;
      set(E, ex, ey, es); set(Hn, ex + (-Tx * c2 + Px * s2) * FOREARM, ey + (-Ty * c2 + Py * s2) * FOREARM, es + (-Ts * c2 + Ps * s2) * FOREARM);
      const Hp = side ? HPR : HPL, Kn = side ? KNR : KNL, Ft = side ? FTR : FTL;
      const hx = px - Tx * 0.04 + RhX * 0.1 * sg, hy = py - Ty * 0.04, hs = ps - Ts * 0.04 + RhS * 0.1 * sg;
      set(Hp, hx, hy, hs);
      const fx = hx + Fx * feet[side][0], fs = hs + Fs * feet[side][0];
      let tx = fx - hx, ty = ground(fx, fs) + FOOT_H + feet[side][1] - hy, ts = fs - hs, d = Math.hypot(tx, ty, ts);
      const maxD = thigh + shin - 0.002;
      if (d > maxD){ tx *= maxD / d; ty *= maxD / d; ts *= maxD / d; d = maxD; }
      const nx = tx / d, ny = ty / d, ns = ts / d, along = (thigh * thigh - shin * shin + d * d) / (2 * d);
      const h = Math.sqrt(Math.max(0, thigh * thigh - along * along));
      let bx = Fx - nx * (Fx * nx + Fs * ns), by = -ny * (Fx * nx + Fs * ns), bs = Fs - ns * (Fx * nx + Fs * ns);
      const bl = Math.hypot(bx, by, bs) || 1; bx /= bl; by /= bl; bs /= bl;
      set(Kn, hx + nx * along + bx * h, hy + ny * along + by * h, hs + ns * along + bs * h); set(Ft, hx + tx, hy + ty, hs + ts);
    }
  }
  function rebuildBodyDist(){
    DIST = []; const tmp = { s:0, x:0, yaw:0, phase:0, v:0, freq:1 }, o = new Float64Array(NP * 3);
    writePose(tmp, o, 'idle', 0);
    const len = (a, b) => Math.hypot(o[a*3]-o[b*3], o[a*3+1]-o[b*3+1], o[a*3+2]-o[b*3+2]);
    for (let i = 0; i < TORSO.length; i++) for (let j = i + 1; j < TORSO.length; j++) DIST.push([TORSO[i], TORSO[j], len(TORSO[i], TORSO[j])]);
    DIST.push([HEAD,NECK,len(HEAD,NECK)],[HEAD,SHL,len(HEAD,SHL)],[HEAD,SHR,len(HEAD,SHR)],
      [SHL,ELL,UPARM],[ELL,HNL,FOREARM],[SHR,ELR,UPARM],[ELR,HNR,FOREARM],
      [HPL,KNL,cfg.thigh],[KNL,FTL,cfg.shin],[HPR,KNR,cfg.thigh],[KNR,FTR,cfg.shin]);
  }
  rebuildBodyDist();

  function solveDist(p, a, b, rest, minOnly){
    const ia = a * 3, ib = b * 3, dx = p[ib]-p[ia], dy = p[ib+1]-p[ia+1], ds = p[ib+2]-p[ia+2];
    const d = Math.sqrt(dx*dx + dy*dy + ds*ds) || 1e-6;
    if (minOnly && d >= rest) return;
    const wa = INV_M[a] / (INV_M[a] + INV_M[b]), wb = 1 - wa, f = (d - rest) / d;
    p[ia]+=dx*f*wa; p[ia+1]+=dy*f*wa; p[ia+2]+=ds*f*wa; p[ib]-=dx*f*wb; p[ib+1]-=dy*f*wb; p[ib+2]-=ds*f*wb;
  }
  const _f = [0,0,0];
  function kneeLimit(p){
    const rx = p[HPR*3]-p[HPL*3], ry = p[HPR*3+1]-p[HPL*3+1], rs = p[HPR*3+2]-p[HPL*3+2];
    const ux = p[NECK*3]-p[PELVIS*3], uy = p[NECK*3+1]-p[PELVIS*3+1], us = p[NECK*3+2]-p[PELVIS*3+2];
    const fx = ry*us-rs*uy, fy = rs*ux-rx*us, fs = rx*uy-ry*ux, l = Math.hypot(fx,fy,fs) || 1;
    _f[0]=fx/l; _f[1]=fy/l; _f[2]=fs/l;
    for (const [Hp, Kn, Ft] of [[HPL,KNL,FTL],[HPR,KNR,FTR]]){
      let d = 0; for (let a=0;a<3;a++) d += (p[Kn*3+a] - (p[Hp*3+a] + p[Ft*3+a]) / 2) * _f[a];
      if (d < 0) for (let a=0;a<3;a++) p[Kn*3+a] -= _f[a] * d;
    }
  }
  function reachArms(p){
    const ux = p[NECK*3]-p[PELVIS*3], uy = p[NECK*3+1]-p[PELVIS*3+1], us = p[NECK*3+2]-p[PELVIS*3+2], ul = Math.hypot(ux,uy,us) || 1;
    for (const [S, E, Hn] of [[SHL,ELL,HNL],[SHR,ELR,HNR]]) for (const [i, L] of [[E,UPARM],[Hn,UPARM+FOREARM]])
      for (let a=0;a<3;a++) p[i*3+a] += (p[S*3+a] + [ux,uy,us][a] / ul * L - p[i*3+a]) * 0.25;
  }
  const _gy = new Float64Array(NP), _pen = new Float64Array(NP);
  function ragStep(c, dt, reaching){
    const p = c.p, q = c.q, gdt = 9.8 * dt * dt;
    for (let i = 0; i < NP * 3; i++){ const v = (p[i] - q[i]) * RAG_DAMP; q[i] = p[i]; p[i] += v; }
    for (let i = 0; i < NP; i++) p[i * 3 + 1] -= gdt;
    if (reaching) reachArms(p);
    for (let i = 0; i < NP; i++){ _gy[i] = ground(p[i*3], p[i*3+2]) + PART_R[i]; _pen[i] = 0; }
    const lim = cfg.halfW - 0.1;
    for (let it = 0; it < RAG_ITER; it++){
      for (const [a,b,r] of DIST) solveDist(p, a, b, r, false);
      for (const [a,b,r] of MIN_DIST) solveDist(p, a, b, r, true);
      kneeLimit(p);
      for (let i = 0; i < NP; i++){
        const k = i * 3;
        if (p[k+1] < _gy[i]){ _pen[i] += _gy[i] - p[k+1]; p[k+1] = _gy[i]; }
        if (p[k] > lim){ p[k] = lim; q[k] = Math.max(q[k], lim); }
        if (p[k] < -lim){ p[k] = -lim; q[k] = Math.min(q[k], -lim); }
      }
    }
    for (let i = 0; i < NP; i++){
      if (_pen[i] <= 0) continue;
      const k = i * 3, x = p[k], s = p[k+2];
      let nx = -slopeX(x, s), ny = 1, ns = -slopeS(x, s);
      const nl = Math.hypot(nx, ny, ns); nx/=nl; ny/=nl; ns/=nl;
      let vx = p[k]-q[k], vy = p[k+1]-q[k+1], vs = p[k+2]-q[k+2];
      const vn0 = vx*nx + vy*ny + vs*ns;
      if (vn0 < 0){ vx-=vn0*nx; vy-=vn0*ny; vs-=vn0*ns; }
      const vn = vx*nx + vy*ny + vs*ns, tx = vx-vn*nx, ty = vy-vn*ny, ts = vs-vn*ns, tl = Math.hypot(tx,ty,ts);
      const fr = RAG_MU * (_pen[i] + Math.max(0, -vn0)), kk = tl > 1e-9 ? Math.max(0, tl - fr) / tl : 0;
      q[k] = p[k] - (vn*nx + tx*kk); q[k+1] = p[k+1] - (vn*ny + ty*kk); q[k+2] = p[k+2] - (vn*ns + ts*kk);
    }
  }

  // ---------- 人の見た目 ----------
  const CYL = [[PELVIS,NECK,'shirt',0.16,0.105],[NECK,HEAD,'skin',0.045,0.045],[SHL,ELL,'shirt',0.05,0.05],[SHR,ELR,'shirt',0.05,0.05],
    [ELL,HNL,'skin',0.038,0.038],[ELR,HNR,'skin',0.038,0.038],[HPL,KNL,'pants',0.075,0.075],[HPR,KNR,'pants',0.075,0.075],[KNL,FTL,'pants',0.055,0.055],[KNR,FTR,'pants',0.055,0.055]];
  const SPH = [[PELVIS,'pants',0.16,0.11,0.11],[HEAD,'skin',0.105,0.115,0.105],[HEAD,'hair',0.11,0.1,0.112],[SHL,'shirt',0.062,0.062,0.062],[SHR,'shirt',0.062,0.062,0.062],
    [ELL,'skin',0.04,0.04,0.04],[ELR,'skin',0.04,0.04,0.04],[HNL,'skin',0.045,0.05,0.035],[HNR,'skin',0.045,0.05,0.035],[KNL,'pants',0.06,0.06,0.06],[KNR,'pants',0.06,0.06,0.06],[FTL,'shoe',0.05,0.045,0.125],[FTR,'shoe',0.05,0.045,0.125]];
  const bodyMat = new THREE.MeshStandardMaterial({ roughness:0.85, metalness:0 });
  const cylMesh = new THREE.InstancedMesh(new THREE.CylinderGeometry(1, 1, 1, 10), bodyMat, CH_ALLOC * CYL.length);
  const sphMesh = new THREE.InstancedMesh(new THREE.SphereGeometry(1, 12, 10), bodyMat, CH_ALLOC * SPH.length);
  for (const m of [cylMesh, sphMesh]){ m.frustumCulled = false; m.castShadow = m.receiveShadow = true; scene.add(m); }
  const PALETTE = { skin:list('--skins'), shirt:list('--shirts'), pants:list('--pants'), hair:list('--hair'), shoe:list('--shoes') };
  const chasers = [];
  for (let i = 0; i < CH_ALLOC; i++){
    const pick = (k, n) => lin(PALETTE[k][(i * n + Math.floor(i / PALETTE[k].length)) % PALETTE[k].length]);
    const col = { skin:pick('skin',3), shirt:pick('shirt',1), pants:pick('pants',5), hair:pick('hair',2), shoe:pick('shoe',3) };
    if (i < 5) col.shirt = lin(RACER_COLORS[(i + 1) % RACER_COLORS.length]);
    CYL.forEach((d, j) => cylMesh.setColorAt(i * CYL.length + j, col[d[2]]));
    SPH.forEach((d, j) => sphMesh.setColorAt(i * SPH.length + j, col[d[1]]));
    chasers.push({ p:new Float64Array(NP*3), q:new Float64Array(NP*3), a:new Float64Array(NP*3), active:false, idx:i, isPlayer:false, ctrlId:null, label:'', crouchK:0 });
  }
  cylMesh.instanceColor.needsUpdate = sphMesh.instanceColor.needsUpdate = true;

  // ---------- チーズAI＆入力ルーティング ----------
  function cheeseAIInput(ch){
    let avoidForce = 0;
    const look = cfg.course === 'board' ? Math.max(18, cfg.boardGap * 0.9) : 10;
    for (const o of obstacles){
      const ahead = o.s - ch.s;
      if (ahead < 0.2 || ahead > look) continue;
      const half = halfWidth(o) + (cfg.course === 'board' ? 1.2 : 0.75);
      if (Math.abs(ch.x - o.x) < half + 0.6){
        const dir = o.kind === 'board' ? (o.x < 0 ? 1 : -1) : (ch.x < o.x ? -1 : 1);
        avoidForce += dir * (1 - ahead / look) * (cfg.course === 'board' ? 3.5 : 2.5);
      }
    }
    let dodgeForce = 0, pushY = cfg.isRace ? (0.65 + 0.35 * ch.aiSpeedK) : 0.35;
    if (!cfg.isRace){
      let nearDist = 999, nearX = 0;
      for (let i = 0; i < activeN; i++){
        const c = chasers[i], ds = ch.s - c.s;
        if (ds > -1 && ds < 10){ const d = Math.hypot(ds, ch.x - c.x); if (d < nearDist){ nearDist = d; nearX = c.x; } }
      }
      if (nearDist < 8){
        let dir = ch.x >= nearX ? 1 : -1;
        if (ch.x > cfg.halfW - 1.8) dir = -1; if (ch.x < -cfg.halfW + 1.8) dir = 1;
        dodgeForce = dir * (1 - nearDist / 8) * 1.4; pushY = 0.85;
      }
    }
    const wander = Math.sin(playTime * (0.9 + ch.idx * 0.17) + obSeed + ch.idx * 2.1) * 0.22 + ch.aiBias * 0.15;
    const wallForce = ch.x > cfg.halfW - 1.5 ? -1.2 : ch.x < -cfg.halfW + 1.5 ? 1.2 : 0;
    return { x:clamp(avoidForce + dodgeForce + wander + wallForce, -1, 1), y:pushY };
  }
  function getCtrlForId(ctrlId, consumeDive){
    if (ctrlId === 'host'){
      const inp = input(); let d = false;
      if (consumeDive){ d = diveRequested; diveRequested = false; }
      return { x:inp.x, y:inp.y, dive:d };
    }
    const cl = net.clients.get(ctrlId);
    if (cl){
      let d = false; if (consumeDive){ d = cl.input.dive; cl.input.dive = false; }
      return { x:cl.input.x, y:cl.input.y, dive:d };
    }
    return null;
  }

  // ---------- 人の動き ----------
  const setPose = (c, mode, k) => { c.q.set(c.p); writePose(c, c.p, mode, k); };
  function resetChaser(c, i, initial){
    c.state = initial ? 'wait' : 'run';
    if (initial){
      if (c.isPlayer){
        const pCount = Math.max(1, cfg.humanPlayerCount), gap = Math.min(2.2, (cfg.halfW * 1.4) / pCount);
        c.s = -3.2; c.x = clamp((i - (pCount - 1) / 2) * gap, -cfg.halfW + 0.8, cfg.halfW - 0.8);
      } else {
        const perRow = Math.max(6, Math.min(20, Math.floor((cfg.halfW * 2 - 1.4) / 1.4)));
        const row = Math.floor(i / perRow) + (cfg.humanPlayerCount > 0 ? 1 : 0), col = i % perRow, gap = Math.min(1.6, (cfg.halfW * 2 - 1.4) / perRow);
        c.s = -3.2 - row * 1.35 - (col % 2) * 0.4; c.x = clamp((col - (perRow - 1) / 2) * gap, -cfg.halfW + 0.6, cfg.halfW - 0.6);
      }
    } else {
      const mc = mainCheese();
      c.s = mc.s - SPAWN_BEHIND - Math.random() * 4;
      c.x = clamp(mc.x + (Math.random() * 2 - 1) * (cfg.halfW * 0.5), -cfg.halfW + 0.5, cfg.halfW - 0.5);
    }
    c.v = initial ? 0 : Math.max(mainCheese().v, 5);
    c.vx = 0; c.t = 0; c.yaw = 0; c.freq = 1.2; c.crouchK = 0; c.canCatch = false;
    c.cool = initial ? (c.isPlayer ? cfg.startDelay : cfg.startDelay + cfg.startGrace) : 0;
    c.phase = Math.random(); c.vary = c.isPlayer ? 0 : (Math.random() * 2 - 1) * SPEED_VAR;
    c.off = (Math.random() * 2 - 1) * Math.min(5, cfg.halfW * 0.35);
    writePose(c, c.p, initial ? 'ready' : 'run', 0); c.q.set(c.p);
    if (!initial) for (let n = 0; n < NP; n++) c.q[n * 3 + 2] -= c.v * STEP;
  }

  const cheeseHeightOf = c => c.y - cfg.cheeseR - ground(c.x, c.s);
  function updateCheeseOne(c, dt){
    if (c.held){ const p = c.held.p, k = c.hand * 3; c.x = p[k]; c.y = p[k+1]; c.s = p[k+2]; return; }
    if (game === 'playing'){
      const k = c.grounded ? 1 : cfg.airControl;
      const ctrl = c.finished ? { x:0, y:-0.6 } : (c.ctrlId === 'ai' ? cheeseAIInput(c) : (getCtrlForId(c.ctrlId, false) || cheeseAIInput(c)));
      const cVxMax = cfg.mode === 'human' ? 4.2 : cfg.vxMax, cLatAcc = cfg.mode === 'human' ? 18 : cfg.latAcc;
      c.vx += clamp(ctrl.x * cVxMax - c.vx, -cLatAcc * k * dt, cLatAcc * k * dt);
      const pushScale = Math.max(1, cfg.targetSpeed / 9.5);
      if (ctrl.y > 0) c.v += ctrl.y * PUSH_ACC * pushScale * k * dt;
      else if (ctrl.y < 0) c.v = Math.max(Math.min(c.v, 0), c.v + ctrl.y * BRAKE_ACC * pushScale * k * dt);
      if (c.grounded){ c.vx -= c.tilt * TILT_DRIFT * dt; c.v -= cfg.turnCost * Math.abs(c.vx) * dt; }
      const along = c.finished ? cfg.gAlong * 0.15 : cfg.gAlong * (c.ctrlId === 'ai' && cfg.isRace ? (0.94 + 0.12 * c.aiSpeedK) : 1);
      c.v = Math.max(-3, c.v + (along - cfg.drag * c.v * Math.abs(c.v)) * dt);
      if (cheeseHeightOf(c) < 0.5 && inBush(c.x, c.s)) c.v = Math.max(Math.min(c.v, 0), c.v - BUSH_DRAG * dt);
    }
    c.s += c.v * dt; c.x += c.vx * dt;
    if (game === 'playing' && !c.finished) cheeseObstacles(c);
    if (c.x < -cfg.xlim){ c.x = -cfg.xlim; c.vx = Math.max(0, c.vx); }
    if (c.x > cfg.xlim){ c.x = cfg.xlim; c.vx = Math.min(0, c.vx); }
    c.vy -= cfg.gFall * dt; c.y += c.vy * dt;
    const gy = ground(c.x, c.s) + cfg.cheeseR;
    if (c.y <= gy){
      const gvy = slopeS(c.x, c.s) * c.v + slopeX(c.x, c.s) * c.vx, hit = gvy - c.vy;
      if (hit > cfg.bounceMin){
        c.vy = gvy + hit * cfg.bounce; c.tiltV += (Math.random() - 0.5) * hit * TILT_KICK * 4;
        if (game === 'playing') c.vx += (Math.random() - 0.5) * hit * 0.3;
      } else c.vy = gvy;
      c.y = gy; c.grounded = true;
    } else if (c.y - gy > 0.03) c.grounded = false;
    c.tiltV += (-TILT_SPRING * c.tilt - TILT_DAMP * c.tiltV) * dt;
    c.tilt = clamp(c.tilt + c.tiltV * dt, -0.6, 0.6);
    c.rot = (c.rot + c.v / cfg.cheeseR * dt) % TAU;

    if (game === 'playing' && !c.finished && c.s >= cfg.goalDist){
      c.finished = true; c.finishTime = playTime;
      if (cfg.isRace){ if (!raceOrder.includes(c.idx)) raceOrder.push(c.idx); }
      else { c.s = cfg.goalDist; maxDist = cfg.goalDist; finishCaught(null, 0, 'cheese_goal', -1); }
    }
  }
  function collideCheeses(){
    for (let i = 0; i < cheeseN; i++) for (let j = i + 1; j < cheeseN; j++){
      const a = cheeses[i], b = cheeses[j], dx = a.x - b.x, dy = a.y - b.y, ds = a.s - b.s, d = Math.hypot(dx, dy, ds), minD = cfg.cheeseR * 2.1;
      if (d > 1e-5 && d < minD){
        const pen = (minD - d) * 0.5, nx = dx / d, ns = ds / d;
        a.x = clamp(a.x + nx * pen, -cfg.xlim, cfg.xlim); b.x = clamp(b.x - nx * pen, -cfg.xlim, cfg.xlim);
        a.s += ns * pen; b.s -= ns * pen;
        const vn = (a.vx - b.vx) * nx + (a.v - b.v) * ns;
        if (vn < 0){ const imp = -0.65 * vn; a.vx += imp * nx; b.vx -= imp * nx; a.v += imp * ns; b.v -= imp * ns; }
      }
    }
  }
  function finishCaught(c, hand, resOutcome, winIdx){
    game = 'caught'; outcome = resOutcome; winnerIdx = winIdx !== undefined ? winIdx : -1; endJoy(); overT = 0;
    if (c){ const mc = mainCheese(); mc.held = c; mc.hand = hand; mc.v = mc.vx = mc.vy = 0; }
    for (const o of chasers) if (o !== c && (o.state === 'run' || o.state === 'windup' || o.state === 'wait')) o.state = 'idle';
    if (cfg.isOnline && net.role === 'host') sendHostSnapshot(true);
  }
  const _d3 = (p, i, x, y, s) => Math.hypot(p[i*3]-x, p[i*3+1]-y, p[i*3+2]-s);
  function launch(c){
    const mc = mainCheese(); c.state = 'rag'; c.t = 0; c.canCatch = true; writePose(c, c.p, 'reach', 0);
    let tx, ts;
    if (c.isPlayer && mc.s - c.s > -1 && mc.s - c.s < 5.5 && Math.abs(mc.x - c.x) < 3.2){
      ts = mc.s + mc.v * DIVE_T * 0.85; tx = clamp(mc.x + mc.vx * DIVE_T * 0.5, -cfg.halfW + 0.5, cfg.halfW - 0.5);
    } else if (c.isPlayer){
      ts = c.s + Math.max(c.v, 6) * DIVE_T + 1.8; tx = clamp(c.x + c.vx * DIVE_T, -cfg.halfW + 0.5, cfg.halfW - 0.5);
    } else {
      ts = mc.s + mc.v * DIVE_T; tx = clamp(mc.x + mc.vx * DIVE_T * AIM, -cfg.halfW + 0.5, cfg.halfW - 0.5);
    }
    const dx = tx - c.x, ds = ts - c.s, dl = Math.hypot(dx, ds) || 1;
    const px = tx - dx / dl * DIVE_REACH, ps = ts - ds / dl * DIVE_REACH, p = c.p, k = PELVIS * 3;
    const vx = (px - p[k]) / DIVE_T, vs = (ps - p[k+2]) / DIVE_T, vy = (ground(px, ps) + 0.35 - p[k+1]) / DIVE_T + 0.5 * 9.8 * DIVE_T;
    const wx = DIVE_SPIN * (ds / dl), ws = DIVE_SPIN * (-dx / dl);
    for (let i = 0; i < NP; i++){
      const ox = p[i*3]-p[k], oy = p[i*3+1]-p[k+1], os = p[i*3+2]-p[k+2];
      c.q[i*3] = p[i*3] - (vx - ws * oy) * STEP; c.q[i*3+1] = p[i*3+1] - (vy + ws * ox - wx * os) * STEP; c.q[i*3+2] = p[i*3+2] - (vs + wx * oy) * STEP;
    }
  }
  function updateChaser(c, dt){
    const mc = mainCheese(), behind = mc.s - c.s; c.cool -= dt;
    switch (c.state){
      case 'wait': c.t += dt; setPose(c, 'ready', 0); if (c.t >= cfg.startDelay){ c.state = 'run'; c.t = 0; } break;
      case 'run': case 'windup': {
        let wantDive = false;
        const playerCtrl = c.isPlayer ? getCtrlForId(c.ctrlId, true) : null;
        if (playerCtrl){
          wantDive = playerCtrl.dive;
          let target = (cfg.targetSpeed + 1.0) * cfg.chSpeedMult;
          if (playerCtrl.y > 0) target += playerCtrl.y * Math.max(2.2, cfg.targetSpeed * 0.12) * cfg.chSpeedMult;
          else if (playerCtrl.y < 0) target = Math.max(0, target + playerCtrl.y * Math.max(7, cfg.targetSpeed * 0.4));
          if (behind > 18) target += Math.min(4.5, (behind - 18) * 0.2);
          c.v += clamp(target - c.v, -Math.max(12, cfg.runAcc * 1.4) * dt, Math.max(12, cfg.runAcc * 1.4) * dt);
          const pVx = Math.max(cfg.vxMax, 5.2);
          c.vx += clamp(playerCtrl.x * pVx - c.vx, -Math.max(28, pVx * 5) * dt, Math.max(28, pVx * 5) * dt);
        } else {
          let target = Math.min((cfg.targetSpeed + 0.9 + mc.s * 0.0008) * cfg.chSpeedMult, (Math.max(mc.v, 3) + 1.3 + mc.s * 0.0004) * cfg.chSpeedMult) + c.vary;
          if (behind > CATCHUP_DIST) target += CATCHUP * cfg.chSpeedMult;
          if (behind < 0.4) target = Math.min(target, Math.max(mc.v, cfg.course === 'board' ? 5 : 0));
          c.v += clamp(Math.max(0, target) - c.v, -cfg.runAcc * dt, cfg.runAcc * dt);
          const want = mc.x + c.off * clamp((behind - 2) / 8, 0, 1);
          c.vx += clamp(clamp((want - c.x) * 3, -cfg.chVx, cfg.chVx) - c.vx, -Math.max(15, cfg.chVx * 2.5) * dt, Math.max(15, cfg.chVx * 2.5) * dt);
        }
        c.s += c.v * dt; c.x = clamp(c.x + c.vx * dt, -cfg.halfW + 0.5, cfg.halfW - 0.5);
        c.yaw = Math.atan2(c.vx, Math.max(c.v, 1)) * 0.8; c.freq = clamp(c.v * 0.32, 1.2, 3.0); c.phase += c.freq * dt;
        if (cfg.course === 'board' && game === 'playing' && c.cool <= 0 && Math.abs(mc.s - c.s) < 1.25 && Math.abs(mc.x - c.x) < 0.95 && cheeseHeightOf(mc) < CATCH_HEIGHT){
          setPose(c, 'reach', 0); finishCaught(c, HNR, c.isPlayer ? 'human_win' : 'caught', c.idx); break;
        }
        if (c.state === 'run'){
          setPose(c, 'run', 0);
          if (playerCtrl){ if (game === 'playing' && wantDive && c.cool <= 0){ c.state = 'windup'; c.t = 0; c.crouchK = 0; } }
          else if (game === 'playing' && c.cool <= 0 && behind > -DIVE_BACK && behind < DIVE_RANGE && Math.abs(mc.x - c.x) < DIVE_DX && cheeseHeightOf(mc) < CATCH_HEIGHT){ c.state = 'windup'; c.t = 0; c.crouchK = 0; }
        } else {
          c.t += dt; const wDur = playerCtrl ? WINDUP * 0.55 : WINDUP; c.crouchK = Math.min(1, c.t / wDur);
          setPose(c, 'crouch', c.crouchK); if (c.t >= wDur) launch(c);
        }
        break;
      }
      case 'rag': {
        c.t += dt; ragStep(c, dt, c.t < DIVE_T * 0.9 && mc.held !== c);
        const p = c.p, k = PELVIS * 3; c.x = p[k]; c.s = p[k+2]; c.v = (p[k+2] - c.q[k+2]) / dt;
        if (game === 'playing' && c.canCatch && c.t < CATCH_WINDOW && cheeseHeightOf(mc) < CATCH_HEIGHT){
          const X = mc.x, Y = mc.y, S = mc.s, dl = _d3(p,HNL,X,Y,S), dr = _d3(p,HNR,X,Y,S);
          const body = Math.min(_d3(p,NECK,X,Y,S), _d3(p,PELVIS,X,Y,S), _d3(p,SHL,X,Y,S), _d3(p,SHR,X,Y,S)), bonus = c.isPlayer ? 0.16 : 0;
          if (Math.min(dl, dr) < cfg.cheeseR + 0.25 + bonus || body < cfg.cheeseR + 0.3 + bonus){ finishCaught(c, dl < dr ? HNL : HNR, c.isPlayer ? 'human_win' : 'caught', c.idx); break; }
        }
        const sp = Math.hypot(p[k]-c.q[k], p[k+1]-c.q[k+1], p[k+2]-c.q[k+2]) / dt;
        if (mc.held !== c && c.t > RAG_MIN && (sp < RAG_STILL || c.t > RAG_MAX)){
          c.state = 'getup'; c.t = 0; c.a.set(p); c.s0 = c.s; c.x0 = c.x; c.g0 = ground(c.x, c.s); c.v = Math.max(0, Math.min(c.v, 4)); c.vx = 0; c.yaw = 0; c.phase = 0;
        }
        break;
      }
      case 'getup': {
        c.t += dt; c.v += (3 - c.v) * Math.min(1, 3 * dt); c.s += c.v * dt; c.freq = clamp(c.v * 0.32, 1.2, 3.0); c.phase += c.freq * dt;
        c.q.set(c.p); writePose(c, c.p, 'run', 0);
        const k = smooth(Math.min(1, c.t / GETUP_T)), dS = c.s - c.s0, dX = c.x - c.x0, dY = ground(c.x, c.s) - c.g0;
        for (let i = 0; i < NP; i++){ c.p[i*3] = lerp(c.a[i*3]+dX, c.p[i*3], k); c.p[i*3+1] = lerp(c.a[i*3+1]+dY, c.p[i*3+1], k); c.p[i*3+2] = lerp(c.a[i*3+2]+dS, c.p[i*3+2], k); }
        if (c.t >= GETUP_T){ c.state = game === 'playing' ? 'run' : 'idle'; c.t = 0; c.cool = c.isPlayer ? 0.35 : COOLDOWN; }
        break;
      }
      case 'idle':
        c.v = Math.max(0, c.v - 8 * dt); if (behind < 1.5 && Math.abs(mc.x - c.x) < 1) c.v = 0;
        c.s += c.v * dt; c.freq = clamp(c.v * 0.32, 1.2, 3.0); c.phase += c.freq * dt; setPose(c, 'run', 0); break;
    }
    if (game === 'playing' && !c.isPlayer && behind > RESPAWN_BEHIND && mc.held !== c) resetChaser(c, c.idx, false);
    if (game === 'playing' && c.isPlayer && behind > 50 && c.state === 'run'){ c.s = mc.s - 28; c.x = clamp(mc.x, -cfg.halfW + 1, cfg.halfW - 1); c.v = Math.max(c.v, mc.v); }
  }
  function separate(){
    const mv = s => s === 'run' || s === 'windup' || s === 'idle';
    for (let i = 0; i < activeN; i++) for (let j = i + 1; j < activeN; j++){
      const a = chasers[i], b = chasers[j];
      if (!mv(a.state) || !mv(b.state)) continue;
      if (Math.abs(a.s - b.s) < 0.8 && Math.abs(a.x - b.x) < 0.6){
        const push = (0.6 - Math.abs(a.x - b.x)) * 0.5 * (a.x >= b.x ? 1 : -1);
        a.x = clamp(a.x + push, -cfg.halfW + 0.5, cfg.halfW - 0.5); b.x = clamp(b.x - push, -cfg.halfW + 0.5, cfg.halfW - 0.5);
      }
    }
  }
  function step(dt){
    if (game === 'playing') playTime += dt;
    updateObstacles();
    for (let i = 0; i < cheeseN; i++) updateCheeseOne(cheeses[i], dt);
    if (cfg.isRace && game === 'playing'){
      collideCheeses();
      const humans = cheeses.slice(0, cheeseN).filter(c => c.ctrlId !== 'ai');
      const anyDone = humans.some(c => c.finished), allDone = humans.length > 0 && humans.every(c => c.finished);
      if (anyDone) raceFinishTimer += dt;
      if (allDone || (anyDone && raceFinishTimer >= 6.0) || raceOrder.length >= cheeseN) finishCaught(null, 0, 'race_done', raceOrder[0] ?? 0);
    } else {
      const mc = mainCheese();
      if (game === 'playing' && cfg.humanPlayerCount === 0){
        const n = Math.min(cfg.chMax, cfg.chStart + Math.floor(mc.s / cfg.chAddEvery));
        while (activeN < n){ const c = chasers[activeN++]; c.isPlayer = false; c.ctrlId = null; resetChaser(c, activeN - 1, false); c.active = true; }
      }
      for (let i = 0; i < activeN; i++) updateChaser(chasers[i], dt);
      separate();
      if (mc.held) updateCheeseOne(mc, dt);
      if (game === 'playing') maxDist = Math.min(cfg.goalDist, Math.max(maxDist, mc.s));
    }
    recordTrail();
    S0 = cfg.isRace ? focusCheese().s : (cfg.myRole === 'human' && activeN > cfg.myIndex ? chasers[cfg.myIndex].s : mainCheese().s);
  }

  // ---------- ゲスト補間 ----------
  function applyGuestInterpolation(dt){
    const sn = net.snap; if (!sn) return;
    const k = Math.min(1, dt * 18);
    cheeseN = sn.ch.length;
    for (let i = 0; i < cheeseN; i++){
      const sc = sn.ch[i], c = cheeses[i];
      if (Math.hypot(sc.s - c.s, sc.x - c.x) > 10){ c.s = sc.s; c.x = sc.x; c.y = sc.y; }
      else { c.s = lerp(c.s, sc.s, k); c.x = lerp(c.x, sc.x, k); c.y = lerp(c.y, sc.y, k); }
      c.v = sc.v; c.vx = sc.vx; c.tilt = lerp(c.tilt, sc.tl, k); c.rot = (c.rot + c.v / cfg.cheeseR * dt) % TAU;
      c.held = sc.h >= 0 ? chasers[sc.h] : null; c.hand = sc.hd; c.finished = !!sc.fn; c.finishTime = sc.ft || 0;
    }
    activeN = sn.cs.length;
    for (let i = 0; i < activeN; i++){
      const d = sn.cs[i], c = chasers[i], prev = c.state;
      c.active = true; c.state = d.st; c.v = d.v; c.cool = d.cl; c.freq = clamp(c.v * 0.32, 1.2, 3.0);
      if (d.p){
        const jmp = prev !== d.st && prev !== 'rag' && prev !== 'getup';
        for (let j = 0; j < NP * 3; j++) c.p[j] = jmp ? d.p[j] : lerp(c.p[j], d.p[j], Math.min(1, dt * 22));
        c.x = c.p[PELVIS*3]; c.s = c.p[PELVIS*3+2];
      } else {
        if (Math.hypot(d.s - c.s, d.x - c.x) > 10){ c.s = d.s; c.x = d.x; c.yaw = d.yw; c.phase = d.ph; }
        else { c.s = lerp(c.s, d.s, k); c.x = lerp(c.x, d.x, k); c.yaw = lerp(c.yaw, d.yw, k); c.phase += c.freq * dt; }
        writePose(c, c.p, d.st === 'wait' ? 'ready' : d.st === 'windup' ? 'crouch' : 'run', d.ck || 0);
      }
    }
    maxDist = sn.md; playTime = sn.pt; if (sn.ro) raceOrder = sn.ro.slice(); winnerIdx = sn.wc ?? -1;
    if (sn.g !== game){
      if (sn.g === 'caught' && game === 'playing'){ game = 'caught'; outcome = sn.oc; endJoy(); }
      else if (sn.g === 'over' && game !== 'over'){ outcome = sn.oc; showOver(); }
    }
    recordTrail();
    S0 = cfg.isRace ? focusCheese().s : (cfg.myRole === 'human' && activeN > cfg.myIndex ? chasers[cfg.myIndex].s : mainCheese().s);
    updateObstacles();
  }

  // ---------- 描画 ----------
  const RP = Array.from({ length:NP }, () => new THREE.Vector3());
  const _m = new THREE.Matrix4(), _x = new THREE.Vector3(), _y = new THREE.Vector3(), _z = new THREE.Vector3(), _r = new THREE.Vector3(), _u = new THREE.Vector3(), _b = new THREE.Vector3(), _c = new THREE.Vector3();
  function drawChaser(c, idx){
    for (let i = 0; i < NP; i++) rel(RP[i], c.p[i*3+2], c.p[i*3], c.p[i*3+1]);
    _u.subVectors(RP[NECK], RP[PELVIS]).normalize();
    _r.subVectors(RP[SHR], RP[SHL]).add(_c.subVectors(RP[HPR], RP[HPL]));
    _r.addScaledVector(_u, -_r.dot(_u)).normalize(); _b.crossVectors(_r, _u);
    CYL.forEach(([a, b, , ra, rb], j) => {
      _y.subVectors(RP[b], RP[a]); const len = _y.length() || 1e-4; _y.divideScalar(len);
      _x.copy(_r).addScaledVector(_y, -_r.dot(_y)); if (_x.lengthSq() < 1e-4) _x.copy(_b).addScaledVector(_y, -_b.dot(_y));
      _x.normalize(); _z.crossVectors(_x, _y);
      _m.makeBasis(_x.multiplyScalar(ra), _y.multiplyScalar(len), _z.multiplyScalar(rb));
      _m.setPosition(_c.addVectors(RP[a], RP[b]).multiplyScalar(0.5)); cylMesh.setMatrixAt(idx * CYL.length + j, _m);
    });
    SPH.forEach(([a, key, sx, sy, sz], j) => {
      _c.copy(RP[a]); _x.copy(_r); _y.copy(_u); _z.copy(_b);
      if (key === 'hair') _c.addScaledVector(_u, 0.025).addScaledVector(_b, 0.012);
      if (key === 'shoe'){
        _y.subVectors(RP[a === FTL ? KNL : KNR], RP[a]).normalize(); _z.copy(_b).addScaledVector(_y, -_b.dot(_y));
        if (_z.lengthSq() < 1e-4) _z.copy(_u); _z.normalize(); _x.crossVectors(_y, _z); _c.addScaledVector(_z, -0.05).addScaledVector(_y, -0.03);
      }
      _m.makeBasis(_x.multiplyScalar(sx), _y.multiplyScalar(sy), _z.multiplyScalar(sz)); _m.setPosition(_c); sphMesh.setMatrixAt(idx * SPH.length + j, _m);
    });
  }
  const getRaceStandings = () => cheeses.slice(0, cheeseN).sort((a, b) => a.finished && b.finished ? a.finishTime - b.finishTime : a.finished ? -1 : b.finished ? 1 : b.s - a.s);

  let camX = 0, zoom = 0;
  const _camP = new THREE.Vector3(), _camL = new THREE.Vector3(), _zP = new THREE.Vector3(), _zL = new THREE.Vector3();
  function draw(dt){
    updateChunks(); updateFence(); drawObstacles(); updateGoalGate();
    for (let i = 0; i < MAX_CHEESES; i++){
      const vis = cheeseVisuals[i]; vis.grp.visible = i < cheeseN; if (!vis.grp.visible) continue;
      const c = cheeses[i]; rel(vis.grp.position, c.s, c.x, c.y);
      if (!c.held){ vis.grp.rotation.y = -Math.atan2(c.vx, Math.max(c.v, 1)); vis.grp.rotation.x = Math.atan(slopeS(c.x, c.s)); vis.grp.rotation.z = -c.vx / Math.max(1, cfg.vxMax) * 0.3 + c.tilt; }
      vis.spin.rotation.x = -c.rot;
    }
    for (let i = 0; i < activeN; i++) drawChaser(chasers[i], i);
    cylMesh.count = activeN * CYL.length; sphMesh.count = activeN * SPH.length;
    cylMesh.instanceMatrix.needsUpdate = sphMesh.instanceMatrix.needsUpdate = true;

    const bob = Math.sin(performance.now() * 0.008) * 0.1;
    for (let i = 0; i < MAX_CHEESES; i++) markers[i].visible = false;
    if (cfg.isRace){
      for (let i = 0; i < cheeseN; i++){ const c = cheeses[i], mk = markers[i]; mk.visible = true; mk.material.color.set(c.color); rel(mk.position, c.s, c.x, c.y + cfg.cheeseR + 0.65 + bob); mk.rotation.y = performance.now() * 0.003; }
    } else if (cfg.humanPlayerCount > 0){
      for (let i = 0; i < Math.min(cfg.humanPlayerCount, activeN, MAX_CHEESES); i++){
        const pl = chasers[i], mk = markers[i]; mk.visible = true; mk.material.color.set(cfg.myRole === 'human' && i === cfg.myIndex ? '#ffd23f' : '#38bdf8');
        rel(mk.position, pl.p[HEAD*3+2], pl.p[HEAD*3], pl.p[HEAD*3+1] + 0.65 + bob); mk.rotation.y = performance.now() * 0.003;
      }
      if (cfg.myRole === 'human' && activeN > cfg.myIndex){ const me = chasers[cfg.myIndex]; $('diveBtn').classList.toggle('disabled', !(game === 'playing' && me.state === 'run' && me.cool <= 0)); }
    }

    const fc = focusCheese();
    if (!cfg.isRace && cfg.myRole === 'human' && activeN > cfg.myIndex){
      const pl = chasers[cfg.myIndex], baseBack = Math.max(6.5, cfg.camBack);
      camX += (pl.x * 0.8 - camX) * Math.min(1, dt * 5);
      _camP.set(camX, SLOPE * baseBack + cfg.camUp, baseBack); _camL.set(lerp(camX, fc.x, 0.25), -SLOPE * 5.5 + 0.5, -5.5);
    } else {
      camX += (fc.x * cfg.camFollowX - camX) * Math.min(1, dt * 4);
      const camY = fc.y + SLOPE * S0 - bump(fc.x, fc.s);
      _camP.set(camX, SLOPE * cfg.camBack + cfg.camUp + camY * 0.3, cfg.camBack); _camL.set(camX, -SLOPE * CAM_LOOK + camY * 0.3, -CAM_LOOK);
    }
    const wantZoom = (game === 'caught' || game === 'over') && outcome !== 'race_done' && outcome !== 'cheese_goal' ? 1 : 0;
    zoom += (wantZoom - zoom) * Math.min(1, dt * ZOOM_SPEED);
    if (zoom > 0.001){
      rel(_zL, fc.s, fc.x, fc.y); _zP.set(_zL.x + ZOOM_SIDE, ground(fc.x, fc.s - ZOOM_BACK) + SLOPE * S0 + ZOOM_UP, _zL.z + ZOOM_BACK);
      const k = smooth(zoom); _camP.lerp(_zP, k); _camL.lerp(_zL, k);
    }
    camera.position.copy(_camP); camera.lookAt(_camL); drawTrail(dt);
    sun.target.position.set(camX, 0, -2); sun.position.set(camX - 6, 16, -10);

    if (cfg.isRace){
      const st = getRaceStandings(), rk = st.findIndex(c => c.idx === cfg.myIndex) + 1, rem = Math.max(0, Math.ceil(cfg.goalDist - fc.s)), key = `r:${rk}:${rem}:${Math.floor(playTime*2)}`;
      if (key !== shownHudKey){ shownHudKey = key; $('hud').textContent = `${rk}位 / ${cheeseN}  (残り ${rem}m)`; $('hudSub').textContent = `ゴール ${cfg.goalDist}m / ${playTime.toFixed(1)}秒`; }
    } else if (cfg.isOnline){
      const mc = mainCheese(), d = Math.min(cfg.goalDist, Math.max(0, Math.floor(mc.s))), me = cfg.myRole === 'human' && activeN > cfg.myIndex ? chasers[cfg.myIndex] : null, gap = me ? Math.max(0, Math.round(mc.s - me.s)) : 0, key = `o:${cfg.myRole}:${d}:${gap}`;
      if (key !== shownHudKey){
        shownHudKey = key;
        if (cfg.myRole === 'cheese'){ $('hud').textContent = `${d} / ${cfg.goalDist} m`; $('hudSub').textContent = `あなた：チーズ役（ゴールで勝ち）/ 追っ手 ${activeN}人`; }
        else { $('hud').textContent = `チーズまで ${gap} m`; $('hudSub').textContent = `あなた：人間役（障害物すり抜け）/ チーズ ${d}/${cfg.goalDist}m`; }
      }
    } else if (cfg.mode === 'human'){
      const mc = mainCheese(), gap = activeN > 0 ? Math.max(0, Math.round(mc.s - chasers[0].s)) : 0, d = Math.min(cfg.goalDist, Math.max(0, Math.floor(mc.s))), key = `h:${gap}:${d}`;
      if (key !== shownHudKey){ shownHudKey = key; $('hud').textContent = `チーズまで ${gap} m`; $('hudSub').textContent = `チーズ ${d}/${cfg.goalDist}m / BEST ${formatBest('human', currentBest)}`; }
    } else {
      const d = Math.min(cfg.goalDist, Math.max(0, Math.floor(maxDist))), key = `c:${d}`;
      if (key !== shownHudKey){ shownHudKey = key; $('hud').textContent = `${d} / ${cfg.goalDist} m`; $('hudSub').textContent = `BEST ${Math.round(currentBest === null ? d : Math.max(currentBest, d))} m（${cfg.goalDist}mで勝ち!）`; }
    }
    renderer.render(scene, camera);
  }

  // ---------- 設定反映 ----------
  function applyUserConfig(src, isOnlineMatch, myRoleOverride, myIdxOverride, humanPlayersCount){
    const s = src || userCfg, isOnline = !!isOnlineMatch, isRace = isOnline ? s.onlineGame === 'race' : s.mode === 'race';
    const course = isOnline ? s.onlineCourse : (s.mode === 'board' ? 'board' : s.mode === 'race' ? s.raceCourse : 'normal'), isBoard = course === 'board';
    cfg.mode = isOnline ? 'online' : s.mode; cfg.onlineType = s.onlineType || 'room'; cfg.isOnline = isOnline; cfg.isRace = isRace; cfg.course = course;
    cfg.goalDist = clamp(Math.round(s.goalDist || MAX_GOAL), 300, MAX_GOAL);
    cfg.myRole = isRace ? 'cheese' : (isOnline ? myRoleOverride : (s.mode === 'human' ? 'human' : 'cheese'));
    cfg.myIndex = myIdxOverride || 0; cfg.humanPlayerCount = isRace ? 0 : (isOnline ? (humanPlayersCount || 1) : (s.mode === 'human' ? 1 : 0));
    cfg.targetSpeed = s.speed; cfg.vxMax = s.vxMax; cfg.chSpeedMult = s.chSpeed; cfg.boardGap = s.boardGap; cfg.camUp = s.camUp;
    if (isRace){ cfg.chStart = cfg.chMax = 0; }
    else if (isOnline){ cfg.chStart = cfg.chMax = clamp(cfg.humanPlayerCount + Math.round(s.chasers), cfg.humanPlayerCount, 35); }
    else { cfg.chStart = clamp(Math.round(s.chasers), 1, CH_ALLOC); cfg.chMax = s.mode === 'human' ? cfg.chStart : Math.min(CH_ALLOC, Math.max(12, cfg.chStart + 6)); }
    if (isBoard){
      cfg.halfW=20; cfg.cheeseR=0.24; cfg.cheeseW=0.12; cfg.gFall=250; cfg.drag=1.0; cfg.gAlong=cfg.targetSpeed*cfg.targetSpeed;
      cfg.turnCost=0; cfg.latAcc=Math.max(80,cfg.vxMax*5); cfg.bounce=0; cfg.bounceMin=1e30; cfg.airControl=1.0; cfg.chAddEvery=100000;
      cfg.startDelay=isOnline?0.5:0.01; cfg.startGrace=1.0; cfg.runAcc=Math.max(30,cfg.targetSpeed*1.6); cfg.chVx=Math.max(10,cfg.vxMax*0.55);
      cfg.boardW=cfg.halfW; cfg.thigh=0.1; cfg.shin=0.7; cfg.camBack=5; cfg.camFollowX=1.0; cfg.camVfovMax=110;
    } else {
      cfg.halfW=isRace?10:8; cfg.cheeseR=0.32; cfg.cheeseW=0.2; cfg.gFall=9.8;
      cfg.gAlong = 9.8 * Math.sin(Math.atan(SLOPE)) * Math.pow(cfg.targetSpeed / 9.5, 1.3);
      cfg.drag = cfg.gAlong / (cfg.targetSpeed * cfg.targetSpeed);
      cfg.turnCost=0.1; cfg.latAcc=Math.max(22,cfg.vxMax*5); cfg.bounce=0.3; cfg.bounceMin=1.2; cfg.airControl=0.6; cfg.chAddEvery=100;
      cfg.startDelay=cfg.humanPlayerCount>0?0.5:1.0; cfg.startGrace=cfg.humanPlayerCount>0?1.0:2.0; cfg.runAcc=Math.max(7,cfg.targetSpeed*0.8); cfg.chVx=Math.max(3.2,cfg.vxMax*0.7);
      cfg.thigh=0.43; cfg.shin=0.43; cfg.camBack=cfg.myRole==='human'?7:8; cfg.camFollowX=0.75; cfg.camVfovMax=65;
    }
    cfg.xlim = cfg.halfW - cfg.cheeseW / 2 - 0.1;
    for (const v of cheeseVisuals) v.mesh.scale.set(cfg.cheeseW, cfg.cheeseR, cfg.cheeseR);
    rebuildBodyDist(); resize();
  }

  // ---------- オンライン通信（最大6人：Host 1 + Guests 最大5） ----------
  const setNetStatus = (st, tx) => { $('netDot').className = 'dot ' + (st || ''); $('netStatus').textContent = tx; };
  const r2 = v => Math.round(v * 100) / 100;
  const cleanRoomText = raw => raw.trim().replace(/[！-～]/g, ch => String.fromCharCode(ch.charCodeAt(0) - 0xFEE0)).replace(/\s+/g, '');
  function encodeRoomId(raw){
    const s = cleanRoomText(raw).toLowerCase(); if (!s) return '';
    return Array.from(new TextEncoder().encode(s), b => b.toString(16).padStart(2, '0')).join('').slice(0, 48);
  }
  function clearNetTimers(){
    if (net.startTimer){ clearTimeout(net.startTimer); net.startTimer = null; }
    if (net.joinTimer){ clearTimeout(net.joinTimer); net.joinTimer = null; }
    if (net.helloTimer){ clearInterval(net.helloTimer); net.helloTimer = null; }
  }
  function buildHostRoster(){
    const arr = [{ id:'host', name:'ホスト' }]; let n = 1;
    for (const cl of net.clients.values()) arr.push({ id:cl.id, name:`ゲスト${n++}` });
    net.roster = arr; return arr;
  }
  const broadcastToClients = obj => { for (const cl of net.clients.values()) if (cl.conn && cl.conn.open){ try{ cl.conn.send(obj); }catch{} } };
  const sendToHost = obj => { if (net.hostConn && net.hostConn.open){ try{ net.hostConn.send(obj); }catch{} } };
  const sendLobbyPreview = () => { if (net.role === 'host') broadcastToClients({ t:'lobby', cfg:{ ...userCfg }, roster:buildHostRoster() }); };

  function sendHostSnapshot(force){
    if (net.role !== 'host' || net.clients.size === 0) return;
    const now = performance.now(); if (!force && now - net.lastSend < SYNC_MS) return; net.lastSend = now;
    const ch = [];
    for (let i = 0; i < cheeseN; i++){ const c = cheeses[i]; ch.push({ s:r2(c.s), x:r2(c.x), y:r2(c.y), v:r2(c.v), vx:r2(c.vx), tl:r2(c.tilt), h:c.held?c.held.idx:-1, hd:c.hand, fn:c.finished?1:0, ft:r2(c.finishTime) }); }
    const cs = [];
    for (let i = 0; i < activeN; i++){
      const c = chasers[i], item = { st:c.state, s:r2(c.s), x:r2(c.x), v:r2(c.v), yw:r2(c.yaw), ph:r2(c.phase), cl:r2(c.cool) };
      if (c.state === 'windup') item.ck = r2(c.crouchK);
      if (c.state === 'rag' || c.state === 'getup'){ const arr = new Array(NP * 3); for (let j = 0; j < NP * 3; j++) arr[j] = r2(c.p[j]); item.p = arr; }
      cs.push(item);
    }
    broadcastToClients({ t:'snap', g:game, oc:outcome, wc:winnerIdx, md:r2(maxDist), pt:r2(playTime), ro:raceOrder, ch, cs });
  }
  function sendGuestInput(force){
    if (!net.connected || net.role !== 'guest' || game !== 'playing') return;
    const now = performance.now(); if (!force && !diveRequested && now - net.lastSend < SYNC_MS) return; net.lastSend = now;
    const inp = input(); sendToHost({ t:'in', x:r2(inp.x), y:r2(inp.y), d:diveRequested }); diveRequested = false;
  }
  function handleHostData(client, msg){
    if (!msg || typeof msg !== 'object') return;
    if (msg.t === 'hello'){ try{ client.conn.send({ t:'welcome', myId:client.id, cfg:{ ...userCfg }, roster:buildHostRoster() }); }catch{} }
    else if (msg.t === 'in'){ client.input.x = clamp(Number(msg.x)||0, -1, 1); client.input.y = clamp(Number(msg.y)||0, -1, 1); if (msg.d) client.input.dive = true; }
  }
  function handleGuestData(msg){
    if (!msg || typeof msg !== 'object') return;
    if (msg.t === 'welcome'){
      clearNetTimers(); net.connected = true; net.searching = false; net.myId = msg.myId;
      if (msg.cfg) Object.assign(userCfg, msg.cfg, { mode:'online' });
      if (msg.roster) net.roster = msg.roster;
      setNetStatus('ok', `接続完了！（部屋: ${net.room} / ${net.roster.length}人参加中）ホスト開始待ち`); syncMenuUI();
    } else if (msg.t === 'lobby'){
      if (msg.cfg) Object.assign(userCfg, msg.cfg, { mode:'online' });
      if (msg.roster) net.roster = msg.roster;
      if (net.connected) setNetStatus('ok', `接続中（部屋: ${net.room} / ${net.roster.length}人参加中）ホスト開始待ち`);
      syncMenuUI();
    } else if (msg.t === 'start'){
      clearNetTimers(); net.connected = true; net.searching = false;
      Object.assign(userCfg, msg.cfg, { mode:'online' });
      const a = msg.assign[net.myId] || { role:'human', idx:0 };
      startMatchInternal(true, msg.seed, a.role, a.idx, msg.cheeseN, msg.humanCount, null, msg.labels);
    } else if (msg.t === 'snap') net.snap = msg;
    else if (msg.t === 'menu') setupWorld(false);
  }
  function disconnectNet(silent){
    net.searchGen++; clearNetTimers();
    for (const cl of net.clients.values()){ try{ cl.conn.close(); }catch{} } net.clients.clear();
    if (net.hostConn){ try{ net.hostConn.close(); }catch{} net.hostConn = null; }
    if (net.peer){ try{ net.peer.destroy(); }catch{} net.peer = null; }
    net.role = 'none'; net.searching = false; net.connected = false; net.room = ''; net.roster = []; net.snap = null;
    if (!silent) setNetStatus('', userCfg.onlineType === 'free' ? 'ルールとコースを選んで開始してください' : '合言葉を決めて部屋を作るか入ってください（最大6人）');
    syncMenuUI();
  }
  function acceptHostConnection(conn, gen, isFree){
    if (gen !== net.searchGen || net.clients.size >= MAX_ONLINE_PLAYERS - 1 || game !== 'menu'){
      const rej = () => { try{ conn.send({ t:'busy' }); }catch{} setTimeout(() => { try{ conn.close(); }catch{} }, 200); };
      if (conn.open) rej(); else conn.on('open', rej); return;
    }
    const cid = conn.peer, cl = { conn, id:cid, input:{ x:0, y:0, dive:false } };
    net.clients.set(cid, cl);
    conn.on('data', msg => handleHostData(cl, msg));
    const onOpen = () => {
      if (gen !== net.searchGen || !net.clients.has(cid)) return;
      net.connected = true; const roster = buildHostRoster();
      try{ conn.send({ t:'welcome', myId:cid, cfg:{ ...userCfg }, roster }); }catch{}
      broadcastToClients({ t:'lobby', cfg:{ ...userCfg }, roster });
      if (isFree){
        setNetStatus('ok', `${roster.length}人集まりました！${roster.length >= MAX_ONLINE_PLAYERS ? '間もなく開始…' : '数秒後に開始（受付中）'}`);
        if (net.startTimer) clearTimeout(net.startTimer);
        net.startTimer = setTimeout(() => { if (gen === net.searchGen && game === 'menu' && net.clients.size > 0) hostLaunchOnlineMatch(); }, roster.length >= MAX_ONLINE_PLAYERS ? 900 : 3500);
      } else setNetStatus('ok', `${roster.length}/${MAX_ONLINE_PLAYERS}人参加中（部屋: ${net.room}）「対戦スタート」で開始`);
      syncMenuUI();
    };
    if (conn.open) onOpen(); else conn.on('open', onOpen);
    conn.on('close', () => {
      if (!net.clients.has(cid)) return;
      net.clients.delete(cid);
      if (game !== 'menu'){
        for (let i = 0; i < cheeseN; i++) if (cheeses[i].ctrlId === cid) cheeses[i].ctrlId = 'ai';
        for (let i = 0; i < activeN; i++) if (chasers[i].ctrlId === cid){ chasers[i].isPlayer = false; chasers[i].ctrlId = null; }
      }
      const roster = buildHostRoster(); net.connected = net.clients.size > 0;
      broadcastToClients({ t:'lobby', cfg:{ ...userCfg }, roster });
      if (game === 'menu'){
        if (net.clients.size === 0){ if (net.startTimer){ clearTimeout(net.startTimer); net.startTimer = null; } setNetStatus('wait', isFree ? `待機中（${net.room}）… 参加者を待っています` : `部屋「${net.room}」で待機中…（1/${MAX_ONLINE_PLAYERS}人）`); }
        else setNetStatus('ok', `${roster.length}/${MAX_ONLINE_PLAYERS}人参加中（部屋: ${net.room}）`);
        syncMenuUI();
      }
    });
  }

  function startFreeMatch(){
    if (typeof Peer === 'undefined'){ setNetStatus('err', 'PeerJS読み込み失敗（ネット確認）'); return; }
    disconnectNet(true); const gen = ++net.searchGen, key = `${userCfg.onlineGame}_${userCfg.onlineCourse}`;
    Object.assign(userCfg, FREE_RULES[key], { mode:'online', onlineType:'free' });
    net.matchKind = 'free'; net.searching = true; setNetStatus('wait', '全国の対戦相手を探しています…'); syncMenuUI();
    tryFreeSlot(0, key, gen);
  }
  function tryFreeSlot(idx, key, gen){
    if (gen !== net.searchGen) return;
    if (idx >= FREE_SLOTS){ setNetStatus('wait', '混み合っています… 再探索中'); net.startTimer = setTimeout(() => tryFreeSlot(0, key, gen), 1500); return; }
    const slotId = `${FREE_PREFIX}${key}-${idx}`, hostPeer = new Peer(slotId, PEER_OPTS);
    net.peer = hostPeer; let decided = false;
    setNetStatus('wait', `対戦相手を探しています…（エリア ${idx + 1}）`);
    hostPeer.on('open', () => {
      if (gen !== net.searchGen){ try{ hostPeer.destroy(); }catch{} return; }
      decided = true; net.role = 'host'; net.myId = 'host'; net.room = `全国#${idx + 1}`; buildHostRoster();
      setNetStatus('wait', `待機中（${net.room}）… 誰かが参加すると自動で始まります`); syncMenuUI();
    });
    hostPeer.on('disconnected', () => { if (gen === net.searchGen && !hostPeer.destroyed){ try{ hostPeer.reconnect(); }catch{} } });
    hostPeer.on('connection', conn => acceptHostConnection(conn, gen, true));
    hostPeer.on('error', err => {
      if (gen !== net.searchGen || decided) return;
      decided = true; try{ hostPeer.destroy(); }catch{} net.peer = null;
      if (err.type === 'unavailable-id') probeFreeSlotAsGuest(idx, key, gen);
      else net.startTimer = setTimeout(() => tryFreeSlot(idx + 1, key, gen), 400);
    });
  }
  function probeFreeSlotAsGuest(idx, key, gen){
    if (gen !== net.searchGen) return;
    const slotId = `${FREE_PREFIX}${key}-${idx}`, guestPeer = new Peer(PEER_OPTS); net.peer = guestPeer; let settled = false;
    const failAndNext = () => { if (settled || gen !== net.searchGen) return; settled = true; clearTimeout(timer); try{ guestPeer.destroy(); }catch{} if (net.peer === guestPeer) net.peer = null; tryFreeSlot(idx + 1, key, gen); };
    const timer = setTimeout(failAndNext, 4000);
    guestPeer.on('open', () => {
      if (settled || gen !== net.searchGen){ try{ guestPeer.destroy(); }catch{} return; }
      const conn = guestPeer.connect(slotId, { reliable:true, serialization:'json' });
      conn.on('open', () => { try{ conn.send({ t:'hello' }); }catch{} });
      conn.on('data', msg => {
        if (gen !== net.searchGen) return;
        if (!settled){
          if (msg && msg.t === 'busy'){ failAndNext(); return; }
          if (msg && (msg.t === 'welcome' || msg.t === 'start')){
            settled = true; clearTimeout(timer); net.role = 'guest'; net.room = `全国#${idx + 1}`; net.hostConn = conn;
            conn.on('close', () => { const wp = game !== 'menu'; disconnectNet(true); setNetStatus('err', 'ホストとの接続が切れました'); if (wp) setupWorld(false); else syncMenuUI(); });
            handleGuestData(msg);
          }
        } else handleGuestData(msg);
      });
      conn.on('close', () => { if (!settled) failAndNext(); }); conn.on('error', () => { if (!settled) failAndNext(); });
    });
    guestPeer.on('error', () => { if (!settled) failAndNext(); });
  }

  function createRoom(){
    if (typeof Peer === 'undefined'){ setNetStatus('err', 'PeerJS読み込み失敗'); return; }
    let code = cleanRoomText($('roomCode').value);
    if (!code){ code = String(Math.floor(1000 + Math.random() * 9000)); $('roomCode').value = code; }
    disconnectNet(true); const gen = ++net.searchGen;
    net.matchKind = 'room'; net.role = 'host'; net.myId = 'host'; net.room = code; buildHostRoster();
    setNetStatus('wait', `部屋「${code}」を作成中…`); syncMenuUI();
    const peer = new Peer(ROOM_PREFIX + encodeRoomId(code), PEER_OPTS); net.peer = peer;
    peer.on('open', () => { if (gen === net.searchGen){ setNetStatus('wait', `部屋「${code}」作成完了！（1/${MAX_ONLINE_PLAYERS}人）最大5人まで入れます`); syncMenuUI(); } });
    peer.on('disconnected', () => { if (gen === net.searchGen && !peer.destroyed){ try{ peer.reconnect(); }catch{} } });
    peer.on('connection', conn => acceptHostConnection(conn, gen, false));
    peer.on('error', err => { if (gen !== net.searchGen) return; setNetStatus('err', err.type === 'unavailable-id' ? `合言葉「${code}」は使用中です。別にしてください` : `エラー(${err.type})`); disconnectNet(true); });
  }
  function joinRoom(){
    if (typeof Peer === 'undefined'){ setNetStatus('err', 'PeerJS読み込み失敗'); return; }
    const code = cleanRoomText($('roomCode').value); if (!code){ setNetStatus('err', '相手の合言葉を入力してください'); return; }
    disconnectNet(true); const gen = ++net.searchGen;
    net.matchKind = 'room'; net.role = 'guest'; net.room = code;
    setNetStatus('wait', `部屋「${code}」へ接続中…`); syncMenuUI();
    const peer = new Peer(PEER_OPTS); net.peer = peer;
    net.joinTimer = setTimeout(() => { if (gen === net.searchGen && !net.connected){ disconnectNet(true); setNetStatus('err', 'タイムアウトしました。相手が先に「部屋を作る」を押しているか確認してください'); syncMenuUI(); } }, 12000);
    peer.on('open', () => {
      if (gen !== net.searchGen) return;
      const conn = peer.connect(ROOM_PREFIX + encodeRoomId(code), { reliable:true, serialization:'json' }); net.hostConn = conn;
      conn.on('data', msg => { if (gen !== net.searchGen) return; if (msg && msg.t === 'busy'){ disconnectNet(true); setNetStatus('err', `部屋「${code}」は満員または対戦中です`); syncMenuUI(); return; } handleGuestData(msg); });
      const onOpen = () => {
        if (gen !== net.searchGen || net.hostConn !== conn) return;
        const hi = () => { if (gen === net.searchGen && !net.connected && conn.open){ try{ conn.send({ t:'hello' }); }catch{} } };
        hi(); let t = 0; net.helloTimer = setInterval(() => { if (++t > 6 || net.connected || gen !== net.searchGen){ clearInterval(net.helloTimer); net.helloTimer = null; return; } hi(); }, 600);
      };
      if (conn.open) onOpen(); else conn.on('open', onOpen);
      conn.on('close', () => { if (gen !== net.searchGen) return; const wp = game !== 'menu'; disconnectNet(true); setNetStatus('err', 'ホストとの接続が切れました'); if (wp) setupWorld(false); else syncMenuUI(); });
      conn.on('error', () => { if (gen !== net.searchGen) return; disconnectNet(true); setNetStatus('err', 'P2P接続失敗。別の回線でお試しください'); syncMenuUI(); });
    });
    peer.on('error', err => { if (gen !== net.searchGen) return; disconnectNet(true); setNetStatus('err', err.type === 'peer-unavailable' ? `部屋「${code}」が見つかりません` : `接続エラー(${err.type})`); syncMenuUI(); });
  }

  function hostLaunchOnlineMatch(){
    if (net.role !== 'host' || net.clients.size === 0) return;
    clearNetTimers(); const roster = buildHostRoster(), seed = Math.floor(Math.random() * 1e6), assign = {}, labels = { cheeses:[], humans:[] };
    let totalCheeses = 1, humanCount = 0;
    if (userCfg.onlineGame === 'race'){
      totalCheeses = clamp(Math.max(roster.length, Math.round(userCfg.chasers)), roster.length, MAX_CHEESES);
      roster.forEach((p, i) => { assign[p.id] = { role:'cheese', idx:i }; labels.cheeses.push(p.name); });
      for (let i = roster.length; i < totalCheeses; i++) labels.cheeses.push(`CPU${i - roster.length + 1}`);
    } else {
      let cOwnerIdx = userCfg.hostRole === 'cheese' ? 0 : 1;
      if (net.matchKind === 'free') cOwnerIdx = Math.floor(Math.random() * roster.length);
      assign[roster[cOwnerIdx].id] = { role:'cheese', idx:0 }; labels.cheeses.push(roster[cOwnerIdx].name);
      let hIdx = 0;
      roster.forEach((p, i) => { if (i !== cOwnerIdx){ assign[p.id] = { role:'human', idx:hIdx++ }; labels.humans.push(p.name); } });
      humanCount = hIdx;
    }
    broadcastToClients({ t:'start', seed, cfg:{ ...userCfg, mode:'online' }, assign, cheeseN:totalCheeses, humanCount, labels });
    const ha = assign['host'];
    startMatchInternal(true, seed, ha.role, ha.idx, totalCheeses, humanCount, assign, labels);
  }

  // ---------- 試合開始・メニュー・結果 ----------
  function startMatchInternal(isOnline, fixedSeed, myRole, myIdx, totalCheeses, humanCount, assignMap, labels){
    applyUserConfig(userCfg, isOnline, myRole, myIdx, humanCount);
    cheeseN = cfg.isRace ? clamp(totalCheeses, 2, MAX_CHEESES) : 1;
    raceOrder = []; raceFinishTimer = 0; winnerIdx = -1;
    const startCheeseS = (!cfg.isRace && cfg.humanPlayerCount > 0) ? 4.5 : 0;
    for (let i = 0; i < cheeseN; i++){
      const c = cheeses[i], gap = cfg.isRace ? Math.min(2.4, (cfg.halfW * 1.5) / cheeseN) : 0, sx = cfg.isRace ? (i - (cheeseN - 1) / 2) * gap : 0;
      Object.assign(c, { s:startCheeseS, x:sx, y:ground(sx, startCheeseS) + cfg.cheeseR, v:0, vx:0, vy:0, rot:0, tilt:0, tiltV:0, grounded:true, held:null, hand:0, finished:false, finishTime:0, aiBias:Math.random()*2-1, aiSpeedK:0.5+Math.random()*0.5 });
      if (isOnline && assignMap){
        let owner = 'ai'; for (const [pid, a] of Object.entries(assignMap)) if (a.role === 'cheese' && a.idx === i) owner = pid;
        c.ctrlId = owner; c.label = (labels && labels.cheeses[i]) || `P${i + 1}`;
      } else if (!isOnline){ c.ctrlId = cfg.mode === 'human' ? 'ai' : (i === 0 ? 'host' : 'ai'); c.label = i === 0 ? 'あなた' : `CPU${i}`; }
      else c.label = (labels && labels.cheeses[i]) || `P${i + 1}`;
    }
    activeN = cfg.chStart;
    for (let i = 0; i < CH_ALLOC; i++){
      const c = chasers[i]; c.active = i < activeN; c.isPlayer = i < cfg.humanPlayerCount; c.ctrlId = null;
      if (c.isPlayer){
        if (isOnline && assignMap){ for (const [pid, a] of Object.entries(assignMap)) if (a.role === 'human' && a.idx === i) c.ctrlId = pid; c.label = (labels && labels.humans[i]) || `人間${i + 1}`; }
        else if (!isOnline){ c.ctrlId = 'host'; c.label = 'あなた'; }
        else c.label = (labels && labels.humans[i]) || `人間${i + 1}`;
      } else c.label = 'AI追っ手';
      if (c.active) resetChaser(c, i, true);
    }
    S0 = (!cfg.isRace && cfg.myRole === 'human') ? -3.2 : startCheeseS;
    maxDist = 0; camX = 0; zoom = 0; trail.length = 0; trailK = 0; overT = 0; acc = 0; playTime = 0;
    shownHudKey = ''; diveRequested = false; outcome = 'caught'; net.snap = null;
    for (const cl of net.clients.values()) cl.input = { x:0, y:0, dive:false };
    currentBest = getBest(cfg.mode); endJoy();
    for (const ch of chunks) ch.k = null; clearObstacles(fixedSeed); updateObstacles();
    $('resultOverlay').classList.add('hidden'); game = 'playing';
    $('menuOverlay').classList.add('hidden'); $('hudWrap').classList.remove('hidden'); $('menuBtn').classList.remove('hidden');
    $('diveBtn').classList.toggle('hidden', cfg.isRace || cfg.myRole !== 'human');
  }

  function setupWorld(startPlaying){
    if (startPlaying){
      if (userCfg.mode === 'online'){ if (net.role === 'host' && net.clients.size > 0) hostLaunchOnlineMatch(); return; }
      const isRace = userCfg.mode === 'race', cnt = isRace ? clamp(Math.round(userCfg.chasers), 2, MAX_CHEESES) : 1;
      startMatchInternal(false, undefined, userCfg.mode === 'human' ? 'human' : 'cheese', 0, cnt, userCfg.mode === 'human' ? 1 : 0, null, null); return;
    }
    const isOnline = userCfg.mode === 'online';
    applyUserConfig(userCfg, isOnline, isOnline ? userCfg.hostRole : (userCfg.mode === 'human' ? 'human' : 'cheese'), 0, userCfg.mode === 'human' ? 1 : 0);
    cheeseN = cfg.isRace ? clamp(Math.round(userCfg.chasers), 2, MAX_CHEESES) : 1;
    for (let i = 0; i < cheeseN; i++){ const sx = cfg.isRace ? (i - (cheeseN - 1) / 2) * 2.0 : 0; Object.assign(cheeses[i], { s:0, x:sx, y:ground(sx, 0) + cfg.cheeseR, v:0, vx:0, vy:0, rot:0, tilt:0, tiltV:0, grounded:true, held:null, hand:0, finished:false }); }
    S0 = 0; maxDist = 0; camX = 0; zoom = 0; trail.length = 0; trailK = 0; overT = 0; acc = 0; playTime = 0;
    shownHudKey = ''; diveRequested = false; endJoy();
    activeN = Math.min(cfg.chStart, 20);
    for (let i = 0; i < CH_ALLOC; i++){ chasers[i].active = i < activeN; chasers[i].isPlayer = false; if (chasers[i].active) resetChaser(chasers[i], i, true); }
    for (const ch of chunks) ch.k = null; clearObstacles(); updateObstacles();
    game = 'menu'; $('resultOverlay').classList.add('hidden'); $('menuOverlay').classList.remove('hidden');
    $('hudWrap').classList.add('hidden'); $('menuBtn').classList.add('hidden'); $('diveBtn').classList.add('hidden');
    syncMenuUI();
  }

  function showOver(){
    game = 'over'; $('diveBtn').classList.add('hidden');
    if (cfg.isOnline && net.role === 'host') sendHostSnapshot(true);
    const d = Math.min(cfg.goalDist, Math.max(1, Math.floor(maxDist))), titleEl = $('resTitle'), rankEl = $('rankList');
    rankEl.classList.add('hidden'); rankEl.innerHTML = ''; let isNew = false;

    if (cfg.isRace){
      const st = getRaceStandings(), myRank = st.findIndex(c => c.idx === cfg.myIndex) + 1, myC = cheeses[cfg.myIndex];
      $('resMode').textContent = `${cfg.isOnline ? 'オンライン' : ''}チーズレース（${cfg.goalDist}m）`;
      titleEl.classList.remove('hidden'); titleEl.textContent = myRank === 1 ? '1位 優勝！' : `${myRank}位 フィニッシュ`; titleEl.classList.toggle('fail', myRank > 1);
      $('dist').textContent = myC.finished ? `${myC.finishTime.toFixed(1)} 秒` : `${Math.floor(myC.s)} m`;
      if (!cfg.isOnline && myRank === 1 && myC.finished) isNew = saveBest('race', myC.finishTime);
      $('resBest').textContent = cfg.isOnline ? `${cheeseN}チーズ対戦` : `BEST ${formatBest('race', getBest('race'))}`;
      rankEl.classList.remove('hidden');
      st.forEach((c, idx) => {
        const li = document.createElement('li'); li.className = 'rank-item' + (c.idx === cfg.myIndex ? ' me' : '');
        li.innerHTML = `<span><i class="rank-dot" style="background:${c.color}"></i>${idx + 1}位 ${c.label}${c.idx === cfg.myIndex ? ' (あなた)' : ''}</span><span>${c.finished ? c.finishTime.toFixed(1) + '秒' : Math.floor(c.s) + 'm'}</span>`;
        rankEl.appendChild(li);
      });
    } else if (cfg.isOnline){
      $('resMode').textContent = `オンライン鬼ごっこ（${cfg.course === 'board' ? '板よけ' : '通常'} / ${cfg.goalDist}m）`;
      titleEl.classList.remove('hidden');
      const cheeseWon = outcome === 'cheese_goal', iWon = (cfg.myRole === 'cheese' && cheeseWon) || (cfg.myRole === 'human' && !cheeseWon);
      titleEl.textContent = iWon ? 'あなたの勝ち！' : '相手の勝ち…'; titleEl.classList.toggle('fail', !iWon);
      if (cheeseWon){ $('dist').textContent = `${cfg.goalDist}m GOAL!`; $('resBest').textContent = `チーズがゴール到達！（${playTime.toFixed(1)}秒）`; }
      else { $('dist').textContent = `${d} m`; $('resBest').textContent = `${(winnerIdx >= 0 && chasers[winnerIdx] ? chasers[winnerIdx].label : '追っ手')} が捕獲！（${playTime.toFixed(1)}秒）`; }
    } else if (cfg.mode === 'human'){
      $('resMode').textContent = PRESETS.human.label; titleEl.classList.remove('hidden');
      if (outcome === 'human_win'){ titleEl.textContent = 'チーズ捕獲成功！'; titleEl.classList.remove('fail'); isNew = saveBest('human', d); $('dist').textContent = `${d} m`; }
      else { titleEl.textContent = outcome === 'cheese_goal' ? `${cfg.goalDist}m ゴールされた…` : '他の追っ手に先を越された…'; titleEl.classList.add('fail'); $('dist').textContent = '失敗'; }
      $('resBest').textContent = `BEST ${formatBest('human', getBest('human'))}（${playTime.toFixed(1)}秒）`;
    } else {
      $('resMode').textContent = PRESETS[cfg.mode].label;
      if (outcome === 'cheese_goal'){ titleEl.classList.remove('hidden'); titleEl.classList.remove('fail'); titleEl.textContent = `${cfg.goalDist}m ゴール！チーズの勝ち！`; }
      else titleEl.classList.add('hidden');
      isNew = saveBest(cfg.mode, d); $('dist').textContent = `${d} m`; $('resBest').textContent = `BEST ${formatBest(cfg.mode, getBest(cfg.mode))}`;
    }
    const isHost = cfg.isOnline && net.role === 'host' && net.clients.size > 0, isFree = cfg.isOnline && cfg.onlineType === 'free';
    $('nextFreeBtn').classList.toggle('hidden', !isFree);
    $('swapRetryBtn').classList.toggle('hidden', !(isHost && !cfg.isRace));
    $('retryBtn').classList.toggle('hidden', cfg.isOnline && (!isHost || isFree));
    $('toMenuBtn').textContent = cfg.isOnline ? 'ロビーへ' : 'モード・設定';
    $('resNewBest').classList.toggle('hidden', !isNew);
    $('resultOverlay').classList.remove('hidden');
  }

  // ---------- メインループ ----------
  let last = performance.now(), acc = 0;
  function loop(now){
    const dt = Math.min((now - last) / 1000, 1 / 30); last = now;
    if (game !== 'menu'){
      if (cfg.isOnline && net.role === 'guest'){ sendGuestInput(false); applyGuestInterpolation(dt); }
      else {
        acc += dt * (game === 'playing' ? 1 : SLOW_SCALE);
        while (acc >= STEP){ step(STEP); acc -= STEP; }
        if (game === 'caught'){ overT += dt; if (overT >= (outcome === 'race_done' || outcome === 'cheese_goal' ? 0.9 : OVER_DELAY)) showOver(); }
        if (cfg.isOnline && net.role === 'host') sendHostSnapshot(false);
      }
    }
    draw(dt); requestAnimationFrame(loop);
  }

  // ---------- UI連動 ----------
  function syncMenuUI(){
    const m = userCfg.mode, isOnline = m === 'online', isRace = isOnline ? userCfg.onlineGame === 'race' : m === 'race';
    const isFree = isOnline && userCfg.onlineType === 'free', isRoom = isOnline && userCfg.onlineType === 'room', isHuman = m === 'human';
    const isBoard = isOnline ? userCfg.onlineCourse === 'board' : (m === 'board' || (m === 'race' && userCfg.raceCourse === 'board'));
    const isGuest = isOnline && net.role === 'guest', busyNet = net.searching || net.role !== 'none';

    document.querySelectorAll('.mode-btn').forEach(b => b.classList.toggle('active', b.dataset.mode === m));
    $('modeHint').textContent = PRESETS[m].hint;
    $('bestBox').classList.toggle('hidden', isOnline);
    $('raceCourseBox').classList.toggle('hidden', m !== 'race');
    $('onlineBox').classList.toggle('hidden', !isOnline);
    if (m === 'race') document.querySelectorAll('[data-rcourse]').forEach(b => b.classList.toggle('active', b.dataset.rcourse === userCfg.raceCourse));
    if (!isOnline){ $('menuBestLabel').textContent = `${PRESETS[m].label} ベスト`; $('menuBestVal').textContent = formatBest(m, getBest(m)); }
    else {
      document.querySelectorAll('[data-match]').forEach(b => { b.classList.toggle('active', b.dataset.match === userCfg.onlineType); b.disabled = busyNet; });
      document.querySelectorAll('[data-otype]').forEach(b => { b.classList.toggle('active', b.dataset.otype === userCfg.onlineGame); b.disabled = isFree ? busyNet : isGuest; });
      document.querySelectorAll('[data-course]').forEach(b => { b.classList.toggle('active', b.dataset.course === userCfg.onlineCourse); b.disabled = isFree ? busyNet : isGuest; });
      $('freeMatchUI').classList.toggle('hidden', !isFree); $('roomMatchUI').classList.toggle('hidden', !isRoom); $('roleToggleBox').classList.toggle('hidden', isRace);
      const chips = $('playerChips');
      if (net.roster.length > 0){ chips.classList.remove('hidden'); chips.innerHTML = net.roster.map(p => `<span class="chip${p.id === net.myId ? ' me' : ''}">${p.name}${p.id === net.myId ? '(あなた)' : ''}</span>`).join(''); }
      else chips.classList.add('hidden');
      if (isFree){ $('freeSearchBtn').classList.toggle('hidden', busyNet); $('freeCancelBtn').classList.toggle('hidden', !busyNet); $('freeStartNowBtn').classList.toggle('hidden', !(net.role === 'host' && net.clients.size > 0)); }
      else {
        $('connectForm').classList.toggle('hidden', net.role !== 'none'); $('connectedControls').classList.toggle('hidden', net.role === 'none');
        $('roomStartBtn').classList.toggle('hidden', !(net.role === 'host' && net.clients.size > 0));
        $('copyInviteBtn').classList.toggle('hidden', net.role !== 'host');
        $('roleToggleLabel').textContent = isGuest ? 'ホストが役割を設定します' : 'ホストの役割（他は人間）';
        document.querySelectorAll('[data-role]').forEach(b => { b.classList.toggle('active', b.dataset.role === userCfg.hostRole); b.disabled = isGuest; });
      }
    }
    $('settingsArea').classList.toggle('hidden', isFree); $('bottomStartRow').classList.toggle('hidden', isFree); $('rowChSpeed').classList.toggle('hidden', isRace);
    const chInput = $('optChasers');
    if (isRace){ chInput.min='2'; chInput.max=String(MAX_CHEESES); userCfg.chasers=clamp(userCfg.chasers,2,MAX_CHEESES); $('lblChasers').textContent=isOnline?'チーズ総数（足りない分はCPU）':'チーズの総数（自分＋CPU）'; }
    else if (isRoom){ chInput.min='0'; chInput.max='30'; userCfg.chasers=clamp(userCfg.chasers,0,30); $('lblChasers').textContent='追加AI追っ手（0人で人間のみ）'; }
    else { chInput.min='1'; chInput.max='300'; userCfg.chasers=clamp(userCfg.chasers,1,300); $('lblChasers').textContent=isHuman?'参加人数（自分含む）':'追っ手の人数'; }
    $('lblVx').textContent = isHuman ? '自分の横移動の速さ' : '横移動の速さ';
    $('lblChSpeed').textContent = (isHuman || isOnline) ? '人間の走る速さ' : '追っ手の速さ';
    $('optGoal').value = userCfg.goalDist; $('valGoal').textContent = `${userCfg.goalDist} m`;
    $('optSpeed').value = userCfg.speed; $('valSpeed').textContent = `${userCfg.speed} m/s`;
    $('optVx').value = userCfg.vxMax; $('valVx').textContent = `${userCfg.vxMax} m/s`;
    chInput.value = userCfg.chasers; $('valChasers').textContent = isRace ? `${userCfg.chasers} 個` : `${userCfg.chasers} 人`;
    $('optChSpeed').value = userCfg.chSpeed; $('valChSpeed').textContent = `${userCfg.chSpeed.toFixed(2).replace(/\.00$/, '.0')} 倍`;
    $('optBoardGap').value = userCfg.boardGap; $('valBoardGap').textContent = `${userCfg.boardGap} m`;
    $('optCamUp').value = userCfg.camUp; $('valCamUp').textContent = `${userCfg.camUp} m`;
    $('rowBoardGap').classList.toggle('hidden', !isBoard);
    $('settingsArea').querySelectorAll('input').forEach(inp => { inp.disabled = isGuest; }); $('presetResetBtn').disabled = isGuest;
    const startBtn = $('startBtn');
    if (isRoom){
      if (net.role === 'host' && net.clients.size > 0){ startBtn.disabled = false; startBtn.textContent = `対戦スタート (${net.roster.length}人)`; }
      else if (net.role === 'guest' && net.connected){ startBtn.disabled = true; startBtn.textContent = 'ホストの開始待ち…'; }
      else { startBtn.disabled = true; startBtn.textContent = '2人以上揃うと開始可能'; }
    } else if (!isFree){ startBtn.disabled = false; startBtn.textContent = 'スタート'; }
  }

  document.querySelectorAll('.mode-btn').forEach(btn => btn.addEventListener('click', () => {
    const m = btn.dataset.mode;
    if (userCfg.mode === 'online' && m !== 'online' && (net.role !== 'none' || net.searching)) disconnectNet(false);
    Object.assign(userCfg, { mode:m, ...PRESETS[m] });
    if (m === 'online'){ userCfg.onlineCourse = 'normal'; userCfg.onlineGame = 'chase'; setNetStatus('', userCfg.onlineType === 'free' ? 'ルールとコースを選んで開始してください' : '合言葉を決めて部屋を作るか入ってください（最大6人）'); }
    setupWorld(false);
  }));
  document.querySelectorAll('[data-rcourse]').forEach(btn => btn.addEventListener('click', () => {
    const c = btn.dataset.rcourse; userCfg.raceCourse = c;
    if (c === 'board'){ userCfg.speed = 55; userCfg.vxMax = 30; userCfg.boardGap = 28; userCfg.camUp = 38; }
    else { userCfg.speed = 24; userCfg.vxMax = 11; userCfg.camUp = 10; }
    setupWorld(false);
  }));
  document.querySelectorAll('[data-match]').forEach(btn => btn.addEventListener('click', () => {
    if (net.searching || net.role !== 'none') return;
    userCfg.onlineType = btn.dataset.match;
    if (userCfg.onlineType === 'free') Object.assign(userCfg, FREE_RULES[`${userCfg.onlineGame}_${userCfg.onlineCourse}`]);
    setNetStatus('', userCfg.onlineType === 'free' ? 'ルールとコースを選んで開始してください' : '合言葉を決めて部屋を作るか入ってください（最大6人）');
    setupWorld(false);
  }));
  document.querySelectorAll('[data-otype]').forEach(btn => btn.addEventListener('click', () => {
    userCfg.onlineGame = btn.dataset.otype;
    if (userCfg.onlineType === 'free') Object.assign(userCfg, FREE_RULES[`${userCfg.onlineGame}_${userCfg.onlineCourse}`]);
    else userCfg.chasers = userCfg.onlineGame === 'race' ? 6 : 0;
    setupWorld(false); sendLobbyPreview();
  }));
  document.querySelectorAll('[data-course]').forEach(btn => btn.addEventListener('click', () => {
    const c = btn.dataset.course; userCfg.onlineCourse = c;
    if (userCfg.onlineType === 'free') Object.assign(userCfg, FREE_RULES[`${userCfg.onlineGame}_${c}`]);
    else if (c === 'board'){ userCfg.speed = 50; userCfg.vxMax = 28; userCfg.boardGap = 28; userCfg.camUp = 38; }
    else { userCfg.speed = userCfg.onlineGame === 'race' ? 24 : 16; userCfg.vxMax = 10; userCfg.camUp = 8; }
    setupWorld(false); sendLobbyPreview();
  }));
  document.querySelectorAll('[data-role]').forEach(btn => btn.addEventListener('click', () => { userCfg.hostRole = btn.dataset.role; syncMenuUI(); sendLobbyPreview(); }));

  $('freeSearchBtn').addEventListener('click', startFreeMatch);
  $('freeStartNowBtn').addEventListener('click', hostLaunchOnlineMatch);
  $('freeCancelBtn').addEventListener('click', () => disconnectNet(false));
  $('randCodeBtn').addEventListener('click', () => { $('roomCode').value = String(Math.floor(1000 + Math.random() * 9000)); });
  $('hostBtn').addEventListener('click', createRoom);
  $('joinBtn').addEventListener('click', joinRoom);
  $('disconnectBtn').addEventListener('click', () => disconnectNet(false));
  $('roomStartBtn').addEventListener('click', () => setupWorld(true));
  $('copyInviteBtn').addEventListener('click', async () => {
    const code = net.room || cleanRoomText($('roomCode').value); if (!code) return;
    const isWeb = location.protocol === 'http:' || location.protocol === 'https:';
    const text = isWeb ? `${location.origin}${location.pathname}?room=${encodeURIComponent(code)}` : code;
    try { await navigator.clipboard.writeText(text); const b = $('copyInviteBtn'), o = b.textContent; b.textContent = 'コピー完了!'; setTimeout(() => b.textContent = o, 1400); }
    catch { prompt('相手に送ってください', text); }
  });

  const onSet = () => { syncMenuUI(); sendLobbyPreview(); };
  $('optGoal').addEventListener('input', e => { userCfg.goalDist = parseInt(e.target.value, 10); onSet(); });
  $('optSpeed').addEventListener('input', e => { userCfg.speed = parseFloat(e.target.value); onSet(); });
  $('optVx').addEventListener('input', e => { userCfg.vxMax = parseFloat(e.target.value); onSet(); });
  $('optChasers').addEventListener('input', e => { userCfg.chasers = parseInt(e.target.value, 10); onSet(); });
  $('optChSpeed').addEventListener('input', e => { userCfg.chSpeed = parseFloat(e.target.value); onSet(); });
  $('optBoardGap').addEventListener('input', e => { userCfg.boardGap = parseInt(e.target.value, 10); onSet(); });
  $('optCamUp').addEventListener('input', e => { userCfg.camUp = parseInt(e.target.value, 10); cfg.camUp = userCfg.camUp; onSet(); });
  $('presetResetBtn').addEventListener('click', () => { const m = userCfg.mode; Object.assign(userCfg, { mode:m, ...PRESETS[m] }); if (m === 'online'){ userCfg.onlineCourse = 'normal'; userCfg.onlineGame = 'chase'; } setupWorld(false); sendLobbyPreview(); });
  $('startBtn').addEventListener('click', () => setupWorld(true));
  $('retryBtn').addEventListener('click', () => setupWorld(true));
  $('swapRetryBtn').addEventListener('click', () => { userCfg.hostRole = userCfg.hostRole === 'cheese' ? 'human' : 'cheese'; sendLobbyPreview(); setupWorld(true); });
  $('nextFreeBtn').addEventListener('click', () => { setupWorld(false); startFreeMatch(); });
  $('toMenuBtn').addEventListener('click', () => { if (cfg.isOnline){ if (cfg.onlineType === 'free') disconnectNet(false); else if (net.role === 'host') broadcastToClients({ t:'menu' }); } setupWorld(false); });
  $('menuBtn').addEventListener('click', () => { if (cfg.isOnline){ if (cfg.onlineType === 'free' || net.role === 'guest') disconnectNet(false); else if (net.role === 'host') broadcastToClients({ t:'menu' }); } setupWorld(false); });
  $('diveBtn').addEventListener('pointerdown', e => { e.preventDefault(); e.stopPropagation(); if (game === 'playing' && !cfg.isRace && cfg.myRole === 'human'){ diveRequested = true; if (cfg.isOnline && net.role === 'guest') sendGuestInput(true); } });

  // ---------- 操作 ----------
  let dragId = null, joyX0 = 0, joyY0 = 0;
  function input(){
    if (dragId !== null) return Math.hypot(stick.x, stick.y) < JOY_DEAD ? { x:0, y:0 } : stick;
    return { x:(keys.right?1:0)-(keys.left?1:0), y:(keys.up?1:0)-(keys.down?1:0) };
  }
  const endJoy = () => { dragId = null; stick.x = stick.y = 0; $('joy').classList.add('hidden'); };
  cv.addEventListener('pointerdown', e => {
    if (game !== 'playing' || dragId !== null) return;
    e.preventDefault(); cv.setPointerCapture(e.pointerId); dragId = e.pointerId;
    const g = $('game').getBoundingClientRect(); joyX0 = e.clientX - g.left; joyY0 = e.clientY - g.top;
    $('joy').style.left = joyX0 + 'px'; $('joy').style.top = joyY0 + 'px'; $('knob').style.transform = ''; $('joy').classList.remove('hidden');
  });
  cv.addEventListener('pointermove', e => {
    if (e.pointerId !== dragId) return;
    const g = $('game').getBoundingClientRect(); let dx = e.clientX - g.left - joyX0, dy = e.clientY - g.top - joyY0, m = Math.hypot(dx, dy);
    if (m > JOY_R){ dx *= JOY_R / m; dy *= JOY_R / m; }
    stick.x = dx / JOY_R; stick.y = -dy / JOY_R; $('knob').style.transform = `translate(${dx}px, ${dy}px)`;
  });
  const endDrag = e => { if (e.pointerId === dragId) endJoy(); };
  cv.addEventListener('pointerup', endDrag); cv.addEventListener('pointercancel', endDrag);

  document.addEventListener('keydown', e => {
    if (document.activeElement && document.activeElement.tagName === 'INPUT') return;
    if (game === 'over' && (e.key === 'Enter' || e.key === ' ')){ if (!cfg.isOnline){ e.preventDefault(); setupWorld(true); } return; }
    if (game === 'menu' && e.key === 'Enter'){ if (userCfg.mode !== 'online' || (userCfg.onlineType === 'room' && !$('startBtn').disabled)){ e.preventDefault(); setupWorld(true); } return; }
    if (game === 'playing' && !cfg.isRace && cfg.myRole === 'human' && (e.key === ' ' || e.code === 'Space')){ e.preventDefault(); diveRequested = true; if (cfg.isOnline && net.role === 'guest') sendGuestInput(true); return; }
    if (e.key === 'ArrowLeft' || e.key === 'a') keys.left = true;
    if (e.key === 'ArrowRight' || e.key === 'd') keys.right = true;
    if (e.key === 'ArrowUp' || e.key === 'w') keys.up = true;
    if (e.key === 'ArrowDown' || e.key === 's') keys.down = true;
  });
  document.addEventListener('keyup', e => {
    if (e.key === 'ArrowLeft' || e.key === 'a') keys.left = false;
    if (e.key === 'ArrowRight' || e.key === 'd') keys.right = false;
    if (e.key === 'ArrowUp' || e.key === 'w') keys.up = false;
    if (e.key === 'ArrowDown' || e.key === 's') keys.down = false;
  });
  addEventListener('blur', () => { keys.left = keys.right = keys.up = keys.down = false; });
  document.addEventListener('touchstart', e => { if (e.target === cv) e.preventDefault(); }, { passive:false });
  document.addEventListener('touchmove', e => { if (dragId !== null) e.preventDefault(); }, { passive:false });
  document.addEventListener('contextmenu', e => { if (e.target === cv) e.preventDefault(); });
  let lastTouchEnd = 0;
  document.addEventListener('touchend', e => {
    if (e.target instanceof Element && e.target.closest('button, input, .card')) return;
    const now = Date.now(); if (now - lastTouchEnd < 350) e.preventDefault(); lastTouchEnd = now;
  }, { passive:false });

  function resize(){
    const w = $('game').clientWidth, h = $('game').clientHeight;
    renderer.setSize(w, h); camera.aspect = w / h;
    const vfov = 2 * Math.atan(Math.tan(CAM_HFOV / 2 * Math.PI / 180) / camera.aspect) * 180 / Math.PI;
    camera.fov = clamp(vfov, 50, cfg.camVfovMax); camera.updateProjectionMatrix();
  }
  addEventListener('resize', resize);

  const inviteRoom = new URLSearchParams(location.search).get('room');
  if (inviteRoom){
    Object.assign(userCfg, { mode:'online', onlineType:'room', onlineGame:'chase', onlineCourse:'normal', ...PRESETS.online });
    $('roomCode').value = inviteRoom;
    setNetStatus('', `招待された合言葉「${inviteRoom}」が入りました。「部屋に入る」を押してください`);
  } else $('roomCode').value = String(Math.floor(1000 + Math.random() * 9000));

  setupWorld(false);
  requestAnimationFrame(loop);
})();
</script>
</body>
</html>
