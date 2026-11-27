<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no, viewport-fit=cover">
<title>転がりチーズ オンライン対戦</title>
<style>
  :root{
    --bg:#a9cdea; --fg:#1d2b1d; --panel:#1f3524; --panel-fg:#ffffff;
    --accent:#ffd23f; --accent-fg:#1f3524; --ok:#69db7c; --danger:#ff6b6b; --muted:rgba(255,255,255,.14);
    --sky:#a9cdea; --grass:#5f8f3e; --grass-dark:#4c7731;
    --cheese-rind:#e3a03a; --cheese-face:#f3cf72; --fence:#7a5a3a;
    --skins:#f1c9a5,#e0ac85,#c68a62,#8d5a3b;
    --shirts:#f2f2f2,#c93a3a,#2f5fbf,#3a8a4a,#262626,#e0b52e,#7a3fae,#e07a2c,#4fa3c7,#b8b8b8;
    --pants:#2f3b55,#3a3a3a,#5a6b7d,#4b3a2a,#22304a,#6b6b52;
    --hair:#2a1d14,#5a3a22,#a87a4a,#161616,#cdb27a;
    --shoes:#eeeeee,#2e2e2e,#7a3b2a,#3a4a6a;
    --rock:#8c8a82; --bark:#6b4a2f; --wood:#c9a574; --straw:#d8b55a;
    --gorse:#34502a; --gorse-light:#6f8f3c; --gorse-flower:#efc72c;
    --board:#d69e56; --board-line:#7a4e24; --trail:#fff4d6;
  }
  *{box-sizing:border-box;-webkit-tap-highlight-color:transparent}
  html,body{margin:0;height:100%;overflow:hidden}
  body{
    background:var(--bg); color:var(--fg);
    font-family:"Hiragino Sans","Hiragino Kaku Gothic ProN","Yu Gothic","Noto Sans JP",system-ui,sans-serif;
    touch-action:manipulation; -webkit-user-select:none; user-select:none; -webkit-touch-callout:none;
  }
  #game{position:fixed;inset:0}
  canvas{display:block;touch-action:none}
  .hidden{display:none !important}
  #joy{position:absolute;left:0;top:0;width:120px;height:120px;margin:-60px 0 0 -60px;border-radius:50%;
    background:rgba(255,255,255,.22);border:2px solid rgba(255,255,255,.7);pointer-events:none}
  #knob{position:absolute;left:50%;top:50%;width:52px;height:52px;margin:-26px 0 0 -26px;border-radius:50%;background:rgba(255,255,255,.9)}
  #hudWrap{position:absolute;left:0;right:0;top:calc(env(safe-area-inset-top,0px) + 12px);
    display:flex;flex-direction:column;align-items:center;pointer-events:none}
  #hud{font-size:30px;font-weight:800;font-variant-numeric:tabular-nums;line-height:1.15;
    color:#162618;text-shadow:0 2px 6px rgba(255,255,255,.78)}
  #hudSub{margin-top:3px;font-size:12px;font-weight:700;font-variant-numeric:tabular-nums;
    background:rgba(31,53,36,.84);color:#ffd23f;padding:3px 12px;border-radius:999px}
  #menuBtn{position:absolute;left:12px;top:calc(env(safe-area-inset-top,0px) + 12px);
    border:none;border-radius:10px;padding:9px 13px;font-size:12px;font-weight:700;cursor:pointer;
    background:rgba(31,53,36,.84);color:#fff;backdrop-filter:blur(4px);z-index:5}
  #menuBtn:active{transform:scale(.96)}
  #diveBtn{position:absolute;right:20px;bottom:calc(env(safe-area-inset-bottom,0px) + 26px);
    width:104px;height:104px;border-radius:50%;border:3px solid rgba(255,255,255,.85);
    background:rgba(255,210,63,.92);color:#1f3524;font-size:16px;font-weight:900;
    box-shadow:0 8px 20px rgba(0,0,0,.3);cursor:pointer;z-index:5;
    display:flex;flex-direction:column;align-items:center;justify-content:center;line-height:1.15;touch-action:none}
  #diveBtn small{font-size:10px;font-weight:700;opacity:.75;margin-top:2px}
  #diveBtn:active{transform:scale(.92);background:#ffe070}
  #diveBtn.disabled{opacity:.45;transform:scale(.95)}
  .overlay{position:fixed;inset:0;display:flex;align-items:center;justify-content:center;padding:12px;
    background:rgba(15,28,18,.5);backdrop-filter:blur(4px);z-index:10}
  .card{width:100%;max-width:450px;max-height:94vh;overflow-y:auto;
    background:var(--panel);color:var(--panel-fg);border-radius:18px;padding:18px 16px;
    box-shadow:0 16px 40px rgba(0,0,0,.42)}
  .card h1{margin:0 0 11px;text-align:center;font-size:22px;letter-spacing:.04em}
  .modes{display:grid;grid-template-columns:1fr 1fr;gap:7px;margin-bottom:10px}
  .mode-btn{border:2px solid rgba(255,255,255,.18);border-radius:11px;padding:8px 6px;cursor:pointer;
    background:rgba(255,255,255,.06);color:#fff;text-align:center;transition:.15s}
  .mode-btn strong{display:block;font-size:13px;margin-bottom:2px}
  .mode-btn span{display:block;font-size:10px;opacity:.8;line-height:1.2}
  .mode-btn.active{border-color:var(--accent);background:rgba(255,210,63,.18);color:var(--accent)}
  .mode-btn.active span{color:#fff;opacity:.92}
  .mode-hint{font-size:11.5px;line-height:1.45;color:rgba(255,255,255,.88);
    background:rgba(255,255,255,.07);border-radius:10px;padding:8px 10px;margin-bottom:10px}
  .best-box{display:flex;justify-content:space-between;align-items:center;
    background:rgba(0,0,0,.25);border-radius:10px;padding:8px 13px;margin-bottom:10px;font-size:13px;font-weight:700}
  .best-box .val{color:var(--accent);font-size:18px;font-variant-numeric:tabular-nums}
  .online-box{background:rgba(0,0,0,.24);border:1px solid rgba(255,255,255,.14);border-radius:12px;
    padding:11px;margin-bottom:11px}
  .sub-tabs{display:grid;grid-template-columns:1fr 1fr;gap:6px;margin-bottom:10px}
  .sub-tab{border:1.5px solid rgba(255,255,255,.18);border-radius:9px;padding:8px 6px;cursor:pointer;
    background:rgba(255,255,255,.05);color:#fff;font-size:12.5px;font-weight:800}
  .sub-tab.active{border-color:var(--accent);background:var(--accent);color:var(--accent-fg)}
  .sub-tab:disabled{opacity:.45;cursor:not-allowed}
  .status-line{display:flex;align-items:center;gap:8px;font-size:12.5px;font-weight:700;
    background:rgba(255,255,255,.06);border-radius:9px;padding:8px 10px;margin-bottom:10px;line-height:1.35}
  .dot{width:10px;height:10px;border-radius:50%;background:#adb5bd;flex-shrink:0}
  .dot.wait{background:var(--accent);animation:pulse 1s infinite}
  .dot.ok{background:var(--ok)}
  .dot.err{background:var(--danger)}
  @keyframes pulse{0%,100%{opacity:1;transform:scale(1)}50%{opacity:.4;transform:scale(.85)}}
  .room-row{display:flex;gap:6px;margin-bottom:8px}
  .room-input{flex:1;min-width:0;border:1px solid rgba(255,255,255,.25);border-radius:9px;
    background:rgba(255,255,255,.1);color:#fff;padding:9px 11px;font-size:15px;font-weight:700;
    -webkit-user-select:text;user-select:text;outline:none}
  .room-input:focus{border-color:var(--accent)}
  .room-input::placeholder{color:rgba(255,255,255,.45);font-weight:400}
  .mini-btn{border:none;border-radius:9px;padding:0 11px;font-size:12px;font-weight:700;cursor:pointer;
    background:var(--muted);color:#fff;white-space:nowrap}
  .mini-btn:active{transform:scale(.96)}
  .toggle-group{margin-top:9px}
  .toggle-label{font-size:11.5px;font-weight:700;opacity:.85;margin-bottom:5px;display:block}
  .toggle-row{display:grid;grid-template-columns:1fr 1fr;gap:6px}
  .seg-btn{border:1.5px solid rgba(255,255,255,.2);border-radius:9px;padding:7px 6px;cursor:pointer;
    background:rgba(255,255,255,.05);color:#fff;font-size:12px;font-weight:700}
  .seg-btn.active{border-color:var(--accent);background:rgba(255,210,63,.2);color:var(--accent)}
  .seg-btn:disabled{opacity:.5;cursor:default}
  .free-note{margin:8px 0 0;font-size:11px;opacity:.76;line-height:1.4}
  .settings{display:flex;flex-direction:column;gap:8px;margin-bottom:14px}
  .setting-row{background:rgba(255,255,255,.06);border-radius:10px;padding:7px 11px}
  .setting-head{display:flex;justify-content:space-between;align-items:baseline;margin-bottom:4px;font-size:12.5px;font-weight:700}
  .setting-val{color:var(--accent);font-variant-numeric:tabular-nums;font-size:12.5px}
  input[type=range]{width:100%;accent-color:var(--accent);cursor:pointer;margin:0}
  .btn-row{display:flex;gap:8px;flex-wrap:wrap}
  .btn{flex:1;min-width:110px;border:none;border-radius:11px;padding:12px 12px;font-size:14px;font-weight:800;cursor:pointer}
  .btn-primary{background:var(--accent);color:var(--accent-fg)}
  .btn-sub{background:var(--muted);color:#fff}
  .btn-danger{background:rgba(255,107,107,.24);color:#ffb3b3}
  .btn:disabled{opacity:.42;cursor:not-allowed}
  .btn:not(:disabled):active{transform:scale(.97)}
  .result-card{text-align:center;max-width:360px}
  .result-mode{font-size:12.5px;opacity:.82;margin:0 0 6px;font-weight:700}
  .result-title{font-size:22px;font-weight:900;margin:0 0 6px;color:var(--accent)}
  .result-title.fail{color:#ff8a80}
  .result-value{font-size:42px;font-weight:900;margin:0 0 6px;font-variant-numeric:tabular-nums;line-height:1.1}
  .result-best{font-size:13.5px;font-weight:700;margin:0 0 16px;color:rgba(255,255,255,.88)}
  .new-best{display:inline-block;background:var(--accent);color:var(--accent-fg);
    font-size:12px;font-weight:900;padding:3px 10px;border-radius:999px;margin-bottom:8px}
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

  <!-- タイトル＆設定メニュー -->
  <div id="menuOverlay" class="overlay">
    <div class="card">
      <h1>転がりチーズ</h1>
      <div class="modes">
        <button type="button" class="mode-btn active" data-mode="normal">
          <strong>通常モード</strong><span>チーズで逃げる</span>
        </button>
        <button type="button" class="mode-btn" data-mode="board">
          <strong>板よけモード</strong><span>左右の板を回避</span>
        </button>
        <button type="button" class="mode-btn" data-mode="human">
          <strong>人間モード</strong><span>AIチーズを捕まえる</span>
        </button>
        <button type="button" class="mode-btn" data-mode="online">
          <strong>オンライン対戦</strong><span>全国フリー / 合言葉</span>
        </button>
      </div>

      <div class="mode-hint" id="modeHint"></div>

      <div class="best-box" id="bestBox">
        <span id="menuBestLabel">通常モード ベスト</span>
        <span class="val" id="menuBestVal">0 m</span>
      </div>

      <!-- オンライン対戦ロビー -->
      <div class="online-box hidden" id="onlineBox">
        <div class="sub-tabs">
          <button type="button" class="sub-tab active" data-match="free">全国フリーマッチ</button>
          <button type="button" class="sub-tab" data-match="room">合言葉マッチ</button>
        </div>

        <div class="status-line">
          <span class="dot" id="netDot"></span>
          <span id="netStatus">コースを選んでマッチングを開始してください</span>
        </div>

        <div class="toggle-group" style="margin-top:0;margin-bottom:9px">
          <span class="toggle-label">対戦コース</span>
          <div class="toggle-row">
            <button type="button" class="seg-btn active" data-course="normal">通常コース</button>
            <button type="button" class="seg-btn" data-course="board">板よけコース</button>
          </div>
        </div>

        <!-- 全国フリーマッチ -->
        <div id="freeMatchUI">
          <div class="btn-row">
            <button id="freeSearchBtn" type="button" class="btn btn-primary">全国フリーマッチ開始</button>
            <button id="freeCancelBtn" type="button" class="btn btn-danger hidden">マッチング中止</button>
          </div>
          <p class="free-note">※同じコースで探している全国のプレイヤーと自動で繋がります。役割はランダム決定（完全1vs1）。</p>
        </div>

        <!-- 合言葉マッチ -->
        <div id="roomMatchUI" class="hidden">
          <div id="connectForm">
            <div class="room-row">
              <input id="roomCode" class="room-input" type="text" maxlength="20" placeholder="合言葉（例: 4821 / あいう）" autocomplete="off">
              <button id="randCodeBtn" type="button" class="mini-btn">ランダム</button>
            </div>
            <div class="btn-row">
              <button id="hostBtn" type="button" class="btn btn-primary">部屋を作る</button>
              <button id="joinBtn" type="button" class="btn btn-sub">部屋に入る</button>
            </div>
          </div>

          <div id="connectedControls" class="hidden">
            <div class="btn-row" style="margin-bottom:8px">
              <button id="roomStartBtn" type="button" class="btn btn-primary hidden">対戦スタート</button>
              <button id="copyInviteBtn" type="button" class="btn btn-sub hidden">招待URLコピー</button>
              <button id="disconnectBtn" type="button" class="btn btn-danger">接続を切る</button>
            </div>
          </div>

          <div class="toggle-group">
            <span class="toggle-label" id="roleToggleLabel">ホストの役割（相手は逆の役割）</span>
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
        <div class="setting-row">
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
      <p class="result-best" id="resBest">BEST 0 m</p>
      <div class="btn-row" id="resBtns">
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

  // PeerJS接続設定（複数のSTUNサーバーを指定して接続成功率を上げる）
  const ROOM_PREFIX = 'kcheese-r2-';
  const FREE_PREFIX = 'kcheese-f2-';
  const FREE_SLOTS = 15;
  const PEER_OPTS = {
    debug: 0,
    config: {
      iceServers: [
        { urls: 'stun:stun.l.google.com:19302' },
        { urls: 'stun:stun1.l.google.com:19302' },
        { urls: 'stun:stun2.l.google.com:19302' },
        { urls: 'stun:stun.cloudflare.com:3478' },
      ],
    },
  };

  // ---------- モード別プリセット ----------
  const PRESETS = {
    normal: {
      label: '通常モード',
      hint: 'チーズを操作して後ろの追っ手から逃げよう。障害物をうまく使って振り切れ！',
      speed: 9.5, vxMax: 4.5, chasers: 6, chSpeed: 1.0, boardGap: 25, camUp: 5,
    },
    board: {
      label: '板よけモード',
      hint: '左右どちらかに等間隔で現れる板を超高速でよけ続けろ！ぶつかると即キャッチ。',
      speed: 80, vxMax: 30, chasers: 100, chSpeed: 1.0, boardGap: 25, camUp: 50,
    },
    human: {
      label: '人間モード（追っ手側）',
      hint: '黄色い矢印のキャラを操作！矢印/WASDで走り、スペースキーか右下ボタンで飛びついてチーズを捕まえよう。',
      speed: 9.5, vxMax: 5.5, chasers: 6, chSpeed: 1.05, boardGap: 25, camUp: 5,
    },
    online: {
      label: 'オンライン対戦',
      hint: '全国フリーマッチと合言葉マッチが選べます。チーズは100m引き離せば勝ち、人間は捕まえれば勝ち！',
      speed: 9.5, vxMax: 5.0, chasers: 0, chSpeed: 1.05, boardGap: 28, camUp: 6,
    },
  };

  const FREE_RULES = {
    normal: { onlineCourse:'normal', speed:9.5, vxMax:5.2, chasers:0, chSpeed:1.06, boardGap:28, camUp:6 },
    board:  { onlineCourse:'board',  speed:45,  vxMax:26,  chasers:0, chSpeed:1.05, boardGap:28, camUp:36 },
  };

  const userCfg = {
    mode: 'normal',
    onlineType: 'free',
    onlineCourse: 'normal',
    hostRole: 'cheese',
    ...PRESETS.normal,
  };

  const cfg = {
    mode: 'normal', course: 'normal', isOnline: false, onlineType: 'free',
    myRole: 'cheese', humanPlayerActive: false,
    halfW: 8, xlim: 7.8, cheeseR: 0.32, cheeseW: 0.2,
    gFall: 9.8, gAlong: 6.556, drag: 0.073, turnCost: 0.1,
    vxMax: 4.5, latAcc: 22, bounce: 0.3, bounceMin: 1.2, airControl: 0.6,
    chStart: 6, chMax: 12, chAddEvery: 100, startDelay: 1.0, startGrace: 2.0,
    chSpeedMult: 1.0, runAcc: 7, chVx: 3.2,
    boardGap: 25, boardW: 20, boardH: 3.2, boardThick: 1.6,
    avoidLook: 6, thigh: 0.43, shin: 0.43,
    camBack: 8, camUp: 5, camFollowX: 0.75, camVfovMax: 65, targetSpeed: 9.5,
  };

  const net = {
    peer: null, conn: null, role: 'none', matchKind: 'room',
    searching: false, connected: false, room: '', searchGen: 0,
    startTimer: null, joinTimer: null, helloTimer: null,
    remoteInput: { x:0, y:0, dive:false }, lastSend: 0, snap: null,
  };

  // ---------- 固定定数 ----------
  const SLOPE = 0.9, CH_ALLOC = 300;
  const TILT_KICK = 0.35, TILT_SPRING = 30, TILT_DAMP = 4, TILT_DRIFT = 3;
  const JOY_R = 50, JOY_DEAD = 0.12, PUSH_ACC = 0.6, BRAKE_ACC = 6;
  const CATCHUP_DIST = 10, CATCHUP = 3.5, SPEED_VAR = 0.3;
  const DIVE_RANGE = 3.0, DIVE_BACK = 0.5, DIVE_DX = 2.2, WINDUP = 0.2;
  const DIVE_T = 0.42, DIVE_REACH = 0.9, DIVE_SPIN = 2.2, AIM = 0.7;
  const CATCH_WINDOW = DIVE_T + 0.35, CATCH_HEIGHT = 0.95;
  const RAG_MIN = 1.0, RAG_MAX = 2.6, RAG_STILL = 2.5, GETUP_T = 0.6, COOLDOWN = 0.8;
  const RESPAWN_BEHIND = 30, SPAWN_BEHIND = 20;
  const ESCAPE_LIMIT_SOLO = 80, ESCAPE_LIMIT_ONLINE = 100;
  const OVER_DELAY = 2.0, SLOW_SCALE = 0.25, ZOOM_SPEED = 3;
  const ZOOM_BACK = 3.0, ZOOM_UP = 1.8, ZOOM_SIDE = 1.3;
  const RAG_ITER = 8, RAG_DAMP = 0.999, RAG_MU = 0.85, RUN_DUTY = 0.28;
  const UPARM = 0.29, FOREARM = 0.27, FOOT_H = 0.07;

  const OB_BAND = 4, OB_START = 25, OB_RATE0 = 0.6, OB_RATE_GROW = 1/500, OB_RATE_MAX = 1, OB_SECOND = 1/600;
  const OB_KIND = [['rock', 0.3], ['bush', 0.25], ['log', 0.25], ['bale', 0.2]];
  const OB_AHEAD = 160, OB_BEHIND = 30;
  const ROCK_VARIANTS = 5, BUSH_VARIANTS = 3, BUSH_SPIKES = 340, BUSH_FLOWERS = 60;
  const OB_BOUNCE = 0.35, OB_LOSS = 0.8, BUSH_DRAG = 6, BUSH_RUN = 0.55;
  const TRIP_MARGIN = 0.25, TRIP_SPIN = 3.5, TRIP_HOP = 1.0, TRIP_COOL = 1.0;
  const SKILL_MIN = 0.3, SKILL_MAX = 0.92;
  const STEP = 1 / 120, SYNC_MS = 33;
  const TRAIL_FADE = 0.6, TRAIL_N = 36, TRAIL_STEP = 0.15, TRAIL_ALPHA = 0.55, TRAIL_TAPER = 0.6;
  const CHUNK_LEN = 25, CHUNK_SEG_S = 50, TERRAIN_HALF = 28, TERRAIN_SEG_X = 76;
  const OUTSIDE_SHADE = 0.55, CHUNK_COUNT = 9, CHUNK_BEHIND = 20, GRASS_TILE = 2, POST_GAP = 2.5;
  const FOG_NEAR = 55, FOG_FAR = 165, SHADOW_SIZE = 14, CAM_LOOK = -1.5, CAM_HFOV = 60;

  // ---------- ベストスコア保存 ----------
  function bestKey(mode){ return `runawayCheese.best.${mode}`; }
  function getBest(mode){
    if (mode === 'online') return null;
    try {
      const raw = localStorage.getItem(bestKey(mode));
      if (raw === null) return null;
      const n = parseInt(raw, 10);
      return Number.isFinite(n) && n > 0 ? n : null;
    } catch { return null; }
  }
  function saveBest(mode, val){
    if (mode === 'online' || val <= 0) return false;
    const prev = getBest(mode);
    const better = mode === 'human' ? (prev === null || val < prev) : (prev === null || val > prev);
    if (better){
      try { localStorage.setItem(bestKey(mode), String(val)); } catch {}
      return true;
    }
    return false;
  }
  function formatBest(mode, val){
    if (val === null) return '---';
    return mode === 'human' ? `最短 ${val} m` : `${val} m`;
  }

  // ---------- 地形の形 ----------
  function bump(x, s){
    return 0.5 * Math.sin(0.31 * s + 1.4 * Math.sin(0.043 * s)) * (0.75 + 0.25 * Math.sin(0.37 * x + 0.11 * s))
         + 0.14 * Math.sin(0.83 * s + 0.6 * x + 2.0)
         + 0.08 * Math.sin(1.37 * s - 0.9 * x + 0.5)
         + 0.12 * Math.sin(0.23 * x - 0.17 * s)
         + 0.03 * Math.sin(2.1 * s + 1.7 * x);
  }
  const ground = (x, s) => -SLOPE * s + bump(x, s);
  const EPS = 0.04;
  const slopeS = (x, s) => (bump(x, s + EPS) - bump(x, s - EPS)) / (2 * EPS) - SLOPE;
  const slopeX = (x, s) => (bump(x + EPS, s) - bump(x - EPS, s)) / (2 * EPS);

  // ---------- 状態 ----------
  let game = 'menu', outcome = 'caught', S0 = 0, overT = 0, playTime = 0;
  const stick = { x:0, y:0 };
  let activeN = 0, shownHudKey = '', currentBest = null, maxDist = 0, diveRequested = false;
  const cheese = { s:0, x:0, y:0, v:0, vx:0, vy:0, rot:0, tilt:0, tiltV:0, grounded:true, held:null, hand:0 };
  const keys = { left:false, right:false, up:false, down:false };

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
  sun.castShadow = true;
  sun.shadow.mapSize.set(2048, 2048);
  Object.assign(sun.shadow.camera, { left:-SHADOW_SIZE, right:SHADOW_SIZE, top:SHADOW_SIZE, bottom:-SHADOW_SIZE, near:1, far:60 });
  sun.shadow.bias = -0.0004;
  sun.shadow.normalBias = 0.03;
  scene.add(sun, sun.target);

  const playerMarker = new THREE.Mesh(
    new THREE.ConeGeometry(0.24, 0.45, 4).rotateX(Math.PI),
    new THREE.MeshBasicMaterial({ color:0xffd23f })
  );
  playerMarker.visible = false;
  scene.add(playerMarker);

  const UP = new THREE.Vector3(0, 1, 0);
  const rel = (out, s, x, y) => out.set(x, y + SLOPE * S0, -(s - S0));

  // ---------- テクスチャ ----------
  function rand(seed){ let t = seed >>> 0; return () => { t = (t + 0x6D2B79F5) >>> 0; let r = Math.imul(t ^ (t >>> 15), 1 | t); r ^= r + Math.imul(r ^ (r >>> 7), 61 | r); return ((r ^ (r >>> 14)) >>> 0) / 4294967296; }; }
  function tileNoise(S, cells, rnd){
    const g = new Float32Array(cells * cells).map(() => rnd());
    const out = new Float32Array(S * S);
    for (let y = 0; y < S; y++) for (let x = 0; x < S; x++){
      const fx = x / S * cells, fy = y / S * cells;
      const x0 = Math.floor(fx), y0 = Math.floor(fy), tx = smooth(fx - x0), ty = smooth(fy - y0);
      const a = g[(y0 % cells) * cells + x0 % cells], b = g[(y0 % cells) * cells + (x0 + 1) % cells];
      const c = g[((y0 + 1) % cells) * cells + x0 % cells], d = g[((y0 + 1) % cells) * cells + (x0 + 1) % cells];
      out[y * S + x] = lerp(lerp(a, b, tx), lerp(c, d, tx), ty);
    }
    return out;
  }
  function canvasTex(S, H, fn){
    const c = document.createElement('canvas'); c.width = S; c.height = H;
    const g = c.getContext('2d'), img = g.createImageData(S, H);
    fn(img.data);
    g.putImageData(img, 0, 0);
    const t = new THREE.CanvasTexture(c);
    t.encoding = THREE.sRGBEncoding;
    return t;
  }
  function grassTexture(){
    const S = 256, rnd = rand(7);
    const base = new THREE.Color(css('--grass')), dark = new THREE.Color(css('--grass-dark'));
    const n1 = tileNoise(S, 4, rnd), n2 = tileNoise(S, 16, rnd);
    const lum = new Float32Array(S * S);
    for (let i = 0; i < S * S; i++) lum[i] = 0.55 * n1[i] + 0.45 * n2[i] + (rnd() - 0.5) * 0.25;
    for (let k = 0; k < 9000; k++){
      const x0 = Math.floor(rnd() * S), y0 = Math.floor(rnd() * S), len = 3 + Math.floor(rnd() * 9);
      const slant = (rnd() - 0.5) * 0.6, sh = (rnd() - 0.45) * 0.7;
      for (let t = 0; t < len; t++){
        const x = (x0 + Math.round(t * slant) + S) % S, y = (y0 - t + S) % S;
        lum[y * S + x] += sh * (1 - t / len);
      }
    }
    const t = canvasTex(S, S, d => {
      for (let i = 0; i < S * S; i++){
        const v = clamp(lum[i], 0, 1);
        d[i * 4]     = Math.round(255 * lerp(dark.r, base.r * 1.15, v));
        d[i * 4 + 1] = Math.round(255 * lerp(dark.g, base.g * 1.12, v));
        d[i * 4 + 2] = Math.round(255 * lerp(dark.b, base.b * 1.05, v));
        d[i * 4 + 3] = 255;
      }
    });
    t.wrapS = t.wrapT = THREE.RepeatWrapping;
    t.anisotropy = renderer.capabilities.getMaxAnisotropy();
    return t;
  }
  function rindTexture(){
    const S = 256, H = 64, rnd = rand(11), c = new THREE.Color(css('--cheese-rind'));
    const n = tileNoise(S, 32, rnd);
    return canvasTex(S, H, d => {
      for (let y = 0; y < H; y++) for (let x = 0; x < S; x++){
        const k = 0.82 + 0.25 * n[(y * 4 % S) * S + x] + (rnd() - 0.5) * 0.08;
        const i = (y * S + x) * 4;
        d[i] = Math.round(255 * clamp(c.r * k, 0, 1)); d[i + 1] = Math.round(255 * clamp(c.g * k, 0, 1));
        d[i + 2] = Math.round(255 * clamp(c.b * k, 0, 1)); d[i + 3] = 255;
      }
    });
  }
  function faceTexture(){
    const S = 128, rnd = rand(13), face = new THREE.Color(css('--cheese-face')), rind = new THREE.Color(css('--cheese-rind'));
    const n = tileNoise(S, 16, rnd);
    return canvasTex(S, S, d => {
      for (let y = 0; y < S; y++) for (let x = 0; x < S; x++){
        const dx = (x + 0.5) / S * 2 - 1, dy = (y + 0.5) / S * 2 - 1, r = Math.sqrt(dx * dx + dy * dy);
        const base = r > 0.9 ? rind : face;
        const k = 0.9 + 0.15 * n[y * S + x] + (rnd() - 0.5) * 0.05;
        const i = (y * S + x) * 4;
        d[i] = Math.round(255 * clamp(base.r * k, 0, 1)); d[i + 1] = Math.round(255 * clamp(base.g * k, 0, 1));
        d[i + 2] = Math.round(255 * clamp(base.b * k, 0, 1)); d[i + 3] = 255;
      }
    });
  }
  function boardTexture(){
    const S = 256, H = 128, rnd = rand(31);
    const c = new THREE.Color(css('--board')), line = new THREE.Color(css('--board-line'));
    const n = tileNoise(S, 16, rnd);
    return canvasTex(S, H, d => {
      for (let y = 0; y < H; y++) for (let x = 0; x < S; x++){
        const border = x < 6 || x >= S - 6 || y < 6 || y >= H - 6 || (y % 32 < 3);
        const base = border ? line : c;
        const grain = 0.88 + 0.18 * n[(y * 2 % S) * S + x] + (rnd() - 0.5) * 0.05;
        const i = (y * S + x) * 4;
        d[i] = Math.round(255 * clamp(base.r * grain, 0, 1));
        d[i + 1] = Math.round(255 * clamp(base.g * grain, 0, 1));
        d[i + 2] = Math.round(255 * clamp(base.b * grain, 0, 1));
        d[i + 3] = 255;
      }
    });
  }

  // ---------- 地形 ----------
  const NX = TERRAIN_SEG_X, NS = CHUNK_SEG_S, VN = (NX + 1) * (NS + 1);
  const terrainIdx = [];
  for (let j = 0; j < NS; j++) for (let i = 0; i < NX; i++){
    const a = j * (NX + 1) + i, b = a + NX + 1, c = a + 1, d = b + 1;
    terrainIdx.push(a, c, b, c, d, b);
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
    mesh.frustumCulled = false;
    mesh.receiveShadow = true;
    scene.add(mesh);
    chunks.push({ mesh, k:null });
  }
  function buildChunk(ch, k){
    ch.k = k;
    const s0 = k * CHUNK_LEN;
    const uv0 = (((s0 % 100) + 100) % 100) / GRASS_TILE;
    const geo = ch.mesh.geometry;
    const p = geo.attributes.position.array, n = geo.attributes.normal.array;
    const col = geo.attributes.color.array, uv = geo.attributes.uv.array;
    let q = 0, u = 0;
    for (let j = 0; j <= NS; j++){
      const s = s0 + j * CHUNK_LEN / NS;
      for (let i = 0; i <= NX; i++){
        const x = -TERRAIN_HALF + i * 2 * TERRAIN_HALF / NX;
        p[q] = x; p[q + 1] = bump(x, s) - SLOPE * (s - s0); p[q + 2] = -(s - s0);
        const gx = slopeX(x, s), gs = slopeS(x, s), L = Math.hypot(gx, 1, gs);
        n[q] = -gx / L; n[q + 1] = 1 / L; n[q + 2] = gs / L;
        const out = Math.abs(x) > cfg.halfW ? OUTSIDE_SHADE : 1;
        const shade = out * (0.9 + 0.1 * Math.sin(x * 0.7 + s * 0.31) * Math.sin(s * 0.13 - x * 0.4));
        col[q] = col[q + 1] = col[q + 2] = shade;
        uv[u] = x / GRASS_TILE; uv[u + 1] = uv0 + (s - s0) / GRASS_TILE;
        q += 3; u += 2;
      }
    }
    for (const a of ['position', 'normal', 'color', 'uv']) geo.attributes[a].needsUpdate = true;
  }
  function updateChunks(){
    const k0 = Math.floor((S0 - CHUNK_BEHIND) / CHUNK_LEN);
    for (const ch of chunks) if (ch.k === null || ch.k < k0 || ch.k >= k0 + CHUNK_COUNT) ch.k = null;
    for (let k = k0; k < k0 + CHUNK_COUNT; k++){
      if (!chunks.some(ch => ch.k === k)) buildChunk(chunks.find(ch => ch.k === null), k);
    }
    for (const ch of chunks){
      const s0 = ch.k * CHUNK_LEN;
      ch.mesh.position.set(0, -SLOPE * (s0 - S0), -(s0 - S0));
    }
  }

  // ---------- 柵 ----------
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
        const x = side * (cfg.halfW + 0.1);
        const y1 = ground(x, s1), y2 = ground(x, s2);
        rel(dummy.position, s1, x, y1 + 0.5);
        dummy.rotation.set(0, 0, 0); dummy.scale.set(1, 1, 1); dummy.updateMatrix();
        posts.setMatrixAt(pi++, dummy.matrix);
        for (const h of [0.45, 0.9]){
          rel(dummy.position, (s1 + s2) / 2, x, (y1 + y2) / 2 + h);
          dummy.rotation.set(Math.atan2(y2 - y1, s2 - s1), 0, 0);
          dummy.scale.set(1, 1, Math.hypot(POST_GAP, y2 - y1));
          dummy.updateMatrix();
          rails.setMatrixAt(ri++, dummy.matrix);
        }
      }
    }
    posts.instanceMatrix.needsUpdate = true;
    rails.instanceMatrix.needsUpdate = true;
  }

  // ---------- チーズ ----------
  const cheeseGroup = new THREE.Group();
  cheeseGroup.rotation.order = 'YXZ';
  const cheeseSpin = new THREE.Group();
  cheeseGroup.add(cheeseSpin);
  const cheeseMesh = new THREE.Mesh(
    new THREE.CylinderGeometry(1, 1, 1, 48).rotateZ(Math.PI / 2),
    [
      new THREE.MeshStandardMaterial({ map:rindTexture(), roughness:0.75 }),
      new THREE.MeshStandardMaterial({ map:faceTexture(), roughness:0.8 }),
      new THREE.MeshStandardMaterial({ map:faceTexture(), roughness:0.8 }),
    ]
  );
  cheeseMesh.castShadow = true;
  cheeseSpin.add(cheeseMesh);
  scene.add(cheeseGroup);

  // ---------- チーズの軌跡 ----------
  const trail = [];
  let trailK = 0;
  const trailGeo = new THREE.BufferGeometry();
  trailGeo.setAttribute('position', new THREE.BufferAttribute(new Float32Array(TRAIL_N * 2 * 3), 3));
  trailGeo.setAttribute('alpha', new THREE.BufferAttribute(new Float32Array(TRAIL_N * 2), 1));
  {
    const idx = [];
    for (let i = 0; i < TRAIL_N - 1; i++){ const a = i * 2; idx.push(a, a + 1, a + 2, a + 1, a + 3, a + 2); }
    trailGeo.setIndex(idx);
  }
  const trailMesh = new THREE.Mesh(trailGeo, new THREE.ShaderMaterial({
    uniforms:{ color:{ value:new THREE.Color(css('--trail')) } },
    vertexShader:'attribute float alpha; varying float vA; void main(){ vA = alpha; gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0); }',
    fragmentShader:'uniform vec3 color; varying float vA; void main(){ gl_FragColor = vec4(color, vA); }',
    transparent:true, depthWrite:false, side:THREE.DoubleSide,
  }));
  trailMesh.frustumCulled = false;
  scene.add(trailMesh);
  function recordTrail(){
    if (cheese.held) return;
    const t = trail[0];
    if (t && Math.hypot(cheese.s - t.s, cheese.x - t.x, cheese.y - t.y) < TRAIL_STEP) return;
    trail.unshift({ s:cheese.s, x:cheese.x, y:cheese.y });
    if (trail.length > TRAIL_N) trail.pop();
  }
  const _tp = Array.from({ length:TRAIL_N }, () => new THREE.Vector3());
  const _tt = new THREE.Vector3(), _ts = new THREE.Vector3();
  function drawTrail(dt){
    const startSp = Math.max(8, cfg.targetSpeed * 0.92);
    const want = cheese.held ? 0 : clamp((cheese.v - startSp) / TRAIL_FADE, 0, 1);
    trailK += (want - trailK) * Math.min(1, dt * 6);
    const n = trail.length;
    trailMesh.visible = trailK > 0.01 && n > 1;
    if (!trailMesh.visible) return;
    rel(_tp[0], cheese.s, cheese.x, cheese.y);
    for (let i = 1; i < TRAIL_N; i++){ const p = trail[Math.min(i, n - 1)]; rel(_tp[i], p.s, p.x, p.y); }
    const pos = trailGeo.attributes.position.array, al = trailGeo.attributes.alpha.array;
    for (let i = 0; i < TRAIL_N; i++){
      _tt.subVectors(_tp[Math.max(i - 1, 0)], _tp[Math.min(i + 1, TRAIL_N - 1)]);
      _ts.crossVectors(_tt, UP);
      const u = i / (TRAIL_N - 1);
      if (_ts.lengthSq() > 1e-10) _ts.normalize().multiplyScalar(cfg.cheeseW / 2 * (1 - TRAIL_TAPER * u));
      else _ts.set(0, 0, 0);
      pos[i * 6] = _tp[i].x + _ts.x; pos[i * 6 + 1] = _tp[i].y + _ts.y; pos[i * 6 + 2] = _tp[i].z + _ts.z;
      pos[i * 6 + 3] = _tp[i].x - _ts.x; pos[i * 6 + 4] = _tp[i].y - _ts.y; pos[i * 6 + 5] = _tp[i].z - _ts.z;
      al[i * 2] = al[i * 2 + 1] = i < n ? trailK * TRAIL_ALPHA * Math.pow(1 - u, 1.5) : 0;
    }
    trailGeo.attributes.position.needsUpdate = true;
    trailGeo.attributes.alpha.needsUpdate = true;
  }

  // ---------- 障害物 ----------
  const obstacles = [];
  let obSeed = 1, obNextBand = 0;
  const h01 = n => { let t = Math.imul(n ^ 0x9e3779b9, 0x85ebca6b); t ^= t >>> 13; t = Math.imul(t, 0xc2b2ae35); t ^= t >>> 16; return (t >>> 0) / 4294967296; };

  function rockTexture(){
    const S = 128, rnd = rand(21), c = new THREE.Color(css('--rock'));
    const n1 = tileNoise(S, 8, rnd), n2 = tileNoise(S, 32, rnd);
    return canvasTex(S, S, d => {
      for (let i = 0; i < S * S; i++){
        const k = 0.7 + 0.35 * n1[i] + 0.2 * n2[i] + (rnd() - 0.5) * 0.12;
        d[i * 4] = Math.round(255 * clamp(c.r * k, 0, 1)); d[i * 4 + 1] = Math.round(255 * clamp(c.g * k, 0, 1));
        d[i * 4 + 2] = Math.round(255 * clamp(c.b * k * 0.97, 0, 1)); d[i * 4 + 3] = 255;
      }
    });
  }
  function barkTexture(){
    const S = 128, rnd = rand(23), c = new THREE.Color(css('--bark'));
    const stripe = new Float32Array(S).map(() => rnd());
    const n = tileNoise(S, 16, rnd);
    return canvasTex(S, S, d => {
      for (let y = 0; y < S; y++) for (let x = 0; x < S; x++){
        const k = 0.6 + 0.45 * stripe[(x + Math.floor(n[y * S + x] * 6)) % S] * (0.7 + 0.3 * n[y * S + x]);
        const i = (y * S + x) * 4;
        d[i] = Math.round(255 * clamp(c.r * k, 0, 1)); d[i + 1] = Math.round(255 * clamp(c.g * k, 0, 1));
        d[i + 2] = Math.round(255 * clamp(c.b * k, 0, 1)); d[i + 3] = 255;
      }
    });
  }
  function ringTexture(){
    const S = 128, rnd = rand(25), wood = new THREE.Color(css('--wood')), bark = new THREE.Color(css('--bark'));
    return canvasTex(S, S, d => {
      for (let y = 0; y < S; y++) for (let x = 0; x < S; x++){
        const dx = (x + 0.5) / S * 2 - 1, dy = (y + 0.5) / S * 2 - 1, r = Math.sqrt(dx * dx + dy * dy);
        const base = r > 0.88 ? bark : wood;
        const k = (r > 0.88 ? 1 : 0.85 + 0.15 * Math.cos(r * 60)) + (rnd() - 0.5) * 0.06;
        const i = (y * S + x) * 4;
        d[i] = Math.round(255 * clamp(base.r * k, 0, 1)); d[i + 1] = Math.round(255 * clamp(base.g * k, 0, 1));
        d[i + 2] = Math.round(255 * clamp(base.b * k, 0, 1)); d[i + 3] = 255;
      }
    });
  }
  function strawTexture(top){
    const S = 128, rnd = rand(top ? 29 : 27), c = new THREE.Color(css('--straw'));
    const lum = new Float32Array(S * S).map(() => 0.75 + (rnd() - 0.5) * 0.2);
    for (let k = 0; k < 2500; k++){
      const x0 = Math.floor(rnd() * S), y0 = Math.floor(rnd() * S), len = 6 + Math.floor(rnd() * 14), sh = (rnd() - 0.5) * 0.5;
      for (let t = 0; t < len; t++) lum[y0 * S + (x0 + t) % S] += sh;
    }
    return canvasTex(S, S, d => {
      for (let y = 0; y < S; y++) for (let x = 0; x < S; x++){
        let k = lum[y * S + x];
        if (top){
          const dx = (x + 0.5) / S * 2 - 1, dy = (y + 0.5) / S * 2 - 1;
          const r = Math.sqrt(dx * dx + dy * dy), a = Math.atan2(dy, dx);
          k *= 0.85 + 0.15 * Math.sin(r * 40 - a);
        }
        const i = (y * S + x) * 4;
        d[i] = Math.round(255 * clamp(c.r * k, 0, 1)); d[i + 1] = Math.round(255 * clamp(c.g * k, 0, 1));
        d[i + 2] = Math.round(255 * clamp(c.b * k, 0, 1)); d[i + 3] = 255;
      }
    });
  }
  const rockMat = new THREE.MeshStandardMaterial({ map:rockTexture(), roughness:0.95, flatShading:true });
  const barkMat = new THREE.MeshStandardMaterial({ map:barkTexture(), roughness:0.95 });
  const ringMat = new THREE.MeshStandardMaterial({ map:ringTexture(), roughness:0.9 });
  const strawSideMat = new THREE.MeshStandardMaterial({ map:strawTexture(false), roughness:1 });
  const strawTopMat = new THREE.MeshStandardMaterial({ map:strawTexture(true), roughness:1 });
  const bushMat = new THREE.MeshStandardMaterial({ vertexColors:true, roughness:1, flatShading:true });
  const boardMat = new THREE.MeshStandardMaterial({ map:boardTexture(), roughness:0.85 });

  const rockGeos = [];
  for (let v = 0; v < ROCK_VARIANTS; v++){
    const rnd = rand(101 + v), g = new THREE.IcosahedronGeometry(1, 2), pos = g.attributes.position;
    const ph = Array.from({ length:9 }, () => rnd() * TAU);
    for (let i = 0; i < pos.count; i++){
      const x = pos.getX(i), y = pos.getY(i), z = pos.getZ(i);
      const k = 1 + 0.16 * Math.sin(x * 2.1 + ph[0]) * Math.sin(y * 1.7 + ph[1]) + 0.12 * Math.sin(z * 2.6 + ph[2] + x)
              + 0.07 * Math.sin(x * 5.3 + y * 4.1 + ph[3]) + 0.05 * Math.sin(z * 6.1 - y * 3.7 + ph[4]);
      pos.setXYZ(i, x * k, y * k, z * k);
    }
    g.computeVertexNormals();
    rockGeos.push(g);
  }
  const logGeo = new THREE.CylinderGeometry(1, 1, 1, 16).rotateZ(Math.PI / 2);
  const baleGeo = new THREE.CylinderGeometry(1, 1, 1, 28).translate(0, 0.5, 0);
  const boardGeo = new THREE.BoxGeometry(1, 1, 1).translate(0, 0.5, 0);

  function mergeParts(parts){
    let n = 0;
    for (const p of parts) n += p.g.attributes.position.count;
    const pos = new Float32Array(n * 3), nor = new Float32Array(n * 3), col = new Float32Array(n * 3);
    let o = 0;
    for (const { g, c } of parts){
      const gp = g.attributes.position, gn = g.attributes.normal;
      for (let i = 0; i < gp.count; i++){
        const k = (o + i) * 3, x = gp.getX(i), y = gp.getY(i), z = gp.getZ(i);
        pos[k] = x; pos[k + 1] = y; pos[k + 2] = z;
        nor[k] = gn.getX(i); nor[k + 1] = gn.getY(i); nor[k + 2] = gn.getZ(i);
        const cc = typeof c === 'function' ? c(x, y, z) : c;
        col[k] = cc.r; col[k + 1] = cc.g; col[k + 2] = cc.b;
      }
      o += gp.count;
    }
    const g = new THREE.BufferGeometry();
    g.setAttribute('position', new THREE.BufferAttribute(pos, 3));
    g.setAttribute('normal', new THREE.BufferAttribute(nor, 3));
    g.setAttribute('color', new THREE.BufferAttribute(col, 3));
    return g;
  }
  function bushGeometry(seed){
    const rnd = rand(seed);
    const leaf = lin(css('--gorse')), leafLight = lin(css('--gorse-light')), flower = lin(css('--gorse-flower'));
    const ph = Array.from({ length:4 }, () => rnd() * TAU);
    const rad = (x, z) => 1 + 0.16 * Math.sin(Math.atan2(z, x) * 3 + ph[0]) + 0.08 * Math.sin(Math.atan2(z, x) * 7 + ph[1]);
    const SQUASH = 0.6;
    const surf = (dx, dy, dz) => { const r = rad(dx, dz); return new THREE.Vector3(dx * r, dy * r * SQUASH, dz * r); };
    const parts = [];
    const mound = new THREE.SphereGeometry(1, 16, 8, 0, TAU, 0, Math.PI / 2).toNonIndexed();
    const mp = mound.attributes.position;
    for (let i = 0; i < mp.count; i++){
      const v = surf(mp.getX(i), mp.getY(i), mp.getZ(i));
      mp.setXYZ(i, v.x * 0.8, v.y * 0.8, v.z * 0.8);
    }
    mound.computeVertexNormals();
    parts.push({ g:mound, c:(x, y) => leaf.clone().multiplyScalar(0.45 + 0.5 * y / SQUASH) });
    const cone = new THREE.ConeGeometry(1, 1, 4).translate(0, 0.5, 0).toNonIndexed();
    const m = new THREE.Matrix4(), q = new THREE.Quaternion(), sc = new THREE.Vector3(), dir = new THREE.Vector3();
    const randDir = () => {
      const a = rnd() * TAU, y = Math.pow(rnd(), 0.7) * 0.95 + 0.05, h = Math.sqrt(1 - y * y);
      return [Math.cos(a) * h, y, Math.sin(a) * h];
    };
    for (let i = 0; i < BUSH_SPIKES; i++){
      const [dx, dy, dz] = randDir();
      const p = surf(dx, dy, dz).multiplyScalar(0.85);
      dir.set(dx + (rnd() - 0.5) * 0.6, dy * 1.3 + 0.2 + (rnd() - 0.5) * 0.4, dz + (rnd() - 0.5) * 0.6).normalize();
      q.setFromUnitVectors(UP, dir);
      const len = 0.18 + rnd() * 0.22;
      sc.set(0.025 + rnd() * 0.015, len, 0.025 + rnd() * 0.015);
      m.compose(p, q, sc);
      parts.push({ g:cone.clone().applyMatrix4(m), c:leaf.clone().lerp(leafLight, rnd()).multiplyScalar(0.6 + 0.5 * dy) });
    }
    const bud = new THREE.IcosahedronGeometry(1, 0);
    for (let i = 0; i < BUSH_FLOWERS; i++){
      const [dx, dy, dz] = randDir();
      const p = surf(dx, dy, dz).multiplyScalar(0.98 + rnd() * 0.12);
      const r = 0.022 + rnd() * 0.015;
      m.compose(p, q.identity(), sc.set(r, r, r));
      parts.push({ g:bud.clone().applyMatrix4(m), c:flower.clone().multiplyScalar(0.85 + rnd() * 0.25) });
    }
    return mergeParts(parts);
  }
  const bushGeos = Array.from({ length:BUSH_VARIANTS }, (_, v) => bushGeometry(201 + v));

  const pools = { rock:[], bush:[], log:[], bale:[], board:[] };
  function makeMesh(kind){
    let m;
    if (kind === 'rock') m = new THREE.Mesh(rockGeos[0], rockMat);
    if (kind === 'log') m = new THREE.Mesh(logGeo, [barkMat, ringMat, ringMat]);
    if (kind === 'bale') m = new THREE.Mesh(baleGeo, [strawSideMat, strawTopMat, strawTopMat]);
    if (kind === 'bush') m = new THREE.Mesh(bushGeos[0], bushMat);
    if (kind === 'board') m = new THREE.Mesh(boardGeo, boardMat);
    m.castShadow = m.receiveShadow = true;
    scene.add(m);
    return m;
  }
  function takeMesh(o){
    const m = pools[o.kind].pop() || makeMesh(o.kind);
    m.visible = true;
    m.rotation.set(0, o.yaw || 0, 0);
    if (o.kind === 'rock'){ m.geometry = rockGeos[o.shape]; m.scale.set(o.r, o.r * 0.8, o.r); }
    if (o.kind === 'bush'){ m.geometry = bushGeos[o.shape]; m.scale.set(o.r, o.h, o.r); }
    if (o.kind === 'log') m.scale.set(o.len, o.r, o.r);
    if (o.kind === 'bale') m.scale.set(o.r, o.h, o.r);
    if (o.kind === 'board') m.scale.set(o.len, o.h + 1.0, o.thick);
    return m;
  }
  function releaseMesh(o){ o.mesh.visible = false; pools[o.kind].push(o.mesh); }

  function genBand(b){
    if (cfg.course === 'board'){
      const s0 = b * cfg.boardGap;
      if (s0 < Math.max(25, cfg.boardGap)) return;
      const rnd = rand(obSeed * 100003 + b * 7919);
      const side = rnd() < 0.5 ? -1 : 1;
      const o = { kind:'board', s:s0, x:side * (cfg.halfW - cfg.boardW / 2), len:cfg.boardW, h:cfg.boardH, thick:cfg.boardThick, id:b, yaw:0 };
      o.mesh = takeMesh(o);
      obstacles.push(o);
      return;
    }
    const s0 = b * OB_BAND;
    if (s0 < OB_START) return;
    const rnd = rand(obSeed * 100003 + b * 7919);
    const rate = Math.min(OB_RATE_MAX, OB_RATE0 + s0 * OB_RATE_GROW);
    let n = rnd() < rate ? 1 : 0;
    if (n && rnd() < s0 * OB_SECOND) n = 2;
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
      o.mesh = takeMesh(o);
      obstacles.push(o);
    }
  }
  function updateObstacles(){
    const bandLen = cfg.course === 'board' ? cfg.boardGap : OB_BAND;
    const aheadRef = Math.max(S0, cheese.s), behindRef = Math.min(S0, cheese.s);
    while (obNextBand * bandLen < aheadRef + OB_AHEAD) genBand(obNextBand++);
    for (let i = obstacles.length - 1; i >= 0; i--){
      if (obstacles[i].s < behindRef - OB_BEHIND){ releaseMesh(obstacles[i]); obstacles.splice(i, 1); }
    }
  }
  function clearObstacles(fixedSeed){
    for (const o of obstacles) releaseMesh(o);
    obstacles.length = 0; obNextBand = 0;
    obSeed = fixedSeed !== undefined ? fixedSeed : Math.floor(Math.random() * 1e6);
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
        rel(m.position, o.s, o.x, (g1 + g2) / 2 + o.r * 0.85);
        m.rotation.set(0, 0, Math.atan2(g2 - g1, o.len));
      }
    }
  }

  const _n = [0, 0, 0];
  function obstacleHit(o, x, y, s, pr){
    if (o.kind === 'board'){
      const minX = o.x - o.len / 2, maxX = o.x + o.len / 2;
      const minS = o.s - o.thick / 2, maxS = o.s + o.thick / 2;
      const gy = ground(clamp(x, minX, maxX), o.s);
      const cx = clamp(x, minX, maxX), cy = clamp(y, gy - 1.5, gy + o.h), cs = clamp(s, minS, maxS);
      const dx = x - cx, dy = y - cy, ds = s - cs, d = Math.hypot(dx, dy, ds);
      if (d >= pr) return 0;
      if (d < 1e-6){ _n[0] = 0; _n[1] = 0; _n[2] = -1; return (s - minS) + pr; }
      _n[0] = dx / d; _n[1] = dy / d; _n[2] = ds / d;
      return pr - d;
    }
    if (o.kind === 'rock' || o.kind === 'log'){
      let cx = o.x, cy, R;
      if (o.kind === 'rock'){ cy = ground(o.x, o.s) + o.r * 0.25; R = o.r * 0.9 + pr; }
      else {
        const x1 = o.x - o.len / 2, t = clamp((x - x1) / o.len, 0, 1);
        cx = x1 + t * o.len; cy = lerp(ground(x1, o.s), ground(x1 + o.len, o.s), t) + o.r * 0.85; R = o.r + pr;
      }
      const dx = x - cx, dy = y - cy, ds = s - o.s, d = Math.hypot(dx, dy, ds);
      if (d >= R || d < 1e-6) return 0;
      _n[0] = dx / d; _n[1] = dy / d; _n[2] = ds / d;
      return R - d;
    }
    if (o.kind === 'bale'){
      const gy = ground(o.x, o.s), top = gy + o.h;
      const dx = x - o.x, ds = s - o.s, d = Math.hypot(dx, ds) || 1e-6;
      const side = o.r + pr - d, up = top + pr - y;
      if (side <= 0 || up <= 0 || y < gy - pr) return 0;
      if (up < side){ _n[0] = 0; _n[1] = 1; _n[2] = 0; return up; }
      _n[0] = dx / d; _n[1] = 0; _n[2] = ds / d;
      return side;
    }
    return 0;
  }
  const hard = o => o.kind !== 'bush';
  const halfWidth = o => (o.kind === 'log' || o.kind === 'board') ? o.len / 2 : o.r;
  function obstaclesNear(x, s, range){
    const out = [];
    for (const o of obstacles){
      const sRad = o.kind === 'board' ? o.thick / 2 : 0;
      if (hard(o) && Math.abs(o.s - s) < sRad + range && Math.abs(o.x - x) < halfWidth(o) + range) out.push(o);
    }
    return out;
  }
  function inBush(x, s){
    if (cfg.course === 'board') return false;
    for (const o of obstacles) if (o.kind === 'bush' && Math.hypot(x - o.x, s - o.s) < o.r * 0.9) return true;
    return false;
  }
  function tripTest(o, x, s){
    if (o.kind === 'board') return Math.abs(s - o.s) < o.thick / 2 + TRIP_MARGIN && Math.abs(x - o.x) < o.len / 2 + 0.15;
    if (o.kind === 'rock') return Math.hypot(x - o.x, s - o.s) < o.r * 0.9 + TRIP_MARGIN;
    if (o.kind === 'bale') return Math.hypot(x - o.x, s - o.s) < o.r + TRIP_MARGIN;
    if (o.kind === 'log') return Math.abs(s - o.s) < o.r + TRIP_MARGIN && Math.abs(x - o.x) < o.len / 2 + 0.15;
    return false;
  }
  function avoidX(c, want){
    for (const o of obstacles){
      const ahead = o.s - c.s;
      if (ahead < 0.2 || ahead > cfg.avoidLook) continue;
      const half = halfWidth(o) + 0.6;
      if (Math.abs(c.x - o.x) > half + 0.4 && Math.abs(want - o.x) > half) continue;
      if (h01(c.idx * 7349 + o.id * 31 + obSeed) > c.skill) continue;
      let goLeft = want < o.x;
      if (goLeft && o.x - half < -cfg.halfW + 0.5) goLeft = false;
      if (!goLeft && o.x + half > cfg.halfW - 0.5) goLeft = true;
      return goLeft ? Math.min(want, o.x - half) : Math.max(want, o.x + half);
    }
    return want;
  }
  function cheeseObstacles(c){
    for (const o of obstacles){
      const sLim = o.kind === 'board' ? o.thick / 2 + 2.5 : 3.5;
      if (!hard(o) || Math.abs(o.s - c.s) > sLim) continue;
      const d = obstacleHit(o, c.x, c.y, c.s, cfg.cheeseR * 0.92);
      if (d <= 0) continue;
      c.x += _n[0] * d; c.y += _n[1] * d; c.s += _n[2] * d;
      const vn = c.vx * _n[0] + c.vy * _n[1] + c.v * _n[2];
      if (vn < 0){
        if (o.kind === 'board' && _n[2] < -0.5){
          c.v = -2.5; c.vx *= 0.2; c.vy = Math.max(0, c.vy);
        } else {
          c.vx -= (1 + OB_BOUNCE) * vn * _n[0];
          c.vy -= (1 + OB_BOUNCE) * vn * _n[1];
          c.v -= (1 + OB_BOUNCE) * vn * _n[2];
          c.vx *= OB_LOSS; c.v *= OB_LOSS; c.grounded = false;
        }
        c.tiltV += (Math.random() - 0.5) * Math.min(20, Math.abs(vn)) * TILT_KICK * 3;
      }
    }
  }
  function trip(c){
    c.state = 'rag'; c.t = 0; c.canCatch = false;
    const p = c.p, q = c.q, k = PELVIS * 3;
    const sy = Math.sin(c.yaw), cy = Math.cos(c.yaw);
    const wx = TRIP_SPIN * cy, ws = -TRIP_SPIN * sy;
    for (let i = 0; i < NP; i++){
      const ox = p[i * 3] - p[k], oy = p[i * 3 + 1] - p[k + 1], os = p[i * 3 + 2] - p[k + 2];
      q[i * 3] -= -ws * oy * STEP;
      q[i * 3 + 1] -= (ws * ox - wx * os + TRIP_HOP) * STEP;
      q[i * 3 + 2] -= wx * oy * STEP;
    }
  }

  // ---------- 人の体 ----------
  const HEAD = 0, NECK = 1, PELVIS = 2, SHL = 3, SHR = 4, ELL = 5, ELR = 6, HNL = 7, HNR = 8;
  const HPL = 9, HPR = 10, KNL = 11, KNR = 12, FTL = 13, FTR = 14, NP = 15;
  const PART_R = [0.11, 0.06, 0.1, 0.06, 0.06, 0.05, 0.05, 0.05, 0.05, 0.07, 0.07, 0.06, 0.06, 0.05, 0.05];
  const INV_M = [1/4, 1/5, 1/12, 1/4, 1/4, 1/1.6, 1/1.6, 1/0.6, 1/0.6, 1/6, 1/6, 1/4, 1/4, 1/1.5, 1/1.5];
  const TORSO = [NECK, SHL, SHR, PELVIS, HPL, HPR];
  let DIST = [];
  const MIN_DIST = [[HPL, FTL, 0.33], [HPR, FTR, 0.33], [SHL, HNL, 0.2], [SHR, HNR, 0.2]];

  function writePose(c, o, mode, k){
    const sy = Math.sin(c.yaw), cy = Math.cos(c.yaw);
    const Fx = sy, Fs = cy, Rx = cy, Rs = -sy;
    const set = (i, x, y, s) => { o[i * 3] = x; o[i * 3 + 1] = y; o[i * 3 + 2] = s; };
    const ph = c.phase, v = mode === 'run' ? c.v : 0, vr = Math.min(1, v / 3);
    let lean = 0, hipY = 0.93, twist = 0, armL = 0.1, armR = 0.1, bend = 0.3;
    const feet = [[0, 0], [0, 0]];
    if (mode === 'run'){
      lean = 0.18 * Math.min(1, v / 5);
      hipY = 0.885 - 0.035 * vr * Math.cos(4 * Math.PI * (ph - RUN_DUTY / 2));
      twist = 0.15 * vr * Math.cos(TAU * ph);
      armR = 0.1 + 0.75 * vr * Math.cos(TAU * ph);
      armL = 0.1 - 0.75 * vr * Math.cos(TAU * ph);
      bend = 0.3 + 1.2 * vr;
      const a = clamp(v / Math.max(0.5, c.freq) * RUN_DUTY / 2, 0, 0.45);
      for (let side = 0; side < 2; side++){
        const r = frac(ph + side * 0.5);
        if (r < RUN_DUTY){ feet[side][0] = a * (1 - 2 * r / RUN_DUTY); }
        else {
          const u = (r - RUN_DUTY) / (1 - RUN_DUTY);
          feet[side][0] = -a + 2 * a * smooth(u) - 0.3 * vr * Math.sin(Math.PI * u);
          feet[side][1] = (0.08 + 0.27 * Math.min(1, v / 6)) * Math.sin(Math.PI * u);
        }
      }
    } else if (mode === 'crouch'){
      lean = -0.55 * k; hipY = 0.88 - 0.18 * k; armL = armR = -0.9 * k; bend = 0.6;
      feet[0][0] = 0.25; feet[1][0] = -0.2;
    } else if (mode === 'reach'){
      lean = -1.0; hipY = 0.78; armL = armR = 2.7; bend = 0.15;
      feet[0][0] = -0.45; feet[1][0] = -0.25; feet[1][1] = 0.2;
    } else if (mode === 'ready'){
      lean = -0.35; hipY = 0.86; armL = -0.3; armR = 0.5; bend = 1.2;
      feet[0][0] = 0.2; feet[1][0] = -0.25;
    }
    const gy = ground(c.x, c.s), px = c.x, py = gy + hipY, ps = c.s;
    const cl = Math.cos(lean), sl = Math.sin(lean);
    const Tx = -Fx * sl, Ty = cl, Ts = -Fs * sl;
    const Px = Fx * cl, Py = sl, Ps = Fs * cl;
    const ct = Math.cos(twist), st = Math.sin(twist);
    const RhX = Rx * ct - Fx * st, RhS = Rs * ct - Fs * st;
    const RsX = Rx * ct + Fx * st, RsS = Rs * ct + Fs * st;
    set(PELVIS, px, py, ps);
    set(NECK, px + Tx * 0.52, py + Ty * 0.52, ps + Ts * 0.52);
    set(HEAD, px + Tx * 0.7 + Px * 0.03, py + Ty * 0.7 + Py * 0.03, ps + Ts * 0.7 + Ps * 0.03);
    const thigh = cfg.thigh, shin = cfg.shin;
    for (let side = 0; side < 2; side++){
      const sg = side ? 1 : -1;
      const S = side ? SHR : SHL, E = side ? ELR : ELL, Hn = side ? HNR : HNL;
      const sx = px + Tx * 0.47 + RsX * 0.19 * sg, syy = py + Ty * 0.47, ss = ps + Ts * 0.47 + RsS * 0.19 * sg;
      set(S, sx, syy, ss);
      const th = side ? armR : armL;
      const c1 = Math.cos(th), s1 = Math.sin(th), c2 = Math.cos(th + bend), s2 = Math.sin(th + bend);
      const ex = sx + (-Tx * c1 + Px * s1) * UPARM + Rx * 0.03 * sg, ey = syy + (-Ty * c1 + Py * s1) * UPARM, es = ss + (-Ts * c1 + Ps * s1) * UPARM + Rs * 0.03 * sg;
      set(E, ex, ey, es);
      set(Hn, ex + (-Tx * c2 + Px * s2) * FOREARM, ey + (-Ty * c2 + Py * s2) * FOREARM, es + (-Ts * c2 + Ps * s2) * FOREARM);
      const Hp = side ? HPR : HPL, Kn = side ? KNR : KNL, Ft = side ? FTR : FTL;
      const hx = px - Tx * 0.04 + RhX * 0.1 * sg, hy = py - Ty * 0.04, hs = ps - Ts * 0.04 + RhS * 0.1 * sg;
      set(Hp, hx, hy, hs);
      const fx = hx + Fx * feet[side][0], fs = hs + Fs * feet[side][0];
      let tx = fx - hx, ty = ground(fx, fs) + FOOT_H + feet[side][1] - hy, ts = fs - hs;
      let d = Math.hypot(tx, ty, ts);
      const maxD = thigh + shin - 0.002;
      if (d > maxD){ tx *= maxD / d; ty *= maxD / d; ts *= maxD / d; d = maxD; }
      const nx = tx / d, ny = ty / d, ns = ts / d;
      const along = (thigh * thigh - shin * shin + d * d) / (2 * d);
      const h = Math.sqrt(Math.max(0, thigh * thigh - along * along));
      let bx = Fx - nx * (Fx * nx + Fs * ns), by = -ny * (Fx * nx + Fs * ns), bs = Fs - ns * (Fx * nx + Fs * ns);
      const bl = Math.hypot(bx, by, bs) || 1;
      bx /= bl; by /= bl; bs /= bl;
      set(Kn, hx + nx * along + bx * h, hy + ny * along + by * h, hs + ns * along + bs * h);
      set(Ft, hx + tx, hy + ty, hs + ts);
    }
  }

  function rebuildBodyDist(){
    DIST = [];
    const tmp = { s:0, x:0, yaw:0, phase:0, v:0, freq:1 };
    const o = new Float64Array(NP * 3);
    writePose(tmp, o, 'idle', 0);
    const len = (a, b) => Math.hypot(o[a * 3] - o[b * 3], o[a * 3 + 1] - o[b * 3 + 1], o[a * 3 + 2] - o[b * 3 + 2]);
    for (let i = 0; i < TORSO.length; i++) for (let j = i + 1; j < TORSO.length; j++) DIST.push([TORSO[i], TORSO[j], len(TORSO[i], TORSO[j])]);
    DIST.push([HEAD, NECK, len(HEAD, NECK)], [HEAD, SHL, len(HEAD, SHL)], [HEAD, SHR, len(HEAD, SHR)]);
    DIST.push([SHL, ELL, UPARM], [ELL, HNL, FOREARM], [SHR, ELR, UPARM], [ELR, HNR, FOREARM]);
    DIST.push([HPL, KNL, cfg.thigh], [KNL, FTL, cfg.shin], [HPR, KNR, cfg.thigh], [KNR, FTR, cfg.shin]);
  }
  rebuildBodyDist();

  function solveDist(p, a, b, rest, minOnly){
    const ia = a * 3, ib = b * 3;
    const dx = p[ib] - p[ia], dy = p[ib + 1] - p[ia + 1], ds = p[ib + 2] - p[ia + 2];
    const d = Math.sqrt(dx * dx + dy * dy + ds * ds) || 1e-6;
    if (minOnly && d >= rest) return;
    const wa = INV_M[a] / (INV_M[a] + INV_M[b]), wb = 1 - wa, f = (d - rest) / d;
    p[ia] += dx * f * wa; p[ia + 1] += dy * f * wa; p[ia + 2] += ds * f * wa;
    p[ib] -= dx * f * wb; p[ib + 1] -= dy * f * wb; p[ib + 2] -= ds * f * wb;
  }
  function torsoForward(p, out){
    const rx = p[HPR * 3] - p[HPL * 3], ry = p[HPR * 3 + 1] - p[HPL * 3 + 1], rs = p[HPR * 3 + 2] - p[HPL * 3 + 2];
    const ux = p[NECK * 3] - p[PELVIS * 3], uy = p[NECK * 3 + 1] - p[PELVIS * 3 + 1], us = p[NECK * 3 + 2] - p[PELVIS * 3 + 2];
    const fx = ry * us - rs * uy, fy = rs * ux - rx * us, fs = rx * uy - ry * ux;
    const l = Math.hypot(fx, fy, fs) || 1;
    out[0] = fx / l; out[1] = fy / l; out[2] = fs / l;
  }
  const _f = [0, 0, 0];
  function kneeLimit(p){
    torsoForward(p, _f);
    for (const [Hp, Kn, Ft] of [[HPL, KNL, FTL], [HPR, KNR, FTR]]){
      let d = 0;
      for (let a = 0; a < 3; a++) d += (p[Kn * 3 + a] - (p[Hp * 3 + a] + p[Ft * 3 + a]) / 2) * _f[a];
      if (d < 0) for (let a = 0; a < 3; a++) p[Kn * 3 + a] -= _f[a] * d;
    }
  }
  function reachArms(p){
    const ux = p[NECK * 3] - p[PELVIS * 3], uy = p[NECK * 3 + 1] - p[PELVIS * 3 + 1], us = p[NECK * 3 + 2] - p[PELVIS * 3 + 2];
    const ul = Math.hypot(ux, uy, us) || 1;
    for (const [S, E, Hn] of [[SHL, ELL, HNL], [SHR, ELR, HNR]]){
      for (const [i, L] of [[E, UPARM], [Hn, UPARM + FOREARM]]){
        for (let a = 0; a < 3; a++){
          const t = p[S * 3 + a] + [ux, uy, us][a] / ul * L;
          p[i * 3 + a] += (t - p[i * 3 + a]) * 0.25;
        }
      }
    }
  }

  const _gy = new Float64Array(NP), _pen = new Float64Array(NP), _nrm = new Float64Array(NP * 3), _hasN = new Uint8Array(NP);
  function ragStep(c, dt, reaching){
    const p = c.p, q = c.q, gdt = 9.8 * dt * dt;
    for (let i = 0; i < NP * 3; i++){ const v = (p[i] - q[i]) * RAG_DAMP; q[i] = p[i]; p[i] += v; }
    for (let i = 0; i < NP; i++) p[i * 3 + 1] -= gdt;
    if (reaching) reachArms(p);
    for (let i = 0; i < NP; i++){ _gy[i] = ground(p[i * 3], p[i * 3 + 2]) + PART_R[i]; _pen[i] = 0; _hasN[i] = 0; }
    const near = obstaclesNear(p[PELVIS * 3], p[PELVIS * 3 + 2], 3), lim = cfg.halfW - 0.1;
    for (let it = 0; it < RAG_ITER; it++){
      for (const [a, b, r] of DIST) solveDist(p, a, b, r, false);
      for (const [a, b, r] of MIN_DIST) solveDist(p, a, b, r, true);
      kneeLimit(p);
      for (let i = 0; i < NP; i++){
        const k = i * 3;
        if (p[k + 1] < _gy[i]){ _pen[i] += _gy[i] - p[k + 1]; p[k + 1] = _gy[i]; }
        for (const o of near){
          const d = obstacleHit(o, p[k], p[k + 1], p[k + 2], PART_R[i]);
          if (d > 0){
            p[k] += _n[0] * d; p[k + 1] += _n[1] * d; p[k + 2] += _n[2] * d;
            _pen[i] += d; _hasN[i] = 1; _nrm[k] = _n[0]; _nrm[k + 1] = _n[1]; _nrm[k + 2] = _n[2];
          }
        }
        if (p[k] > lim){ p[k] = lim; q[k] = Math.max(q[k], lim); }
        if (p[k] < -lim){ p[k] = -lim; q[k] = Math.min(q[k], -lim); }
      }
    }
    for (let i = 0; i < NP; i++){
      if (_pen[i] <= 0) continue;
      const k = i * 3, x = p[k], s = p[k + 2];
      let nx = -slopeX(x, s), ny = 1, ns = -slopeS(x, s);
      if (_hasN[i]){ nx = _nrm[k]; ny = _nrm[k + 1]; ns = _nrm[k + 2]; }
      const nl = Math.hypot(nx, ny, ns); nx /= nl; ny /= nl; ns /= nl;
      let vx = p[k] - q[k], vy = p[k + 1] - q[k + 1], vs = p[k + 2] - q[k + 2];
      const vn0 = vx * nx + vy * ny + vs * ns;
      if (vn0 < 0){ vx -= vn0 * nx; vy -= vn0 * ny; vs -= vn0 * ns; }
      const vn = vx * nx + vy * ny + vs * ns;
      const tx = vx - vn * nx, ty = vy - vn * ny, ts = vs - vn * ns;
      const tl = Math.hypot(tx, ty, ts);
      const fr = RAG_MU * (_pen[i] + Math.max(0, -vn0));
      const kk = tl > 1e-9 ? Math.max(0, tl - fr) / tl : 0;
      q[k] = p[k] - (vn * nx + tx * kk); q[k + 1] = p[k + 1] - (vn * ny + ty * kk); q[k + 2] = p[k + 2] - (vn * ns + ts * kk);
    }
  }

  // ---------- 人の見た目 ----------
  const CYL = [[PELVIS, NECK, 'shirt', 0.16, 0.105], [NECK, HEAD, 'skin', 0.045, 0.045],
    [SHL, ELL, 'shirt', 0.05, 0.05], [SHR, ELR, 'shirt', 0.05, 0.05], [ELL, HNL, 'skin', 0.038, 0.038], [ELR, HNR, 'skin', 0.038, 0.038],
    [HPL, KNL, 'pants', 0.075, 0.075], [HPR, KNR, 'pants', 0.075, 0.075], [KNL, FTL, 'pants', 0.055, 0.055], [KNR, FTR, 'pants', 0.055, 0.055]];
  const SPH = [[PELVIS, 'pants', 0.16, 0.11, 0.11], [HEAD, 'skin', 0.105, 0.115, 0.105], [HEAD, 'hair', 0.11, 0.1, 0.112],
    [SHL, 'shirt', 0.062, 0.062, 0.062], [SHR, 'shirt', 0.062, 0.062, 0.062], [ELL, 'skin', 0.04, 0.04, 0.04], [ELR, 'skin', 0.04, 0.04, 0.04],
    [HNL, 'skin', 0.045, 0.05, 0.035], [HNR, 'skin', 0.045, 0.05, 0.035], [KNL, 'pants', 0.06, 0.06, 0.06], [KNR, 'pants', 0.06, 0.06, 0.06],
    [FTL, 'shoe', 0.05, 0.045, 0.125], [FTR, 'shoe', 0.05, 0.045, 0.125]];
  const bodyMat = new THREE.MeshStandardMaterial({ roughness:0.85, metalness:0 });
  const cylMesh = new THREE.InstancedMesh(new THREE.CylinderGeometry(1, 1, 1, 12), bodyMat, CH_ALLOC * CYL.length);
  const sphMesh = new THREE.InstancedMesh(new THREE.SphereGeometry(1, 16, 12), bodyMat, CH_ALLOC * SPH.length);
  for (const m of [cylMesh, sphMesh]){ m.frustumCulled = false; m.castShadow = true; m.receiveShadow = true; scene.add(m); }

  const PALETTE = { skin:list('--skins'), shirt:list('--shirts'), pants:list('--pants'), hair:list('--hair'), shoe:list('--shoes') };
  const chasers = [];
  for (let i = 0; i < CH_ALLOC; i++){
    const pick = (k, n) => lin(PALETTE[k][(i * n + Math.floor(i / PALETTE[k].length)) % PALETTE[k].length]);
    const col = { skin:pick('skin', 3), shirt:pick('shirt', 1), pants:pick('pants', 5), hair:pick('hair', 2), shoe:pick('shoe', 3) };
    if (i === 0) col.shirt = lin('#e63946');
    CYL.forEach((d, j) => cylMesh.setColorAt(i * CYL.length + j, col[d[2]]));
    SPH.forEach((d, j) => sphMesh.setColorAt(i * SPH.length + j, col[d[1]]));
    chasers.push({ p:new Float64Array(NP * 3), q:new Float64Array(NP * 3), a:new Float64Array(NP * 3), active:false, idx:i, isPlayer:false, crouchK:0 });
  }
  cylMesh.instanceColor.needsUpdate = sphMesh.instanceColor.needsUpdate = true;

  // ---------- チーズAI ----------
  function cheeseAIInput(){
    let avoidForce = 0;
    for (const o of obstacles){
      const ahead = o.s - cheese.s;
      if (ahead < 0.2 || ahead > 9) continue;
      const half = halfWidth(o) + 0.7;
      if (Math.abs(cheese.x - o.x) < half + 0.5) avoidForce += (cheese.x < o.x ? -1 : 1) * (1 - ahead / 9) * 2.4;
    }
    let nearDist = 999, nearX = 0;
    for (let i = 0; i < activeN; i++){
      const c = chasers[i], ds = cheese.s - c.s;
      if (ds > -1 && ds < 10){ const d = Math.hypot(ds, cheese.x - c.x); if (d < nearDist){ nearDist = d; nearX = c.x; } }
    }
    let dodgeForce = 0, pushY = 0.25;
    if (nearDist < 8){
      let dir = cheese.x >= nearX ? 1 : -1;
      if (cheese.x > cfg.halfW - 1.8) dir = -1;
      if (cheese.x < -cfg.halfW + 1.8) dir = 1;
      dodgeForce = dir * (1 - nearDist / 8) * 1.4;
      pushY = 0.85;
    }
    const wander = Math.sin(playTime * 1.1 + obSeed) * 0.25;
    let wallForce = 0;
    if (cheese.x > cfg.halfW - 1.6) wallForce = -1.2;
    if (cheese.x < -cfg.halfW + 1.6) wallForce = 1.2;
    return { x: clamp(avoidForce + dodgeForce + wander + wallForce, -1, 1), y: pushY };
  }

  function getCheeseCtrl(){
    if (cfg.isOnline) return cfg.myRole === 'cheese' ? input() : net.remoteInput;
    return cfg.mode === 'human' ? cheeseAIInput() : input();
  }
  function getHumanCtrl(){
    if (cfg.isOnline){
      if (cfg.myRole === 'human'){ const d = diveRequested; diveRequested = false; return { ...input(), dive: d }; }
      const d = net.remoteInput.dive; net.remoteInput.dive = false;
      return { x: net.remoteInput.x, y: net.remoteInput.y, dive: d };
    }
    const d = diveRequested; diveRequested = false;
    return { ...input(), dive: d };
  }

  // ---------- 人の動き ----------
  function setPose(c, mode, k){ c.q.set(c.p); writePose(c, c.p, mode, k); }
  function resetChaser(c, i, initial){
    c.isPlayer = cfg.humanPlayerActive && i === 0;
    c.state = initial ? 'wait' : 'run';
    if (initial){
      if (c.isPlayer){ c.s = -3.2; c.x = 0; }
      else {
        const perRow = Math.max(6, Math.min(20, Math.floor((cfg.halfW * 2 - 1.4) / 1.4)));
        const row = Math.floor(i / perRow), countInRow = Math.min(perRow, cfg.chStart - row * perRow), col = i % perRow;
        const gap = Math.min(1.6, (cfg.halfW * 2 - 1.4) / Math.max(1, countInRow));
        c.s = -3.2 - row * 1.35 - (col % 2) * 0.4;
        c.x = clamp((col - (countInRow - 1) / 2) * gap, -cfg.halfW + 0.6, cfg.halfW - 0.6);
        if (cfg.humanPlayerActive && Math.abs(c.x) < 1.1 && Math.abs(c.s + 3.2) < 1.2){
          c.x = (i % 2 ? 1 : -1) * (1.4 + (i % 3) * 0.7);
        }
      }
    } else {
      c.s = cheese.s - SPAWN_BEHIND - Math.random() * 4;
      c.x = clamp(cheese.x + (Math.random() * 2 - 1) * (cfg.halfW * 0.5), -cfg.halfW + 0.5, cfg.halfW - 0.5);
    }
    c.v = initial ? 0 : Math.max(cheese.v, 5);
    c.vx = 0; c.t = 0; c.yaw = 0; c.freq = 1.2; c.crouchK = 0;
    c.cool = initial ? (c.isPlayer ? cfg.startDelay : cfg.startDelay + cfg.startGrace) : 0;
    c.phase = Math.random();
    c.vary = c.isPlayer ? 0 : (Math.random() * 2 - 1) * SPEED_VAR;
    c.off = (Math.random() * 2 - 1) * Math.min(5, cfg.halfW * 0.35);
    c.skill = lerp(SKILL_MIN, SKILL_MAX, Math.random());
    c.tripCool = 0; c.canCatch = false;
    writePose(c, c.p, initial ? 'ready' : 'run', 0);
    c.q.set(c.p);
    if (!initial){ for (let n = 0; n < NP; n++) c.q[n * 3 + 2] -= c.v * STEP; }
  }

  const cheeseHeight = () => cheese.y - cfg.cheeseR - ground(cheese.x, cheese.s);

  function updateCheese(dt){
    const c = cheese;
    if (c.held){ const p = c.held.p, k = c.hand * 3; c.x = p[k]; c.y = p[k + 1]; c.s = p[k + 2]; return; }
    if (game === 'playing'){
      const k = c.grounded ? 1 : cfg.airControl;
      const { x:ix, y:iy } = getCheeseCtrl();
      const cVxMax = cfg.mode === 'human' ? 4.2 : cfg.vxMax;
      const cLatAcc = cfg.mode === 'human' ? 18 : cfg.latAcc;
      c.vx += clamp(ix * cVxMax - c.vx, -cLatAcc * k * dt, cLatAcc * k * dt);
      const pushScale = Math.max(1, cfg.targetSpeed / 9.5);
      if (iy > 0) c.v += iy * PUSH_ACC * pushScale * k * dt;
      else if (iy < 0) c.v = Math.max(Math.min(c.v, 0), c.v + iy * BRAKE_ACC * pushScale * k * dt);
      if (c.grounded){ c.vx -= c.tilt * TILT_DRIFT * dt; c.v -= cfg.turnCost * Math.abs(c.vx) * dt; }
      c.v = Math.max(-3, c.v + (cfg.gAlong - cfg.drag * c.v * Math.abs(c.v)) * dt);
      if (cheeseHeight() < 0.5 && inBush(c.x, c.s)) c.v = Math.max(Math.min(c.v, 0), c.v - BUSH_DRAG * dt);
    }
    c.s += c.v * dt; c.x += c.vx * dt;
    if (game === 'playing') cheeseObstacles(c);
    if (c.x < -cfg.xlim){ c.x = -cfg.xlim; c.vx = Math.max(0, c.vx); }
    if (c.x > cfg.xlim){ c.x = cfg.xlim; c.vx = Math.min(0, c.vx); }
    c.vy -= cfg.gFall * dt; c.y += c.vy * dt;
    const gy = ground(c.x, c.s) + cfg.cheeseR;
    if (c.y <= gy){
      const gvy = slopeS(c.x, c.s) * c.v + slopeX(c.x, c.s) * c.vx, hit = gvy - c.vy;
      if (hit > cfg.bounceMin){
        c.vy = gvy + hit * cfg.bounce;
        c.tiltV += (Math.random() - 0.5) * hit * TILT_KICK * 4;
        if (game === 'playing') c.vx += (Math.random() - 0.5) * hit * 0.3;
      } else c.vy = gvy;
      c.y = gy; c.grounded = true;
    } else if (c.y - gy > 0.03) c.grounded = false;
    c.tiltV += (-TILT_SPRING * c.tilt - TILT_DAMP * c.tiltV) * dt;
    c.tilt = clamp(c.tilt + c.tiltV * dt, -0.6, 0.6);
    c.rot = (c.rot + c.v / cfg.cheeseR * dt) % TAU;
  }

  function finishCaught(c, hand, resOutcome){
    game = 'caught'; outcome = resOutcome; endJoy(); overT = 0;
    if (c){ cheese.held = c; cheese.hand = hand; cheese.v = cheese.vx = cheese.vy = 0; }
    for (const o of chasers) if (o !== c && (o.state === 'run' || o.state === 'windup' || o.state === 'wait')) o.state = 'idle';
    if (cfg.isOnline && net.role === 'host') sendHostSnapshot(true);
  }

  const _d3 = (p, i, x, y, s) => Math.hypot(p[i * 3] - x, p[i * 3 + 1] - y, p[i * 3 + 2] - s);

  function launch(c){
    c.state = 'rag'; c.t = 0; c.canCatch = true;
    writePose(c, c.p, 'reach', 0);
    let tx, ts;
    if (c.isPlayer){
      const dsC = cheese.s - c.s, dxC = cheese.x - c.x;
      if (dsC > -1 && dsC < 5.5 && Math.abs(dxC) < 3.2){
        ts = cheese.s + cheese.v * DIVE_T * 0.85;
        tx = clamp(cheese.x + cheese.vx * DIVE_T * 0.5, -cfg.halfW + 0.5, cfg.halfW - 0.5);
      } else {
        ts = c.s + Math.max(c.v, 6) * DIVE_T + 1.8;
        tx = clamp(c.x + c.vx * DIVE_T, -cfg.halfW + 0.5, cfg.halfW - 0.5);
      }
    } else {
      ts = cheese.s + cheese.v * DIVE_T;
      tx = clamp(cheese.x + cheese.vx * DIVE_T * AIM, -cfg.halfW + 0.5, cfg.halfW - 0.5);
    }
    const dx = tx - c.x, ds = ts - c.s, dl = Math.hypot(dx, ds) || 1;
    const px = tx - dx / dl * DIVE_REACH, ps = ts - ds / dl * DIVE_REACH;
    const p = c.p, k = PELVIS * 3;
    const vx = (px - p[k]) / DIVE_T, vs = (ps - p[k + 2]) / DIVE_T;
    const vy = (ground(px, ps) + 0.35 - p[k + 1]) / DIVE_T + 0.5 * 9.8 * DIVE_T;
    const rx = ds / dl, rs = -dx / dl;
    for (let i = 0; i < NP; i++){
      const ox = p[i * 3] - p[k], oy = p[i * 3 + 1] - p[k + 1], os = p[i * 3 + 2] - p[k + 2];
      const wx = DIVE_SPIN * rx, ws = DIVE_SPIN * rs;
      c.q[i * 3] = p[i * 3] - (vx - ws * oy) * STEP;
      c.q[i * 3 + 1] = p[i * 3 + 1] - (vy + ws * ox - wx * os) * STEP;
      c.q[i * 3 + 2] = p[i * 3 + 2] - (vs + wx * oy) * STEP;
    }
  }

  function updateChaser(c, dt){
    const behind = cheese.s - c.s;
    c.cool -= dt;
    switch (c.state){
      case 'wait':
        c.t += dt; setPose(c, 'ready', 0);
        if (c.t >= cfg.startDelay){ c.state = 'run'; c.t = 0; }
        break;
      case 'run':
      case 'windup': {
        let wantDive = false;
        if (c.isPlayer){
          const ctrl = getHumanCtrl();
          wantDive = ctrl.dive;
          const baseRun = (cfg.targetSpeed + 1.0) * cfg.chSpeedMult;
          let target = baseRun;
          if (ctrl.y > 0) target += ctrl.y * Math.max(2.2, cfg.targetSpeed * 0.12) * cfg.chSpeedMult;
          else if (ctrl.y < 0) target = Math.max(0, baseRun + ctrl.y * Math.max(7, cfg.targetSpeed * 0.4));
          if (inBush(c.x, c.s)) target *= BUSH_RUN;
          c.v += clamp(target - c.v, -Math.max(12, cfg.runAcc * 1.4) * dt, Math.max(12, cfg.runAcc * 1.4) * dt);
          const pVx = Math.max(cfg.vxMax, 5.2);
          c.vx += clamp(ctrl.x * pVx - c.vx, -Math.max(28, pVx * 5) * dt, Math.max(28, pVx * 5) * dt);
        } else {
          const runMax = (cfg.targetSpeed + 0.9 + cheese.s * 0.001) * cfg.chSpeedMult;
          const baseFollow = (Math.max(cheese.v, 3) + 1.3 + cheese.s * 0.0005) * cfg.chSpeedMult;
          let target = Math.min(runMax, baseFollow) + c.vary;
          if (behind > CATCHUP_DIST) target += CATCHUP * cfg.chSpeedMult;
          if (behind < 0.4) target = Math.min(target, Math.max(cheese.v, cfg.course === 'board' ? 5 : 0));
          if (inBush(c.x, c.s)) target *= BUSH_RUN;
          target = Math.max(0, target);
          c.v += clamp(target - c.v, -cfg.runAcc * dt, cfg.runAcc * dt);
          const want = avoidX(c, cheese.x + c.off * clamp((behind - 2) / 8, 0, 1));
          const tvx = clamp((want - c.x) * 3, -cfg.chVx, cfg.chVx);
          c.vx += clamp(tvx - c.vx, -Math.max(15, cfg.chVx * 2.5) * dt, Math.max(15, cfg.chVx * 2.5) * dt);
        }
        c.s += c.v * dt;
        c.x = clamp(c.x + c.vx * dt, -cfg.halfW + 0.5, cfg.halfW - 0.5);
        c.yaw = Math.atan2(c.vx, Math.max(c.v, 1)) * 0.8;
        c.freq = clamp(c.v * 0.32, 1.2, 3.0);
        c.phase += c.freq * dt;
        c.tripCool -= dt;
        if (c.tripCool <= 0 && obstacles.some(o => tripTest(o, c.x, c.s))){ trip(c); break; }

        if (cfg.course === 'board' && game === 'playing' && c.cool <= 0){
          const nb = cheese.s - c.s;
          if (Math.abs(nb) < 1.2 && Math.abs(cheese.x - c.x) < 0.9 && cheeseHeight() < CATCH_HEIGHT){
            setPose(c, 'reach', 0);
            finishCaught(c, HNR, cfg.humanPlayerActive ? (c.isPlayer ? 'human_win' : 'rival_win') : 'caught');
            break;
          }
        }
        if (c.state === 'run'){
          setPose(c, 'run', 0);
          if (c.isPlayer){
            if (game === 'playing' && wantDive && c.cool <= 0){ c.state = 'windup'; c.t = 0; c.crouchK = 0; }
          } else if (game === 'playing' && c.cool <= 0 && behind > -DIVE_BACK && behind < DIVE_RANGE &&
              Math.abs(cheese.x - c.x) < DIVE_DX && cheeseHeight() < CATCH_HEIGHT){
            c.state = 'windup'; c.t = 0; c.crouchK = 0;
          }
        } else {
          c.t += dt;
          const wDur = c.isPlayer ? WINDUP * 0.55 : WINDUP;
          c.crouchK = Math.min(1, c.t / wDur);
          setPose(c, 'crouch', c.crouchK);
          if (c.t >= wDur) launch(c);
        }
        break;
      }
      case 'rag': {
        c.t += dt;
        ragStep(c, dt, c.t < DIVE_T * 0.9 && cheese.held !== c);
        const p = c.p, k = PELVIS * 3;
        c.x = p[k]; c.s = p[k + 2]; c.v = (p[k + 2] - c.q[k + 2]) / dt;
        if (game === 'playing' && c.canCatch && c.t < CATCH_WINDOW && cheeseHeight() < CATCH_HEIGHT){
          const X = cheese.x, Y = cheese.y, S = cheese.s;
          const dl = _d3(p, HNL, X, Y, S), dr = _d3(p, HNR, X, Y, S);
          const body = Math.min(_d3(p, NECK, X, Y, S), _d3(p, PELVIS, X, Y, S), _d3(p, SHL, X, Y, S), _d3(p, SHR, X, Y, S));
          const bonus = c.isPlayer ? 0.14 : 0;
          if (Math.min(dl, dr) < cfg.cheeseR + 0.25 + bonus || body < cfg.cheeseR + 0.3 + bonus){
            finishCaught(c, dl < dr ? HNL : HNR, cfg.humanPlayerActive ? (c.isPlayer ? 'human_win' : 'rival_win') : 'caught');
            break;
          }
        }
        const sp = Math.hypot(p[k] - c.q[k], p[k + 1] - c.q[k + 1], p[k + 2] - c.q[k + 2]) / dt;
        if (cheese.held !== c && c.t > RAG_MIN && (sp < RAG_STILL || c.t > RAG_MAX)){
          c.state = 'getup'; c.t = 0; c.a.set(p);
          c.s0 = c.s; c.x0 = c.x; c.g0 = ground(c.x, c.s);
          c.v = Math.max(0, Math.min(c.v, 4)); c.vx = 0; c.yaw = 0; c.phase = 0;
        }
        break;
      }
      case 'getup': {
        c.t += dt; c.v += (3 - c.v) * Math.min(1, 3 * dt); c.s += c.v * dt;
        c.freq = clamp(c.v * 0.32, 1.2, 3.0); c.phase += c.freq * dt;
        c.q.set(c.p); writePose(c, c.p, 'run', 0);
        const k = smooth(Math.min(1, c.t / GETUP_T));
        const dS = c.s - c.s0, dX = c.x - c.x0, dY = ground(c.x, c.s) - c.g0;
        for (let i = 0; i < NP; i++){
          c.p[i * 3] = lerp(c.a[i * 3] + dX, c.p[i * 3], k);
          c.p[i * 3 + 1] = lerp(c.a[i * 3 + 1] + dY, c.p[i * 3 + 1], k);
          c.p[i * 3 + 2] = lerp(c.a[i * 3 + 2] + dS, c.p[i * 3 + 2], k);
        }
        if (c.t >= GETUP_T){ c.state = game === 'playing' ? 'run' : 'idle'; c.t = 0; c.cool = c.isPlayer ? 0.35 : COOLDOWN; c.tripCool = TRIP_COOL; }
        break;
      }
      case 'idle':
        c.v = Math.max(0, c.v - 8 * dt);
        if (behind < 1.5 && Math.abs(cheese.x - c.x) < 1) c.v = 0;
        c.s += c.v * dt; c.freq = clamp(c.v * 0.32, 1.2, 3.0); c.phase += c.freq * dt;
        setPose(c, 'run', 0);
        break;
    }
    if (game === 'playing' && !c.isPlayer && behind > RESPAWN_BEHIND && cheese.held !== c) resetChaser(c, c.idx, false);
  }

  function separate(){
    const moving = s => s === 'run' || s === 'windup' || s === 'idle';
    for (let i = 0; i < activeN; i++) for (let j = i + 1; j < activeN; j++){
      const a = chasers[i], b = chasers[j];
      if (!moving(a.state) || !moving(b.state)) continue;
      const ds = a.s - b.s, dx = a.x - b.x;
      if (Math.abs(ds) < 0.8 && Math.abs(dx) < 0.6){
        const push = (0.6 - Math.abs(dx)) * 0.5 * (dx >= 0 ? 1 : -1);
        a.x = clamp(a.x + push, -cfg.halfW + 0.5, cfg.halfW - 0.5);
        b.x = clamp(b.x - push, -cfg.halfW + 0.5, cfg.halfW - 0.5);
      }
    }
  }

  function step(dt){
    if (game === 'playing') playTime += dt;
    updateObstacles();
    updateCheese(dt);
    if (game === 'playing' && !cfg.humanPlayerActive){
      const n = Math.min(cfg.chMax, cfg.chStart + Math.floor(cheese.s / cfg.chAddEvery));
      while (activeN < n){ const c = chasers[activeN++]; resetChaser(c, activeN - 1, false); c.active = true; }
    }
    for (let i = 0; i < activeN; i++) updateChaser(chasers[i], dt);
    separate();
    if (cheese.held) updateCheese(dt);
    recordTrail();

    if (cfg.humanPlayerActive && activeN > 0){
      const pl = chasers[0];
      if (game === 'playing'){
        maxDist = Math.max(maxDist, Math.max(0, cheese.s));
        const lim = cfg.isOnline ? ESCAPE_LIMIT_ONLINE : ESCAPE_LIMIT_SOLO;
        if (cheese.s - pl.s > lim) finishCaught(null, 0, 'escaped');
      }
    } else if (game === 'playing') maxDist = Math.max(maxDist, cheese.s);

    S0 = (cfg.myRole === 'human' && activeN > 0) ? chasers[0].s : cheese.s;
  }

  // ---------- ゲスト側補間 ----------
  function applyGuestInterpolation(dt){
    const sn = net.snap;
    if (!sn) return;
    const k = Math.min(1, dt * 18), sc = sn.ch;
    if (Math.hypot(sc.s - cheese.s, sc.x - cheese.x) > 10){ cheese.s = sc.s; cheese.x = sc.x; cheese.y = sc.y; }
    else { cheese.s = lerp(cheese.s, sc.s, k); cheese.x = lerp(cheese.x, sc.x, k); cheese.y = lerp(cheese.y, sc.y, k); }
    cheese.v = sc.v; cheese.vx = sc.vx; cheese.tilt = lerp(cheese.tilt, sc.tl, k);
    cheese.rot = (cheese.rot + cheese.v / cfg.cheeseR * dt) % TAU;
    cheese.held = sc.h >= 0 ? chasers[sc.h] : null; cheese.hand = sc.hd;

    activeN = sn.cs.length;
    for (let i = 0; i < activeN; i++){
      const d = sn.cs[i], c = chasers[i];
      c.active = true; c.isPlayer = cfg.humanPlayerActive && i === 0;
      const prevState = c.state;
      c.state = d.st; c.v = d.v; c.cool = d.cl; c.freq = clamp(c.v * 0.32, 1.2, 3.0);
      if (d.p){
        const jump = prevState !== d.st && prevState !== 'rag' && prevState !== 'getup';
        for (let j = 0; j < NP * 3; j++) c.p[j] = jump ? d.p[j] : lerp(c.p[j], d.p[j], Math.min(1, dt * 22));
        c.x = c.p[PELVIS * 3]; c.s = c.p[PELVIS * 3 + 2];
      } else {
        if (Math.hypot(d.s - c.s, d.x - c.x) > 10){ c.s = d.s; c.x = d.x; c.yaw = d.yw; c.phase = d.ph; }
        else { c.s = lerp(c.s, d.s, k); c.x = lerp(c.x, d.x, k); c.yaw = lerp(c.yaw, d.yw, k); c.phase += c.freq * dt; }
        writePose(c, c.p, d.st === 'wait' ? 'ready' : d.st === 'windup' ? 'crouch' : 'run', d.ck || 0);
      }
    }
    maxDist = sn.md; playTime = sn.pt;
    if (sn.g !== game){
      if (sn.g === 'caught' && game === 'playing'){ game = 'caught'; outcome = sn.oc; endJoy(); }
      else if (sn.g === 'over' && game !== 'over'){ outcome = sn.oc; showOver(); }
    }
    recordTrail();
    S0 = (cfg.myRole === 'human' && activeN > 0) ? chasers[0].s : cheese.s;
    updateObstacles();
  }

  // ---------- 描画 ----------
  const RP = Array.from({ length:NP }, () => new THREE.Vector3());
  const _m = new THREE.Matrix4(), _x = new THREE.Vector3(), _y = new THREE.Vector3(), _z = new THREE.Vector3();
  const _right = new THREE.Vector3(), _up = new THREE.Vector3(), _back = new THREE.Vector3(), _c = new THREE.Vector3();
  function drawChaser(c, idx){
    for (let i = 0; i < NP; i++) rel(RP[i], c.p[i * 3 + 2], c.p[i * 3], c.p[i * 3 + 1]);
    _up.subVectors(RP[NECK], RP[PELVIS]).normalize();
    _right.subVectors(RP[SHR], RP[SHL]).add(_c.subVectors(RP[HPR], RP[HPL]));
    _right.addScaledVector(_up, -_right.dot(_up)).normalize();
    _back.crossVectors(_right, _up);
    CYL.forEach(([a, b, , ra, rb], j) => {
      _y.subVectors(RP[b], RP[a]);
      const len = _y.length() || 1e-4;
      _y.divideScalar(len);
      _x.copy(_right).addScaledVector(_y, -_right.dot(_y));
      if (_x.lengthSq() < 1e-4) _x.copy(_back).addScaledVector(_y, -_back.dot(_y));
      _x.normalize(); _z.crossVectors(_x, _y);
      _m.makeBasis(_x.multiplyScalar(ra), _y.multiplyScalar(len), _z.multiplyScalar(rb));
      _m.setPosition(_c.addVectors(RP[a], RP[b]).multiplyScalar(0.5));
      cylMesh.setMatrixAt(idx * CYL.length + j, _m);
    });
    SPH.forEach(([a, key, sx, sy, sz], j) => {
      _c.copy(RP[a]); _x.copy(_right); _y.copy(_up); _z.copy(_back);
      if (key === 'hair'){ _c.addScaledVector(_up, 0.025).addScaledVector(_back, 0.012); }
      if (key === 'shoe'){
        const kn = a === FTL ? KNL : KNR;
        _y.subVectors(RP[kn], RP[a]).normalize();
        _z.copy(_back).addScaledVector(_y, -_back.dot(_y));
        if (_z.lengthSq() < 1e-4) _z.copy(_up);
        _z.normalize(); _x.crossVectors(_y, _z);
        _c.addScaledVector(_z, -0.05).addScaledVector(_y, -0.03);
      }
      _m.makeBasis(_x.multiplyScalar(sx), _y.multiplyScalar(sy), _z.multiplyScalar(sz));
      _m.setPosition(_c);
      sphMesh.setMatrixAt(idx * SPH.length + j, _m);
    });
  }

  let camX = 0, zoom = 0;
  const _camP = new THREE.Vector3(), _camL = new THREE.Vector3(), _zP = new THREE.Vector3(), _zL = new THREE.Vector3();
  function draw(dt){
    updateChunks(); updateFence(); drawObstacles();
    rel(cheeseGroup.position, cheese.s, cheese.x, cheese.y);
    if (!cheese.held){
      cheeseGroup.rotation.y = -Math.atan2(cheese.vx, Math.max(cheese.v, 1));
      cheeseGroup.rotation.x = Math.atan(slopeS(cheese.x, cheese.s));
      cheeseGroup.rotation.z = -cheese.vx / Math.max(1, cfg.vxMax) * 0.3 + cheese.tilt;
    }
    cheeseSpin.rotation.x = -cheese.rot;

    for (let i = 0; i < activeN; i++) drawChaser(chasers[i], i);
    cylMesh.count = activeN * CYL.length; sphMesh.count = activeN * SPH.length;
    cylMesh.instanceMatrix.needsUpdate = sphMesh.instanceMatrix.needsUpdate = true;

    if (cfg.humanPlayerActive && activeN > 0){
      const pl = chasers[0];
      playerMarker.visible = true;
      rel(playerMarker.position, pl.p[HEAD * 3 + 2], pl.p[HEAD * 3], pl.p[HEAD * 3 + 1] + 0.65 + Math.sin(performance.now() * 0.008) * 0.1);
      playerMarker.rotation.y = performance.now() * 0.003;
      if (cfg.myRole === 'human') $('diveBtn').classList.toggle('disabled', !(game === 'playing' && pl.state === 'run' && pl.cool <= 0));
    } else playerMarker.visible = false;

    if (cfg.myRole === 'human' && activeN > 0){
      const pl = chasers[0];
      camX += (pl.x * 0.8 - camX) * Math.min(1, dt * 5);
      const baseBack = Math.max(6.5, cfg.camBack);
      _camP.set(camX, SLOPE * baseBack + cfg.camUp, baseBack);
      _camL.set(lerp(camX, cheese.x, 0.25), -SLOPE * 5.5 + 0.5, -5.5);
    } else {
      camX += (cheese.x * cfg.camFollowX - camX) * Math.min(1, dt * 4);
      const camY = cheese.y + SLOPE * S0 - bump(cheese.x, cheese.s);
      _camP.set(camX, SLOPE * cfg.camBack + cfg.camUp + camY * 0.3, cfg.camBack);
      _camL.set(camX, -SLOPE * CAM_LOOK + camY * 0.3, -CAM_LOOK);
    }

    const wantZoom = (game === 'caught' || game === 'over') && outcome !== 'escaped' ? 1 : 0;
    zoom += (wantZoom - zoom) * Math.min(1, dt * ZOOM_SPEED);
    if (zoom > 0.001){
      rel(_zL, cheese.s, cheese.x, cheese.y);
      _zP.set(_zL.x + ZOOM_SIDE, ground(cheese.x, cheese.s - ZOOM_BACK) + SLOPE * S0 + ZOOM_UP, _zL.z + ZOOM_BACK);
      const k = smooth(zoom);
      _camP.lerp(_zP, k); _camL.lerp(_zL, k);
    }
    camera.position.copy(_camP); camera.lookAt(_camL);
    drawTrail(dt);
    sun.target.position.set(camX, 0, -2); sun.position.set(camX - 6, 16, -10);

    if (cfg.isOnline){
      const gap = activeN > 0 ? Math.max(0, Math.round(cheese.s - chasers[0].s)) : 0;
      const runD = Math.max(0, Math.floor(maxDist));
      const tag = cfg.onlineType === 'free' ? '全国フリー' : '合言葉対戦';
      const key = `o:${cfg.myRole}:${gap}:${runD}`;
      if (key !== shownHudKey){
        shownHudKey = key;
        if (cfg.myRole === 'cheese'){
          $('hud').textContent = `${runD} m`;
          $('hudSub').textContent = `[${tag}] あなた：チーズ役 / 差 ${gap}m（${ESCAPE_LIMIT_ONLINE}mで勝利）`;
        } else {
          $('hud').textContent = `チーズまで ${gap} m`;
          $('hudSub').textContent = `[${tag}] あなた：人間役 / 地点 ${runD}m`;
        }
      }
    } else if (cfg.mode === 'human'){
      const gap = activeN > 0 ? Math.max(0, Math.round(cheese.s - chasers[0].s)) : 0;
      const runD = Math.max(0, Math.floor(maxDist));
      const key = `h:${gap}:${runD}`;
      if (key !== shownHudKey){
        shownHudKey = key;
        $('hud').textContent = `チーズまで ${gap} m`;
        $('hudSub').textContent = `地点 ${runD} m  /  BEST ${formatBest('human', currentBest)}`;
      }
    } else {
      const d = Math.floor(maxDist), key = `c:${d}`;
      if (key !== shownHudKey){
        shownHudKey = key;
        $('hud').textContent = `${d} m`;
        $('hudSub').textContent = `BEST ${currentBest === null ? d : Math.max(currentBest, d)} m`;
      }
    }
    renderer.render(scene, camera);
  }

  // ---------- 設定反映 ----------
  function applyUserConfig(src, isOnlineMatch, myRoleOverride){
    const s = src || userCfg, isOnline = !!isOnlineMatch;
    const course = isOnline ? s.onlineCourse : (s.mode === 'board' ? 'board' : 'normal');
    const isBoard = course === 'board', humanActive = isOnline || s.mode === 'human';

    cfg.mode = isOnline ? 'online' : s.mode;
    cfg.onlineType = s.onlineType || 'room';
    cfg.course = course; cfg.isOnline = isOnline; cfg.humanPlayerActive = humanActive;
    cfg.myRole = isOnline ? myRoleOverride : (s.mode === 'human' ? 'human' : 'cheese');
    cfg.targetSpeed = s.speed; cfg.vxMax = s.vxMax;
    cfg.chStart = isOnline ? clamp(1 + Math.round(s.chasers), 1, 31) : clamp(Math.round(s.chasers), 1, CH_ALLOC);
    cfg.chSpeedMult = s.chSpeed; cfg.boardGap = s.boardGap; cfg.camUp = s.camUp;

    if (isBoard){
      cfg.halfW = 20; cfg.cheeseR = 0.22; cfg.cheeseW = 0.1; cfg.gFall = 250; cfg.drag = 1.0;
      cfg.gAlong = cfg.targetSpeed * cfg.targetSpeed * cfg.drag;
      cfg.turnCost = 0; cfg.latAcc = Math.max(80, cfg.vxMax * 5);
      cfg.bounce = 0; cfg.bounceMin = 1e30; cfg.airControl = 1.0;
      cfg.chMax = cfg.chStart; cfg.chAddEvery = 100000;
      cfg.startDelay = isOnline ? 0.5 : 0.01; cfg.startGrace = 1.0;
      cfg.runAcc = Math.max(30, cfg.targetSpeed * 1.6); cfg.chVx = Math.max(10, cfg.vxMax * 0.55);
      cfg.boardW = cfg.halfW; cfg.avoidLook = Math.max(14, cfg.boardGap * 0.8);
      cfg.thigh = 0.1; cfg.shin = 0.7; cfg.camBack = 5; cfg.camFollowX = 1.0; cfg.camVfovMax = 110;
    } else {
      cfg.halfW = 8; cfg.cheeseR = 0.32; cfg.cheeseW = 0.2; cfg.gFall = 9.8;
      const baseAlong = 9.8 * Math.sin(Math.atan(SLOPE));
      cfg.gAlong = baseAlong * Math.pow(cfg.targetSpeed / 9.5, 1.3);
      cfg.drag = cfg.gAlong / (cfg.targetSpeed * cfg.targetSpeed);
      cfg.turnCost = 0.1; cfg.latAcc = Math.max(22, cfg.vxMax * 5);
      cfg.bounce = 0.3; cfg.bounceMin = 1.2; cfg.airControl = 0.6;
      cfg.chMax = humanActive ? cfg.chStart : Math.min(CH_ALLOC, Math.max(12, cfg.chStart + 6));
      cfg.chAddEvery = 100; cfg.startDelay = humanActive ? 0.5 : 1.0; cfg.startGrace = humanActive ? 1.0 : 2.0;
      cfg.runAcc = Math.max(7, cfg.targetSpeed * 0.8); cfg.chVx = Math.max(3.2, cfg.vxMax * 0.7);
      cfg.avoidLook = 6; cfg.thigh = 0.43; cfg.shin = 0.43;
      cfg.camBack = cfg.myRole === 'human' ? 7 : 8; cfg.camFollowX = 0.75; cfg.camVfovMax = 65;
    }
    cfg.xlim = cfg.halfW - cfg.cheeseW / 2 - 0.1;
    cheeseMesh.scale.set(cfg.cheeseW, cfg.cheeseR, cfg.cheeseR);
    rebuildBodyDist();
    resize();
  }

  // ---------- オンライン通信（PeerJS） ----------
  function setNetStatus(state, text){
    $('netDot').className = 'dot ' + (state || '');
    $('netStatus').textContent = text;
  }
  const r2 = v => Math.round(v * 100) / 100;

  function cleanRoomText(raw){
    return raw.trim()
      .replace(/[！-～]/g, ch => String.fromCharCode(ch.charCodeAt(0) - 0xFEE0))
      .replace(/\s+/g, '');
  }
  function encodeRoomId(raw){
    const s = cleanRoomText(raw).toLowerCase();
    if (!s) return '';
    const bytes = new TextEncoder().encode(s);
    let hex = '';
    for (let i = 0; i < bytes.length; i++) hex += bytes[i].toString(16).padStart(2, '0');
    return hex.slice(0, 48);
  }
  function sendMsg(obj){ if (net.conn && net.conn.open){ try { net.conn.send(obj); } catch {} } }
  function clearNetTimers(){
    if (net.startTimer){ clearTimeout(net.startTimer); net.startTimer = null; }
    if (net.joinTimer){ clearTimeout(net.joinTimer); net.joinTimer = null; }
    if (net.helloTimer){ clearInterval(net.helloTimer); net.helloTimer = null; }
  }

  function sendHostSnapshot(force){
    if (!net.connected || net.role !== 'host') return;
    const now = performance.now();
    if (!force && now - net.lastSend < SYNC_MS) return;
    net.lastSend = now;
    const cs = [];
    for (let i = 0; i < activeN; i++){
      const c = chasers[i];
      const item = { st:c.state, s:r2(c.s), x:r2(c.x), v:r2(c.v), yw:r2(c.yaw), ph:r2(c.phase), cl:r2(c.cool) };
      if (c.state === 'windup') item.ck = r2(c.crouchK);
      if (c.state === 'rag' || c.state === 'getup'){
        const arr = new Array(NP * 3);
        for (let j = 0; j < NP * 3; j++) arr[j] = r2(c.p[j]);
        item.p = arr;
      }
      cs.push(item);
    }
    sendMsg({
      t:'snap', g:game, oc:outcome, md:r2(maxDist), pt:r2(playTime),
      ch:{ s:r2(cheese.s), x:r2(cheese.x), y:r2(cheese.y), v:r2(cheese.v), vx:r2(cheese.vx), tl:r2(cheese.tilt), h:cheese.held ? cheese.held.idx : -1, hd:cheese.hand },
      cs,
    });
  }

  function sendGuestInput(force){
    if (!net.connected || net.role !== 'guest' || game !== 'playing') return;
    const now = performance.now();
    if (!force && !diveRequested && now - net.lastSend < SYNC_MS) return;
    net.lastSend = now;
    const inp = input();
    sendMsg({ t:'in', x:r2(inp.x), y:r2(inp.y), d:diveRequested });
    diveRequested = false;
  }

  function sendLobbyPreview(){
    if (!net.connected || net.role !== 'host' || net.matchKind !== 'room') return;
    sendMsg({ t:'lobby', cfg:{ ...userCfg } });
  }

  function handlePeerMessage(msg){
    if (!msg || typeof msg !== 'object') return;
    if (msg.t === 'hello' && net.role === 'host'){
      sendMsg({ t:'welcome', cfg:{ ...userCfg } });
      return;
    }
    if (msg.t === 'welcome' && net.role === 'guest'){
      if (net.joinTimer){ clearTimeout(net.joinTimer); net.joinTimer = null; }
      if (net.helloTimer){ clearInterval(net.helloTimer); net.helloTimer = null; }
      net.connected = true;
      if (msg.cfg) Object.assign(userCfg, msg.cfg, { mode:'online', onlineType:'room' });
      setNetStatus('ok', `接続完了！（部屋: ${net.room}）ホストが開始するのを待っています`);
      syncMenuUI();
      return;
    }
    if (msg.t === 'in' && net.role === 'host'){
      net.remoteInput.x = clamp(Number(msg.x) || 0, -1, 1);
      net.remoteInput.y = clamp(Number(msg.y) || 0, -1, 1);
      if (msg.d) net.remoteInput.dive = true;
    } else if (msg.t === 'lobby' && net.role === 'guest'){
      Object.assign(userCfg, msg.cfg, { mode:'online' });
      syncMenuUI();
    } else if (msg.t === 'start' && net.role === 'guest'){
      if (net.joinTimer){ clearTimeout(net.joinTimer); net.joinTimer = null; }
      if (net.helloTimer){ clearInterval(net.helloTimer); net.helloTimer = null; }
      net.connected = true;
      Object.assign(userCfg, msg.cfg, { mode:'online' });
      startMatch(true, msg.seed, msg.cfg.hostRole === 'cheese' ? 'human' : 'cheese');
    } else if (msg.t === 'snap' && net.role === 'guest'){
      net.snap = msg;
    } else if (msg.t === 'menu' && net.role === 'guest'){
      setupWorld(false);
    }
  }

  function bindActiveConnection(conn){
    net.conn = conn;
    conn.on('data', handlePeerMessage);
    conn.on('close', () => {
      if (net.conn !== conn) return;
      const wasPlaying = game !== 'menu';
      net.connected = false; net.conn = null; net.snap = null;
      clearNetTimers();
      if (net.matchKind === 'free'){
        disconnectNet(true);
        setNetStatus('err', '相手との接続が切れました。もう一度探せます');
      } else if (net.role === 'host'){
        setNetStatus('wait', `相手が退出しました。部屋「${net.room}」で待機中…`);
      } else {
        disconnectNet(true);
        setNetStatus('err', 'ホストとの接続が切れました');
      }
      if (wasPlaying) setupWorld(false);
      else syncMenuUI();
    });
    conn.on('error', () => { setNetStatus('err', '通信エラーが発生しました'); syncMenuUI(); });
  }

  function disconnectNet(silent){
    net.searchGen++; clearNetTimers();
    if (net.conn){ try { net.conn.close(); } catch {} net.conn = null; }
    if (net.peer){ try { net.peer.destroy(); } catch {} net.peer = null; }
    net.role = 'none'; net.searching = false; net.connected = false; net.room = ''; net.snap = null;
    if (!silent){
      setNetStatus('', userCfg.onlineType === 'free'
        ? 'コースを選んでマッチングを開始してください'
        : '未接続：合言葉を決めて部屋を作るか入ってください');
    }
    syncMenuUI();
  }

  // --- 全国フリーマッチ ---
  function startFreeMatch(){
    if (typeof Peer === 'undefined'){ setNetStatus('err', 'PeerJSの読み込みに失敗しました（ネット接続を確認）'); return; }
    disconnectNet(true);
    const gen = ++net.searchGen, course = userCfg.onlineCourse;
    Object.assign(userCfg, FREE_RULES[course], { mode:'online', onlineType:'free' });
    net.matchKind = 'free'; net.searching = true;
    setNetStatus('wait', `全国の対戦相手を探しています…（${course === 'board' ? '板よけ' : '通常'}コース）`);
    syncMenuUI();
    tryFreeSlot(0, course, gen);
  }

  function tryFreeSlot(idx, course, gen){
    if (gen !== net.searchGen) return;
    if (idx >= FREE_SLOTS){
      setNetStatus('wait', '空き部屋が混み合っています… 再探索中');
      net.startTimer = setTimeout(() => tryFreeSlot(0, course, gen), 1500);
      return;
    }
    const slotId = `${FREE_PREFIX}${course}-${idx}`;
    setNetStatus('wait', `全国の対戦相手を探しています…（エリア ${idx + 1}）`);
    const hostPeer = new Peer(slotId, PEER_OPTS);
    net.peer = hostPeer;
    let decided = false;

    hostPeer.on('open', () => {
      if (gen !== net.searchGen){ try{ hostPeer.destroy(); }catch{} return; }
      decided = true; net.role = 'host'; net.room = `全国 #${idx + 1}`;
      setNetStatus('wait', `待機中（エリア ${idx + 1}）… 全国の誰かが参加すると自動で始まります`);
      syncMenuUI();
    });
    hostPeer.on('disconnected', () => { if (gen === net.searchGen && !hostPeer.destroyed){ try { hostPeer.reconnect(); } catch {} } });
    hostPeer.on('connection', conn => {
      if (gen !== net.searchGen){ try{ conn.close(); }catch{} return; }
      if (net.conn){
        const sendBusy = () => { try{ conn.send({ t:'busy' }); }catch{} setTimeout(() => { try{ conn.close(); }catch{} }, 250); };
        if (conn.open) sendBusy(); else conn.on('open', sendBusy);
        return;
      }
      net.conn = conn;
      const onAccepted = () => {
        if (gen !== net.searchGen || net.conn !== conn || net.connected) return;
        net.connected = true; net.searching = false;
        net.remoteInput = { x:0, y:0, dive:false };
        bindActiveConnection(conn);
        userCfg.hostRole = Math.random() < 0.5 ? 'cheese' : 'human';
        const myLabel = userCfg.hostRole === 'cheese' ? 'チーズ役（逃げる）' : '人間役（追う）';
        sendMsg({ t:'free_accept', guestRole: userCfg.hostRole === 'cheese' ? 'human' : 'cheese' });
        setNetStatus('ok', `マッチング成立！あなたは【${myLabel}】です。間もなく開始…`);
        syncMenuUI();
        net.startTimer = setTimeout(() => {
          if (gen !== net.searchGen || !net.connected) return;
          const seed = Math.floor(Math.random() * 1e6);
          const matchCfg = { ...userCfg, ...FREE_RULES[course], mode:'online', onlineType:'free', hostRole:userCfg.hostRole };
          sendMsg({ t:'start', seed, cfg:matchCfg });
          startMatch(true, seed, userCfg.hostRole);
        }, 1100);
      };
      if (conn.open) onAccepted(); else conn.on('open', onAccepted);
      conn.on('close', () => { if (!net.connected && net.conn === conn) net.conn = null; });
    });
    hostPeer.on('error', err => {
      if (gen !== net.searchGen) return;
      if (!decided && err.type === 'unavailable-id'){
        decided = true; try { hostPeer.destroy(); } catch {} net.peer = null;
        probeFreeSlotAsGuest(idx, course, gen);
      } else if (!decided){
        decided = true; try { hostPeer.destroy(); } catch {} net.peer = null;
        net.startTimer = setTimeout(() => tryFreeSlot(idx + 1, course, gen), 400);
      }
    });
  }

  function probeFreeSlotAsGuest(idx, course, gen){
    if (gen !== net.searchGen) return;
    const slotId = `${FREE_PREFIX}${course}-${idx}`;
    const guestPeer = new Peer(PEER_OPTS);
    net.peer = guestPeer;
    let settled = false;
    const failAndNext = () => {
      if (settled || gen !== net.searchGen) return;
      settled = true; clearTimeout(timer);
      try { guestPeer.destroy(); } catch {}
      if (net.peer === guestPeer) net.peer = null;
      tryFreeSlot(idx + 1, course, gen);
    };
    const timer = setTimeout(failAndNext, 4000);

    guestPeer.on('open', () => {
      if (settled || gen !== net.searchGen){ try{ guestPeer.destroy(); }catch{} return; }
      const conn = guestPeer.connect(slotId, { reliable:true, serialization:'json' });
      conn.on('data', msg => {
        if (gen !== net.searchGen || settled) return;
        if (msg && msg.t === 'busy'){ failAndNext(); return; }
        if (msg && (msg.t === 'free_accept' || msg.t === 'start')){
          settled = true; clearTimeout(timer);
          net.role = 'guest'; net.room = `全国 #${idx + 1}`; net.connected = true; net.searching = false;
          bindActiveConnection(conn);
          if (msg.t === 'free_accept'){
            const myLabel = msg.guestRole === 'cheese' ? 'チーズ役（逃げる）' : '人間役（追う）';
            setNetStatus('ok', `マッチング成立！あなたは【${myLabel}】です。間もなく開始…`);
            syncMenuUI();
          } else handlePeerMessage(msg);
        }
      });
      conn.on('close', () => { if (!settled) failAndNext(); });
      conn.on('error', () => { if (!settled) failAndNext(); });
    });
    guestPeer.on('error', () => { if (!settled) failAndNext(); });
  }

  // --- 合言葉マッチ ---
  function createRoom(){
    if (typeof Peer === 'undefined'){ setNetStatus('err', 'PeerJSの読み込みに失敗しました（ネット接続を確認）'); return; }
    let displayCode = cleanRoomText($('roomCode').value);
    if (!displayCode){ displayCode = String(Math.floor(1000 + Math.random() * 9000)); $('roomCode').value = displayCode; }
    const peerKey = encodeRoomId(displayCode);
    disconnectNet(true);
    const gen = ++net.searchGen;
    net.matchKind = 'room'; net.role = 'host'; net.room = displayCode;
    setNetStatus('wait', `部屋「${displayCode}」を作成中…`);
    syncMenuUI();

    const peer = new Peer(ROOM_PREFIX + peerKey, PEER_OPTS);
    net.peer = peer;
    peer.on('open', () => {
      if (gen !== net.searchGen) return;
      setNetStatus('wait', `部屋「${displayCode}」を作成しました！相手に同じ合言葉で入ってもらってください`);
      syncMenuUI();
    });
    peer.on('disconnected', () => { if (gen === net.searchGen && !peer.destroyed){ try { peer.reconnect(); } catch {} } });
    peer.on('connection', conn => {
      if (gen !== net.searchGen){ try{ conn.close(); }catch{} return; }
      if (net.conn && net.conn.open){
        const reject = () => { try{ conn.send({ t:'busy' }); }catch{} setTimeout(() => { try{ conn.close(); }catch{} }, 250); };
        if (conn.open) reject(); else conn.on('open', reject);
        return;
      }
      bindActiveConnection(conn);
      const onHostReady = () => {
        if (gen !== net.searchGen || net.conn !== conn) return;
        net.connected = true;
        net.remoteInput = { x:0, y:0, dive:false };
        setNetStatus('ok', `相手が入室しました！（部屋: ${net.room}）「対戦スタート」を押してください`);
        sendMsg({ t:'welcome', cfg:{ ...userCfg } });
        syncMenuUI();
      };
      if (conn.open) onHostReady(); else conn.on('open', onHostReady);
    });
    peer.on('error', err => {
      if (gen !== net.searchGen) return;
      if (err.type === 'unavailable-id'){
        setNetStatus('err', `合言葉「${displayCode}」は使用中です。「ランダム」で別の合言葉にしてください`);
      } else {
        setNetStatus('err', `エラー(${err.type})：通信環境を確認してもう一度お試しください`);
      }
      disconnectNet(true);
    });
  }

  function joinRoom(){
    if (typeof Peer === 'undefined'){ setNetStatus('err', 'PeerJSの読み込みに失敗しました（ネット接続を確認）'); return; }
    const displayCode = cleanRoomText($('roomCode').value);
    if (!displayCode){ setNetStatus('err', '相手の合言葉を入力してください'); return; }
    const peerKey = encodeRoomId(displayCode);
    disconnectNet(true);
    const gen = ++net.searchGen;
    net.matchKind = 'room'; net.role = 'guest'; net.room = displayCode;
    setNetStatus('wait', `サーバーに接続中…（合言葉: ${displayCode}）`);
    syncMenuUI();

    const peer = new Peer(PEER_OPTS);
    net.peer = peer;
    net.joinTimer = setTimeout(() => {
      if (gen !== net.searchGen || net.connected) return;
      disconnectNet(true);
      setNetStatus('err', '接続がタイムアウトしました。相手が先に「部屋を作る」を押しているか、Wi-Fi制限がないか確認してください');
      syncMenuUI();
    }, 12000);

    peer.on('open', () => {
      if (gen !== net.searchGen) return;
      setNetStatus('wait', `部屋「${displayCode}」のホストへ接続中…`);
      const conn = peer.connect(ROOM_PREFIX + peerKey, { reliable:true, serialization:'json' });
      bindActiveConnection(conn);
      conn.on('data', msg => {
        if (msg && msg.t === 'busy' && gen === net.searchGen){
          disconnectNet(true); setNetStatus('err', `部屋「${displayCode}」は満員です`); syncMenuUI();
        }
      });
      const onGuestOpen = () => {
        if (gen !== net.searchGen || net.conn !== conn) return;
        setNetStatus('wait', `部屋「${displayCode}」と通信確認中…`);
        const sendHello = () => {
          if (gen !== net.searchGen || net.connected || !conn.open) return;
          try { conn.send({ t:'hello' }); } catch {}
        };
        sendHello();
        let tries = 0;
        net.helloTimer = setInterval(() => {
          if (++tries > 6 || net.connected || gen !== net.searchGen){ clearInterval(net.helloTimer); net.helloTimer = null; return; }
          sendHello();
        }, 600);
      };
      if (conn.open) onGuestOpen(); else conn.on('open', onGuestOpen);
      conn.on('error', () => {
        if (gen !== net.searchGen) return;
        disconnectNet(true);
        setNetStatus('err', 'P2P接続に失敗しました。スマホ回線など別のネットワークでお試しください');
        syncMenuUI();
      });
    });
    peer.on('error', err => {
      if (gen !== net.searchGen) return;
      disconnectNet(true);
      if (err.type === 'peer-unavailable'){
        setNetStatus('err', `部屋「${displayCode}」が見つかりません。相手が先に「部屋を作る」を押してから入ってください`);
      } else {
        setNetStatus('err', `接続エラー(${err.type})：通信環境を確認してください`);
      }
      syncMenuUI();
    });
  }

  // ---------- ループ ----------
  let last = performance.now(), acc = 0;
  function loop(now){
    const dt = Math.min((now - last) / 1000, 1 / 30);
    last = now;
    if (game !== 'menu'){
      if (cfg.isOnline && net.role === 'guest'){
        sendGuestInput(false);
        applyGuestInterpolation(dt);
      } else {
        acc += dt * (game === 'playing' ? 1 : SLOW_SCALE);
        while (acc >= STEP){ step(STEP); acc -= STEP; }
        if (game === 'caught'){
          overT += dt;
          if (overT >= (outcome === 'escaped' ? 0.7 : OVER_DELAY)) showOver();
        }
        if (cfg.isOnline && net.role === 'host') sendHostSnapshot(false);
      }
    }
    draw(dt);
    requestAnimationFrame(loop);
  }

  // ---------- 開始・メニュー・結果 ----------
  function startMatch(isOnline, fixedSeed, myRole){
    applyUserConfig(userCfg, isOnline, myRole);
    const startCheeseS = cfg.humanPlayerActive ? 4.5 : 0;
    Object.assign(cheese, { s:startCheeseS, x:0, y:ground(0, startCheeseS) + cfg.cheeseR, v:0, vx:0, vy:0, rot:0, tilt:0, tiltV:0, grounded:true, held:null, hand:0 });
    S0 = cfg.myRole === 'human' ? -3.2 : startCheeseS;
    maxDist = 0; camX = 0; zoom = 0; trail.length = 0; trailK = 0; overT = 0; acc = 0; playTime = 0;
    shownHudKey = ''; diveRequested = false; outcome = 'caught';
    net.remoteInput = { x:0, y:0, dive:false }; net.snap = null;
    currentBest = getBest(cfg.mode); endJoy();
    activeN = cfg.chStart;
    chasers.forEach((c, i) => {
      c.active = i < cfg.chStart; c.isPlayer = cfg.humanPlayerActive && i === 0;
      if (c.active) resetChaser(c, i, true);
    });
    for (const ch of chunks) ch.k = null;
    clearObstacles(fixedSeed); updateObstacles();

    $('resultOverlay').classList.add('hidden');
    game = 'playing';
    $('menuOverlay').classList.add('hidden');
    $('hudWrap').classList.remove('hidden');
    $('menuBtn').classList.remove('hidden');
    $('diveBtn').classList.toggle('hidden', cfg.myRole !== 'human');
  }

  function setupWorld(startPlaying){
    if (startPlaying){
      if (userCfg.mode === 'online'){
        if (!net.connected || net.role !== 'host') return;
        const seed = Math.floor(Math.random() * 1e6);
        sendMsg({ t:'start', seed, cfg:{ ...userCfg } });
        startMatch(true, seed, userCfg.hostRole);
        return;
      }
      startMatch(false, undefined, userCfg.mode === 'human' ? 'human' : 'cheese');
      return;
    }
    const previewRole = userCfg.mode === 'online'
      ? (net.role === 'guest' ? (userCfg.hostRole === 'cheese' ? 'human' : 'cheese') : userCfg.hostRole)
      : (userCfg.mode === 'human' ? 'human' : 'cheese');
    applyUserConfig(userCfg, userCfg.mode === 'online', previewRole);
    const startCheeseS = cfg.humanPlayerActive ? 4.5 : 0;
    Object.assign(cheese, { s:startCheeseS, x:0, y:ground(0, startCheeseS) + cfg.cheeseR, v:0, vx:0, vy:0, rot:0, tilt:0, tiltV:0, grounded:true, held:null, hand:0 });
    S0 = cfg.myRole === 'human' ? -3.2 : 0;
    maxDist = 0; camX = 0; zoom = 0; trail.length = 0; trailK = 0; overT = 0; acc = 0; playTime = 0;
    shownHudKey = ''; diveRequested = false; endJoy();
    activeN = cfg.chStart;
    chasers.forEach((c, i) => { c.active = i < cfg.chStart; if (c.active) resetChaser(c, i, true); });
    for (const ch of chunks) ch.k = null;
    clearObstacles(); updateObstacles();

    game = 'menu';
    $('resultOverlay').classList.add('hidden');
    $('menuOverlay').classList.remove('hidden');
    $('hudWrap').classList.add('hidden');
    $('menuBtn').classList.add('hidden');
    $('diveBtn').classList.add('hidden');
    syncMenuUI();
  }

  function showOver(){
    game = 'over';
    $('diveBtn').classList.add('hidden');
    if (cfg.isOnline && net.role === 'host') sendHostSnapshot(true);

    const d = Math.max(1, Math.floor(maxDist));
    $('resMode').textContent = cfg.isOnline
      ? `${cfg.onlineType === 'free' ? '全国フリーマッチ' : '合言葉対戦'}（${cfg.course === 'board' ? '板よけ' : '通常'}コース）`
      : PRESETS[cfg.mode].label;

    const titleEl = $('resTitle');
    let isNew = false;

    if (cfg.isOnline){
      titleEl.classList.remove('hidden');
      const cheeseWon = outcome === 'escaped', iAmCheese = cfg.myRole === 'cheese';
      const iWon = (iAmCheese && cheeseWon) || (!iAmCheese && !cheeseWon);
      titleEl.textContent = iWon ? 'あなたの勝ち！' : '相手の勝ち…';
      titleEl.classList.toggle('fail', !iWon);
      if (cheeseWon){
        $('dist').textContent = '逃げ切り！';
        $('resBest').textContent = `チーズが ${ESCAPE_LIMIT_ONLINE}m 引き離した（${d}m地点 / ${playTime.toFixed(1)}秒）`;
      } else {
        $('dist').textContent = `${d} m`;
        $('resBest').textContent = `${outcome === 'rival_win' ? 'AI追っ手が捕獲' : '人間プレイヤーが捕獲'}（タイム ${playTime.toFixed(1)} 秒）`;
      }
      const isHost = net.role === 'host' && net.connected, isFree = cfg.onlineType === 'free';
      $('nextFreeBtn').classList.toggle('hidden', !isFree);
      $('swapRetryBtn').classList.toggle('hidden', !isHost);
      $('retryBtn').classList.toggle('hidden', !isHost || isFree);
      $('retryBtn').textContent = 'もう一回';
      $('toMenuBtn').textContent = 'ロビーへ';
    } else if (cfg.mode === 'human'){
      titleEl.classList.remove('hidden');
      if (outcome === 'human_win'){
        titleEl.textContent = 'チーズ捕獲成功！'; titleEl.classList.remove('fail');
        isNew = saveBest('human', d); $('dist').textContent = `${d} m`;
      } else {
        titleEl.textContent = outcome === 'rival_win' ? 'ライバルに先を越された…' : 'チーズに逃げられた…';
        titleEl.classList.add('fail'); $('dist').textContent = '失敗';
      }
      $('resBest').textContent = `BEST ${formatBest('human', getBest('human'))}（タイム ${playTime.toFixed(1)} 秒）`;
      $('nextFreeBtn').classList.add('hidden'); $('retryBtn').classList.remove('hidden');
      $('swapRetryBtn').classList.add('hidden'); $('toMenuBtn').textContent = 'モード・設定';
    } else {
      titleEl.classList.add('hidden');
      isNew = saveBest(cfg.mode, d); $('dist').textContent = `${d} m`;
      $('resBest').textContent = `BEST ${formatBest(cfg.mode, getBest(cfg.mode))}`;
      $('nextFreeBtn').classList.add('hidden'); $('retryBtn').classList.remove('hidden');
      $('swapRetryBtn').classList.add('hidden'); $('toMenuBtn').textContent = 'モード・設定';
    }
    $('resNewBest').classList.toggle('hidden', !isNew);
    $('resultOverlay').classList.remove('hidden');
  }

  // ---------- メニューUI連動 ----------
  function syncMenuUI(){
    const isOnline = userCfg.mode === 'online', isFree = isOnline && userCfg.onlineType === 'free';
    const isRoom = isOnline && userCfg.onlineType === 'room', isHuman = userCfg.mode === 'human';
    const isBoard = isOnline ? userCfg.onlineCourse === 'board' : userCfg.mode === 'board';
    const isGuest = isOnline && net.role === 'guest', busyNet = net.searching || net.role !== 'none';

    document.querySelectorAll('.mode-btn').forEach(b => b.classList.toggle('active', b.dataset.mode === userCfg.mode));
    $('modeHint').textContent = PRESETS[userCfg.mode].hint;
    $('bestBox').classList.toggle('hidden', isOnline);
    $('onlineBox').classList.toggle('hidden', !isOnline);

    if (!isOnline){
      $('menuBestLabel').textContent = `${PRESETS[userCfg.mode].label} ベスト`;
      $('menuBestVal').textContent = formatBest(userCfg.mode, getBest(userCfg.mode));
    } else {
      document.querySelectorAll('[data-match]').forEach(b => { b.classList.toggle('active', b.dataset.match === userCfg.onlineType); b.disabled = busyNet; });
      document.querySelectorAll('[data-course]').forEach(b => { b.classList.toggle('active', b.dataset.course === userCfg.onlineCourse); b.disabled = isFree ? busyNet : isGuest; });
      $('freeMatchUI').classList.toggle('hidden', !isFree);
      $('roomMatchUI').classList.toggle('hidden', !isRoom);
      if (isFree){
        $('freeSearchBtn').classList.toggle('hidden', busyNet);
        $('freeCancelBtn').classList.toggle('hidden', !busyNet);
      } else {
        $('connectForm').classList.toggle('hidden', net.role !== 'none');
        $('connectedControls').classList.toggle('hidden', net.role === 'none');
        const hostReady = net.role === 'host' && net.connected;
        $('roomStartBtn').classList.toggle('hidden', !hostReady);
        $('copyInviteBtn').classList.toggle('hidden', net.role !== 'host' || net.connected);
        $('roleToggleLabel').textContent = isGuest
          ? `あなたの役割：${userCfg.hostRole === 'cheese' ? '人間役（追う）' : 'チーズ役（逃げる）'}（ホストが設定）`
          : 'ホスト（あなた）の役割（相手は逆の役割）';
        document.querySelectorAll('[data-role]').forEach(b => { b.classList.toggle('active', b.dataset.role === userCfg.hostRole); b.disabled = isGuest; });
      }
    }

    $('settingsArea').classList.toggle('hidden', isFree);
    $('bottomStartRow').classList.toggle('hidden', isFree);

    const chInput = $('optChasers');
    if (isRoom){
      chInput.min = '0'; chInput.max = '30'; userCfg.chasers = clamp(userCfg.chasers, 0, 30);
      $('lblChasers').textContent = '追加AI追っ手（0人で完全1vs1）';
    } else if (!isFree){
      chInput.min = '1'; chInput.max = '300'; userCfg.chasers = clamp(userCfg.chasers, 1, 300);
      $('lblChasers').textContent = isHuman ? '参加人数（自分含む）' : '追っ手の人数';
    }
    $('lblVx').textContent = isHuman ? '自分の横移動の速さ' : '横移動の速さ';
    $('lblChSpeed').textContent = (isHuman || isOnline) ? '人間の走る速さ' : '追っ手の速さ';

    $('optSpeed').value = userCfg.speed; $('valSpeed').textContent = `${userCfg.speed} m/s`;
    $('optVx').value = userCfg.vxMax; $('valVx').textContent = `${userCfg.vxMax} m/s`;
    chInput.value = userCfg.chasers; $('valChasers').textContent = `${userCfg.chasers} 人`;
    $('optChSpeed').value = userCfg.chSpeed; $('valChSpeed').textContent = `${userCfg.chSpeed.toFixed(2).replace(/\.00$/, '.0')} 倍`;
    $('optBoardGap').value = userCfg.boardGap; $('valBoardGap').textContent = `${userCfg.boardGap} m`;
    $('optCamUp').value = userCfg.camUp; $('valCamUp').textContent = `${userCfg.camUp} m`;

    $('rowBoardGap').classList.toggle('hidden', !isBoard);
    $('settingsArea').querySelectorAll('input').forEach(inp => { inp.disabled = isGuest; });
    $('presetResetBtn').disabled = isGuest;

    const startBtn = $('startBtn');
    if (isRoom){
      if (net.role === 'host' && net.connected){ startBtn.disabled = false; startBtn.textContent = '対戦スタート'; }
      else if (net.role === 'guest' && net.connected){ startBtn.disabled = true; startBtn.textContent = 'ホストの開始待ち…'; }
      else { startBtn.disabled = true; startBtn.textContent = '接続すると開始可能'; }
    } else if (!isFree){ startBtn.disabled = false; startBtn.textContent = 'スタート'; }
  }

  document.querySelectorAll('.mode-btn').forEach(btn => {
    btn.addEventListener('click', () => {
      const m = btn.dataset.mode;
      if (userCfg.mode === 'online' && m !== 'online' && (net.role !== 'none' || net.searching)) disconnectNet(false);
      Object.assign(userCfg, { mode:m, ...PRESETS[m] });
      if (m === 'online'){
        userCfg.onlineCourse = 'normal';
        if (userCfg.onlineType === 'free') Object.assign(userCfg, FREE_RULES.normal);
        setNetStatus('', userCfg.onlineType === 'free'
          ? 'コースを選んでマッチングを開始してください'
          : '未接続：合言葉を決めて部屋を作るか入ってください');
      }
      setupWorld(false);
    });
  });

  document.querySelectorAll('[data-match]').forEach(btn => {
    btn.addEventListener('click', () => {
      if (net.searching || net.role !== 'none') return;
      userCfg.onlineType = btn.dataset.match;
      if (userCfg.onlineType === 'free'){
        Object.assign(userCfg, FREE_RULES[userCfg.onlineCourse]);
        setNetStatus('', 'コースを選んでマッチングを開始してください');
      } else setNetStatus('', '未接続：合言葉を決めて部屋を作るか入ってください');
      setupWorld(false);
    });
  });

  document.querySelectorAll('[data-role]').forEach(btn => {
    btn.addEventListener('click', () => { userCfg.hostRole = btn.dataset.role; syncMenuUI(); sendLobbyPreview(); });
  });

  document.querySelectorAll('[data-course]').forEach(btn => {
    btn.addEventListener('click', () => {
      const c = btn.dataset.course;
      userCfg.onlineCourse = c;
      if (userCfg.onlineType === 'free') Object.assign(userCfg, FREE_RULES[c]);
      else if (c === 'board'){ userCfg.speed = 45; userCfg.vxMax = 24; userCfg.boardGap = 28; userCfg.camUp = 36; }
      else { userCfg.speed = 9.5; userCfg.vxMax = 5.0; userCfg.camUp = 6; }
      setupWorld(false); sendLobbyPreview();
    });
  });

  $('freeSearchBtn').addEventListener('click', startFreeMatch);
  $('freeCancelBtn').addEventListener('click', () => disconnectNet(false));
  $('randCodeBtn').addEventListener('click', () => { $('roomCode').value = String(Math.floor(1000 + Math.random() * 9000)); });
  $('hostBtn').addEventListener('click', createRoom);
  $('joinBtn').addEventListener('click', joinRoom);
  $('disconnectBtn').addEventListener('click', () => disconnectNet(false));
  $('roomStartBtn').addEventListener('click', () => setupWorld(true));

  $('copyInviteBtn').addEventListener('click', async () => {
    const code = net.room || cleanRoomText($('roomCode').value);
    if (!code) return;
    const isWeb = location.protocol === 'http:' || location.protocol === 'https:';
    const text = isWeb ? `${location.origin}${location.pathname}?room=${encodeURIComponent(code)}` : code;
    try {
      await navigator.clipboard.writeText(text);
      const b = $('copyInviteBtn'), old = b.textContent;
      b.textContent = isWeb ? 'URLコピー完了!' : '合言葉コピー完了!';
      setTimeout(() => { b.textContent = old; }, 1600);
    } catch { prompt(isWeb ? 'このURLを相手に送ってください' : 'この合言葉を相手に伝えてください', text); }
  });

  const onSettingChange = () => { syncMenuUI(); sendLobbyPreview(); };
  $('optSpeed').addEventListener('input', e => { userCfg.speed = parseFloat(e.target.value); onSettingChange(); });
  $('optVx').addEventListener('input', e => { userCfg.vxMax = parseFloat(e.target.value); onSettingChange(); });
  $('optChasers').addEventListener('input', e => { userCfg.chasers = parseInt(e.target.value, 10); onSettingChange(); });
  $('optChSpeed').addEventListener('input', e => { userCfg.chSpeed = parseFloat(e.target.value); onSettingChange(); });
  $('optBoardGap').addEventListener('input', e => { userCfg.boardGap = parseInt(e.target.value, 10); onSettingChange(); });
  $('optCamUp').addEventListener('input', e => { userCfg.camUp = parseInt(e.target.value, 10); cfg.camUp = userCfg.camUp; onSettingChange(); });

  $('presetResetBtn').addEventListener('click', () => {
    const m = userCfg.mode;
    Object.assign(userCfg, { mode:m, ...PRESETS[m] });
    if (m === 'online') userCfg.onlineCourse = 'normal';
    setupWorld(false); sendLobbyPreview();
  });

  $('startBtn').addEventListener('click', () => setupWorld(true));
  $('retryBtn').addEventListener('click', () => setupWorld(true));
  $('swapRetryBtn').addEventListener('click', () => {
    userCfg.hostRole = userCfg.hostRole === 'cheese' ? 'human' : 'cheese';
    sendLobbyPreview(); setupWorld(true);
  });
  $('nextFreeBtn').addEventListener('click', () => { setupWorld(false); startFreeMatch(); });
  $('toMenuBtn').addEventListener('click', () => {
    if (cfg.isOnline){
      if (cfg.onlineType === 'free') disconnectNet(false);
      else if (net.role === 'host') sendMsg({ t:'menu' });
    }
    setupWorld(false);
  });
  $('menuBtn').addEventListener('click', () => {
    if (cfg.isOnline){
      if (cfg.onlineType === 'free' || net.role === 'guest') disconnectNet(false);
      else if (net.role === 'host') sendMsg({ t:'menu' });
    }
    setupWorld(false);
  });

  $('diveBtn').addEventListener('pointerdown', e => {
    e.preventDefault(); e.stopPropagation();
    if (game === 'playing' && cfg.myRole === 'human'){
      diveRequested = true;
      if (cfg.isOnline && net.role === 'guest') sendGuestInput(true);
    }
  });

  // ---------- 操作 ----------
  let dragId = null, joyX0 = 0, joyY0 = 0;
  function input(){
    if (dragId !== null){
      const m = Math.hypot(stick.x, stick.y);
      return m < JOY_DEAD ? { x:0, y:0 } : stick;
    }
    return { x:(keys.right ? 1 : 0) - (keys.left ? 1 : 0), y:(keys.up ? 1 : 0) - (keys.down ? 1 : 0) };
  }
  function endJoy(){ dragId = null; stick.x = stick.y = 0; $('joy').classList.add('hidden'); }
  cv.addEventListener('pointerdown', e => {
    if (game !== 'playing' || dragId !== null) return;
    e.preventDefault(); cv.setPointerCapture(e.pointerId); dragId = e.pointerId;
    const g = $('game').getBoundingClientRect();
    joyX0 = e.clientX - g.left; joyY0 = e.clientY - g.top;
    $('joy').style.left = joyX0 + 'px'; $('joy').style.top = joyY0 + 'px';
    $('knob').style.transform = ''; $('joy').classList.remove('hidden');
  });
  cv.addEventListener('pointermove', e => {
    if (e.pointerId !== dragId) return;
    const g = $('game').getBoundingClientRect();
    let dx = e.clientX - g.left - joyX0, dy = e.clientY - g.top - joyY0;
    const m = Math.hypot(dx, dy);
    if (m > JOY_R){ dx *= JOY_R / m; dy *= JOY_R / m; }
    stick.x = dx / JOY_R; stick.y = -dy / JOY_R;
    $('knob').style.transform = `translate(${dx}px, ${dy}px)`;
  });
  const endDrag = e => { if (e.pointerId === dragId) endJoy(); };
  cv.addEventListener('pointerup', endDrag);
  cv.addEventListener('pointercancel', endDrag);

  document.addEventListener('keydown', e => {
    if (document.activeElement && document.activeElement.tagName === 'INPUT') return;
    if (game === 'over' && (e.key === 'Enter' || e.key === ' ')){ if (!cfg.isOnline){ e.preventDefault(); setupWorld(true); } return; }
    if (game === 'menu' && e.key === 'Enter'){
      if (userCfg.mode !== 'online' || (userCfg.onlineType === 'room' && !$('startBtn').disabled)){ e.preventDefault(); setupWorld(true); }
      return;
    }
    if (game === 'playing' && cfg.myRole === 'human' && (e.key === ' ' || e.code === 'Space')){
      e.preventDefault(); diveRequested = true;
      if (cfg.isOnline && net.role === 'guest') sendGuestInput(true);
      return;
    }
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
    const now = Date.now();
    if (now - lastTouchEnd < 350) e.preventDefault();
    lastTouchEnd = now;
  }, { passive:false });

  // ---------- 画面サイズ ----------
  function resize(){
    const w = $('game').clientWidth, h = $('game').clientHeight;
    renderer.setSize(w, h);
    camera.aspect = w / h;
    const vfov = 2 * Math.atan(Math.tan(CAM_HFOV / 2 * Math.PI / 180) / camera.aspect) * 180 / Math.PI;
    camera.fov = clamp(vfov, 50, cfg.camVfovMax);
    camera.updateProjectionMatrix();
  }
  addEventListener('resize', resize);

  // URLに ?room=xxx が付いていれば合言葉マッチ画面を自動で開く
  const params = new URLSearchParams(location.search);
  const inviteRoom = params.get('room');
  if (inviteRoom){
    Object.assign(userCfg, { mode:'online', onlineType:'room', onlineCourse:'normal', ...PRESETS.online });
    $('roomCode').value = inviteRoom;
    setNetStatus('', `招待された合言葉「${inviteRoom}」が入りました。「部屋に入る」を押してください`);
  } else {
    $('roomCode').value = String(Math.floor(1000 + Math.random() * 9000));
  }

  setupWorld(false);
  requestAnimationFrame(loop);
})();
</script>
</body>
</html>
