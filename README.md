<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no, viewport-fit=cover">
<title>転がりチーズ DX</title>
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
  body{background:var(--bg);color:var(--fg);font-family:"Hiragino Sans","Yu Gothic","Noto Sans JP",system-ui,sans-serif;touch-action:manipulation;-webkit-user-select:none;user-select:none}
  #game{position:fixed;inset:0} canvas{display:block;touch-action:none} .hidden{display:none !important}
  #joy{position:absolute;left:0;top:0;width:120px;height:120px;margin:-60px 0 0 -60px;border-radius:50%;background:rgba(255,255,255,.22);border:2px solid rgba(255,255,255,.7);pointer-events:none}
  #knob{position:absolute;left:50%;top:50%;width:52px;height:52px;margin:-26px 0 0 -26px;border-radius:50%;background:rgba(255,255,255,.9)}
  #hudWrap{position:absolute;left:0;right:0;top:calc(env(safe-area-inset-top,0px)+10px);display:flex;flex-direction:column;align-items:center;pointer-events:none;z-index:4}
  #hud{font-size:26px;font-weight:800;font-variant-numeric:tabular-nums;color:#162618;text-shadow:0 2px 6px rgba(255,255,255,.85)}
  #hudSub{margin-top:3px;font-size:12px;font-weight:700;background:rgba(31,53,36,.85);color:#ffd23f;padding:3px 12px;border-radius:999px}
  #rankBarWrap{margin-top:7px;width:min(320px,78vw);background:rgba(20,36,24,.78);border:1.5px solid rgba(255,255,255,.28);border-radius:999px;padding:6px 12px;box-shadow:0 4px 12px rgba(0,0,0,.22)}
  #rankTrack{position:relative;height:8px;background:rgba(255,255,255,.22);border-radius:999px}
  #rankTrack::after{content:"GOAL";position:absolute;right:-4px;top:-14px;font-size:9px;font-weight:900;color:#ffd23f;letter-spacing:.04em}
  .rank-pin{position:absolute;top:50%;width:12px;height:12px;border-radius:50%;transform:translate(-50%,-50%);border:1.5px solid rgba(0,0,0,.55);transition:left .08s linear}
  .rank-pin.me{width:16px;height:16px;border:2.5px solid #fff;box-shadow:0 0 8px #ffd23f;z-index:3}
  #miniMap{position:absolute;right:12px;top:calc(env(safe-area-inset-top,0px)+74px);width:86px;height:172px;border-radius:12px;border:2px solid rgba(255,255,255,.65);background:rgba(20,36,24,.78);box-shadow:0 6px 18px rgba(0,0,0,.3);pointer-events:none;z-index:4}
  #toast{margin-top:8px;font-size:15px;font-weight:900;padding:5px 14px;border-radius:999px;background:rgba(0,0,0,.78);color:#fff;opacity:0;transform:translateY(-6px);transition:.2s}
  #toast.show{opacity:1;transform:translateY(0)} #toast.boost{background:rgba(0,180,150,.92)} #toast.fall{background:rgba(220,53,69,.92)}
  #menuBtn{position:absolute;left:12px;top:calc(env(safe-area-inset-top,0px)+12px);border:none;border-radius:10px;padding:9px 13px;font-size:12px;font-weight:700;cursor:pointer;background:rgba(31,53,36,.85);color:#fff;z-index:5}
  #diveBtn{position:absolute;right:18px;bottom:calc(env(safe-area-inset-bottom,0px)+24px);width:100px;height:100px;border-radius:50%;border:3px solid rgba(255,255,255,.85);background:rgba(255,210,63,.92);color:#1f3524;font-size:15px;font-weight:900;box-shadow:0 8px 20px rgba(0,0,0,.3);cursor:pointer;z-index:5;display:flex;flex-direction:column;align-items:center;justify-content:center;touch-action:none}
  #diveBtn small{font-size:10px;opacity:.75;margin-top:2px} #diveBtn.disabled{opacity:.45;transform:scale(.95)}
  .overlay{position:fixed;inset:0;display:flex;align-items:center;justify-content:center;padding:10px;background:rgba(15,28,18,.52);backdrop-filter:blur(4px);z-index:10}
  .card{width:100%;max-width:460px;max-height:94vh;overflow-y:auto;background:var(--panel);color:var(--panel-fg);border-radius:18px;padding:15px 14px;box-shadow:0 16px 40px rgba(0,0,0,.45)}
  .card h1{margin:0 0 8px;text-align:center;font-size:20px}
  .top-bar{display:flex;gap:6px;margin-bottom:9px;align-items:center}
  .top-pill{flex:1;border:1.5px solid rgba(255,255,255,.2);border-radius:10px;background:rgba(0,0,0,.25);color:#fff;padding:7px 8px;font-size:11.5px;font-weight:800;cursor:pointer;text-align:center}
  .top-pill.gold{border-color:rgba(255,210,63,.55);color:var(--accent)}
  .modes{display:grid;grid-template-columns:repeat(3,1fr);gap:6px;margin-bottom:8px}
  .mode-btn{border:2px solid rgba(255,255,255,.18);border-radius:10px;padding:7px 4px;cursor:pointer;background:rgba(255,255,255,.06);color:#fff;text-align:center}
  .mode-btn strong{display:block;font-size:11.5px;margin-bottom:2px} .mode-btn span{display:block;font-size:9px;opacity:.82;line-height:1.2}
  .mode-btn.active{border-color:var(--accent);background:rgba(255,210,63,.2);color:var(--accent)}
  .mode-hint{font-size:11.5px;line-height:1.42;color:rgba(255,255,255,.92);background:rgba(255,255,255,.07);border-radius:9px;padding:7px 10px;margin-bottom:8px}
  .best-box{display:flex;justify-content:space-between;align-items:center;background:rgba(0,0,0,.25);border-radius:10px;padding:7px 12px;margin-bottom:8px;font-size:12.5px;font-weight:700}
  .best-box .val{color:var(--accent);font-size:16px;font-variant-numeric:tabular-nums}
  .online-box{background:rgba(0,0,0,.25);border:1px solid rgba(255,255,255,.14);border-radius:12px;padding:10px;margin-bottom:9px}
  .sub-tabs,.toggle-row{display:grid;grid-template-columns:1fr 1fr;gap:6px} .toggle-row.tri{grid-template-columns:repeat(3,1fr)} .sub-tabs{margin-bottom:8px}
  .sub-tab,.seg-btn{border:1.5px solid rgba(255,255,255,.2);border-radius:8px;padding:7px 4px;cursor:pointer;background:rgba(255,255,255,.06);color:#fff;font-size:11.5px;font-weight:700}
  .sub-tab.active{border-color:var(--accent);background:var(--accent);color:var(--accent-fg)}
  .seg-btn.active{border-color:var(--accent);background:rgba(255,210,63,.22);color:var(--accent)}
  .sub-tab:disabled,.seg-btn:disabled{opacity:.45;cursor:not-allowed}
  .status-line{display:flex;align-items:center;gap:8px;font-size:12px;font-weight:700;background:rgba(255,255,255,.06);border-radius:8px;padding:7px 9px;margin-bottom:8px;line-height:1.35}
  .dot{width:10px;height:10px;border-radius:50%;background:#adb5bd;flex-shrink:0} .dot.wait{background:var(--accent);animation:p 1s infinite} .dot.ok{background:var(--ok)} .dot.err{background:var(--danger)}
  @keyframes p{0%,100%{opacity:1}50%{opacity:.35}}
  .chips{display:flex;flex-wrap:wrap;gap:5px;margin:6px 0 8px} .chip{font-size:11px;font-weight:700;padding:3px 9px;border-radius:999px;background:rgba(255,255,255,.12)} .chip.me{background:var(--accent);color:var(--accent-fg)}
  .room-row{display:flex;gap:6px;margin-bottom:8px}
  .room-input{flex:1;min-width:0;border:1px solid rgba(255,255,255,.25);border-radius:8px;background:rgba(255,255,255,.1);color:#fff;padding:8px 10px;font-size:14px;font-weight:700;-webkit-user-select:text;user-select:text;outline:none}
  .mini-btn{border:none;border-radius:8px;padding:0 10px;font-size:12px;font-weight:700;cursor:pointer;background:var(--muted);color:#fff}
  .toggle-group{margin-top:8px} .toggle-label{font-size:11px;font-weight:700;opacity:.85;margin-bottom:4px;display:block}
  .settings{display:flex;flex-direction:column;gap:6px;margin-bottom:11px}
  .setting-row{background:rgba(255,255,255,.06);border-radius:9px;padding:6px 10px}
  .setting-row.locked{opacity:.72}
  .setting-head{display:flex;justify-content:space-between;margin-bottom:3px;font-size:12px;font-weight:700} .setting-val{color:var(--accent);font-variant-numeric:tabular-nums}
  input[type=range]{width:100%;accent-color:var(--accent);cursor:pointer;margin:0} input[type=range]:disabled{cursor:not-allowed}
  .btn-row{display:flex;gap:7px;flex-wrap:wrap}
  .btn{flex:1;min-width:100px;border:none;border-radius:10px;padding:11px 10px;font-size:13.5px;font-weight:800;cursor:pointer}
  .btn-primary{background:var(--accent);color:var(--accent-fg)} .btn-sub{background:var(--muted);color:#fff} .btn-danger{background:rgba(255,107,107,.24);color:#ffb3b3} .btn:disabled{opacity:.42;cursor:not-allowed}
  .result-card{text-align:center;max-width:380px} .result-mode{font-size:12px;opacity:.82;margin:0 0 5px;font-weight:700}
  .result-title{font-size:21px;font-weight:900;margin:0 0 6px;color:var(--accent)} .result-title.fail{color:#ff8a80}
  .result-value{font-size:36px;font-weight:900;margin:0 0 6px;font-variant-numeric:tabular-nums} .result-best{font-size:13px;font-weight:700;margin:0 0 10px;color:rgba(255,255,255,.88)}
  .pt-badge{display:inline-block;background:var(--ok);color:#0f2a15;font-size:13px;font-weight:900;padding:4px 12px;border-radius:999px;margin-bottom:8px}
  .new-best{display:inline-block;background:var(--accent);color:var(--accent-fg);font-size:11.5px;font-weight:900;padding:3px 10px;border-radius:999px;margin-bottom:6px}
  .rank-list{list-style:none;margin:0 0 12px;padding:0;text-align:left;display:flex;flex-direction:column;gap:5px;max-height:240px;overflow-y:auto}
  .rank-item{display:flex;justify-content:space-between;align-items:center;padding:6px 10px;border-radius:8px;background:rgba(255,255,255,.08);font-size:12.5px;font-weight:700}
  .rank-item.me{background:rgba(255,210,63,.22);border:1px solid var(--accent);color:var(--accent)}
  .rank-dot{display:inline-block;width:10px;height:10px;border-radius:50%;margin-right:7px;vertical-align:middle}
  .skin-grid{display:grid;grid-template-columns:1fr 1fr;gap:7px;margin-bottom:12px}
  .skin-item{border:1.5px solid rgba(255,255,255,.2);border-radius:10px;padding:9px 8px;background:rgba(255,255,255,.06);color:#fff;cursor:pointer;text-align:left;display:flex;align-items:center;gap:8px}
  .skin-item.equipped{border-color:var(--accent);background:rgba(255,210,63,.22)}
  .skin-swatch{width:28px;height:28px;border-radius:50%;flex-shrink:0;border:2px solid rgba(255,255,255,.6)}
  .skin-meta strong{display:block;font-size:12px} .skin-meta span{display:block;font-size:10.5px;color:var(--accent);font-weight:700}
</style>
</head>
<body>
<div id="game">
  <button id="menuBtn" type="button" class="hidden">設定 / 退出</button>
  <div id="hudWrap" class="hidden">
    <div id="hud">0 m</div>
    <div id="hudSub">BEST 0 m</div>
    <div id="rankBarWrap" class="hidden"><div id="rankTrack"></div></div>
    <div id="toast"></div>
  </div>
  <canvas id="miniMap" width="86" height="172" class="hidden"></canvas>
  <button id="diveBtn" type="button" class="hidden">飛びつく!<small>Space</small></button>
  <div id="joy" class="hidden" aria-hidden="true"><div id="knob"></div></div>

  <!-- タイトル＆設定 -->
  <div id="menuOverlay" class="overlay">
    <div class="card">
      <h1>転がりチーズ DX</h1>
      <div class="top-bar">
        <button id="openShopBtn" type="button" class="top-pill gold">🧀 <span id="ptsDisp">0</span> pt / スキン</button>
        <button id="openLbBtn" type="button" class="top-pill">🏆 500mランキング</button>
      </div>

      <div class="modes">
        <button type="button" class="mode-btn active" data-mode="normal"><strong>通常モード</strong><span>3000m / +5pt</span></button>
        <button type="button" class="mode-btn" data-mode="board"><strong>板よけモード</strong><span>板を高速回避</span></button>
        <button type="button" class="mode-btn" data-mode="human"><strong>人間モード</strong><span>障害物すり抜け</span></button>
        <button type="button" class="mode-btn" data-mode="race"><strong>チーズレース</strong><span>マップ＆順位バー</span></button>
        <button type="button" class="mode-btn" data-mode="ta500"><strong>500mタイム</strong><span>人なし記録挑戦</span></button>
        <button type="button" class="mode-btn" data-mode="online"><strong>オンライン</strong><span>最大6人対戦</span></button>
      </div>
      <div class="mode-hint" id="modeHint"></div>
      <div class="best-box" id="bestBox"><span id="menuBestLabel">ベスト</span><span class="val" id="menuBestVal">---</span></div>

      <!-- 500mタイムアタック用 プレイヤー名 -->
      <div class="online-box hidden" id="ta500Box">
        <span class="toggle-label" style="margin-top:0">ランキング登録名</span>
        <div class="room-row" style="margin-bottom:0">
          <input id="playerNameInput" class="room-input" type="text" maxlength="14" placeholder="プレイヤー名">
          <button id="taViewLbBtn" type="button" class="mini-btn">順位を見る</button>
        </div>
      </div>

      <div class="toggle-group hidden" id="raceCourseBox" style="margin:0 0 9px">
        <span class="toggle-label">レースコース（ノーマル1位で+5pt・距離固定）</span>
        <div class="toggle-row tri">
          <button type="button" class="seg-btn active" data-rcourse="special">スペシャル</button>
          <button type="button" class="seg-btn" data-rcourse="normal">ノーマル(+5pt)</button>
          <button type="button" class="seg-btn" data-rcourse="board">板よけ</button>
        </div>
      </div>

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
          <div class="toggle-row tri">
            <button type="button" class="seg-btn active" data-course="normal">通常</button>
            <button type="button" class="seg-btn" data-course="special">スペシャル</button>
            <button type="button" class="seg-btn" data-course="board">板よけ</button>
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

      <div class="settings" id="settingsArea">
        <div class="setting-row" id="rowGoal"><div class="setting-head"><label for="optGoal">ゴール距離</label><span class="setting-val" id="valGoal">3000 m</span></div><input id="optGoal" type="range" min="300" max="3000" step="100"></div>
        <div class="setting-row" id="rowSpeed"><div class="setting-head"><label for="optSpeed">チーズの速さ</label><span class="setting-val" id="valSpeed">14 m/s</span></div><input id="optSpeed" type="range" min="5" max="120" step="0.5"></div>
        <div class="setting-row" id="rowVx"><div class="setting-head"><label for="optVx" id="lblVx">横移動の速さ</label><span class="setting-val" id="valVx">6 m/s</span></div><input id="optVx" type="range" min="3" max="60" step="0.5"></div>
        <div class="setting-row" id="rowChasers"><div class="setting-head"><label for="optChasers" id="lblChasers">追っ手の人数</label><span class="setting-val" id="valChasers">6 人</span></div><input id="optChasers" type="range" min="1" max="300" step="1"></div>
        <div class="setting-row" id="rowChSpeed"><div class="setting-head"><label for="optChSpeed" id="lblChSpeed">追っ手の速さ</label><span class="setting-val" id="valChSpeed">1.0 倍</span></div><input id="optChSpeed" type="range" min="0.5" max="1.8" step="0.05"></div>
        <div class="setting-row hidden" id="rowBoardGap"><div class="setting-head"><label for="optBoardGap">板の間隔</label><span class="setting-val" id="valBoardGap">25 m</span></div><input id="optBoardGap" type="range" min="10" max="60" step="1"></div>
        <div class="setting-row"><div class="setting-head"><label for="optCamUp">カメラの高さ</label><span class="setting-val" id="valCamUp">6 m</span></div><input id="optCamUp" type="range" min="3" max="70" step="1"></div>
      </div>

      <div class="btn-row" id="bottomStartRow">
        <button id="presetResetBtn" type="button" class="btn btn-sub">初期値</button>
        <button id="startBtn" type="button" class="btn btn-primary">スタート</button>
      </div>
    </div>
  </div>

  <!-- スキンショップ -->
  <div id="shopOverlay" class="overlay hidden">
    <div class="card">
      <h1>チーズスキンショップ</h1>
      <div class="best-box"><span>所持ポイント（ノーマルAI対戦クリアで+5pt）</span><span class="val" id="shopPtsVal">0 pt</span></div>
      <div class="skin-grid" id="skinGrid"></div>
      <div class="btn-row"><button id="closeShopBtn" type="button" class="btn btn-primary">閉じる</button></div>
    </div>
  </div>

  <!-- 500mタイムアタック ランキング -->
  <div id="lbOverlay" class="overlay hidden">
    <div class="card">
      <h1>🏆 500m タイムランキング</h1>
      <p class="mode-hint">人なし・ノーマル500m固定コースの公式記録。オンライン対戦した相手とも自動で記録が合体します！</p>
      <ul class="rank-list" id="lbList"></ul>
      <div class="btn-row" style="margin-bottom:8px">
        <button id="lbExportBtn" type="button" class="btn btn-sub">記録コードをコピー</button>
        <button id="lbImportBtn" type="button" class="btn btn-sub">相手の記録を取込</button>
      </div>
      <div class="btn-row"><button id="closeLbBtn" type="button" class="btn btn-primary">閉じる</button></div>
    </div>
  </div>

  <!-- 結果画面 -->
  <div id="resultOverlay" class="overlay hidden">
    <div class="card result-card">
      <p class="result-mode" id="resMode"></p>
      <p class="result-title hidden" id="resTitle"></p>
      <div class="pt-badge hidden" id="resPts">+5 ポイント獲得！</div>
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

  const MAX_GOAL = 3000, TA_GOAL = 500, TA_SEED = 500500, MAX_ONLINE_PLAYERS = 6, MAX_CHEESES = 6;
  const RACER_COLORS = ['#ffd23f', '#ff595e', '#38bdf8', '#4ade80', '#c084fc', '#fb923c'];
  const ROOM_PREFIX = 'kcheese-r5-', FREE_PREFIX = 'kcheese-f5-', FREE_SLOTS = 12;
  const PEER_OPTS = { debug:0, config:{ iceServers:[{ urls:'stun:stun.l.google.com:19302' },{ urls:'stun:stun1.l.google.com:19302' },{ urls:'stun:stun.cloudflare.com:3478' }] } };

  // ---------- チーズスキン定義 ----------
  const SKINS = [
    { id:'classic',    name:'チェダー（標準）', cost:0,  rind:'#e3a03a', face:'#f3cf72', swatch:'linear-gradient(135deg,#f3cf72,#e3a03a)', style:'solid' },
    { id:'white',      name:'モッツァレラ',     cost:5,  rind:'#e8e4d8', face:'#ffffff', swatch:'linear-gradient(135deg,#fff,#dcd6c8)',    style:'solid' },
    { id:'blue',       name:'ブルーチーズ',     cost:5,  rind:'#c7cfcc', face:'#eef2ec', swatch:'radial-gradient(circle,#3b6b7a 25%,#eef2ec 65%)', style:'blue' },
    { id:'strawberry', name:'いちごチーズ',     cost:10, rind:'#e65c8a', face:'#ffb3c9', swatch:'linear-gradient(135deg,#ffb3c9,#e65c8a)', style:'solid' },
    { id:'matcha',     name:'抹茶チーズ',       cost:10, rind:'#3d6e2e', face:'#84b861', swatch:'linear-gradient(135deg,#84b861,#3d6e2e)', style:'solid' },
    { id:'gold',       name:'ゴールドチーズ',   cost:15, rind:'#d49b00', face:'#fff07a', swatch:'linear-gradient(135deg,#fff49c,#d49b00)', style:'gold' },
    { id:'cyber',      name:'サイバーネオン',   cost:20, rind:'#00f5d4', face:'#141b2d', swatch:'linear-gradient(135deg,#00f5d4,#141b2d)', style:'cyber' },
    { id:'rainbow',    name:'レインボー',       cost:25, rind:'#ff595e', face:'#ffd23f', swatch:'linear-gradient(135deg,#ff595e,#ffd23f,#4ade80,#38bdf8,#c084fc)', style:'rainbow' },
  ];

  // ---------- プレイヤーデータ（ポイント・スキン・500mランキング） ----------
  const DEFAULT_LB = [
    { name:'スピード王AI', time:33.80, skin:'gold' },
    { name:'チーズ職人',   time:35.90, skin:'blue' },
    { name:'牧場の星',     time:38.40, skin:'white' },
    { name:'見習いチーズ', time:42.10, skin:'classic' },
  ];
  const profile = {
    name: 'チーズ' + Math.floor(100 + Math.random() * 900),
    pts: 0,
    owned: ['classic'],
    skin: 'classic',
    lb500: DEFAULT_LB.slice(),
  };
  function loadProfile(){
    try {
      const raw = localStorage.getItem('runawayCheese.v5.profile');
      if (raw){
        const d = JSON.parse(raw);
        if (d.name) profile.name = String(d.name).slice(0, 14);
        if (Number.isFinite(d.pts)) profile.pts = Math.max(0, Math.floor(d.pts));
        if (Array.isArray(d.owned)) profile.owned = Array.from(new Set(['classic', ...d.owned]));
        if (d.skin && profile.owned.includes(d.skin)) profile.skin = d.skin;
        if (Array.isArray(d.lb500) && d.lb500.length) profile.lb500 = d.lb500;
      }
    } catch {}
  }
  function saveProfile(){
    try { localStorage.setItem('runawayCheese.v5.profile', JSON.stringify(profile)); } catch {}
    $('ptsDisp').textContent = profile.pts;
    $('shopPtsVal').textContent = `${profile.pts} pt`;
  }
  function mergeLeaderboard(entries){
    const map = new Map();
    for (const e of [...profile.lb500, ...entries]){
      if (!e || typeof e.name !== 'string' || !Number.isFinite(e.time) || e.time < 5 || e.time > 999) continue;
      const nm = e.name.trim().slice(0, 14) || '名無し';
      const prev = map.get(nm);
      if (!prev || e.time < prev.time) map.set(nm, { name:nm, time:Math.round(e.time * 100) / 100, skin:e.skin || 'classic' });
    }
    profile.lb500 = Array.from(map.values()).sort((a, b) => a.time - b.time).slice(0, 25);
    saveProfile();
  }
  loadProfile();

  const PRESETS = {
    normal: { label:'通常モード', hint:'【ポイント対象・3000m固定】AI追っ手から逃げ切ってゴールすると+5pt獲得！メーターは変更できません。', goalDist:3000, speed:14, vxMax:6, chasers:6, chSpeed:1.0, boardGap:25, camUp:6 },
    board:  { label:'板よけモード', hint:'左右の板を高速で避けて3000mゴールを目指せ！人間側は板をすり抜けます。', goalDist:3000, speed:75, vxMax:32, chasers:60, chSpeed:1.0, boardGap:25, camUp:48 },
    human:  { label:'人間モード（追っ手）', hint:'障害物をすり抜けられます！矢印/WASDで走り、Spaceか右下ボタンで飛びついて捕まえよう。', goalDist:3000, speed:12, vxMax:7, chasers:6, chSpeed:1.05, boardGap:25, camUp:6 },
    race:   { label:'チーズレース', hint:'ミニマップ＆順位バー搭載！スペシャルは穴・ダッシュボード・一本道あり。ノーマル1位クリアで+5pt（距離固定）！', goalDist:3000, speed:22, vxMax:11, chasers:6, chSpeed:1.0, boardGap:28, camUp:11 },
    ta500:  { label:'500mタイムアタック', hint:'人なし・ノーマル500m固定の公式タイム計測！好タイムを出して全プレイヤーランキング上位を目指そう。', goalDist:500, speed:14, vxMax:6.5, chasers:0, chSpeed:1.0, boardGap:25, camUp:6 },
    online: { label:'オンライン対戦', hint:'最大6人の鬼ごっこ＆レース！対戦相手と500mランキングも自動で同期されます。', goalDist:3000, speed:16, vxMax:8, chasers:0, chSpeed:1.05, boardGap:28, camUp:8 },
  };
  const FREE_RULES = {
    chase_normal:  { onlineGame:'chase', onlineCourse:'normal',  goalDist:3000, speed:16, vxMax:8,  chasers:0, chSpeed:1.05, boardGap:28, camUp:8 },
    chase_special: { onlineGame:'chase', onlineCourse:'special', goalDist:3000, speed:18, vxMax:10, chasers:0, chSpeed:1.05, boardGap:28, camUp:11 },
    chase_board:   { onlineGame:'chase', onlineCourse:'board',   goalDist:3000, speed:48, vxMax:28, chasers:0, chSpeed:1.05, boardGap:28, camUp:38 },
    race_normal:   { onlineGame:'race',  onlineCourse:'normal',  goalDist:3000, speed:24, vxMax:11, chasers:6, chSpeed:1.0,  boardGap:28, camUp:10 },
    race_special:  { onlineGame:'race',  onlineCourse:'special', goalDist:3000, speed:22, vxMax:11, chasers:6, chSpeed:1.0,  boardGap:28, camUp:11 },
    race_board:    { onlineGame:'race',  onlineCourse:'board',   goalDist:3000, speed:52, vxMax:30, chasers:6, chSpeed:1.0,  boardGap:28, camUp:38 },
  };

  const userCfg = { mode:'normal', raceCourse:'special', onlineType:'free', onlineGame:'chase', onlineCourse:'normal', hostRole:'cheese', ...PRESETS.normal };
  const cfg = {
    mode:'normal', onlineType:'room', isOnline:false, isRace:false, isTA:false, isPointEligible:false, course:'normal', goalDist:3000,
    myRole:'cheese', myIndex:0, humanPlayerCount:0, halfW:8, xlim:7.8, cheeseR:0.32, cheeseW:0.2,
    gFall:9.8, gAlong:6.556, drag:0.073, turnCost:0.1, vxMax:4.5, latAcc:22, bounce:0.3, bounceMin:1.2, airControl:0.6,
    chStart:6, chMax:12, chAddEvery:100, startDelay:1.0, startGrace:2.0, chSpeedMult:1.0, runAcc:7, chVx:3.2,
    boardGap:25, boardW:20, boardH:3.2, boardThick:1.6, thigh:0.43, shin:0.43, camBack:8, camUp:6, camFollowX:0.75, camVfovMax:65, targetSpeed:14,
  };
  const net = { peer:null, hostConn:null, clients:new Map(), roster:[], myId:'host', role:'none', matchKind:'room', searching:false, connected:false, room:'', searchGen:0, startTimer:null, joinTimer:null, helloTimer:null, lastSend:0, snap:null };

  const SLOPE = 0.9, SLOPE_ANG = Math.atan(SLOPE), CH_ALLOC = 300, SP_LEN = 50;
  const TILT_KICK = 0.35, TILT_SPRING = 30, TILT_DAMP = 4, TILT_DRIFT = 3, JOY_R = 50, JOY_DEAD = 0.12, PUSH_ACC = 0.8, BRAKE_ACC = 6;
  const CATCHUP_DIST = 10, CATCHUP = 3.5, SPEED_VAR = 0.3, DIVE_RANGE = 3.0, DIVE_BACK = 0.5, DIVE_DX = 2.2, WINDUP = 0.2;
  const DIVE_T = 0.42, DIVE_REACH = 0.9, DIVE_SPIN = 2.2, AIM = 0.7, CATCH_WINDOW = DIVE_T + 0.35, CATCH_HEIGHT = 0.95;
  const RAG_MIN = 1.0, RAG_MAX = 2.6, RAG_STILL = 2.5, GETUP_T = 0.6, COOLDOWN = 0.8, RESPAWN_BEHIND = 30, SPAWN_BEHIND = 20;
  const OVER_DELAY = 1.9, SLOW_SCALE = 0.25, ZOOM_SPEED = 3, ZOOM_BACK = 3.0, ZOOM_UP = 1.8, ZOOM_SIDE = 1.3;
  const RAG_ITER = 8, RAG_DAMP = 0.999, RAG_MU = 0.85, RUN_DUTY = 0.28, UPARM = 0.29, FOREARM = 0.27, FOOT_H = 0.07;
  const OB_BAND = 4, OB_START = 25, OB_RATE0 = 0.6, OB_RATE_GROW = 1/500, OB_RATE_MAX = 1, OB_SECOND = 1/600;
  const OB_KIND = [['rock', 0.3], ['bush', 0.25], ['log', 0.25], ['bale', 0.2]], OB_AHEAD = 160, OB_BEHIND = 30;
  const ROCK_VARIANTS = 5, BUSH_VARIANTS = 3, BUSH_SPIKES = 180, BUSH_FLOWERS = 36, OB_BOUNCE = 0.35, OB_LOSS = 0.8, BUSH_DRAG = 6;
  const STEP = 1 / 120, SYNC_MS = 33, TRAIL_FADE = 0.6, TRAIL_N = 30, TRAIL_STEP = 0.16, TRAIL_ALPHA = 0.55, TRAIL_TAPER = 0.6;
  const CHUNK_LEN = 25, CHUNK_SEG_S = 50, TERRAIN_HALF = 28, TERRAIN_SEG_X = 80, OUTSIDE_SHADE = 0.55, CHUNK_COUNT = 9, CHUNK_BEHIND = 20;
  const GRASS_TILE = 2, POST_GAP = 2.5, FOG_NEAR = 55, FOG_FAR = 165, SHADOW_SIZE = 14, CAM_LOOK = -1.5, CAM_HFOV = 60;

  let toastTimer = null;
  function showToast(tx, cls){
    const el = $('toast'); el.textContent = tx; el.className = 'show' + (cls ? ' ' + cls : '');
    if (toastTimer) clearTimeout(toastTimer); toastTimer = setTimeout(() => { el.className = ''; }, 1300);
  }

  const bestKey = m => `runawayCheese.v5.best.${m}`;
  function getBest(m){ if (m === 'online') return null; try { const n = parseFloat(localStorage.getItem(bestKey(m))); return Number.isFinite(n) && n > 0 ? n : null; } catch { return null; } }
  function saveBest(m, val){
    if (m === 'online' || val <= 0) return false;
    const prev = getBest(m), lower = (m === 'human' || m === 'race' || m === 'ta500');
    if (prev === null || (lower ? val < prev : val > prev)){ try { localStorage.setItem(bestKey(m), String(val)); } catch {} return true; }
    return false;
  }
  const formatBest = (m, v) => v === null ? '---' : (m === 'race' || m === 'ta500' ? `最速 ${v.toFixed(2)} 秒` : m === 'human' ? `最短 ${Math.round(v)} m` : `${Math.round(v)} m`);

  // ---------- 地形＆スペシャルコース ----------
  function bump(x, s){
    return 0.5 * Math.sin(0.31 * s + 1.4 * Math.sin(0.043 * s)) * (0.75 + 0.25 * Math.sin(0.37 * x + 0.11 * s))
         + 0.14 * Math.sin(0.83 * s + 0.6 * x + 2.0) + 0.08 * Math.sin(1.37 * s - 0.9 * x + 0.5)
         + 0.12 * Math.sin(0.23 * x - 0.17 * s) + 0.03 * Math.sin(2.1 * s + 1.7 * x);
  }
  const ground = (x, s) => -SLOPE * s + bump(x, s), EPS = 0.04;
  const slopeS = (x, s) => (bump(x, s + EPS) - bump(x, s - EPS)) / (2 * EPS) - SLOPE;
  const slopeX = (x, s) => (bump(x + EPS, s) - bump(x - EPS, s)) / (2 * EPS);
  function rand(seed){ let t = seed >>> 0; return () => { t = (t + 0x6D2B79F5) >>> 0; let r = Math.imul(t ^ (t >>> 15), 1 | t); r ^= r + Math.imul(r ^ (r >>> 7), 61 | r); return ((r ^ (r >>> 14)) >>> 0) / 4294967296; }; }

  const spCache = new Map();
  let obSeed = 1, obNextBand = 0;
  function getSpSection(idx){
    if (cfg.course !== 'special' || idx < 1) return null;
    if (spCache.has(idx)) return spCache.get(idx);
    const s0 = idx * SP_LEN;
    if (s0 > cfg.goalDist - 35){ spCache.set(idx, null); return null; }
    const rnd = rand(obSeed * 9187 + idx * 3571), pattern = idx % 3;
    const sec = { idx, s0, type:'holes', valley:null, bridges:[], holes:[], boosts:[] };
    if (pattern === 2){
      sec.type = 'bridge';
      const vs = s0 + 12, ve = s0 + 38, bx = (rnd() * 2 - 1) * (cfg.halfW - 3.6);
      sec.valley = { s0:vs, s1:ve };
      if (rnd() < 0.5){
        const mid = (vs + ve) * 0.5, bx2 = clamp(bx + (bx > 0 ? -4.5 : 4.5), -cfg.halfW + 3.0, cfg.halfW - 3.0);
        sec.bridges.push({ x:bx, s0:vs - 1.2, s1:mid + 1.8, w:2.3 }, { x:bx2, s0:mid - 1.8, s1:ve + 1.2, w:2.3 });
      } else sec.bridges.push({ x:bx, s0:vs - 1.2, s1:ve + 1.2, w:2.3 });
      sec.boosts.push({ x:bx, s:s0 + 5.5, w:2.6, len:4.2 });
    } else {
      const nH = pattern === 0 ? 2 : 3;
      for (let i = 0; i < nH; i++){
        const hw = 4.6 + rnd() * 2.2, hl = 5.5 + rnd() * 2.5;
        sec.holes.push({ x:(rnd() * 2 - 1) * (cfg.halfW - hw * 0.5 - 1.5), s:s0 + 10 + i * 13, w:hw, len:hl });
      }
      const nP = 1 + (rnd() < 0.65 ? 1 : 0);
      for (let i = 0; i < nP; i++){
        const ps = s0 + 6 + i * 21; let px = (rnd() * 2 - 1) * (cfg.halfW - 2.4);
        for (const h of sec.holes) if (Math.abs(ps - h.s) < h.len * 0.8 + 3 && Math.abs(px - h.x) < h.w * 0.7 + 1.8) px = h.x > 0 ? -cfg.halfW + 3.2 : cfg.halfW - 3.2;
        sec.boosts.push({ x:px, s:ps, w:2.5, len:4.4 });
      }
    }
    spCache.set(idx, sec); return sec;
  }
  function isTerrainVoid(x, s){
    if (cfg.course !== 'special') return false;
    const sec = getSpSection(Math.floor(s / SP_LEN)); if (!sec) return false;
    if (sec.valley && s >= sec.valley.s0 && s <= sec.valley.s1) return true;
    for (const h of sec.holes) if (Math.abs(x - h.x) < h.w * 0.5 && Math.abs(s - h.s) < h.len * 0.5) return true;
    return false;
  }
  function surfaceAt(x, s){
    if (cfg.course === 'special'){
      const sec = getSpSection(Math.floor(s / SP_LEN));
      if (sec){
        for (const b of sec.bridges){
          if (s >= b.s0 && s <= b.s1 && Math.abs(x - b.x) <= b.w * 0.5){
            const t = clamp((s - b.s0) / Math.max(0.1, b.s1 - b.s0), 0, 1);
            return { solid:true, y:lerp(ground(b.x, b.s0), ground(b.x, b.s1), t) + 0.12, onBridge:true };
          }
        }
        if (isTerrainVoid(x, s)) return { solid:false, y:-1e9, onBridge:false };
      }
    }
    return { solid:true, y:ground(x, s), onBridge:false };
  }
  function findSafeRespawn(s){
    const sec = getSpSection(Math.floor(s / SP_LEN));
    if (sec && sec.valley && s >= sec.valley.s0 - 2) return { s:Math.max(2, sec.valley.s0 - 5), x:sec.bridges[0] ? sec.bridges[0].x : 0 };
    for (let back = 7; back <= 24; back += 3){
      const ts = Math.max(2, s - back);
      for (const tx of [0, -3.5, 3.5, -6.5, 6.5]) if (Math.abs(tx) <= cfg.xlim && !isTerrainVoid(tx, ts) && !isTerrainVoid(tx, ts + 2)) return { s:ts, x:tx };
    }
    return { s:Math.max(2, s - 10), x:0 };
  }
  // === パート1 ここまで ===
  // === パート2 ここから ===
  // ---------- 状態＆three.js ----------
  let game = 'menu', outcome = 'caught', winnerIdx = -1, S0 = 0, overT = 0, playTime = 0;
  let activeN = 0, cheeseN = 1, shownHudKey = '', currentBest = null, maxDist = 0, diveRequested = false, raceOrder = [], raceFinishTimer = 0;
  const stick = { x:0, y:0 }, keys = { left:false, right:false, up:false, down:false };
  const cheeses = Array.from({ length:MAX_CHEESES }, (_, i) => ({
    idx:i, s:0, x:0, y:0, v:0, vx:0, vy:0, rot:0, tilt:0, tiltV:0, grounded:true,
    held:null, hand:0, finished:false, finishTime:0, boostT:0, boostCool:0,
    ctrlId:'ai', label:`P${i+1}`, color:RACER_COLORS[i], skin:'classic', aiBias:0, aiSpeedK:1
  }));
  const mainCheese = () => cheeses[0];

  const renderer = new THREE.WebGLRenderer({ antialias:true });
  renderer.setPixelRatio(Math.min(window.devicePixelRatio || 1, 2)); renderer.outputEncoding = THREE.sRGBEncoding;
  renderer.shadowMap.enabled = true; renderer.shadowMap.type = THREE.PCFSoftShadowMap;
  $('game').prepend(renderer.domElement);
  const cv = renderer.domElement, lin = c => new THREE.Color(c).convertSRGBToLinear();
  const scene = new THREE.Scene(); scene.background = new THREE.Color(css('--sky')); scene.fog = new THREE.Fog(new THREE.Color(css('--sky')), FOG_NEAR, FOG_FAR);
  const camera = new THREE.PerspectiveCamera(60, 1, 0.1, 400);
  scene.add(new THREE.HemisphereLight(lin('#d6e8ff'), lin('#55703a'), 0.75));
  const sun = new THREE.DirectionalLight(lin('#fff3e0'), 1.5); sun.castShadow = true; sun.shadow.mapSize.set(2048, 2048);
  Object.assign(sun.shadow.camera, { left:-SHADOW_SIZE, right:SHADOW_SIZE, top:SHADOW_SIZE, bottom:-SHADOW_SIZE, near:1, far:60 });
  sun.shadow.bias = -0.0004; sun.shadow.normalBias = 0.03; scene.add(sun, sun.target);
  const UP = new THREE.Vector3(0, 1, 0), rel = (out, s, x, y) => out.set(x, y + SLOPE * S0, -(s - S0));

  // ---------- テクスチャ＆スキン ----------
  function tileNoise(S, cells, rnd){
    const g = new Float32Array(cells * cells).map(() => rnd()), out = new Float32Array(S * S);
    for (let y = 0; y < S; y++) for (let x = 0; x < S; x++){
      const fx = x/S*cells, fy = y/S*cells, x0 = Math.floor(fx), y0 = Math.floor(fy), tx = smooth(fx-x0), ty = smooth(fy-y0);
      out[y*S+x] = lerp(lerp(g[(y0%cells)*cells+x0%cells], g[(y0%cells)*cells+(x0+1)%cells], tx), lerp(g[((y0+1)%cells)*cells+x0%cells], g[((y0+1)%cells)*cells+(x0+1)%cells], tx), ty);
    }
    return out;
  }
  function canvasTex(S, H, fn){ const c = document.createElement('canvas'); c.width = S; c.height = H; const g = c.getContext('2d'), img = g.createImageData(S, H); fn(img.data); g.putImageData(img, 0, 0); const t = new THREE.CanvasTexture(c); t.encoding = THREE.sRGBEncoding; return t; }
  function grassTex(){
    const S = 256, rnd = rand(7), b = new THREE.Color(css('--grass')), dk = new THREE.Color(css('--grass-dark')), n1 = tileNoise(S,4,rnd), n2 = tileNoise(S,16,rnd), lum = new Float32Array(S*S);
    for (let i=0;i<S*S;i++) lum[i] = 0.55*n1[i] + 0.45*n2[i] + (rnd()-.5)*0.25;
    for (let k=0;k<5000;k++){ const x0=Math.floor(rnd()*S), y0=Math.floor(rnd()*S), l=3+Math.floor(rnd()*8), sl=(rnd()-.5)*.6, sh=(rnd()-.45)*.7; for (let t=0;t<l;t++) lum[((y0-t+S)%S)*S+((x0+Math.round(t*sl)+S)%S)] += sh*(1-t/l); }
    const t = canvasTex(S, S, d => { for (let i=0;i<S*S;i++){ const v=clamp(lum[i],0,1); d[i*4]=Math.round(255*lerp(dk.r,b.r*1.15,v)); d[i*4+1]=Math.round(255*lerp(dk.g,b.g*1.12,v)); d[i*4+2]=Math.round(255*lerp(dk.b,b.b*1.05,v)); d[i*4+3]=255; } });
    t.wrapS = t.wrapT = THREE.RepeatWrapping; t.anisotropy = renderer.capabilities.getMaxAnisotropy(); return t;
  }
  function boardTex(){ const S=128, H=64, rnd=rand(31), c=new THREE.Color(css('--board')), l=new THREE.Color(css('--board-line')), n=tileNoise(S,16,rnd); return canvasTex(S,H,d=>{ for(let y=0;y<H;y++)for(let x=0;x<S;x++){ const base=(x<4||x>=S-4||y<4||y>=H-4||y%16<2)?l:c, k=.88+.18*n[(y*2%S)*S+x], i=(y*S+x)*4; d[i]=Math.round(255*clamp(base.r*k,0,1)); d[i+1]=Math.round(255*clamp(base.g*k,0,1)); d[i+2]=Math.round(255*clamp(base.b*k,0,1)); d[i+3]=255; } }); }
  function boostTex(){ const c=document.createElement('canvas'); c.width=c.height=128; const g=c.getContext('2d'); g.fillStyle='#005f73'; g.fillRect(0,0,128,128); g.strokeStyle='#ffd23f'; g.lineWidth=6; g.strokeRect(4,4,120,120); g.fillStyle='#00f5d4'; for(let y=26;y<=98;y+=36){ g.beginPath(); g.moveTo(64,y-14); g.lineTo(106,y+10); g.lineTo(86,y+10); g.lineTo(64,y-2); g.lineTo(42,y+10); g.lineTo(22,y+10); g.closePath(); g.fill(); } const t=new THREE.CanvasTexture(c); t.encoding=THREE.sRGBEncoding; return t; }
  function checkerTex(){ const c=document.createElement('canvas'); c.width=256; c.height=64; const g=c.getContext('2d'); for(let y=0;y<4;y++)for(let x=0;x<16;x++){ g.fillStyle=(x+y)%2===0?'#fff':'#181818'; g.fillRect(x*16,y*16,16,16); } const t=new THREE.CanvasTexture(c); t.encoding=THREE.sRGBEncoding; return t; }

  // スキン別のチーズマテリアル生成＆キャッシュ
  const skinMatCache = new Map();
  function buildSkinMats(skinObj, overrideRind){
    const style = skinObj.style || 'solid';
    const rCol = new THREE.Color(overrideRind || skinObj.rind), fCol = new THREE.Color(skinObj.face);
    const rc = document.createElement('canvas'); rc.width = 128; rc.height = 32; const rg = rc.getContext('2d');
    if (style === 'rainbow'){
      const gr = rg.createLinearGradient(0, 0, 128, 0);
      ['#ff595e','#ffca3a','#8ac926','#1982c4','#6a4c93','#ff595e'].forEach((col, i) => gr.addColorStop(i / 5, col));
      rg.fillStyle = gr; rg.fillRect(0, 0, 128, 32);
    } else if (style === 'cyber'){
      rg.fillStyle = '#111827'; rg.fillRect(0, 0, 128, 32);
      rg.fillStyle = '#00f5d4'; rg.fillRect(0, 4, 128, 4); rg.fillRect(0, 24, 128, 4);
      for (let x = 0; x < 128; x += 16) rg.fillRect(x, 0, 3, 32);
    } else {
      rg.fillStyle = '#' + rCol.getHexString(); rg.fillRect(0, 0, 128, 32);
      const rnd = rand(41);
      for (let i = 0; i < 90; i++){ rg.fillStyle = style === 'blue' ? 'rgba(55,105,120,.38)' : 'rgba(255,255,255,.1)'; rg.fillRect(rnd()*128, rnd()*32, 6, 4); }
    }
    const fc = document.createElement('canvas'); fc.width = fc.height = 128; const fg = fc.getContext('2d');
    fg.fillStyle = '#' + rCol.getHexString(); fg.fillRect(0, 0, 128, 128);
    fg.beginPath(); fg.arc(64, 64, 54, 0, TAU);
    if (style === 'rainbow'){
      const gr = fg.createLinearGradient(10, 10, 118, 118);
      ['#ff595e','#ffca3a','#8ac926','#1982c4','#c084fc'].forEach((col, i) => gr.addColorStop(i / 4, col));
      fg.fillStyle = gr;
    } else fg.fillStyle = '#' + fCol.getHexString();
    fg.fill();
    if (style === 'blue'){
      const rnd = rand(73); fg.fillStyle = '#366878';
      for (let i = 0; i < 24; i++){ const a = rnd()*TAU, rad = rnd()*42; fg.beginPath(); fg.arc(64+Math.cos(a)*rad, 64+Math.sin(a)*rad, 3+rnd()*4, 0, TAU); fg.fill(); }
    } else if (style === 'cyber'){
      fg.strokeStyle = '#00f5d4'; fg.lineWidth = 4; fg.beginPath(); fg.arc(64, 64, 48, 0, TAU); fg.stroke();
      fg.beginPath(); fg.arc(64, 64, 24, 0, TAU); fg.stroke();
    } else if (style === 'gold'){
      fg.strokeStyle = '#fff9c4'; fg.lineWidth = 5; fg.beginPath(); fg.arc(64, 64, 42, 0, TAU); fg.stroke();
    }
    const rTex = new THREE.CanvasTexture(rc); rTex.encoding = THREE.sRGBEncoding;
    const fTex = new THREE.CanvasTexture(fc); fTex.encoding = THREE.sRGBEncoding;
    const metal = style === 'gold' ? 0.55 : style === 'cyber' ? 0.25 : 0;
    const rough = style === 'gold' ? 0.28 : style === 'cyber' ? 0.35 : 0.78;
    const emi = style === 'cyber' ? lin('#007a70') : style === 'gold' ? lin('#4a3400') : lin('#000000');
    const emiI = style === 'cyber' ? 0.65 : style === 'gold' ? 0.25 : 0;
    const rm = new THREE.MeshStandardMaterial({ map:rTex, roughness:rough, metalness:metal, emissive:emi, emissiveIntensity:emiI });
    const fm = new THREE.MeshStandardMaterial({ map:fTex, roughness:rough, metalness:metal, emissive:emi, emissiveIntensity:emiI });
    return [rm, fm, fm];
  }
  function getSkinMats(skinId, overrideRind){
    const key = `${skinId || 'classic'}|${overrideRind || ''}`;
    if (skinMatCache.has(key)) return skinMatCache.get(key);
    const obj = SKINS.find(s => s.id === skinId) || SKINS[0];
    const mats = buildSkinMats(obj, overrideRind);
    skinMatCache.set(key, mats); return mats;
  }

  // ---------- 地形メッシュ（穴くり抜き対応）・柵・ギミック・ゴール ----------
  const NX = TERRAIN_SEG_X, NS = CHUNK_SEG_S, VN = (NX + 1) * (NS + 1), MAX_TRIS = NX * NS * 6;
  const terrainMat = new THREE.MeshStandardMaterial({ map:grassTex(), vertexColors:true, roughness:1, metalness:0 });
  const chunks = [];
  for (let k = 0; k < CHUNK_COUNT; k++){
    const geo = new THREE.BufferGeometry();
    geo.setAttribute('position', new THREE.BufferAttribute(new Float32Array(VN*3), 3));
    geo.setAttribute('normal', new THREE.BufferAttribute(new Float32Array(VN*3), 3));
    geo.setAttribute('color', new THREE.BufferAttribute(new Float32Array(VN*3), 3));
    geo.setAttribute('uv', new THREE.BufferAttribute(new Float32Array(VN*2), 2));
    geo.setIndex(new THREE.BufferAttribute(new Uint32Array(MAX_TRIS), 1));
    const mesh = new THREE.Mesh(geo, terrainMat); mesh.frustumCulled = false; mesh.receiveShadow = true; scene.add(mesh); chunks.push({ mesh, k:null });
  }
  function buildChunk(ch, k){
    ch.k = k; const s0 = k * CHUNK_LEN, uv0 = (((s0 % 100) + 100) % 100) / GRASS_TILE;
    const geo = ch.mesh.geometry, p = geo.attributes.position.array, n = geo.attributes.normal.array, col = geo.attributes.color.array, uv = geo.attributes.uv.array;
    let q = 0, u = 0;
    for (let j = 0; j <= NS; j++){
      const s = s0 + j * CHUNK_LEN / NS;
      for (let i = 0; i <= NX; i++){
        const x = -TERRAIN_HALF + i * 2 * TERRAIN_HALF / NX;
        p[q]=x; p[q+1]=bump(x,s)-SLOPE*(s-s0); p[q+2]=-(s-s0);
        const gx = slopeX(x,s), gs = slopeS(x,s), L = Math.hypot(gx,1,gs);
        n[q]=-gx/L; n[q+1]=1/L; n[q+2]=gs/L;
        const sh = (Math.abs(x) > cfg.halfW ? OUTSIDE_SHADE : 1) * (0.9 + 0.1 * Math.sin(x*0.7 + s*0.31) * Math.sin(s*0.13 - x*0.4));
        col[q]=col[q+1]=col[q+2]=sh; uv[u]=x/GRASS_TILE; uv[u+1]=uv0+(s-s0)/GRASS_TILE; q+=3; u+=2;
      }
    }
    const idx = geo.index.array; let ic = 0;
    for (let j = 0; j < NS; j++){
      const sm = s0 + (j + 0.5) * CHUNK_LEN / NS;
      for (let i = 0; i < NX; i++){
        const xm = -TERRAIN_HALF + (i + 0.5) * 2 * TERRAIN_HALF / NX;
        if (cfg.course === 'special' && isTerrainVoid(xm, sm)) continue;
        const a = j * (NX + 1) + i, b = a + NX + 1;
        idx[ic++]=a; idx[ic++]=a+1; idx[ic++]=b; idx[ic++]=a+1; idx[ic++]=b+1; idx[ic++]=b;
      }
    }
    geo.setDrawRange(0, ic); geo.index.needsUpdate = true;
    for (const a of ['position','normal','color','uv']) geo.attributes[a].needsUpdate = true;
  }
  function updateChunks(){
    const k0 = Math.floor((S0 - CHUNK_BEHIND) / CHUNK_LEN);
    for (const ch of chunks) if (ch.k === null || ch.k < k0 || ch.k >= k0 + CHUNK_COUNT) ch.k = null;
    for (let k = k0; k < k0 + CHUNK_COUNT; k++) if (!chunks.some(ch => ch.k === k)) buildChunk(chunks.find(ch => ch.k === null), k);
    for (const ch of chunks){ const s0 = ch.k * CHUNK_LEN; ch.mesh.position.set(0, -SLOPE * (s0 - S0), -(s0 - S0)); }
  }

  const NPOST = Math.ceil(CHUNK_COUNT * CHUNK_LEN / POST_GAP) + 1, fenceMat = new THREE.MeshStandardMaterial({ color:lin(css('--fence')), roughness:0.9 });
  const posts = new THREE.InstancedMesh(new THREE.BoxGeometry(0.1,1.1,0.1), fenceMat, NPOST*2), rails = new THREE.InstancedMesh(new THREE.BoxGeometry(0.05,0.09,1), fenceMat, NPOST*4);
  for (const m of [posts, rails]){ m.frustumCulled = false; m.castShadow = m.receiveShadow = true; } scene.add(posts, rails);
  const dummy = new THREE.Object3D();
  function updateFence(){
    const n0 = Math.floor((S0 - CHUNK_BEHIND) / POST_GAP); let pi = 0, ri = 0;
    for (let n = n0; n < n0 + NPOST; n++){
      const s1 = n * POST_GAP, s2 = s1 + POST_GAP;
      for (const side of [-1, 1]){
        const x = side * (cfg.halfW + 0.1), y1 = ground(x, s1), y2 = ground(x, s2);
        rel(dummy.position, s1, x, y1 + 0.5); dummy.rotation.set(0,0,0); dummy.scale.set(1,1,1); dummy.updateMatrix(); posts.setMatrixAt(pi++, dummy.matrix);
        for (const h of [0.45, 0.9]){ rel(dummy.position, (s1+s2)/2, x, (y1+y2)/2+h); dummy.rotation.set(Math.atan2(y2-y1,s2-s1),0,0); dummy.scale.set(1,1,Math.hypot(POST_GAP,y2-y1)); dummy.updateMatrix(); rails.setMatrixAt(ri++, dummy.matrix); }
      }
    }
    posts.instanceMatrix.needsUpdate = rails.instanceMatrix.needsUpdate = true;
  }

  const bridgeMesh = new THREE.InstancedMesh(new THREE.BoxGeometry(1,0.35,1), new THREE.MeshStandardMaterial({ map:boardTex(), roughness:0.8 }), 24);
  const boostMesh = new THREE.InstancedMesh(new THREE.BoxGeometry(1,0.14,1), new THREE.MeshStandardMaterial({ map:boostTex(), emissive:lin('#007788'), emissiveIntensity:0.55, roughness:0.4 }), 40);
  const rimMesh = new THREE.InstancedMesh(new THREE.BoxGeometry(1,0.16,1), new THREE.MeshStandardMaterial({ color:lin('#f59e0b'), roughness:0.7 }), 140);
  for (const m of [bridgeMesh, boostMesh, rimMesh]){ m.frustumCulled = false; m.receiveShadow = true; scene.add(m); } bridgeMesh.castShadow = true;
  function drawSpecialProps(){
    if (cfg.course !== 'special'){ bridgeMesh.count = boostMesh.count = rimMesh.count = 0; return; }
    let bi = 0, pi = 0, ri = 0;
    const i0 = Math.max(1, Math.floor((S0 - 30) / SP_LEN)), i1 = Math.floor((S0 + 170) / SP_LEN);
    for (let idx = i0; idx <= i1; idx++){
      const sec = getSpSection(idx); if (!sec) continue;
      for (const b of sec.bridges){
        if (bi >= 24) break;
        const y1 = ground(b.x, b.s0)+0.12, y2 = ground(b.x, b.s1)+0.12, len = b.s1 - b.s0;
        rel(dummy.position, (b.s0+b.s1)*0.5, b.x, (y1+y2)*0.5-0.17); dummy.rotation.set(Math.atan2(y2-y1, len),0,0); dummy.scale.set(b.w,1,Math.hypot(len,y2-y1)); dummy.updateMatrix();
        bridgeMesh.setMatrixAt(bi++, dummy.matrix);
      }
      for (const p of sec.boosts){
        if (pi >= 40) break;
        const y1 = ground(p.x, p.s-p.len/2), y2 = ground(p.x, p.s+p.len/2);
        rel(dummy.position, p.s, p.x, (y1+y2)/2+0.14); dummy.rotation.set(Math.atan2(y2-y1, p.len),0,0); dummy.scale.set(p.w,1,Math.hypot(p.len,y2-y1)); dummy.updateMatrix();
        boostMesh.setMatrixAt(pi++, dummy.matrix);
      }
      for (const h of sec.holes){
        if (ri + 4 > 140) break;
        for (const [ex,es,ew,el] of [[h.x,h.s-h.len/2,h.w+0.5,0.35],[h.x,h.s+h.len/2,h.w+0.5,0.35],[h.x-h.w/2,h.s,0.35,h.len],[h.x+h.w/2,h.s,0.35,h.len]]){
          rel(dummy.position, es, ex, ground(ex, es)+0.08); dummy.rotation.set(-SLOPE_ANG,0,0); dummy.scale.set(ew,1,el/Math.cos(SLOPE_ANG)); dummy.updateMatrix();
          rimMesh.setMatrixAt(ri++, dummy.matrix);
        }
      }
    }
    bridgeMesh.count = bi; boostMesh.count = pi; rimMesh.count = ri;
    bridgeMesh.instanceMatrix.needsUpdate = boostMesh.instanceMatrix.needsUpdate = rimMesh.instanceMatrix.needsUpdate = true;
  }

  const goalGroup = new THREE.Group(), goalMat = new THREE.MeshStandardMaterial({ map:checkerTex(), roughness:0.6 }), goalPoleMat = new THREE.MeshStandardMaterial({ color:lin('#e63946'), roughness:0.5 });
  const goalL = new THREE.Mesh(new THREE.CylinderGeometry(0.22,0.22,7.5,12), goalPoleMat), goalR = new THREE.Mesh(new THREE.CylinderGeometry(0.22,0.22,7.5,12), goalPoleMat);
  const goalBanner = new THREE.Mesh(new THREE.BoxGeometry(1,1.8,0.28), goalMat), goalLine = new THREE.Mesh(new THREE.PlaneGeometry(1,1.4).rotateX(-Math.PI/2), new THREE.MeshBasicMaterial({ map:checkerTex(), side:THREE.DoubleSide }));
  goalGroup.add(goalL, goalR, goalBanner, goalLine); scene.add(goalGroup);
  function updateGoalGate(){
    goalGroup.visible = Math.abs(cfg.goalDist - S0) < 200; if (!goalGroup.visible) return;
    const w = cfg.halfW * 2; rel(goalGroup.position, cfg.goalDist, 0, ground(0, cfg.goalDist));
    goalL.position.set(-cfg.halfW,3.6,0); goalR.position.set(cfg.halfW,3.6,0);
    goalBanner.scale.set(w+0.6,1,1); goalBanner.position.set(0,6.4,0); goalLine.scale.set(w,1,1); goalLine.position.set(0,0.14,0);
  }

  // ---------- チーズ＆軌跡 ----------
  const cheeseGeo = new THREE.CylinderGeometry(1,1,1,32).rotateZ(Math.PI/2), cheeseVisuals = [], markers = [];
  for (let i = 0; i < MAX_CHEESES; i++){
    const grp = new THREE.Group(); grp.rotation.order = 'YXZ'; const spin = new THREE.Group(); grp.add(spin);
    const mesh = new THREE.Mesh(cheeseGeo, getSkinMats('classic', i === 0 ? null : RACER_COLORS[i]));
    mesh.castShadow = true; spin.add(mesh); scene.add(grp); cheeseVisuals.push({ grp, spin, mesh });
    const mk = new THREE.Mesh(new THREE.ConeGeometry(0.24,0.45,4).rotateX(Math.PI), new THREE.MeshBasicMaterial({ color:RACER_COLORS[i] }));
    mk.visible = false; scene.add(mk); markers.push(mk);
  }
  function applyCheeseSkin(idx, skinId, fallbackRind){
    if (idx < 0 || idx >= MAX_CHEESES) return;
    cheeses[idx].skin = skinId || 'classic';
    cheeseVisuals[idx].mesh.material = getSkinMats(skinId || 'classic', fallbackRind || null);
  }

  const trail = []; let trailK = 0;
  const trailGeo = new THREE.BufferGeometry();
  trailGeo.setAttribute('position', new THREE.BufferAttribute(new Float32Array(TRAIL_N*6), 3));
  trailGeo.setAttribute('alpha', new THREE.BufferAttribute(new Float32Array(TRAIL_N*2), 1));
  { const idx = []; for (let i=0;i<TRAIL_N-1;i++){ const a=i*2; idx.push(a,a+1,a+2,a+1,a+3,a+2); } trailGeo.setIndex(idx); }
  const trailMat = new THREE.ShaderMaterial({ uniforms:{ color:{ value:new THREE.Color(css('--trail')) } }, vertexShader:'attribute float alpha; varying float vA; void main(){ vA=alpha; gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.0); }', fragmentShader:'uniform vec3 color; varying float vA; void main(){ gl_FragColor=vec4(color,vA); }', transparent:true, depthWrite:false, side:THREE.DoubleSide });
  const trailMesh = new THREE.Mesh(trailGeo, trailMat); trailMesh.frustumCulled = false; scene.add(trailMesh);
  const focusCheese = () => cfg.isRace ? cheeses[clamp(cfg.myIndex, 0, cheeseN - 1)] : cheeses[0];
  function recordTrail(){ const fc = focusCheese(); if (fc.held) return; const t = trail[0]; if (t && Math.hypot(fc.s-t.s, fc.x-t.x, fc.y-t.y) < TRAIL_STEP) return; trail.unshift({ s:fc.s, x:fc.x, y:fc.y }); if (trail.length > TRAIL_N) trail.pop(); }
  const _tp = Array.from({ length:TRAIL_N }, () => new THREE.Vector3()), _tt = new THREE.Vector3(), _ts = new THREE.Vector3();
  function drawTrail(dt){
    const fc = focusCheese(), want = fc.held ? 0 : (fc.boostT > 0 ? 1 : clamp((fc.v - Math.max(8, cfg.targetSpeed * 0.92)) / TRAIL_FADE, 0, 1));
    trailK += (want - trailK) * Math.min(1, dt * 6);
    const skColor = fc.skin === 'cyber' ? '#00f5d4' : fc.skin === 'strawberry' ? '#ff99c8' : fc.skin === 'gold' ? '#ffe066' : css('--trail');
    trailMat.uniforms.color.value.set(fc.boostT > 0 ? '#00f5d4' : skColor);
    const n = trail.length; trailMesh.visible = trailK > 0.01 && n > 1; if (!trailMesh.visible) return;
    rel(_tp[0], fc.s, fc.x, fc.y); for (let i=1;i<TRAIL_N;i++){ const p = trail[Math.min(i,n-1)]; rel(_tp[i], p.s, p.x, p.y); }
    const pos = trailGeo.attributes.position.array, al = trailGeo.attributes.alpha.array;
    for (let i=0;i<TRAIL_N;i++){
      _tt.subVectors(_tp[Math.max(i-1,0)], _tp[Math.min(i+1,TRAIL_N-1)]); _ts.crossVectors(_tt, UP);
      const u = i / (TRAIL_N - 1); if (_ts.lengthSq() > 1e-10) _ts.normalize().multiplyScalar(cfg.cheeseW/2*(1-TRAIL_TAPER*u)); else _ts.set(0,0,0);
      pos[i*6]=_tp[i].x+_ts.x; pos[i*6+1]=_tp[i].y+_ts.y; pos[i*6+2]=_tp[i].z+_ts.z; pos[i*6+3]=_tp[i].x-_ts.x; pos[i*6+4]=_tp[i].y-_ts.y; pos[i*6+5]=_tp[i].z-_ts.z;
      al[i*2]=al[i*2+1]=i<n ? trailK*TRAIL_ALPHA*Math.pow(1-u,1.5) : 0;
    }
    trailGeo.attributes.position.needsUpdate = trailGeo.attributes.alpha.needsUpdate = true;
  }

  // ---------- 障害物（チーズのみ衝突・人間はすり抜け） ----------
  const obstacles = [];
  const makeSolidTex = (cssKey, seed, amp) => { const S=64, rnd=rand(seed), c=new THREE.Color(css(cssKey)), n=tileNoise(S,8,rnd); return canvasTex(S,S,d=>{ for(let i=0;i<S*S;i++){ const k=0.75+amp*n[i]; d[i*4]=Math.round(255*clamp(c.r*k,0,1)); d[i*4+1]=Math.round(255*clamp(c.g*k,0,1)); d[i*4+2]=Math.round(255*clamp(c.b*k,0,1)); d[i*4+3]=255; } }); };
  const rockMat = new THREE.MeshStandardMaterial({ map:makeSolidTex('--rock',21,0.4), roughness:0.95, flatShading:true });
  const barkMat = new THREE.MeshStandardMaterial({ map:makeSolidTex('--bark',23,0.4), roughness:0.95 });
  const woodMat = new THREE.MeshStandardMaterial({ map:makeSolidTex('--wood',25,0.25), roughness:0.9 });
  const strawMat = new THREE.MeshStandardMaterial({ map:makeSolidTex('--straw',27,0.35), roughness:1 });
  const bushMat = new THREE.MeshStandardMaterial({ vertexColors:true, roughness:1, flatShading:true });
  const boardMat = new THREE.MeshStandardMaterial({ map:boardTex(), roughness:0.85 });

  const rockGeos = Array.from({ length:ROCK_VARIANTS }, (_, v) => {
    const rnd = rand(101+v), g = new THREE.IcosahedronGeometry(1,2), pos = g.attributes.position, ph = [rnd()*TAU,rnd()*TAU,rnd()*TAU];
    for (let i=0;i<pos.count;i++){ const x=pos.getX(i), y=pos.getY(i), z=pos.getZ(i), k=1+.16*Math.sin(x*2.1+ph[0])*Math.sin(y*1.7+ph[1])+.12*Math.sin(z*2.6+ph[2]+x); pos.setXYZ(i,x*k,y*k,z*k); }
    g.computeVertexNormals(); return g;
  });
  const logGeo = new THREE.CylinderGeometry(1,1,1,14).rotateZ(Math.PI/2), baleGeo = new THREE.CylinderGeometry(1,1,1,22).translate(0,0.5,0), boardGeo = new THREE.BoxGeometry(1,1,1).translate(0,0.5,0);
  function bushGeo(seed){
    const rnd = rand(seed), leaf = lin(css('--gorse')), light = lin(css('--gorse-light')), flw = lin(css('--gorse-flower')), parts = [{ g:new THREE.SphereGeometry(0.85,10,5,0,TAU,0,Math.PI/2).scale(1,0.6,1).toNonIndexed(), c:leaf }];
    const cone = new THREE.ConeGeometry(1,1,4).translate(0,0.5,0).toNonIndexed(), bud = new THREE.IcosahedronGeometry(1,0), m = new THREE.Matrix4(), q = new THREE.Quaternion(), sc = new THREE.Vector3(), dir = new THREE.Vector3();
    for (let i=0;i<BUSH_SPIKES;i++){ const a=rnd()*TAU, y=Math.pow(rnd(),.7)*.95+.05, h=Math.sqrt(1-y*y); dir.set(Math.cos(a)*h+(rnd()-.5)*.5, y*1.3+.2, Math.sin(a)*h+(rnd()-.5)*.5).normalize(); q.setFromUnitVectors(UP,dir); m.compose(new THREE.Vector3(Math.cos(a)*h*.85,y*.52,Math.sin(a)*h*.85), q, sc.set(.03,.2+rnd()*.2,.03)); parts.push({ g:cone.clone().applyMatrix4(m), c:leaf.clone().lerp(light,rnd()).multiplyScalar(.65+.45*y) }); }
    for (let i=0;i<BUSH_FLOWERS;i++){ const a=rnd()*TAU, y=Math.pow(rnd(),.7)*.95+.05, h=Math.sqrt(1-y*y), r=.025+rnd()*.015; m.compose(new THREE.Vector3(Math.cos(a)*h*.98,y*.62,Math.sin(a)*h*.98), q.identity(), sc.set(r,r,r)); parts.push({ g:bud.clone().applyMatrix4(m), c:flw }); }
    let n=0; for(const p of parts) n+=p.g.attributes.position.count;
    const pos=new Float32Array(n*3), nor=new Float32Array(n*3), col=new Float32Array(n*3); let o=0;
    for(const {g,c} of parts){ const gp=g.attributes.position, gn=g.attributes.normal; for(let i=0;i<gp.count;i++){ const k=(o+i)*3; pos[k]=gp.getX(i); pos[k+1]=gp.getY(i); pos[k+2]=gp.getZ(i); nor[k]=gn.getX(i); nor[k+1]=gn.getY(i); nor[k+2]=gn.getZ(i); col[k]=c.r; col[k+1]=c.g; col[k+2]=c.b; } o+=gp.count; }
    const g=new THREE.BufferGeometry(); g.setAttribute('position',new THREE.BufferAttribute(pos,3)); g.setAttribute('normal',new THREE.BufferAttribute(nor,3)); g.setAttribute('color',new THREE.BufferAttribute(col,3)); return g;
  }
  const bushGeos = Array.from({ length:BUSH_VARIANTS }, (_, v) => bushGeo(201+v)), pools = { rock:[], bush:[], log:[], bale:[], board:[] };
  function takeMesh(o){
    let m = pools[o.kind].pop();
    if (!m){
      if (o.kind==='rock') m=new THREE.Mesh(rockGeos[0],rockMat); if (o.kind==='log') m=new THREE.Mesh(logGeo,[barkMat,woodMat,woodMat]);
      if (o.kind==='bale') m=new THREE.Mesh(baleGeo,strawMat); if (o.kind==='bush') m=new THREE.Mesh(bushGeos[0],bushMat); if (o.kind==='board') m=new THREE.Mesh(boardGeo,boardMat);
      m.castShadow = m.receiveShadow = true; scene.add(m);
    }
    m.visible = true; m.rotation.set(0, o.yaw||0, 0);
    if (o.kind==='rock'){ m.geometry=rockGeos[o.shape]; m.scale.set(o.r,o.r*.8,o.r); }
    if (o.kind==='bush'){ m.geometry=bushGeos[o.shape]; m.scale.set(o.r,o.h,o.r); }
    if (o.kind==='log') m.scale.set(o.len,o.r,o.r); if (o.kind==='bale') m.scale.set(o.r,o.h,o.r); if (o.kind==='board') m.scale.set(o.len,o.h+1,o.thick);
    return m;
  }
  const releaseMesh = o => { o.mesh.visible = false; pools[o.kind].push(o.mesh); };

  function genBand(b){
    if (cfg.course === 'board'){
      const s0 = b * cfg.boardGap; if (s0 < Math.max(25, cfg.boardGap) || s0 > cfg.goalDist - 12) return;
      const rnd = rand(obSeed*100003 + b*7919), side = rnd() < 0.5 ? -1 : 1;
      const o = { kind:'board', s:s0, x:side*(cfg.halfW - cfg.boardW/2), len:cfg.boardW, h:cfg.boardH, thick:cfg.boardThick, id:b, yaw:0 };
      o.mesh = takeMesh(o); obstacles.push(o); return;
    }
    const s0 = b * OB_BAND; if (s0 < OB_START || s0 > cfg.goalDist - 10) return;
    const rnd = rand(obSeed*100003 + b*7919), rate = cfg.course === 'special' ? 0.24 : Math.min(OB_RATE_MAX, OB_RATE0 + s0 * OB_RATE_GROW);
    let n = rnd() < rate ? 1 : 0; if (cfg.course !== 'special' && n && rnd() < s0 * OB_SECOND) n = 2;
    for (let i = 0; i < n; i++){
      let u = rnd(), kind = OB_KIND[0][0]; for (const [k, w] of OB_KIND){ if (u < w){ kind = k; break; } u -= w; }
      const os = s0 + rnd() * OB_BAND, o = { kind, s:os, id:b*4+i, r:0, len:0, h:0, yaw:rnd()*TAU };
      if (kind==='rock'){ o.r=0.3+rnd()*0.4; o.shape=Math.floor(rnd()*ROCK_VARIANTS); }
      if (kind==='bush'){ o.r=0.7+rnd()*0.5; o.h=0.9+rnd()*0.5; o.shape=Math.floor(rnd()*BUSH_VARIANTS); }
      if (kind==='log'){ o.r=0.2+rnd()*0.1; o.len=2+rnd()*2.5; o.yaw=0; } if (kind==='bale'){ o.r=0.65; o.h=1.2; }
      const half = (kind === 'log' ? o.len/2 : o.r) + 0.3; o.x = -cfg.halfW + half + rnd() * 2 * (cfg.halfW - half);
      if (cfg.course === 'special'){ const sec = getSpSection(Math.floor(os / SP_LEN)); if (sec && (sec.type === 'bridge' || isTerrainVoid(o.x, os) || isTerrainVoid(o.x-half, os) || isTerrainVoid(o.x+half, os))) continue; }
      o.mesh = takeMesh(o); obstacles.push(o);
    }
  }
  function updateObstacles(){
    const bandLen = cfg.course === 'board' ? cfg.boardGap : OB_BAND;
    let ah = S0, bh = S0; for (let i=0;i<cheeseN;i++){ ah = Math.max(ah, cheeses[i].s); bh = Math.min(bh, cheeses[i].s); }
    while (obNextBand * bandLen < Math.min(ah + OB_AHEAD, cfg.goalDist + 20)) genBand(obNextBand++);
    for (let i = obstacles.length - 1; i >= 0; i--) if (obstacles[i].s < bh - OB_BEHIND){ releaseMesh(obstacles[i]); obstacles.splice(i, 1); }
  }
  function clearObstacles(fixedSeed){ for (const o of obstacles) releaseMesh(o); obstacles.length = 0; obNextBand = 0; spCache.clear(); obSeed = fixedSeed !== undefined ? fixedSeed : Math.floor(Math.random()*1e6); }
  function drawObstacles(){
    for (const o of obstacles){
      const m = o.mesh;
      if (o.kind==='rock') rel(m.position,o.s,o.x,ground(o.x,o.s)+o.r*.25); if (o.kind==='bush') rel(m.position,o.s,o.x,ground(o.x,o.s)-.08);
      if (o.kind==='bale') rel(m.position,o.s,o.x,ground(o.x,o.s)-.06); if (o.kind==='board') rel(m.position,o.s,o.x,ground(o.x,o.s)-1);
      if (o.kind==='log'){ const g1=ground(o.x-o.len/2,o.s), g2=ground(o.x+o.len/2,o.s); rel(m.position,o.s,o.x,(g1+g2)/2+o.r*.85); m.rotation.set(0,0,Math.atan2(g2-g1,o.len)); }
    }
  }
  const _n = [0,0,0];
  function obstacleHit(o, x, y, s, pr){
    if (o.kind === 'board'){
      const minX=o.x-o.len/2, maxX=o.x+o.len/2, minS=o.s-o.thick/2, maxS=o.s+o.thick/2, gy=ground(clamp(x,minX,maxX),o.s);
      const dx=x-clamp(x,minX,maxX), dy=y-clamp(y,gy-1.5,gy+o.h), ds=s-clamp(s,minS,maxS), d=Math.hypot(dx,dy,ds);
      if (d>=pr) return 0; if (d<1e-6){ _n[0]=0;_n[1]=0;_n[2]=-1; return (s-minS)+pr; } _n[0]=dx/d;_n[1]=dy/d;_n[2]=ds/d; return pr-d;
    }
    if (o.kind==='rock'||o.kind==='log'){
      let cx=o.x, cy, R;
      if (o.kind==='rock'){ cy=ground(o.x,o.s)+o.r*.25; R=o.r*.9+pr; }
      else { const x1=o.x-o.len/2, t=clamp((x-x1)/o.len,0,1); cx=x1+t*o.len; cy=lerp(ground(x1,o.s),ground(x1+o.len,o.s),t)+o.r*.85; R=o.r+pr; }
      const dx=x-cx, dy=y-cy, ds=s-o.s, d=Math.hypot(dx,dy,ds); if (d>=R||d<1e-6) return 0; _n[0]=dx/d;_n[1]=dy/d;_n[2]=ds/d; return R-d;
    }
    if (o.kind==='bale'){
      const gy=ground(o.x,o.s), dx=x-o.x, ds=s-o.s, d=Math.hypot(dx,ds)||1e-6, side=o.r+pr-d, up=gy+o.h+pr-y;
      if (side<=0||up<=0||y<gy-pr) return 0; if (up<side){ _n[0]=0;_n[1]=1;_n[2]=0; return up; } _n[0]=dx/d;_n[1]=0;_n[2]=ds/d; return side;
    }
    return 0;
  }
  const hard = o => o.kind !== 'bush', halfWidth = o => (o.kind==='log'||o.kind==='board') ? o.len/2 : o.r;
  const inBush = (x, s) => cfg.course !== 'board' && obstacles.some(o => o.kind==='bush' && Math.hypot(x-o.x, s-o.s) < o.r*0.9);
  function cheeseObstacles(c){
    for (const o of obstacles){
      if (!hard(o) || Math.abs(o.s - c.s) > (o.kind==='board' ? o.thick/2+2.5 : 3.5)) continue;
      const d = obstacleHit(o, c.x, c.y, c.s, cfg.cheeseR*0.92); if (d <= 0) continue;
      c.x+=_n[0]*d; c.y+=_n[1]*d; c.s+=_n[2]*d; const vn = c.vx*_n[0] + c.vy*_n[1] + c.v*_n[2];
      if (vn < 0){
        if (o.kind==='board' && _n[2] < -0.5){ c.v=-2.5; c.vx*=.2; c.vy=Math.max(0,c.vy); }
        else { c.vx-=(1+OB_BOUNCE)*vn*_n[0]; c.vy-=(1+OB_BOUNCE)*vn*_n[1]; c.v-=(1+OB_BOUNCE)*vn*_n[2]; c.vx*=OB_LOSS; c.v*=OB_LOSS; c.grounded=false; }
        c.tiltV += (Math.random()-.5)*Math.min(20,Math.abs(vn))*TILT_KICK*3;
      }
    }
  }

  // ---------- 人の体（人間側は障害物を完全すり抜け） ----------
  const HEAD=0,NECK=1,PELVIS=2,SHL=3,SHR=4,ELL=5,ELR=6,HNL=7,HNR=8,HPL=9,HPR=10,KNL=11,KNR=12,FTL=13,FTR=14,NP=15;
  const PART_R=[.11,.06,.1,.06,.06,.05,.05,.05,.05,.07,.07,.06,.06,.05,.05], INV_M=[1/4,1/5,1/12,1/4,1/4,1/1.6,1/1.6,1/.6,1/.6,1/6,1/6,1/4,1/4,1/1.5,1/1.5];
  const TORSO=[NECK,SHL,SHR,PELVIS,HPL,HPR], MIN_DIST=[[HPL,FTL,.33],[HPR,FTR,.33],[SHL,HNL,.2],[SHR,HNR,.2]]; let DIST=[];
  function writePose(c, o, mode, k){
    const sy=Math.sin(c.yaw), cy=Math.cos(c.yaw), Fx=sy, Fs=cy, Rx=cy, Rs=-sy, set=(i,x,y,s)=>{ o[i*3]=x; o[i*3+1]=y; o[i*3+2]=s; };
    const ph=c.phase, v=mode==='run'?c.v:0, vr=Math.min(1,v/3); let lean=0, hipY=.93, twist=0, armL=.1, armR=.1, bend=.3; const feet=[[0,0],[0,0]];
    if (mode==='run'){
      lean=.18*Math.min(1,v/5); hipY=.885-.035*vr*Math.cos(4*Math.PI*(ph-RUN_DUTY/2)); twist=.15*vr*Math.cos(TAU*ph);
      armR=.1+.75*vr*Math.cos(TAU*ph); armL=.1-.75*vr*Math.cos(TAU*ph); bend=.3+1.2*vr;
      const a=clamp(v/Math.max(.5,c.freq)*RUN_DUTY/2,0,.45);
      for(let s=0;s<2;s++){ const r=frac(ph+s*.5); if(r<RUN_DUTY) feet[s][0]=a*(1-2*r/RUN_DUTY); else { const u=(r-RUN_DUTY)/(1-RUN_DUTY); feet[s][0]=-a+2*a*smooth(u)-.3*vr*Math.sin(Math.PI*u); feet[s][1]=(.08+.27*Math.min(1,v/6))*Math.sin(Math.PI*u); } }
    } else if(mode==='crouch'){ lean=-.55*k; hipY=.88-.18*k; armL=armR=-.9*k; bend=.6; feet[0][0]=.25; feet[1][0]=-.2; }
    else if(mode==='reach'){ lean=-1; hipY=.78; armL=armR=2.7; bend=.15; feet[0][0]=-.45; feet[1][0]=-.25; feet[1][1]=.2; }
    else if(mode==='ready'){ lean=-.35; hipY=.86; armL=-.3; armR=.5; bend=1.2; feet[0][0]=.2; feet[1][0]=-.25; }
    const py=ground(c.x,c.s)+hipY, px=c.x, ps=c.s, cl=Math.cos(lean), sl=Math.sin(lean), Tx=-Fx*sl, Ty=cl, Ts=-Fs*sl, Px=Fx*cl, Py=sl, Ps=Fs*cl;
    const ct=Math.cos(twist), st=Math.sin(twist), RhX=Rx*ct-Fx*st, RhS=Rs*ct-Fs*st, RsX=Rx*ct+Fx*st, RsS=Rs*ct+Fs*st;
    set(PELVIS,px,py,ps); set(NECK,px+Tx*.52,py+Ty*.52,ps+Ts*.52); set(HEAD,px+Tx*.7+Px*.03,py+Ty*.7+Py*.03,ps+Ts*.7+Ps*.03);
    const thL=cfg.thigh, shL=cfg.shin;
    for(let side=0;side<2;side++){
      const sg=side?1:-1, S=side?SHR:SHL, E=side?ELR:ELL, Hn=side?HNR:HNL, Hp=side?HPR:HPL, Kn=side?KNR:KNL, Ft=side?FTR:FTL;
      const sx=px+Tx*.47+RsX*.19*sg, syy=py+Ty*.47, ss=ps+Ts*.47+RsS*.19*sg; set(S,sx,syy,ss);
      const th=side?armR:armL, c1=Math.cos(th), s1=Math.sin(th), c2=Math.cos(th+bend), s2=Math.sin(th+bend);
      const ex=sx+(-Tx*c1+Px*s1)*UPARM+Rx*.03*sg, ey=syy+(-Ty*c1+Py*s1)*UPARM, es=ss+(-Ts*c1+Ps*s1)*UPARM+Rs*.03*sg;
      set(E,ex,ey,es); set(Hn,ex+(-Tx*c2+Px*s2)*FOREARM,ey+(-Ty*c2+Py*s2)*FOREARM,es+(-Ts*c2+Ps*s2)*FOREARM);
      const hx=px-Tx*.04+RhX*.1*sg, hy=py-Ty*.04, hs=ps-Ts*.04+RhS*.1*sg; set(Hp,hx,hy,hs);
      const fx=hx+Fx*feet[side][0], fs=hs+Fs*feet[side][0];
      let tx=fx-hx, ty=ground(fx,fs)+FOOT_H+feet[side][1]-hy, ts=fs-hs, d=Math.hypot(tx,ty,ts), maxD=thL+shL-.002;
      if(d>maxD){ tx*=maxD/d; ty*=maxD/d; ts*=maxD/d; d=maxD; }
      const nx=tx/d, ny=ty/d, ns=ts/d, along=(thL*thL-shL*shL+d*d)/(2*d), h=Math.sqrt(Math.max(0,thL*thL-along*along));
      let bx=Fx-nx*(Fx*nx+Fs*ns), by=-ny*(Fx*nx+Fs*ns), bs=Fs-ns*(Fx*nx+Fs*ns), bl=Math.hypot(bx,by,bs)||1; bx/=bl; by/=bl; bs/=bl;
      set(Kn,hx+nx*along+bx*h,hy+ny*along+by*h,hs+ns*along+bs*h); set(Ft,hx+tx,hy+ty,hs+ts);
    }
  }
  function rebuildBodyDist(){
    DIST=[]; const o=new Float64Array(NP*3); writePose({s:0,x:0,yaw:0,phase:0,v:0,freq:1},o,'idle',0);
    const len=(a,b)=>Math.hypot(o[a*3]-o[b*3],o[a*3+1]-o[b*3+1],o[a*3+2]-o[b*3+2]);
    for(let i=0;i<TORSO.length;i++)for(let j=i+1;j<TORSO.length;j++)DIST.push([TORSO[i],TORSO[j],len(TORSO[i],TORSO[j])]);
    DIST.push([HEAD,NECK,len(HEAD,NECK)],[HEAD,SHL,len(HEAD,SHL)],[HEAD,SHR,len(HEAD,SHR)],[SHL,ELL,UPARM],[ELL,HNL,FOREARM],[SHR,ELR,UPARM],[ELR,HNR,FOREARM],[HPL,KNL,cfg.thigh],[KNL,FTL,cfg.shin],[HPR,KNR,cfg.thigh],[KNR,FTR,cfg.shin]);
  }
  rebuildBodyDist();
  function solveDist(p,a,b,r,mo){ const ia=a*3,ib=b*3,dx=p[ib]-p[ia],dy=p[ib+1]-p[ia+1],ds=p[ib+2]-p[ia+2],d=Math.sqrt(dx*dx+dy*dy+ds*ds)||1e-6; if(mo&&d>=r)return; const wa=INV_M[a]/(INV_M[a]+INV_M[b]),wb=1-wa,f=(d-r)/d; p[ia]+=dx*f*wa;p[ia+1]+=dy*f*wa;p[ia+2]+=ds*f*wa;p[ib]-=dx*f*wb;p[ib+1]-=dy*f*wb;p[ib+2]-=ds*f*wb; }
  const _f=[0,0,0], _gy=new Float64Array(NP), _pen=new Float64Array(NP);
  function ragStep(c, dt, reaching){
    const p=c.p, q=c.q, gdt=9.8*dt*dt;
    for(let i=0;i<NP*3;i++){ const v=(p[i]-q[i])*RAG_DAMP; q[i]=p[i]; p[i]+=v; }
    for(let i=0;i<NP;i++) p[i*3+1]-=gdt;
    if(reaching){ const ux=p[NECK*3]-p[PELVIS*3],uy=p[NECK*3+1]-p[PELVIS*3+1],us=p[NECK*3+2]-p[PELVIS*3+2],ul=Math.hypot(ux,uy,us)||1; for(const[S,E,Hn]of[[SHL,ELL,HNL],[SHR,ELR,HNR]])for(const[i,L]of[[E,UPARM],[Hn,UPARM+FOREARM]])for(let a=0;a<3;a++)p[i*3+a]+=(p[S*3+a]+[ux,uy,us][a]/ul*L-p[i*3+a])*.25; }
    for(let i=0;i<NP;i++){ _gy[i]=ground(p[i*3],p[i*3+2])+PART_R[i]; _pen[i]=0; }
    const lim=cfg.halfW-.1;
    for(let it=0;it<RAG_ITER;it++){
      for(const[a,b,r]of DIST)solveDist(p,a,b,r,false); for(const[a,b,r]of MIN_DIST)solveDist(p,a,b,r,true);
      const rx=p[HPR*3]-p[HPL*3],ry=p[HPR*3+1]-p[HPL*3+1],rs=p[HPR*3+2]-p[HPL*3+2],ux=p[NECK*3]-p[PELVIS*3],uy=p[NECK*3+1]-p[PELVIS*3+1],us=p[NECK*3+2]-p[PELVIS*3+2];
      const fx=ry*us-rs*uy,fy=rs*ux-rx*us,fs=rx*uy-ry*ux,l=Math.hypot(fx,fy,fs)||1; _f[0]=fx/l;_f[1]=fy/l;_f[2]=fs/l;
      for(const[Hp,Kn,Ft]of[[HPL,KNL,FTL],[HPR,KNR,FTR]]){ let d=0; for(let a=0;a<3;a++)d+=(p[Kn*3+a]-(p[Hp*3+a]+p[Ft*3+a])/2)*_f[a]; if(d<0)for(let a=0;a<3;a++)p[Kn*3+a]-=_f[a]*d; }
      for(let i=0;i<NP;i++){ const k=i*3; if(p[k+1]<_gy[i]){ _pen[i]+=_gy[i]-p[k+1]; p[k+1]=_gy[i]; } if(p[k]>lim){p[k]=lim;q[k]=Math.max(q[k],lim);} if(p[k]<-lim){p[k]=-lim;q[k]=Math.min(q[k],-lim);} }
    }
    for(let i=0;i<NP;i++){
      if(_pen[i]<=0)continue; const k=i*3,x=p[k],s=p[k+2]; let nx=-slopeX(x,s),ny=1,ns=-slopeS(x,s),nl=Math.hypot(nx,ny,ns); nx/=nl;ny/=nl;ns/=nl;
      let vx=p[k]-q[k],vy=p[k+1]-q[k+1],vs=p[k+2]-q[k+2],vn0=vx*nx+vy*ny+vs*ns; if(vn0<0){vx-=vn0*nx;vy-=vn0*ny;vs-=vn0*ns;}
      const vn=vx*nx+vy*ny+vs*ns,tx=vx-vn*nx,ty=vy-vn*ny,ts=vs-vn*ns,tl=Math.hypot(tx,ty,ts),fr=RAG_MU*(_pen[i]+Math.max(0,-vn0)),kk=tl>1e-9?Math.max(0,tl-fr)/tl:0;
      q[k]=p[k]-(vn*nx+tx*kk); q[k+1]=p[k+1]-(vn*ny+ty*kk); q[k+2]=p[k+2]-(vn*ns+ts*kk);
    }
  }

  const CYL=[[PELVIS,NECK,'shirt',.16,.105],[NECK,HEAD,'skin',.045,.045],[SHL,ELL,'shirt',.05,.05],[SHR,ELR,'shirt',.05,.05],[ELL,HNL,'skin',.038,.038],[ELR,HNR,'skin',.038,.038],[HPL,KNL,'pants',.075,.075],[HPR,KNR,'pants',.075,.075],[KNL,FTL,'pants',.055,.055],[KNR,FTR,'pants',.055,.055]];
  const SPH=[[PELVIS,'pants',.16,.11,.11],[HEAD,'skin',.105,.115,.105],[HEAD,'hair',.11,.1,.112],[SHL,'shirt',.062,.062,.062],[SHR,'shirt',.062,.062,.062],[ELL,'skin',.04,.04,.04],[ELR,'skin',.04,.04,.04],[HNL,'skin',.045,.05,.035],[HNR,'skin',.045,.05,.035],[KNL,'pants',.06,.06,.06],[KNR,'pants',.06,.06,.06],[FTL,'shoe',.05,.045,.125],[FTR,'shoe',.05,.045,.125]];
  const bodyMat=new THREE.MeshStandardMaterial({roughness:.85,metalness:0}), cylMesh=new THREE.InstancedMesh(new THREE.CylinderGeometry(1,1,1,10),bodyMat,CH_ALLOC*CYL.length), sphMesh=new THREE.InstancedMesh(new THREE.SphereGeometry(1,12,10),bodyMat,CH_ALLOC*SPH.length);
  for(const m of[cylMesh,sphMesh]){ m.frustumCulled=false; m.castShadow=m.receiveShadow=true; scene.add(m); }
  const PALETTE={skin:list('--skins'),shirt:list('--shirts'),pants:list('--pants'),hair:list('--hair'),shoe:list('--shoes')}, chasers=[];
  for(let i=0;i<CH_ALLOC;i++){
    const pick=(k,n)=>lin(PALETTE[k][(i*n+Math.floor(i/PALETTE[k].length))%PALETTE[k].length]);
    const col={skin:pick('skin',3),shirt:i<5?lin(RACER_COLORS[(i+1)%RACER_COLORS.length]):pick('shirt',1),pants:pick('pants',5),hair:pick('hair',2),shoe:pick('shoe',3)};
    CYL.forEach((d,j)=>cylMesh.setColorAt(i*CYL.length+j,col[d[2]])); SPH.forEach((d,j)=>sphMesh.setColorAt(i*SPH.length+j,col[d[1]]));
    chasers.push({p:new Float64Array(NP*3),q:new Float64Array(NP*3),a:new Float64Array(NP*3),active:false,idx:i,isPlayer:false,ctrlId:null,label:'',crouchK:0});
  }
  cylMesh.instanceColor.needsUpdate=sphMesh.instanceColor.needsUpdate=true;
  // === パート2 ここまで ===
  // === パート3 ここから ===
  // ---------- AI・操作・物理更新 ----------
  function cheeseAIInput(ch){
    let steer=0, pushY=cfg.isRace?(.62+.38*ch.aiSpeedK):.35;
    if (cfg.course==='special'){
      const lookS=ch.s+11, sNow=getSpSection(Math.floor(ch.s/SP_LEN)), sAh=getSpSection(Math.floor(lookS/SP_LEN));
      const sec=(sNow&&sNow.valley&&ch.s<=sNow.valley.s1+2)?sNow:sAh;
      if (sec&&sec.valley&&lookS>=sec.valley.s0-4&&ch.s<=sec.valley.s1+1){
        let tb=sec.bridges[0], bd=999;
        for(const b of sec.bridges){ const d=Math.abs(ch.s+4-clamp(ch.s+4,b.s0,b.s1))+Math.abs(ch.x-b.x)*.25; if(d<bd){bd=d;tb=b;} }
        if(tb) steer+=clamp((tb.x-ch.x)*.75,-1,1)*1.9;
      } else if(sec){
        for(const h of sec.holes){ const ah=h.s-ch.s; if(ah>-h.len*.4&&ah<15&&Math.abs(ch.x-h.x)<h.w/2+1.3) steer+=((h.x>0&&h.x-h.w/2>-cfg.halfW+2.2)?-1:(h.x<0&&h.x+h.w/2<cfg.halfW-2.2)?1:(ch.x<h.x?-1:1))*2.2; }
        for(const p of sec.boosts){ const ah=p.s-ch.s; if(ah>.5&&ah<14&&Math.abs(ch.x-p.x)<4.5) steer+=clamp((p.x-ch.x)*.4,-.8,.8); }
      }
    }
    const look=cfg.course==='board'?Math.max(18,cfg.boardGap*.9):10;
    for(const o of obstacles){ const ah=o.s-ch.s; if(ah<.2||ah>look)continue; const half=halfWidth(o)+(cfg.course==='board'?1.2:.75); if(Math.abs(ch.x-o.x)<half+.6) steer+=(o.kind==='board'?(o.x<0?1:-1):(ch.x<o.x?-1:1))*(1-ah/look)*(cfg.course==='board'?3.5:2.3); }
    if (!cfg.isRace&&!cfg.isTA){
      let nd=999, nx=0; for(let i=0;i<activeN;i++){ const c=chasers[i],ds=ch.s-c.s; if(ds>-1&&ds<10){ const d=Math.hypot(ds,ch.x-c.x); if(d<nd){nd=d;nx=c.x;} } }
      if(nd<8){ let dir=ch.x>=nx?1:-1; if(ch.x>cfg.halfW-1.8)dir=-1; if(ch.x<-cfg.halfW+1.8)dir=1; steer+=dir*(1-nd/8)*1.4; pushY=.85; }
    }
    steer+=Math.sin(playTime*(.85+ch.idx*.16)+obSeed+ch.idx*2.1)*.16;
    if(ch.x>cfg.halfW-1.4)steer-=1.2; if(ch.x<-cfg.halfW+1.4)steer+=1.2;
    return { x:clamp(steer,-1,1), y:pushY };
  }
  function getCtrlForId(id, cd){
    if(id==='host'){ const inp=input(); let d=false; if(cd){d=diveRequested;diveRequested=false;} return{x:inp.x,y:inp.y,dive:d}; }
    const cl=net.clients.get(id); if(cl){ let d=false; if(cd){d=cl.input.dive;cl.input.dive=false;} return{x:cl.input.x,y:cl.input.y,dive:d}; }
    return null;
  }
  const setPose=(c,m,k)=>{ c.q.set(c.p); writePose(c,c.p,m,k); };
  function resetChaser(c, i, init){
    c.state=init?'wait':'run';
    if(init){
      if(c.isPlayer){ const pc=Math.max(1,cfg.humanPlayerCount), gap=Math.min(2.2,cfg.halfW*1.4/pc); c.s=-3.2; c.x=clamp((i-(pc-1)/2)*gap,-cfg.halfW+.8,cfg.halfW-.8); }
      else { const pr=Math.max(6,Math.min(20,Math.floor((cfg.halfW*2-1.4)/1.4))), row=Math.floor(i/pr)+(cfg.humanPlayerCount>0?1:0), col=i%pr, gap=Math.min(1.6,(cfg.halfW*2-1.4)/pr); c.s=-3.2-row*1.35-(col%2)*.4; c.x=clamp((col-(pr-1)/2)*gap,-cfg.halfW+.6,cfg.halfW-.6); }
    } else { const mc=mainCheese(); c.s=mc.s-SPAWN_BEHIND-Math.random()*4; c.x=clamp(mc.x+(Math.random()*2-1)*cfg.halfW*.5,-cfg.halfW+.5,cfg.halfW-.5); }
    c.v=init?0:Math.max(mainCheese().v,5); c.vx=0; c.t=0; c.yaw=0; c.freq=1.2; c.crouchK=0; c.canCatch=false;
    c.cool=init?(c.isPlayer?cfg.startDelay:cfg.startDelay+cfg.startGrace):0; c.phase=Math.random(); c.vary=c.isPlayer?0:(Math.random()*2-1)*SPEED_VAR; c.off=(Math.random()*2-1)*Math.min(5,cfg.halfW*.35);
    writePose(c,c.p,init?'ready':'run',0); c.q.set(c.p); if(!init)for(let n=0;n<NP;n++)c.q[n*3+2]-=c.v*STEP;
  }

  const cheeseHeightOf = c => c.y - cfg.cheeseR - ground(c.x, c.s);
  function checkSpecialBoost(c){
    if(cfg.course!=='special'||c.boostCool>0||!c.grounded)return;
    const sec=getSpSection(Math.floor(c.s/SP_LEN)); if(!sec)return;
    for(const p of sec.boosts){
      if(Math.abs(c.x-p.x)<=p.w/2+cfg.cheeseR&&Math.abs(c.s-p.s)<=p.len/2+cfg.cheeseR){
        c.v=Math.max(c.v+15,cfg.targetSpeed*1.62); c.boostT=.75; c.boostCool=.45;
        if((cfg.isRace&&c.idx===cfg.myIndex)||(!cfg.isRace&&cfg.myRole==='cheese'&&c.idx===0)) showToast('DASH BOOST!!','boost');
        return;
      }
    }
  }
  function updateCheeseOne(c, dt){
    if(c.held){ const p=c.held.p, k=c.hand*3; c.x=p[k]; c.y=p[k+1]; c.s=p[k+2]; return; }
    if(c.boostT>0) c.boostT=Math.max(0,c.boostT-dt); if(c.boostCool>0) c.boostCool=Math.max(0,c.boostCool-dt);
    if(game==='playing'){
      const k=c.grounded?1:cfg.airControl, ctrl=c.finished?{x:0,y:-.6}:(c.ctrlId==='ai'?cheeseAIInput(c):(getCtrlForId(c.ctrlId,false)||cheeseAIInput(c)));
      const vm=cfg.mode==='human'?4.2:cfg.vxMax, la=cfg.mode==='human'?18:cfg.latAcc, ps=Math.max(1,cfg.targetSpeed/9.5);
      c.vx+=clamp(ctrl.x*vm-c.vx,-la*k*dt,la*k*dt);
      if(ctrl.y>0) c.v+=ctrl.y*PUSH_ACC*ps*k*dt; else if(ctrl.y<0) c.v=Math.max(Math.min(c.v,0),c.v+ctrl.y*BRAKE_ACC*ps*k*dt);
      if(c.grounded){ c.vx-=c.tilt*TILT_DRIFT*dt; c.v-=cfg.turnCost*Math.abs(c.vx)*dt; }
      const along=c.finished?cfg.gAlong*.15:cfg.gAlong*(c.ctrlId==='ai'&&cfg.isRace?(.94+.12*c.aiSpeedK):1);
      c.v=Math.max(-3,c.v+(along-(c.boostT>0?cfg.drag*.42:cfg.drag)*c.v*Math.abs(c.v))*dt);
      if(cheeseHeightOf(c)<.5&&inBush(c.x,c.s)) c.v=Math.max(Math.min(c.v,0),c.v-BUSH_DRAG*dt);
    }
    c.s+=c.v*dt; c.x+=c.vx*dt;
    if(game==='playing'&&!c.finished) cheeseObstacles(c);
    if(c.x<-cfg.xlim){c.x=-cfg.xlim;c.vx=Math.max(0,c.vx);} if(c.x>cfg.xlim){c.x=cfg.xlim;c.vx=Math.min(0,c.vx);}
    c.vy-=cfg.gFall*dt; c.y+=c.vy*dt;
    const sf=surfaceAt(c.x,c.s), gy=sf.y+cfg.cheeseR;
    if(sf.solid&&c.y<=gy){
      const gvy=slopeS(c.x,c.s)*c.v+(sf.onBridge?0:slopeX(c.x,c.s)*c.vx), hit=gvy-c.vy;
      if(hit>cfg.bounceMin){ c.vy=gvy+hit*cfg.bounce; c.tiltV+=(Math.random()-.5)*hit*TILT_KICK*4; if(game==='playing'&&!sf.onBridge)c.vx+=(Math.random()-.5)*hit*.3; } else c.vy=gvy;
      c.y=gy; c.grounded=true;
    } else if(!sf.solid||c.y-gy>.03) c.grounded=false;

    if(game==='playing'&&!c.finished){
      checkSpecialBoost(c);
      if(cfg.course==='special'&&c.y<ground(c.x,c.s)-4.2){
        const rp=findSafeRespawn(c.s); c.s=rp.s; c.x=rp.x; c.y=surfaceAt(rp.x,rp.s).y+cfg.cheeseR+1;
        c.v=cfg.targetSpeed*.45; c.vx=0; c.vy=0; c.tilt=0; c.tiltV=0; c.boostT=0; c.grounded=false;
        if((cfg.isRace&&c.idx===cfg.myIndex)||(!cfg.isRace&&cfg.myRole==='cheese'&&c.idx===0)){ trail.length=0; showToast('落下！少し戻されました','fall'); }
      }
    }
    c.tiltV+=(-TILT_SPRING*c.tilt-TILT_DAMP*c.tiltV)*dt; c.tilt=clamp(c.tilt+c.tiltV*dt,-.6,.6); c.rot=(c.rot+c.v/cfg.cheeseR*dt)%TAU;
    if(game==='playing'&&!c.finished&&c.s>=cfg.goalDist){
      c.finished=true; c.finishTime=playTime;
      if(cfg.isRace){ if(!raceOrder.includes(c.idx))raceOrder.push(c.idx); }
      else if(cfg.isTA){ c.s=cfg.goalDist; maxDist=cfg.goalDist; finishCaught(null,0,'ta_goal',0); }
      else { c.s=cfg.goalDist; maxDist=cfg.goalDist; finishCaught(null,0,'cheese_goal',-1); }
    }
  }
  function collideCheeses(){
    for(let i=0;i<cheeseN;i++)for(let j=i+1;j<cheeseN;j++){
      const a=cheeses[i],b=cheeses[j],dx=a.x-b.x,dy=a.y-b.y,ds=a.s-b.s,d=Math.hypot(dx,dy,ds),m=cfg.cheeseR*2.1;
      if(d>1e-5&&d<m){ const pen=(m-d)*.5,nx=dx/d,ns=ds/d; a.x=clamp(a.x+nx*pen,-cfg.xlim,cfg.xlim); b.x=clamp(b.x-nx*pen,-cfg.xlim,cfg.xlim); a.s+=ns*pen; b.s-=ns*pen; const vn=(a.vx-b.vx)*nx+(a.v-b.v)*ns; if(vn<0){ const imp=-.65*vn; a.vx+=imp*nx;b.vx-=imp*nx;a.v+=imp*ns;b.v-=imp*ns; } }
    }
  }
  function finishCaught(c,h,oc,wi){
    game='caught'; outcome=oc; winnerIdx=wi!==undefined?wi:-1; endJoy(); overT=0;
    if(c){ const mc=mainCheese(); mc.held=c; mc.hand=h; mc.v=mc.vx=mc.vy=0; }
    for(const o of chasers)if(o!==c&&(o.state==='run'||o.state==='windup'||o.state==='wait'))o.state='idle';
    if(cfg.isOnline&&net.role==='host')sendHostSnapshot(true);
  }
  const _d3=(p,i,x,y,s)=>Math.hypot(p[i*3]-x,p[i*3+1]-y,p[i*3+2]-s);
  function launch(c){
    const mc=mainCheese(); c.state='rag'; c.t=0; c.canCatch=true; writePose(c,c.p,'reach',0);
    let tx, ts;
    if(c.isPlayer&&mc.s-c.s>-1&&mc.s-c.s<5.5&&Math.abs(mc.x-c.x)<3.2){ ts=mc.s+mc.v*DIVE_T*.85; tx=clamp(mc.x+mc.vx*DIVE_T*.5,-cfg.halfW+.5,cfg.halfW-.5); }
    else if(c.isPlayer){ ts=c.s+Math.max(c.v,6)*DIVE_T+1.8; tx=clamp(c.x+c.vx*DIVE_T,-cfg.halfW+.5,cfg.halfW-.5); }
    else { ts=mc.s+mc.v*DIVE_T; tx=clamp(mc.x+mc.vx*DIVE_T*AIM,-cfg.halfW+.5,cfg.halfW-.5); }
    const dx=tx-c.x,ds=ts-c.s,dl=Math.hypot(dx,ds)||1,px=tx-dx/dl*DIVE_REACH,ps=ts-ds/dl*DIVE_REACH,p=c.p,k=PELVIS*3;
    const vx=(px-p[k])/DIVE_T,vs=(ps-p[k+2])/DIVE_T,vy=(ground(px,ps)+.35-p[k+1])/DIVE_T+.5*9.8*DIVE_T,wx=DIVE_SPIN*(ds/dl),ws=DIVE_SPIN*(-dx/dl);
    for(let i=0;i<NP;i++){ const ox=p[i*3]-p[k],oy=p[i*3+1]-p[k+1],os=p[i*3+2]-p[k+2]; c.q[i*3]=p[i*3]-(vx-ws*oy)*STEP; c.q[i*3+1]=p[i*3+1]-(vy+ws*ox-wx*os)*STEP; c.q[i*3+2]=p[i*3+2]-(vs+wx*oy)*STEP; }
  }
  function updateChaser(c, dt){
    const mc=mainCheese(), behind=mc.s-c.s; c.cool-=dt;
    switch(c.state){
      case 'wait': c.t+=dt; setPose(c,'ready',0); if(c.t>=cfg.startDelay){c.state='run';c.t=0;} break;
      case 'run': case 'windup': {
        let wantDive=false; const pc=c.isPlayer?getCtrlForId(c.ctrlId,true):null;
        if(pc){
          wantDive=pc.dive; let target=(cfg.targetSpeed+1)*cfg.chSpeedMult;
          if(pc.y>0)target+=pc.y*Math.max(2.2,cfg.targetSpeed*.12)*cfg.chSpeedMult; else if(pc.y<0)target=Math.max(0,target+pc.y*Math.max(7,cfg.targetSpeed*.4));
          if(behind>18)target+=Math.min(4.5,(behind-18)*.2);
          c.v+=clamp(target-c.v,-Math.max(12,cfg.runAcc*1.4)*dt,Math.max(12,cfg.runAcc*1.4)*dt);
          const pv=Math.max(cfg.vxMax,5.2); c.vx+=clamp(pc.x*pv-c.vx,-Math.max(28,pv*5)*dt,Math.max(28,pv*5)*dt);
        } else {
          let target=Math.min((cfg.targetSpeed+.9+mc.s*.0008)*cfg.chSpeedMult,(Math.max(mc.v,3)+1.3+mc.s*.0004)*cfg.chSpeedMult)+c.vary;
          if(behind>CATCHUP_DIST)target+=CATCHUP*cfg.chSpeedMult; if(behind<.4)target=Math.min(target,Math.max(mc.v,cfg.course==='board'?5:0));
          c.v+=clamp(Math.max(0,target)-c.v,-cfg.runAcc*dt,cfg.runAcc*dt);
          const want=mc.x+c.off*clamp((behind-2)/8,0,1); c.vx+=clamp(clamp((want-c.x)*3,-cfg.chVx,cfg.chVx)-c.vx,-Math.max(15,cfg.chVx*2.5)*dt,Math.max(15,cfg.chVx*2.5)*dt);
        }
        c.s+=c.v*dt; c.x=clamp(c.x+c.vx*dt,-cfg.halfW+.5,cfg.halfW-.5); c.yaw=Math.atan2(c.vx,Math.max(c.v,1))*.8; c.freq=clamp(c.v*.32,1.2,3); c.phase+=c.freq*dt;
        if(cfg.course==='board'&&game==='playing'&&c.cool<=0&&Math.abs(mc.s-c.s)<1.25&&Math.abs(mc.x-c.x)<.95&&cheeseHeightOf(mc)<CATCH_HEIGHT){ setPose(c,'reach',0); finishCaught(c,HNR,c.isPlayer?'human_win':'caught',c.idx); break; }
        if(c.state==='run'){ setPose(c,'run',0); if(pc){ if(game==='playing'&&wantDive&&c.cool<=0){c.state='windup';c.t=0;c.crouchK=0;} } else if(game==='playing'&&c.cool<=0&&behind>-DIVE_BACK&&behind<DIVE_RANGE&&Math.abs(mc.x-c.x)<DIVE_DX&&cheeseHeightOf(mc)<CATCH_HEIGHT){c.state='windup';c.t=0;c.crouchK=0;} }
        else { c.t+=dt; const wd=pc?WINDUP*.55:WINDUP; c.crouchK=Math.min(1,c.t/wd); setPose(c,'crouch',c.crouchK); if(c.t>=wd)launch(c); }
        break;
      }
      case 'rag': {
        c.t+=dt; ragStep(c,dt,c.t<DIVE_T*.9&&mc.held!==c); const p=c.p,k=PELVIS*3; c.x=p[k]; c.s=p[k+2]; c.v=(p[k+2]-c.q[k+2])/dt;
        if(game==='playing'&&c.canCatch&&c.t<CATCH_WINDOW&&cheeseHeightOf(mc)<CATCH_HEIGHT){
          const X=mc.x,Y=mc.y,S=mc.s,dl=_d3(p,HNL,X,Y,S),dr=_d3(p,HNR,X,Y,S),bd=Math.min(_d3(p,NECK,X,Y,S),_d3(p,PELVIS,X,Y,S),_d3(p,SHL,X,Y,S),_d3(p,SHR,X,Y,S)),bn=c.isPlayer?.16:0;
          if(Math.min(dl,dr)<cfg.cheeseR+.25+bn||bd<cfg.cheeseR+.3+bn){ finishCaught(c,dl<dr?HNL:HNR,c.isPlayer?'human_win':'caught',c.idx); break; }
        }
        const sp=Math.hypot(p[k]-c.q[k],p[k+1]-c.q[k+1],p[k+2]-c.q[k+2])/dt;
        if(mc.held!==c&&c.t>RAG_MIN&&(sp<RAG_STILL||c.t>RAG_MAX)){ c.state='getup';c.t=0;c.a.set(p);c.s0=c.s;c.x0=c.x;c.g0=ground(c.x,c.s);c.v=Math.max(0,Math.min(c.v,4));c.vx=0;c.yaw=0;c.phase=0; }
        break;
      }
      case 'getup': {
        c.t+=dt; c.v+=(3-c.v)*Math.min(1,3*dt); c.s+=c.v*dt; c.freq=clamp(c.v*.32,1.2,3); c.phase+=c.freq*dt; c.q.set(c.p); writePose(c,c.p,'run',0);
        const k=smooth(Math.min(1,c.t/GETUP_T)),dS=c.s-c.s0,dX=c.x-c.x0,dY=ground(c.x,c.s)-c.g0;
        for(let i=0;i<NP;i++){ c.p[i*3]=lerp(c.a[i*3]+dX,c.p[i*3],k); c.p[i*3+1]=lerp(c.a[i*3+1]+dY,c.p[i*3+1],k); c.p[i*3+2]=lerp(c.a[i*3+2]+dS,c.p[i*3+2],k); }
        if(c.t>=GETUP_T){ c.state=game==='playing'?'run':'idle'; c.t=0; c.cool=c.isPlayer?.35:COOLDOWN; }
        break;
      }
      case 'idle': c.v=Math.max(0,c.v-8*dt); if(behind<1.5&&Math.abs(mc.x-c.x)<1)c.v=0; c.s+=c.v*dt; c.freq=clamp(c.v*.32,1.2,3); c.phase+=c.freq*dt; setPose(c,'run',0); break;
    }
    if(game==='playing'&&!c.isPlayer&&behind>RESPAWN_BEHIND&&mc.held!==c) resetChaser(c,c.idx,false);
    if(game==='playing'&&c.isPlayer&&behind>50&&c.state==='run'){ c.s=mc.s-28; c.x=clamp(mc.x,-cfg.halfW+1,cfg.halfW-1); c.v=Math.max(c.v,mc.v); }
  }
  function step(dt){
    if(game==='playing') playTime+=dt;
    updateObstacles();
    for(let i=0;i<cheeseN;i++) updateCheeseOne(cheeses[i],dt);
    if(cfg.isRace&&game==='playing'){
      collideCheeses();
      const hm=cheeses.slice(0,cheeseN).filter(c=>c.ctrlId!=='ai'), any=hm.some(c=>c.finished), all=hm.length>0&&hm.every(c=>c.finished);
      if(any) raceFinishTimer+=dt;
      if(all||(any&&raceFinishTimer>=6)||raceOrder.length>=cheeseN) finishCaught(null,0,'race_done',raceOrder[0]??0);
    } else if(!cfg.isTA){
      const mc=mainCheese();
      if(game==='playing'&&cfg.humanPlayerCount===0&&cfg.chStart>0){ const n=Math.min(cfg.chMax,cfg.chStart+Math.floor(mc.s/cfg.chAddEvery)); while(activeN<n){ const c=chasers[activeN++]; c.isPlayer=false; c.ctrlId=null; resetChaser(c,activeN-1,false); c.active=true; } }
      for(let i=0;i<activeN;i++) updateChaser(chasers[i],dt);
      const mv=s=>s==='run'||s==='windup'||s==='idle';
      for(let i=0;i<activeN;i++)for(let j=i+1;j<activeN;j++){ const a=chasers[i],b=chasers[j]; if(!mv(a.state)||!mv(b.state))continue; if(Math.abs(a.s-b.s)<.8&&Math.abs(a.x-b.x)<.6){ const p=(.6-Math.abs(a.x-b.x))*.5*(a.x>=b.x?1:-1); a.x=clamp(a.x+p,-cfg.halfW+.5,cfg.halfW-.5); b.x=clamp(b.x-p,-cfg.halfW+.5,cfg.halfW-.5); } }
      if(mc.held) updateCheeseOne(mc,dt);
      if(game==='playing') maxDist=Math.min(cfg.goalDist,Math.max(maxDist,mc.s));
    } else {
      if(game==='playing') maxDist=Math.min(cfg.goalDist,Math.max(maxDist,mainCheese().s));
    }
    recordTrail();
    S0 = cfg.isRace ? focusCheese().s : (cfg.myRole==='human'&&activeN>cfg.myIndex ? chasers[cfg.myIndex].s : mainCheese().s);
  }

  // ---------- チーズレース用 順位バー＆ミニマップ ----------
  const rankPins = [];
  for(let i=0;i<MAX_CHEESES;i++){
    const el=document.createElement('div'); el.className='rank-pin hidden'; el.style.background=RACER_COLORS[i];
    $('rankTrack').appendChild(el); rankPins.push(el);
  }
  function drawRankBar(){
    const show = cfg.isRace && game !== 'menu';
    $('rankBarWrap').classList.toggle('hidden', !show);
    if(!show) return;
    for(let i=0;i<MAX_CHEESES;i++){
      const el=rankPins[i]; el.classList.toggle('hidden', i>=cheeseN);
      if(i>=cheeseN) continue;
      const pct=clamp(cheeses[i].s / cfg.goalDist, 0, 1) * 100;
      el.style.left = pct.toFixed(2) + '%';
      el.style.background = cheeses[i].color;
      el.classList.toggle('me', i === cfg.myIndex);
    }
  }
  const mmCv = $('miniMap'), mmCtx = mmCv.getContext('2d');
  function drawMiniMap(){
    const show = cfg.isRace && game !== 'menu';
    mmCv.classList.toggle('hidden', !show);
    if(!show) return;
    const W=mmCv.width, H=mmCv.height, pad=8, innerW=W-pad*2;
    const fc=focusCheese(), sMin=fc.s-18, sMax=fc.s+110, sSpan=sMax-sMin;
    const toX = x => pad + ((x + cfg.halfW) / (cfg.halfW * 2)) * innerW;
    const toY = s => H - ((s - sMin) / sSpan) * H;

    mmCtx.clearRect(0,0,W,H);
    mmCtx.fillStyle='#2a4b2c'; mmCtx.fillRect(pad, 0, innerW, H);
    // スペシャルコースの谷・穴・板・ダッシュボード
    if(cfg.course==='special'){
      const i0=Math.max(1,Math.floor(sMin/SP_LEN)), i1=Math.floor(sMax/SP_LEN);
      for(let idx=i0;idx<=i1;idx++){
        const sec=getSpSection(idx); if(!sec)continue;
        if(sec.valley){ const y1=toY(sec.valley.s1), y2=toY(sec.valley.s0); mmCtx.fillStyle='#0c1512'; mmCtx.fillRect(pad, y1, innerW, y2-y1); }
        for(const h of sec.holes){ const x1=toX(h.x-h.w/2), x2=toX(h.x+h.w/2), y1=toY(h.s+h.len/2), y2=toY(h.s-h.len/2); mmCtx.fillStyle='#0c1512'; mmCtx.fillRect(x1,y1,x2-x1,y2-y1); mmCtx.strokeStyle='#f59e0b'; mmCtx.lineWidth=1; mmCtx.strokeRect(x1,y1,x2-x1,y2-y1); }
        for(const b of sec.bridges){ const x1=toX(b.x-b.w/2), x2=toX(b.x+b.w/2), y1=toY(b.s1), y2=toY(b.s0); mmCtx.fillStyle='#d69e56'; mmCtx.fillRect(x1,y1,x2-x1,y2-y1); }
        for(const p of sec.boosts){ const x1=toX(p.x-p.w/2), x2=toX(p.x+p.w/2), y1=toY(p.s+p.len/2), y2=toY(p.s-p.len/2); mmCtx.fillStyle='#00f5d4'; mmCtx.fillRect(x1,y1,x2-x1,y2-y1); }
      }
    }
    // 障害物（板や岩）
    for(const o of obstacles){
      if(o.s<sMin||o.s>sMax||o.kind==='bush')continue;
      const y=toY(o.s);
      if(o.kind==='board'){ const x1=toX(o.x-o.len/2), x2=toX(o.x+o.len/2); mmCtx.fillStyle='#d69e56'; mmCtx.fillRect(x1,y-2,x2-x1,4); }
      else { mmCtx.fillStyle='rgba(255,255,255,.35)'; mmCtx.fillRect(toX(o.x)-1.5,y-1.5,3,3); }
    }
    // ゴールライン
    if(cfg.goalDist>=sMin&&cfg.goalDist<=sMax){
      const gy=toY(cfg.goalDist); mmCtx.fillStyle='#fff'; mmCtx.fillRect(pad,gy-2,innerW,4);
      mmCtx.fillStyle='#111'; for(let x=pad;x<pad+innerW;x+=8)mmCtx.fillRect(x,gy-2,4,4);
    }
    // コース両端ライン
    mmCtx.strokeStyle='rgba(255,255,255,.45)'; mmCtx.lineWidth=1.5;
    mmCtx.beginPath(); mmCtx.moveTo(pad,0); mmCtx.lineTo(pad,H); mmCtx.moveTo(W-pad,0); mmCtx.lineTo(W-pad,H); mmCtx.stroke();
    // 全チーズ描画（自分を手前に大きく）
    for(let pass=0;pass<2;pass++){
      for(let i=0;i<cheeseN;i++){
        const isMe=(i===cfg.myIndex); if((pass===0&&isMe)||(pass===1&&!isMe))continue;
        const c=cheeses[i], cx=toX(c.x), cy=clamp(toY(c.s), 5, H-5);
        mmCtx.beginPath(); mmCtx.arc(cx, cy, isMe?5.5:3.8, 0, TAU);
        mmCtx.fillStyle=c.color; mmCtx.fill();
        mmCtx.lineWidth=isMe?2:1; mmCtx.strokeStyle=isMe?'#fff':'#000'; mmCtx.stroke();
      }
    }
  }

  // ---------- ゲスト補間・3D描画 ----------
  function applyGuestInterpolation(dt){
    const sn=net.snap; if(!sn)return; const k=Math.min(1,dt*18); cheeseN=sn.ch.length;
    for(let i=0;i<cheeseN;i++){
      const sc=sn.ch[i], c=cheeses[i], jb=(c.s-sc.s)>8;
      if(Math.hypot(sc.s-c.s,sc.x-c.x)>8){ c.s=sc.s;c.x=sc.x;c.y=sc.y; if(jb&&((cfg.isRace&&i===cfg.myIndex)||(!cfg.isRace&&cfg.myRole==='cheese'&&i===0))){trail.length=0;showToast('落下！少し戻されました','fall');} }
      else { c.s=lerp(c.s,sc.s,k); c.x=lerp(c.x,sc.x,k); c.y=lerp(c.y,sc.y,k); }
      c.v=sc.v; c.vx=sc.vx; c.tilt=lerp(c.tilt,sc.tl,k); c.rot=(c.rot+c.v/cfg.cheeseR*dt)%TAU;
      c.held=sc.h>=0?chasers[sc.h]:null; c.hand=sc.hd; c.finished=!!sc.fn; c.finishTime=sc.ft||0;
      if(sc.bt>.5&&c.boostT<=.1&&((cfg.isRace&&i===cfg.myIndex)||(!cfg.isRace&&cfg.myRole==='cheese'&&i===0))) showToast('DASH BOOST!!','boost');
      c.boostT=sc.bt||0;
    }
    activeN=sn.cs.length;
    for(let i=0;i<activeN;i++){
      const d=sn.cs[i], c=chasers[i], pv=c.state; c.active=true; c.state=d.st; c.v=d.v; c.cool=d.cl; c.freq=clamp(c.v*.32,1.2,3);
      if(d.p){ const jmp=pv!==d.st&&pv!=='rag'&&pv!=='getup'; for(let j=0;j<NP*3;j++)c.p[j]=jmp?d.p[j]:lerp(c.p[j],d.p[j],Math.min(1,dt*22)); c.x=c.p[PELVIS*3]; c.s=c.p[PELVIS*3+2]; }
      else { if(Math.hypot(d.s-c.s,d.x-c.x)>10){c.s=d.s;c.x=d.x;c.yaw=d.yw;c.phase=d.ph;} else {c.s=lerp(c.s,d.s,k);c.x=lerp(c.x,d.x,k);c.yaw=lerp(c.yaw,d.yw,k);c.phase+=c.freq*dt;} writePose(c,c.p,d.st==='wait'?'ready':d.st==='windup'?'crouch':'run',d.ck||0); }
    }
    maxDist=sn.md; playTime=sn.pt; if(sn.ro)raceOrder=sn.ro.slice(); winnerIdx=sn.wc??-1;
    if(sn.g!==game){ if(sn.g==='caught'&&game==='playing'){game='caught';outcome=sn.oc;endJoy();} else if(sn.g==='over'&&game!=='over'){outcome=sn.oc;showOver();} }
    recordTrail(); S0=cfg.isRace?focusCheese().s:(cfg.myRole==='human'&&activeN>cfg.myIndex?chasers[cfg.myIndex].s:mainCheese().s); updateObstacles();
  }

  const RP=Array.from({length:NP},()=>new THREE.Vector3()), _m=new THREE.Matrix4(), _x=new THREE.Vector3(), _y=new THREE.Vector3(), _z=new THREE.Vector3(), _r=new THREE.Vector3(), _u=new THREE.Vector3(), _b=new THREE.Vector3(), _c=new THREE.Vector3();
  function drawChaser(c, idx){
    for(let i=0;i<NP;i++) rel(RP[i],c.p[i*3+2],c.p[i*3],c.p[i*3+1]);
    _u.subVectors(RP[NECK],RP[PELVIS]).normalize(); _r.subVectors(RP[SHR],RP[SHL]).add(_c.subVectors(RP[HPR],RP[HPL])); _r.addScaledVector(_u,-_r.dot(_u)).normalize(); _b.crossVectors(_r,_u);
    CYL.forEach(([a,b,,ra,rb],j)=>{ _y.subVectors(RP[b],RP[a]); const l=_y.length()||1e-4; _y.divideScalar(l); _x.copy(_r).addScaledVector(_y,-_r.dot(_y)); if(_x.lengthSq()<1e-4)_x.copy(_b).addScaledVector(_y,-_b.dot(_y)); _x.normalize(); _z.crossVectors(_x,_y); _m.makeBasis(_x.multiplyScalar(ra),_y.multiplyScalar(l),_z.multiplyScalar(rb)); _m.setPosition(_c.addVectors(RP[a],RP[b]).multiplyScalar(.5)); cylMesh.setMatrixAt(idx*CYL.length+j,_m); });
    SPH.forEach(([a,k,sx,sy,sz],j)=>{ _c.copy(RP[a]); _x.copy(_r); _y.copy(_u); _z.copy(_b); if(k==='hair')_c.addScaledVector(_u,.025).addScaledVector(_b,.012); if(k==='shoe'){ _y.subVectors(RP[a===FTL?KNL:KNR],RP[a]).normalize(); _z.copy(_b).addScaledVector(_y,-_b.dot(_y)); if(_z.lengthSq()<1e-4)_z.copy(_u); _z.normalize(); _x.crossVectors(_y,_z); _c.addScaledVector(_z,-.05).addScaledVector(_y,-.03); } _m.makeBasis(_x.multiplyScalar(sx),_y.multiplyScalar(sy),_z.multiplyScalar(sz)); _m.setPosition(_c); sphMesh.setMatrixAt(idx*SPH.length+j,_m); });
  }
  const getRaceStandings = () => cheeses.slice(0,cheeseN).sort((a,b)=>a.finished&&b.finished?a.finishTime-b.finishTime:a.finished?-1:b.finished?1:b.s-a.s);

  let camX=0, zoom=0; const _camP=new THREE.Vector3(), _camL=new THREE.Vector3(), _zP=new THREE.Vector3(), _zL=new THREE.Vector3();
  function draw(dt){
    updateChunks(); updateFence(); drawObstacles(); drawSpecialProps(); updateGoalGate();
    for(let i=0;i<MAX_CHEESES;i++){
      const v=cheeseVisuals[i]; v.grp.visible=i<cheeseN; if(!v.grp.visible)continue; const c=cheeses[i]; rel(v.grp.position,c.s,c.x,c.y);
      if(!c.held){ v.grp.rotation.y=-Math.atan2(c.vx,Math.max(c.v,1)); v.grp.rotation.x=Math.atan(slopeS(c.x,c.s)); v.grp.rotation.z=-c.vx/Math.max(1,cfg.vxMax)*.3+c.tilt; }
      v.spin.rotation.x=-c.rot;
    }
    for(let i=0;i<activeN;i++) drawChaser(chasers[i],i);
    cylMesh.count=activeN*CYL.length; sphMesh.count=activeN*SPH.length; cylMesh.instanceMatrix.needsUpdate=sphMesh.instanceMatrix.needsUpdate=true;

    const bob=Math.sin(performance.now()*.008)*.1; for(let i=0;i<MAX_CHEESES;i++) markers[i].visible=false;
    if(cfg.isRace){ for(let i=0;i<cheeseN;i++){ const c=cheeses[i],mk=markers[i]; mk.visible=true; mk.material.color.set(c.color); rel(mk.position,c.s,c.x,c.y+cfg.cheeseR+.65+bob); mk.rotation.y=performance.now()*.003; } }
    else if(cfg.humanPlayerCount>0){
      for(let i=0;i<Math.min(cfg.humanPlayerCount,activeN,MAX_CHEESES);i++){ const pl=chasers[i],mk=markers[i]; mk.visible=true; mk.material.color.set(cfg.myRole==='human'&&i===cfg.myIndex?'#ffd23f':'#38bdf8'); rel(mk.position,pl.p[HEAD*3+2],pl.p[HEAD*3],pl.p[HEAD*3+1]+.65+bob); mk.rotation.y=performance.now()*.003; }
      if(cfg.myRole==='human'&&activeN>cfg.myIndex){ const me=chasers[cfg.myIndex]; $('diveBtn').classList.toggle('disabled',!(game==='playing'&&me.state==='run'&&me.cool<=0)); }
    }

    const fc=focusCheese();
    if(!cfg.isRace&&cfg.myRole==='human'&&activeN>cfg.myIndex){
      const pl=chasers[cfg.myIndex], bb=Math.max(6.5,cfg.camBack); camX+=(pl.x*.8-camX)*Math.min(1,dt*5);
      _camP.set(camX,SLOPE*bb+cfg.camUp,bb); _camL.set(lerp(camX,fc.x,.25),-SLOPE*5.5+.5,-5.5);
    } else {
      camX+=(fc.x*cfg.camFollowX-camX)*Math.min(1,dt*4); const cy=clamp(fc.y+SLOPE*S0-bump(fc.x,fc.s),-4,4);
      _camP.set(camX,SLOPE*cfg.camBack+cfg.camUp+cy*.3,cfg.camBack); _camL.set(camX,-SLOPE*CAM_LOOK+cy*.3,-CAM_LOOK);
    }
    zoom+=(((game==='caught'||game==='over')&&outcome!=='race_done'&&outcome!=='cheese_goal'&&outcome!=='ta_goal'?1:0)-zoom)*Math.min(1,dt*ZOOM_SPEED);
    if(zoom>.001){ rel(_zL,fc.s,fc.x,fc.y); _zP.set(_zL.x+ZOOM_SIDE,ground(fc.x,fc.s-ZOOM_BACK)+SLOPE*S0+ZOOM_UP,_zL.z+ZOOM_BACK); const k=smooth(zoom); _camP.lerp(_zP,k); _camL.lerp(_zL,k); }
    camera.position.copy(_camP); camera.lookAt(_camL); drawTrail(dt);
    sun.target.position.set(camX,0,-2); sun.position.set(camX-6,16,-10);

    drawRankBar(); drawMiniMap();

    if(cfg.isTA){
      const rem=Math.max(0,Math.ceil(cfg.goalDist-fc.s)), key=`t:${rem}:${Math.floor(playTime*10)}`;
      if(key!==shownHudKey){ shownHudKey=key; $('hud').textContent=`${playTime.toFixed(2)} 秒`; $('hudSub').textContent=`500mタイムアタック / 残り ${rem}m`; }
    } else if(cfg.isRace){
      const st=getRaceStandings(), rk=st.findIndex(c=>c.idx===cfg.myIndex)+1, rem=Math.max(0,Math.ceil(cfg.goalDist-fc.s)), key=`r:${rk}:${rem}:${Math.floor(playTime*2)}`;
      if(key!==shownHudKey){ shownHudKey=key; $('hud').textContent=`${rk}位 / ${cheeseN} (残り ${rem}m)`; $('hudSub').textContent=`${cfg.course==='special'?'スペシャル':cfg.course==='board'?'板よけ':'ノーマル'}${cfg.isPointEligible?' (+5pt対象)':''} / ${playTime.toFixed(1)}秒`; }
    } else if(cfg.isOnline){
      const mc=mainCheese(), d=Math.min(cfg.goalDist,Math.max(0,Math.floor(mc.s))), me=cfg.myRole==='human'&&activeN>cfg.myIndex?chasers[cfg.myIndex]:null, gap=me?Math.max(0,Math.round(mc.s-me.s)):0, key=`o:${cfg.myRole}:${d}:${gap}`;
      if(key!==shownHudKey){ shownHudKey=key; if(cfg.myRole==='cheese'){$('hud').textContent=`${d} / ${cfg.goalDist} m`;$('hudSub').textContent=`チーズ役（ゴールで勝ち）/ 追っ手 ${activeN}人`;} else {$('hud').textContent=`チーズまで ${gap} m`;$('hudSub').textContent=`人間役（障害物すり抜け）/ チーズ ${d}/${cfg.goalDist}m`;} }
    } else if(cfg.mode==='human'){
      const mc=mainCheese(), gap=activeN>0?Math.max(0,Math.round(mc.s-chasers[0].s)):0, d=Math.min(cfg.goalDist,Math.max(0,Math.floor(mc.s))), key=`h:${gap}:${d}`;
      if(key!==shownHudKey){ shownHudKey=key; $('hud').textContent=`チーズまで ${gap} m`; $('hudSub').textContent=`チーズ ${d}/${cfg.goalDist}m / BEST ${formatBest('human',currentBest)}`; }
    } else {
      const d=Math.min(cfg.goalDist,Math.max(0,Math.floor(maxDist))), key=`c:${d}`;
      if(key!==shownHudKey){ shownHudKey=key; $('hud').textContent=`${d} / ${cfg.goalDist} m`; $('hudSub').textContent=`BEST ${Math.round(currentBest===null?d:Math.max(currentBest,d))} m${cfg.isPointEligible?'（3000mクリアで+5pt!）':''}`; }
    }
    renderer.render(scene, camera);
  }

  // ---------- 設定反映・オンライン（ランキング＆スキン同期対応） ----------
  function isGoalLockedMode(){
    if(userCfg.mode==='ta500') return true;
    if(userCfg.mode==='normal') return true;
    if(userCfg.mode==='race'&&userCfg.raceCourse==='normal') return true;
    return false;
  }
  function applyUserConfig(src, isOn, myR, myI, hCnt){
    const s=src||userCfg, isTA=!isOn&&s.mode==='ta500', isRace=isOn?s.onlineGame==='race':s.mode==='race';
    const course=isTA?'normal':(isOn?s.onlineCourse:(s.mode==='board'?'board':s.mode==='race'?s.raceCourse:'normal')), isB=course==='board';
    cfg.mode=isOn?'online':s.mode; cfg.onlineType=s.onlineType||'room'; cfg.isOnline=!!isOn; cfg.isRace=isRace; cfg.isTA=isTA; cfg.course=course;
    cfg.isPointEligible=!isOn&&((s.mode==='normal')||(s.mode==='race'&&s.raceCourse==='normal'));
    cfg.goalDist=isTA?TA_GOAL:(cfg.isPointEligible?MAX_GOAL:clamp(Math.round(s.goalDist||MAX_GOAL),300,MAX_GOAL));
    cfg.myRole=(isRace||isTA)?'cheese':(isOn?myR:(s.mode==='human'?'human':'cheese'));
    cfg.myIndex=myI||0; cfg.humanPlayerCount=(isRace||isTA)?0:(isOn?(hCnt||1):(s.mode==='human'?1:0));
    cfg.targetSpeed=isTA?14:s.speed; cfg.vxMax=isTA?6.5:s.vxMax; cfg.chSpeedMult=s.chSpeed; cfg.boardGap=s.boardGap; cfg.camUp=s.camUp;
    if(isRace||isTA){ cfg.chStart=cfg.chMax=0; } else if(isOn){ cfg.chStart=cfg.chMax=clamp(cfg.humanPlayerCount+Math.round(s.chasers),cfg.humanPlayerCount,35); }
    else { cfg.chStart=clamp(Math.round(s.chasers),1,CH_ALLOC); cfg.chMax=s.mode==='human'?cfg.chStart:Math.min(CH_ALLOC,Math.max(12,cfg.chStart+6)); }
    if(isB){
      cfg.halfW=20;cfg.cheeseR=.24;cfg.cheeseW=.12;cfg.gFall=250;cfg.drag=1;cfg.gAlong=cfg.targetSpeed*cfg.targetSpeed;cfg.turnCost=0;cfg.latAcc=Math.max(80,cfg.vxMax*5);
      cfg.bounce=0;cfg.bounceMin=1e30;cfg.airControl=1;cfg.chAddEvery=1e5;cfg.startDelay=isOn?.5:.01;cfg.startGrace=1;cfg.runAcc=Math.max(30,cfg.targetSpeed*1.6);cfg.chVx=Math.max(10,cfg.vxMax*.55);
      cfg.boardW=cfg.halfW;cfg.thigh=.1;cfg.shin=.7;cfg.camBack=5;cfg.camFollowX=1;cfg.camVfovMax=110;
    } else {
      cfg.halfW=(isRace||course==='special')?10:8;cfg.cheeseR=.32;cfg.cheeseW=.2;cfg.gFall=11.5;cfg.gAlong=9.8*Math.sin(SLOPE_ANG)*Math.pow(cfg.targetSpeed/9.5,1.3);
      cfg.drag=cfg.gAlong/(cfg.targetSpeed*cfg.targetSpeed);cfg.turnCost=course==='special'?.03:.1;cfg.latAcc=Math.max(26,cfg.vxMax*5.5);
      cfg.bounce=course==='special'?.15:.3;cfg.bounceMin=course==='special'?2.2:1.2;cfg.airControl=course==='special'?.85:.6;cfg.chAddEvery=100;
      cfg.startDelay=cfg.humanPlayerCount>0?.5:1;cfg.startGrace=cfg.humanPlayerCount>0?1:2;cfg.runAcc=Math.max(7,cfg.targetSpeed*.8);cfg.chVx=Math.max(3.2,cfg.vxMax*.7);
      cfg.thigh=.43;cfg.shin=.43;cfg.camBack=cfg.myRole==='human'?7:8;cfg.camFollowX=.75;cfg.camVfovMax=68;
    }
    cfg.xlim=cfg.halfW-cfg.cheeseW/2-.1; for(const v of cheeseVisuals)v.mesh.scale.set(cfg.cheeseW,cfg.cheeseR,cfg.cheeseR); rebuildBodyDist(); resize();
  }

  const setNetStatus=(st,tx)=>{ $('netDot').className='dot '+(st||''); $('netStatus').textContent=tx; }, r2=v=>Math.round(v*100)/100;
  const cleanRoomText=raw=>raw.trim().replace(/[！-～]/g,ch=>String.fromCharCode(ch.charCodeAt(0)-0xFEE0)).replace(/\s+/g,'');
  const encodeRoomId=raw=>{ const s=cleanRoomText(raw).toLowerCase(); return s?Array.from(new TextEncoder().encode(s),b=>b.toString(16).padStart(2,'0')).join('').slice(0,48):''; };
  const clearNetTimers=()=>{ if(net.startTimer){clearTimeout(net.startTimer);net.startTimer=null;} if(net.joinTimer){clearTimeout(net.joinTimer);net.joinTimer=null;} if(net.helloTimer){clearInterval(net.helloTimer);net.helloTimer=null;} };
  const buildHostRoster=()=>{ const arr=[{id:'host',name:profile.name||'ホスト',skin:profile.skin}]; let n=1; for(const cl of net.clients.values())arr.push({id:cl.id,name:cl.name||`ゲスト${n++}`,skin:cl.skin||'classic'}); net.roster=arr; return arr; };
  const broadcastToClients=obj=>{ for(const cl of net.clients.values())if(cl.conn&&cl.conn.open){try{cl.conn.send(obj);}catch{}} };
  const sendToHost=obj=>{ if(net.hostConn&&net.hostConn.open){try{net.hostConn.send(obj);}catch{}} };
  const sendLobbyPreview=()=>{ if(net.role==='host')broadcastToClients({t:'lobby',cfg:{...userCfg},roster:buildHostRoster(),lb:profile.lb500}); };

  function sendHostSnapshot(force){
    if(net.role!=='host'||net.clients.size===0)return; const now=performance.now(); if(!force&&now-net.lastSend<SYNC_MS)return; net.lastSend=now;
    const ch=[], cs=[];
    for(let i=0;i<cheeseN;i++){ const c=cheeses[i]; ch.push({s:r2(c.s),x:r2(c.x),y:r2(c.y),v:r2(c.v),vx:r2(c.vx),tl:r2(c.tilt),h:c.held?c.held.idx:-1,hd:c.hand,fn:c.finished?1:0,ft:r2(c.finishTime),bt:r2(c.boostT)}); }
    for(let i=0;i<activeN;i++){ const c=chasers[i],it={st:c.state,s:r2(c.s),x:r2(c.x),v:r2(c.v),yw:r2(c.yaw),ph:r2(c.phase),cl:r2(c.cool)}; if(c.state==='windup')it.ck=r2(c.crouchK); if(c.state==='rag'||c.state==='getup'){const a=new Array(NP*3);for(let j=0;j<NP*3;j++)a[j]=r2(c.p[j]);it.p=a;} cs.push(it); }
    broadcastToClients({t:'snap',g:game,oc:outcome,wc:winnerIdx,md:r2(maxDist),pt:r2(playTime),ro:raceOrder,ch,cs});
  }
  function sendGuestInput(force){
    if(!net.connected||net.role!=='guest'||game!=='playing')return; const now=performance.now(); if(!force&&!diveRequested&&now-net.lastSend<SYNC_MS)return; net.lastSend=now;
    const inp=input(); sendToHost({t:'in',x:r2(inp.x),y:r2(inp.y),d:diveRequested}); diveRequested=false;
  }
  function handleHostData(cl, msg){
    if(!msg||typeof msg!=='object')return;
    if(msg.t==='hello'){
      if(msg.name) cl.name=String(msg.name).slice(0,14);
      if(msg.skin) cl.skin=String(msg.skin);
      if(Array.isArray(msg.lb)) mergeLeaderboard(msg.lb);
      const roster=buildHostRoster();
      try{cl.conn.send({t:'welcome',myId:cl.id,cfg:{...userCfg},roster,lb:profile.lb500});}catch{}
      broadcastToClients({t:'lobby',cfg:{...userCfg},roster,lb:profile.lb500});
      syncMenuUI();
    } else if(msg.t==='in'){ cl.input.x=clamp(Number(msg.x)||0,-1,1); cl.input.y=clamp(Number(msg.y)||0,-1,1); if(msg.d)cl.input.dive=true; }
  }
  function handleGuestData(msg){
    if(!msg||typeof msg!=='object')return;
    if(Array.isArray(msg.lb)) mergeLeaderboard(msg.lb);
    if(msg.t==='welcome'){ clearNetTimers(); net.connected=true; net.searching=false; net.myId=msg.myId; if(msg.cfg)Object.assign(userCfg,msg.cfg,{mode:'online'}); if(msg.roster)net.roster=msg.roster; setNetStatus('ok',`接続完了！（部屋: ${net.room} / ${net.roster.length}人参加中）ホスト開始待ち`); syncMenuUI(); }
    else if(msg.t==='lobby'){ if(msg.cfg)Object.assign(userCfg,msg.cfg,{mode:'online'}); if(msg.roster)net.roster=msg.roster; if(net.connected)setNetStatus('ok',`接続中（部屋: ${net.room} / ${net.roster.length}人参加中）ホスト開始待ち`); syncMenuUI(); }
    else if(msg.t==='start'){ clearNetTimers(); net.connected=true; net.searching=false; Object.assign(userCfg,msg.cfg,{mode:'online'}); const a=msg.assign[net.myId]||{role:'human',idx:0}; startMatchInternal(true,msg.seed,a.role,a.idx,msg.cheeseN,msg.humanCount,null,msg.labels,msg.skins); }
    else if(msg.t==='snap') net.snap=msg; else if(msg.t==='menu') setupWorld(false);
  }
  function disconnectNet(silent){
    net.searchGen++; clearNetTimers(); for(const cl of net.clients.values()){try{cl.conn.close();}catch{}} net.clients.clear();
    if(net.hostConn){try{net.hostConn.close();}catch{} net.hostConn=null;} if(net.peer){try{net.peer.destroy();}catch{} net.peer=null;}
    net.role='none'; net.searching=false; net.connected=false; net.room=''; net.roster=[]; net.snap=null;
    if(!silent) setNetStatus('',userCfg.onlineType==='free'?'ルールとコースを選んで開始してください':'合言葉を決めて部屋を作るか入ってください（最大6人）');
    syncMenuUI();
  }
  function acceptHostConnection(conn, gen, isFree){
    if(gen!==net.searchGen||net.clients.size>=MAX_ONLINE_PLAYERS-1||game!=='menu'){ const rej=()=>{try{conn.send({t:'busy'});}catch{} setTimeout(()=>{try{conn.close();}catch{}},200);}; if(conn.open)rej(); else conn.on('open',rej); return; }
    const cid=conn.peer, cl={conn,id:cid,name:'',skin:'classic',input:{x:0,y:0,dive:false}}; net.clients.set(cid,cl); conn.on('data',m=>handleHostData(cl,m));
    const onOpen=()=>{
      if(gen!==net.searchGen||!net.clients.has(cid))return; net.connected=true; const roster=buildHostRoster();
      try{conn.send({t:'welcome',myId:cid,cfg:{...userCfg},roster,lb:profile.lb500});}catch{} broadcastToClients({t:'lobby',cfg:{...userCfg},roster,lb:profile.lb500});
      if(isFree){ setNetStatus('ok',`${roster.length}人集まりました！${roster.length>=MAX_ONLINE_PLAYERS?'間もなく開始…':'数秒後に開始（受付中）'}`); if(net.startTimer)clearTimeout(net.startTimer); net.startTimer=setTimeout(()=>{ if(gen===net.searchGen&&game==='menu'&&net.clients.size>0)hostLaunchOnlineMatch(); },roster.length>=MAX_ONLINE_PLAYERS?900:3500); }
      else setNetStatus('ok',`${roster.length}/${MAX_ONLINE_PLAYERS}人参加中（部屋: ${net.room}）「対戦スタート」で開始`);
      syncMenuUI();
    };
    if(conn.open)onOpen(); else conn.on('open',onOpen);
    conn.on('close',()=>{
      if(!net.clients.has(cid))return; net.clients.delete(cid);
      if(game!=='menu'){ for(let i=0;i<cheeseN;i++)if(cheeses[i].ctrlId===cid)cheeses[i].ctrlId='ai'; for(let i=0;i<activeN;i++)if(chasers[i].ctrlId===cid){chasers[i].isPlayer=false;chasers[i].ctrlId=null;} }
      const roster=buildHostRoster(); net.connected=net.clients.size>0; broadcastToClients({t:'lobby',cfg:{...userCfg},roster,lb:profile.lb500});
      if(game==='menu'){ if(net.clients.size===0){if(net.startTimer){clearTimeout(net.startTimer);net.startTimer=null;} setNetStatus('wait',isFree?`待機中（${net.room}）…`:`部屋「${net.room}」待機中（1/${MAX_ONLINE_PLAYERS}人）`);} else setNetStatus('ok',`${roster.length}/${MAX_ONLINE_PLAYERS}人参加中`); syncMenuUI(); }
    });
  }
  function startFreeMatch(){
    if(typeof Peer==='undefined'){setNetStatus('err','PeerJS読み込み失敗');return;}
    disconnectNet(true); const gen=++net.searchGen, key=`${userCfg.onlineGame}_${userCfg.onlineCourse}`;
    Object.assign(userCfg,FREE_RULES[key],{mode:'online',onlineType:'free'}); net.matchKind='free'; net.searching=true; setNetStatus('wait','全国の対戦相手を探しています…'); syncMenuUI();
    tryFreeSlot(0,key,gen);
  }
  function tryFreeSlot(idx, key, gen){
    if(gen!==net.searchGen)return;
    if(idx>=FREE_SLOTS){ setNetStatus('wait','混み合っています… 再探索中'); net.startTimer=setTimeout(()=>tryFreeSlot(0,key,gen),1500); return; }
    const slotId=`${FREE_PREFIX}${key}-${idx}`, hp=new Peer(slotId,PEER_OPTS); net.peer=hp; let dec=false;
    setNetStatus('wait',`対戦相手を探しています…（エリア ${idx+1}）`);
    hp.on('open',()=>{ if(gen!==net.searchGen){try{hp.destroy();}catch{}return;} dec=true;net.role='host';net.myId='host';net.room=`全国#${idx+1}`;buildHostRoster();setNetStatus('wait',`待機中（${net.room}）… 参加待ち`);syncMenuUI(); });
    hp.on('disconnected',()=>{ if(gen===net.searchGen&&!hp.destroyed){try{hp.reconnect();}catch{}} });
    hp.on('connection',c=>acceptHostConnection(c,gen,true));
    hp.on('error',e=>{ if(gen!==net.searchGen||dec)return; dec=true;try{hp.destroy();}catch{}net.peer=null; if(e.type==='unavailable-id')probeFreeSlotAsGuest(idx,key,gen); else net.startTimer=setTimeout(()=>tryFreeSlot(idx+1,key,gen),400); });
  }
  function probeFreeSlotAsGuest(idx, key, gen){
    if(gen!==net.searchGen)return; const slotId=`${FREE_PREFIX}${key}-${idx}`, gp=new Peer(PEER_OPTS); net.peer=gp; let st=false;
    const fail=()=>{ if(st||gen!==net.searchGen)return; st=true; clearTimeout(tm); try{gp.destroy();}catch{} if(net.peer===gp)net.peer=null; tryFreeSlot(idx+1,key,gen); };
    const tm=setTimeout(fail,4000);
    gp.on('open',()=>{
      if(st||gen!==net.searchGen){try{gp.destroy();}catch{}return;}
      const conn=gp.connect(slotId,{reliable:true,serialization:'json'}); conn.on('open',()=>{try{conn.send({t:'hello',name:profile.name,skin:profile.skin,lb:profile.lb500});}catch{}});
      conn.on('data',m=>{ if(gen!==net.searchGen)return; if(!st){ if(m&&m.t==='busy'){fail();return;} if(m&&(m.t==='welcome'||m.t==='start')){ st=true;clearTimeout(tm);net.role='guest';net.room=`全国#${idx+1}`;net.hostConn=conn; conn.on('close',()=>{const wp=game!=='menu';disconnectNet(true);setNetStatus('err','ホスト切断');if(wp)setupWorld(false);else syncMenuUI();}); handleGuestData(m); } } else handleGuestData(m); });
      conn.on('close',()=>{if(!st)fail();}); conn.on('error',()=>{if(!st)fail();});
    });
    gp.on('error',()=>{if(!st)fail();});
  }
  function createRoom(){
    if(typeof Peer==='undefined'){setNetStatus('err','PeerJS読み込み失敗');return;}
    let code=cleanRoomText($('roomCode').value); if(!code){code=String(Math.floor(1000+Math.random()*9000));$('roomCode').value=code;}
    disconnectNet(true); const gen=++net.searchGen; net.matchKind='room'; net.role='host'; net.myId='host'; net.room=code; buildHostRoster();
    setNetStatus('wait',`部屋「${code}」を作成中…`); syncMenuUI();
    const peer=new Peer(ROOM_PREFIX+encodeRoomId(code),PEER_OPTS); net.peer=peer;
    peer.on('open',()=>{ if(gen===net.searchGen){setNetStatus('wait',`部屋「${code}」作成完了！（1/${MAX_ONLINE_PLAYERS}人）`);syncMenuUI();} });
    peer.on('disconnected',()=>{ if(gen===net.searchGen&&!peer.destroyed){try{peer.reconnect();}catch{}} });
    peer.on('connection',c=>acceptHostConnection(c,gen,false));
    peer.on('error',e=>{ if(gen!==net.searchGen)return; setNetStatus('err',e.type==='unavailable-id'?`合言葉「${code}」は使用中です`:`エラー(${e.type})`); disconnectNet(true); });
  }
  function joinRoom(){
    if(typeof Peer==='undefined'){setNetStatus('err','PeerJS読み込み失敗');return;}
    const code=cleanRoomText($('roomCode').value); if(!code){setNetStatus('err','合言葉を入力してください');return;}
    disconnectNet(true); const gen=++net.searchGen; net.matchKind='room'; net.role='guest'; net.room=code; setNetStatus('wait',`部屋「${code}」へ接続中…`); syncMenuUI();
    const peer=new Peer(PEER_OPTS); net.peer=peer;
    net.joinTimer=setTimeout(()=>{ if(gen===net.searchGen&&!net.connected){disconnectNet(true);setNetStatus('err','タイムアウト。相手が先に部屋を作っているか確認してください');syncMenuUI();} },12000);
    peer.on('open',()=>{
      if(gen!==net.searchGen)return;
      const conn=peer.connect(ROOM_PREFIX+encodeRoomId(code),{reliable:true,serialization:'json'}); net.hostConn=conn;
      conn.on('data',m=>{ if(gen!==net.searchGen)return; if(m&&m.t==='busy'){disconnectNet(true);setNetStatus('err','満員または対戦中です');syncMenuUI();return;} handleGuestData(m); });
      const onOpen=()=>{ if(gen!==net.searchGen||net.hostConn!==conn)return; const hi=()=>{if(gen===net.searchGen&&!net.connected&&conn.open){try{conn.send({t:'hello',name:profile.name,skin:profile.skin,lb:profile.lb500});}catch{}}}; hi(); let t=0; net.helloTimer=setInterval(()=>{if(++t>6||net.connected||gen!==net.searchGen){clearInterval(net.helloTimer);net.helloTimer=null;return;}hi();},600); };
      if(conn.open)onOpen(); else conn.on('open',onOpen);
      conn.on('close',()=>{ if(gen!==net.searchGen)return; const wp=game!=='menu'; disconnectNet(true); setNetStatus('err','ホスト切断'); if(wp)setupWorld(false); else syncMenuUI(); });
      conn.on('error',()=>{ if(gen!==net.searchGen)return; disconnectNet(true); setNetStatus('err','P2P接続失敗。別の回線でお試しください'); syncMenuUI(); });
    });
    peer.on('error',e=>{ if(gen!==net.searchGen)return; disconnectNet(true); setNetStatus('err',e.type==='peer-unavailable'?`部屋「${code}」が見つかりません`:`エラー(${e.type})`); syncMenuUI(); });
  }
  function hostLaunchOnlineMatch(){
    if(net.role!=='host'||net.clients.size===0)return; clearNetTimers();
    const roster=buildHostRoster(), seed=Math.floor(Math.random()*1e6), assign={}, labels={cheeses:[],humans:[]}, skins=[]; let tc=1, hc=0;
    if(userCfg.onlineGame==='race'){
      tc=clamp(Math.max(roster.length,Math.round(userCfg.chasers)),roster.length,MAX_CHEESES);
      roster.forEach((p,i)=>{assign[p.id]={role:'cheese',idx:i};labels.cheeses.push(p.name);skins.push(p.skin||'classic');});
      for(let i=roster.length;i<tc;i++){labels.cheeses.push(`CPU${i-roster.length+1}`);skins.push('classic');}
    } else {
      let ci=userCfg.hostRole==='cheese'?0:1; if(net.matchKind==='free')ci=Math.floor(Math.random()*roster.length);
      assign[roster[ci].id]={role:'cheese',idx:0}; labels.cheeses.push(roster[ci].name); skins.push(roster[ci].skin||'classic');
      roster.forEach((p,i)=>{if(i!==ci){assign[p.id]={role:'human',idx:hc++};labels.humans.push(p.name);}});
    }
    broadcastToClients({t:'start',seed,cfg:{...userCfg,mode:'online'},assign,cheeseN:tc,humanCount:hc,labels,skins});
    const ha=assign['host']; startMatchInternal(true,seed,ha.role,ha.idx,tc,hc,assign,labels,skins);
  }

  // ---------- スキンショップ＆ランキングモーダル ----------
  function renderShop(){
    $('shopPtsVal').textContent = `${profile.pts} pt`;
    const grid = $('skinGrid'); grid.innerHTML = '';
    for(const sk of SKINS){
      const owned = profile.owned.includes(sk.id), eq = profile.skin === sk.id;
      const btn = document.createElement('button'); btn.type = 'button';
      btn.className = 'skin-item' + (eq ? ' equipped' : '');
      const status = eq ? '装備中' : owned ? 'タップで装備' : `${sk.cost} pt で解放`;
      btn.innerHTML = `<span class="skin-swatch" style="background:${sk.swatch}"></span><span class="skin-meta"><strong>${sk.name}</strong><span>${status}</span></span>`;
      btn.addEventListener('click', () => {
        if(owned){ profile.skin = sk.id; saveProfile(); applyCheeseSkin(0, profile.skin, null); renderShop(); }
        else if(profile.pts >= sk.cost){ profile.pts -= sk.cost; profile.owned.push(sk.id); profile.skin = sk.id; saveProfile(); applyCheeseSkin(0, profile.skin, null); renderShop(); showToast(`${sk.name}を解放！`,'boost'); }
        else showToast(`あと ${sk.cost - profile.pts} pt 必要です`, 'fall');
      });
      grid.appendChild(btn);
    }
  }
  function renderLeaderboard(){
    const ul = $('lbList'); ul.innerHTML = '';
    const myNm = (profile.name || '').trim();
    profile.lb500.forEach((e, i) => {
      const li = document.createElement('li'), isMe = e.name === myNm;
      const sk = SKINS.find(s => s.id === e.skin) || SKINS[0];
      li.className = 'rank-item' + (isMe ? ' me' : '');
      li.innerHTML = `<span><i class="rank-dot" style="background:${sk.rind}"></i>${i+1}位 ${e.name}${isMe?' (あなた)':''}</span><span>${e.time.toFixed(2)} 秒</span>`;
      ul.appendChild(li);
    });
  }

  // ---------- 開始・結果・ループ・UI ----------
  function startMatchInternal(isOn, fixedSeed, myR, myI, tc, hc, assignMap, labels, skins){
    applyUserConfig(userCfg,isOn,myR,myI,hc); cheeseN=cfg.isRace?clamp(tc,2,MAX_CHEESES):1; raceOrder=[]; raceFinishTimer=0; winnerIdx=-1;
    const scs=(!cfg.isRace&&!cfg.isTA&&cfg.humanPlayerCount>0)?4.5:0;
    for(let i=0;i<cheeseN;i++){
      const c=cheeses[i], gap=cfg.isRace?Math.min(2.4,cfg.halfW*1.5/cheeseN):0, sx=cfg.isRace?(i-(cheeseN-1)/2)*gap:0;
      Object.assign(c,{s:scs,x:sx,y:ground(sx,scs)+cfg.cheeseR,v:0,vx:0,vy:0,rot:0,tilt:0,tiltV:0,grounded:true,held:null,hand:0,finished:false,finishTime:0,boostT:0,boostCool:0,aiBias:Math.random()*2-1,aiSpeedK:.5+Math.random()*.5});
      let sk = 'classic', fallRind = i === 0 ? null : RACER_COLORS[i];
      if(isOn&&assignMap){
        let ow='ai'; for(const[pid,a]of Object.entries(assignMap))if(a.role==='cheese'&&a.idx===i)ow=pid;
        c.ctrlId=ow; c.label=(labels&&labels.cheeses[i])||`P${i+1}`; sk=(skins&&skins[i])||'classic';
        if(sk!=='classic') fallRind=null;
      } else if(!isOn){
        c.ctrlId=cfg.mode==='human'?'ai':(i===0?'host':'ai'); c.label=i===0?(profile.name||'あなた'):`CPU${i}`;
        if(i===0){ sk=profile.skin; fallRind=null; }
      } else {
        c.label=(labels&&labels.cheeses[i])||`P${i+1}`; sk=(skins&&skins[i])||'classic';
        if(sk!=='classic') fallRind=null;
      }
      applyCheeseSkin(i, sk, fallRind);
    }
    activeN=cfg.chStart;
    for(let i=0;i<CH_ALLOC;i++){
      const c=chasers[i]; c.active=i<activeN; c.isPlayer=i<cfg.humanPlayerCount; c.ctrlId=null;
      if(c.isPlayer){
        if(isOn&&assignMap){ for(const[pid,a]of Object.entries(assignMap))if(a.role==='human'&&a.idx===i)c.ctrlId=pid; c.label=(labels&&labels.humans[i])||`人間${i+1}`; }
        else if(!isOn){ c.ctrlId='host'; c.label='あなた'; } else c.label=(labels&&labels.humans[i])||`人間${i+1}`;
      } else c.label='AI追っ手';
      if(c.active) resetChaser(c,i,true);
    }
    S0=(!cfg.isRace&&!cfg.isTA&&cfg.myRole==='human')?-3.2:scs; maxDist=0; camX=0; zoom=0; trail.length=0; trailK=0; overT=0; acc=0; playTime=0;
    shownHudKey=''; diveRequested=false; outcome='caught'; net.snap=null; for(const cl of net.clients.values())cl.input={x:0,y:0,dive:false};
    currentBest=getBest(cfg.mode); endJoy(); for(const ch of chunks)ch.k=null;
    clearObstacles(cfg.isTA ? TA_SEED : fixedSeed); updateObstacles();
    $('resultOverlay').classList.add('hidden'); game='playing'; $('menuOverlay').classList.add('hidden'); $('hudWrap').classList.remove('hidden'); $('menuBtn').classList.remove('hidden');
    $('diveBtn').classList.toggle('hidden',cfg.isRace||cfg.isTA||cfg.myRole!=='human');
  }
  function setupWorld(startPlaying){
    if(startPlaying){
      if(userCfg.mode==='online'){ if(net.role==='host'&&net.clients.size>0)hostLaunchOnlineMatch(); return; }
      const isR=userCfg.mode==='race'; startMatchInternal(false,undefined,userCfg.mode==='human'?'human':'cheese',0,isR?clamp(Math.round(userCfg.chasers),2,MAX_CHEESES):1,userCfg.mode==='human'?1:0,null,null,null); return;
    }
    const isOn=userCfg.mode==='online'; applyUserConfig(userCfg,isOn,isOn?userCfg.hostRole:(userCfg.mode==='human'?'human':'cheese'),0,userCfg.mode==='human'?1:0);
    cheeseN=cfg.isRace?clamp(Math.round(userCfg.chasers),2,MAX_CHEESES):1;
    for(let i=0;i<cheeseN;i++){
      const sx=cfg.isRace?(i-(cheeseN-1)/2)*2:0;
      Object.assign(cheeses[i],{s:0,x:sx,y:ground(sx,0)+cfg.cheeseR,v:0,vx:0,vy:0,rot:0,tilt:0,tiltV:0,grounded:true,held:null,hand:0,finished:false,boostT:0});
      applyCheeseSkin(i, i===0?profile.skin:'classic', i===0?null:RACER_COLORS[i]);
    }
    S0=0;maxDist=0;camX=0;zoom=0;trail.length=0;trailK=0;overT=0;acc=0;playTime=0;shownHudKey='';diveRequested=false;endJoy();
    activeN=Math.min(cfg.chStart,20); for(let i=0;i<CH_ALLOC;i++){chasers[i].active=i<activeN;chasers[i].isPlayer=false;if(chasers[i].active)resetChaser(chasers[i],i,true);}
    for(const ch of chunks)ch.k=null; clearObstacles(cfg.isTA ? TA_SEED : undefined); updateObstacles();
    game='menu'; $('resultOverlay').classList.add('hidden'); $('menuOverlay').classList.remove('hidden'); $('hudWrap').classList.add('hidden'); $('menuBtn').classList.add('hidden'); $('diveBtn').classList.add('hidden');
    $('rankBarWrap').classList.add('hidden'); $('miniMap').classList.add('hidden'); syncMenuUI();
  }
  function showOver(){
    game='over'; $('diveBtn').classList.add('hidden'); if(cfg.isOnline&&net.role==='host')sendHostSnapshot(true);
    const d=Math.min(cfg.goalDist,Math.max(1,Math.floor(maxDist))), tEl=$('resTitle'), rEl=$('rankList'), cLbl=cfg.course==='special'?'スペシャル':cfg.course==='board'?'板よけ':'ノーマル';
    rEl.classList.add('hidden'); rEl.innerHTML=''; let isNew=false, gotPts=0;

    if(cfg.isTA){
      const t = Math.round(playTime * 100) / 100;
      $('resMode').textContent='500m タイムアタック（ノーマル固定）';
      tEl.classList.remove('hidden'); tEl.classList.remove('fail'); tEl.textContent='500m フィニッシュ！';
      isNew = saveBest('ta500', t);
      mergeLeaderboard([{ name:profile.name||'あなた', time:t, skin:profile.skin }]);
      const myRank = profile.lb500.findIndex(e => e.name === (profile.name||'あなた')) + 1;
      $('dist').textContent=`${t.toFixed(2)} 秒`;
      $('resBest').textContent=`ランキング ${myRank}位 / ${profile.lb500.length}人  (BEST ${formatBest('ta500',getBest('ta500'))})`;
      rEl.classList.remove('hidden');
      profile.lb500.slice(0, 6).forEach((e, i) => {
        const li=document.createElement('li'), isMe=e.name===(profile.name||'あなた'), sk=SKINS.find(s=>s.id===e.skin)||SKINS[0];
        li.className='rank-item'+(isMe?' me':'');
        li.innerHTML=`<span><i class="rank-dot" style="background:${sk.rind}"></i>${i+1}位 ${e.name}${isMe?' (あなた)':''}</span><span>${e.time.toFixed(2)}秒</span>`;
        rEl.appendChild(li);
      });
    } else if(cfg.isRace){
      const st=getRaceStandings(), rk=st.findIndex(c=>c.idx===cfg.myIndex)+1, mc=cheeses[cfg.myIndex];
      $('resMode').textContent=`${cfg.isOnline?'オンライン':''}チーズレース（${cLbl} / ${cfg.goalDist}m）`;
      tEl.classList.remove('hidden'); tEl.textContent=rk===1?'1位 優勝！':`${rk}位 フィニッシュ`; tEl.classList.toggle('fail',rk>1);
      $('dist').textContent=mc.finished?`${mc.finishTime.toFixed(2)} 秒`:`${Math.floor(mc.s)} m`;
      if(!cfg.isOnline&&rk===1&&mc.finished){
        isNew=saveBest('race',mc.finishTime);
        if(cfg.isPointEligible) gotPts=5;
      }
      $('resBest').textContent=cfg.isOnline?`${cheeseN}チーズ対戦`:`BEST ${formatBest('race',getBest('race'))}`;
      rEl.classList.remove('hidden');
      st.forEach((c,i)=>{ const li=document.createElement('li'); li.className='rank-item'+(c.idx===cfg.myIndex?' me':''); li.innerHTML=`<span><i class="rank-dot" style="background:${c.color}"></i>${i+1}位 ${c.label}${c.idx===cfg.myIndex?' (あなた)':''}</span><span>${c.finished?c.finishTime.toFixed(2)+'秒':Math.floor(c.s)+'m'}</span>`; rEl.appendChild(li); });
    } else if(cfg.isOnline){
      $('resMode').textContent=`オンライン鬼ごっこ（${cLbl} / ${cfg.goalDist}m）`; tEl.classList.remove('hidden');
      const cw=outcome==='cheese_goal', iw=(cfg.myRole==='cheese'&&cw)||(cfg.myRole==='human'&&!cw);
      tEl.textContent=iw?'あなたの勝ち！':'相手の勝ち…'; tEl.classList.toggle('fail',!iw);
      if(cw){$('dist').textContent=`${cfg.goalDist}m GOAL!`;$('resBest').textContent=`チーズがゴール！（${playTime.toFixed(1)}秒）`;}
      else {$('dist').textContent=`${d} m`;$('resBest').textContent=`${(winnerIdx>=0&&chasers[winnerIdx]?chasers[winnerIdx].label:'追っ手')}が捕獲！（${playTime.toFixed(1)}秒）`;}
    } else if(cfg.mode==='human'){
      $('resMode').textContent=PRESETS.human.label; tEl.classList.remove('hidden');
      if(outcome==='human_win'){tEl.textContent='チーズ捕獲成功！';tEl.classList.remove('fail');isNew=saveBest('human',d);$('dist').textContent=`${d} m`;}
      else {tEl.textContent=outcome==='cheese_goal'?`${cfg.goalDist}m ゴールされた…`:'他の追っ手に先を越された…';tEl.classList.add('fail');$('dist').textContent='失敗';}
      $('resBest').textContent=`BEST ${formatBest('human',getBest('human'))}（${playTime.toFixed(1)}秒）`;
    } else {
      $('resMode').textContent=PRESETS[cfg.mode].label;
      if(outcome==='cheese_goal'){
        tEl.classList.remove('hidden'); tEl.classList.remove('fail'); tEl.textContent=`${cfg.goalDist}m ゴール！チーズの勝ち！`;
        if(cfg.isPointEligible) gotPts=5;
      } else tEl.classList.add('hidden');
      isNew=saveBest(cfg.mode,d); $('dist').textContent=`${d} m`; $('resBest').textContent=`BEST ${formatBest(cfg.mode,getBest(cfg.mode))}`;
    }

    if(gotPts > 0){ profile.pts += gotPts; saveProfile(); $('resPts').textContent = `+${gotPts} ポイント獲得！（所持 ${profile.pts} pt）`; }
    $('resPts').classList.toggle('hidden', gotPts <= 0);

    const isH=cfg.isOnline&&net.role==='host'&&net.clients.size>0, isF=cfg.isOnline&&cfg.onlineType==='free';
    $('nextFreeBtn').classList.toggle('hidden',!isF); $('swapRetryBtn').classList.toggle('hidden',!(isH&&!cfg.isRace)); $('retryBtn').classList.toggle('hidden',cfg.isOnline&&(!isH||isF));
    $('toMenuBtn').textContent=cfg.isOnline?'ロビーへ':'モード・設定'; $('resNewBest').classList.toggle('hidden',!isNew); $('resultOverlay').classList.remove('hidden');
  }

  let last=performance.now(), acc=0;
  function loop(now){
    const dt=Math.min((now-last)/1000,1/30); last=now;
    if(game!=='menu'){
      if(cfg.isOnline&&net.role==='guest'){ sendGuestInput(false); applyGuestInterpolation(dt); }
      else { acc+=dt*(game==='playing'?1:SLOW_SCALE); while(acc>=STEP){step(STEP);acc-=STEP;} if(game==='caught'){overT+=dt;if(overT>=(outcome==='race_done'||outcome==='cheese_goal'||outcome==='ta_goal'?.85:OVER_DELAY))showOver();} if(cfg.isOnline&&net.role==='host')sendHostSnapshot(false); }
    }
    draw(dt); requestAnimationFrame(loop);
  }

  function syncMenuUI(){
    const m=userCfg.mode, isOn=m==='online', isTA=m==='ta500', isR=isOn?userCfg.onlineGame==='race':m==='race', isF=isOn&&userCfg.onlineType==='free', isRm=isOn&&userCfg.onlineType==='room', isH=m==='human';
    const isB=isOn?userCfg.onlineCourse==='board':(m==='board'||(m==='race'&&userCfg.raceCourse==='board')), isG=isOn&&net.role==='guest', busy=net.searching||net.role!=='none';
    const goalLocked = isGoalLockedMode();
    if(isTA) userCfg.goalDist = TA_GOAL;
    else if(goalLocked) userCfg.goalDist = MAX_GOAL;

    document.querySelectorAll('.mode-btn').forEach(b=>b.classList.toggle('active',b.dataset.mode===m));
    $('modeHint').textContent=PRESETS[m].hint; $('bestBox').classList.toggle('hidden',isOn); $('ta500Box').classList.toggle('hidden',!isTA);
    $('raceCourseBox').classList.toggle('hidden',m!=='race'); $('onlineBox').classList.toggle('hidden',!isOn);
    if(m==='race')document.querySelectorAll('[data-rcourse]').forEach(b=>b.classList.toggle('active',b.dataset.rcourse===userCfg.raceCourse));
    if(!isOn){ $('menuBestLabel').textContent=`${PRESETS[m].label} ベスト`; $('menuBestVal').textContent=formatBest(m,getBest(m)); }
    else {
      document.querySelectorAll('[data-match]').forEach(b=>{b.classList.toggle('active',b.dataset.match===userCfg.onlineType);b.disabled=busy;});
      document.querySelectorAll('[data-otype]').forEach(b=>{b.classList.toggle('active',b.dataset.otype===userCfg.onlineGame);b.disabled=isF?busy:isG;});
      document.querySelectorAll('[data-course]').forEach(b=>{b.classList.toggle('active',b.dataset.course===userCfg.onlineCourse);b.disabled=isF?busy:isG;});
      $('freeMatchUI').classList.toggle('hidden',!isF); $('roomMatchUI').classList.toggle('hidden',!isRm); $('roleToggleBox').classList.toggle('hidden',isR);
      const ch=$('playerChips'); if(net.roster.length>0){ch.classList.remove('hidden');ch.innerHTML=net.roster.map(p=>`<span class="chip${p.id===net.myId?' me':''}">${p.name}${p.id===net.myId?'(あなた)':''}</span>`).join('');}else ch.classList.add('hidden');
      if(isF){ $('freeSearchBtn').classList.toggle('hidden',busy); $('freeCancelBtn').classList.toggle('hidden',!busy); $('freeStartNowBtn').classList.toggle('hidden',!(net.role==='host'&&net.clients.size>0)); }
      else { $('connectForm').classList.toggle('hidden',net.role!=='none'); $('connectedControls').classList.toggle('hidden',net.role==='none'); $('roomStartBtn').classList.toggle('hidden',!(net.role==='host'&&net.clients.size>0)); $('copyInviteBtn').classList.toggle('hidden',net.role!=='host'); $('roleToggleLabel').textContent=isG?'ホストが役割を設定します':'ホストの役割（他は人間）'; document.querySelectorAll('[data-role]').forEach(b=>{b.classList.toggle('active',b.dataset.role===userCfg.hostRole);b.disabled=isG;}); }
    }
    $('settingsArea').classList.toggle('hidden',isF); $('bottomStartRow').classList.toggle('hidden',isF);
    $('rowSpeed').classList.toggle('hidden',isTA); $('rowVx').classList.toggle('hidden',isTA);
    $('rowChasers').classList.toggle('hidden',isTA); $('rowChSpeed').classList.toggle('hidden',isR||isTA);
    const ci=$('optChasers');
    if(isR){ci.min='2';ci.max=String(MAX_CHEESES);userCfg.chasers=clamp(userCfg.chasers,2,MAX_CHEESES);$('lblChasers').textContent=isOn?'チーズ総数（足りない分はCPU）':'チーズの総数（自分＋CPU）';}
    else if(isRm){ci.min='0';ci.max='30';userCfg.chasers=clamp(userCfg.chasers,0,30);$('lblChasers').textContent='追加AI追っ手（0人で人間のみ）';}
    else if(!isTA){ci.min='1';ci.max='300';userCfg.chasers=clamp(userCfg.chasers,1,300);$('lblChasers').textContent=isH?'参加人数（自分含む）':'追っ手の人数';}

    $('lblVx').textContent=isH?'自分の横移動の速さ':'横移動の速さ'; $('lblChSpeed').textContent=(isH||isOn)?'人間の走る速さ':'追っ手の速さ';
    $('optGoal').value=userCfg.goalDist;
    $('valGoal').textContent=goalLocked ? `${userCfg.goalDist} m（固定）` : `${userCfg.goalDist} m`;
    $('rowGoal').classList.toggle('locked', goalLocked);
    $('optSpeed').value=userCfg.speed;$('valSpeed').textContent=`${userCfg.speed} m/s`;
    $('optVx').value=userCfg.vxMax;$('valVx').textContent=`${userCfg.vxMax} m/s`; ci.value=userCfg.chasers;$('valChasers').textContent=isR?`${userCfg.chasers} 個`:`${userCfg.chasers} 人`;
    $('optChSpeed').value=userCfg.chSpeed;$('valChSpeed').textContent=`${userCfg.chSpeed.toFixed(2).replace(/\.00$/,'.0')} 倍`;
    $('optBoardGap').value=userCfg.boardGap;$('valBoardGap').textContent=`${userCfg.boardGap} m`; $('optCamUp').value=userCfg.camUp;$('valCamUp').textContent=`${userCfg.camUp} m`;
    $('rowBoardGap').classList.toggle('hidden',!isB);
    $('settingsArea').querySelectorAll('input').forEach(i=>i.disabled=isG);
    $('optGoal').disabled = isG || goalLocked;
    $('presetResetBtn').disabled=isG;

    const sb=$('startBtn');
    if(isRm){ if(net.role==='host'&&net.clients.size>0){sb.disabled=false;sb.textContent=`対戦スタート (${net.roster.length}人)`;} else if(net.role==='guest'&&net.connected){sb.disabled=true;sb.textContent='ホスト開始待ち…';} else {sb.disabled=true;sb.textContent='2人以上揃うと開始可能';} }
    else if(!isF){ sb.disabled=false; sb.textContent=isTA?'タイム計測スタート':'スタート'; }
  }

  // ---------- イベント登録 ----------
  $('openShopBtn').addEventListener('click',()=>{ renderShop(); $('shopOverlay').classList.remove('hidden'); });
  $('closeShopBtn').addEventListener('click',()=>{ $('shopOverlay').classList.add('hidden'); });
  const openLb=()=>{ renderLeaderboard(); $('lbOverlay').classList.remove('hidden'); };
  $('openLbBtn').addEventListener('click',openLb); $('taViewLbBtn').addEventListener('click',openLb);
  $('closeLbBtn').addEventListener('click',()=>{ $('lbOverlay').classList.add('hidden'); });
  $('playerNameInput').addEventListener('input',e=>{ profile.name=e.target.value.trim().slice(0,14)||'名無し'; saveProfile(); });
  $('lbExportBtn').addEventListener('click',async()=>{
    const code = btoa(unescape(encodeURIComponent(JSON.stringify(profile.lb500.slice(0,12)))));
    try{ await navigator.clipboard.writeText(code); showToast('記録コードをコピーしました','boost'); }
    catch{ prompt('この記録コードを相手に送ってください', code); }
  });
  $('lbImportBtn').addEventListener('click',()=>{
    const raw = prompt('相手からもらった記録コードを貼り付けてください');
    if(!raw) return;
    try{ const arr = JSON.parse(decodeURIComponent(escape(atob(raw.trim())))); if(Array.isArray(arr)){ mergeLeaderboard(arr); renderLeaderboard(); showToast('ランキングを合体しました！','boost'); } }
    catch{ alert('コードの形式が正しくありません'); }
  });

  document.querySelectorAll('.mode-btn').forEach(b=>b.addEventListener('click',()=>{ const m=b.dataset.mode; if(userCfg.mode==='online'&&m!=='online'&&(net.role!=='none'||net.searching))disconnectNet(false); Object.assign(userCfg,{mode:m,...PRESETS[m]}); if(m==='race')userCfg.raceCourse='special'; if(m==='online'){userCfg.onlineCourse='normal';userCfg.onlineGame='chase';setNetStatus('',userCfg.onlineType==='free'?'ルールとコースを選んで開始してください':'合言葉を決めて部屋を作るか入ってください（最大6人）');} setupWorld(false); }));
  document.querySelectorAll('[data-rcourse]').forEach(b=>b.addEventListener('click',()=>{ const c=b.dataset.rcourse; userCfg.raceCourse=c; if(c==='board'){userCfg.speed=55;userCfg.vxMax=30;userCfg.boardGap=28;userCfg.camUp=38;} else if(c==='special'){userCfg.speed=22;userCfg.vxMax=11;userCfg.camUp=11;} else {userCfg.goalDist=MAX_GOAL;userCfg.speed=24;userCfg.vxMax=11;userCfg.camUp=10;} setupWorld(false); }));
  document.querySelectorAll('[data-match]').forEach(b=>b.addEventListener('click',()=>{ if(net.searching||net.role!=='none')return; userCfg.onlineType=b.dataset.match; if(userCfg.onlineType==='free')Object.assign(userCfg,FREE_RULES[`${userCfg.onlineGame}_${userCfg.onlineCourse}`]); setNetStatus('',userCfg.onlineType==='free'?'ルールとコースを選んで開始してください':'合言葉を決めて部屋を作るか入ってください（最大6人）'); setupWorld(false); }));
  document.querySelectorAll('[data-otype]').forEach(b=>b.addEventListener('click',()=>{ userCfg.onlineGame=b.dataset.otype; if(userCfg.onlineGame==='race')userCfg.onlineCourse='special'; if(userCfg.onlineType==='free')Object.assign(userCfg,FREE_RULES[`${userCfg.onlineGame}_${userCfg.onlineCourse}`]); else userCfg.chasers=userCfg.onlineGame==='race'?6:0; setupWorld(false); sendLobbyPreview(); }));
  document.querySelectorAll('[data-course]').forEach(b=>b.addEventListener('click',()=>{ const c=b.dataset.course; userCfg.onlineCourse=c; if(userCfg.onlineType==='free')Object.assign(userCfg,FREE_RULES[`${userCfg.onlineGame}_${c}`]); else if(c==='board'){userCfg.speed=50;userCfg.vxMax=28;userCfg.boardGap=28;userCfg.camUp=38;} else if(c==='special'){userCfg.speed=userCfg.onlineGame==='race'?22:18;userCfg.vxMax=10;userCfg.camUp=11;} else {userCfg.speed=userCfg.onlineGame==='race'?24:16;userCfg.vxMax=10;userCfg.camUp=8;} setupWorld(false); sendLobbyPreview(); }));
  document.querySelectorAll('[data-role]').forEach(b=>b.addEventListener('click',()=>{ userCfg.hostRole=b.dataset.role; syncMenuUI(); sendLobbyPreview(); }));
  $('freeSearchBtn').addEventListener('click',startFreeMatch); $('freeStartNowBtn').addEventListener('click',hostLaunchOnlineMatch); $('freeCancelBtn').addEventListener('click',()=>disconnectNet(false));
  $('randCodeBtn').addEventListener('click',()=>{$('roomCode').value=String(Math.floor(1000+Math.random()*9000));});
  $('hostBtn').addEventListener('click',createRoom); $('joinBtn').addEventListener('click',joinRoom); $('disconnectBtn').addEventListener('click',()=>disconnectNet(false)); $('roomStartBtn').addEventListener('click',()=>setupWorld(true));
  $('copyInviteBtn').addEventListener('click',async()=>{ const c=net.room||cleanRoomText($('roomCode').value); if(!c)return; const t=(location.protocol==='http:'||location.protocol==='https:')?`${location.origin}${location.pathname}?room=${encodeURIComponent(c)}`:c; try{await navigator.clipboard.writeText(t);const b=$('copyInviteBtn'),o=b.textContent;b.textContent='コピー完了!';setTimeout(()=>b.textContent=o,1400);}catch{prompt('相手に送ってください',t);} });
  const onSet=()=>{syncMenuUI();sendLobbyPreview();};
  $('optGoal').addEventListener('input',e=>{if(!isGoalLockedMode()){userCfg.goalDist=parseInt(e.target.value,10);onSet();}}); $('optSpeed').addEventListener('input',e=>{userCfg.speed=parseFloat(e.target.value);onSet();});
  $('optVx').addEventListener('input',e=>{userCfg.vxMax=parseFloat(e.target.value);onSet();}); $('optChasers').addEventListener('input',e=>{userCfg.chasers=parseInt(e.target.value,10);onSet();});
  $('optChSpeed').addEventListener('input',e=>{userCfg.chSpeed=parseFloat(e.target.value);onSet();}); $('optBoardGap').addEventListener('input',e=>{userCfg.boardGap=parseInt(e.target.value,10);onSet();});
  $('optCamUp').addEventListener('input',e=>{userCfg.camUp=parseInt(e.target.value,10);cfg.camUp=userCfg.camUp;onSet();});
  $('presetResetBtn').addEventListener('click',()=>{const m=userCfg.mode;Object.assign(userCfg,{mode:m,...PRESETS[m]});if(m==='race')userCfg.raceCourse='special';if(m==='online'){userCfg.onlineCourse='normal';userCfg.onlineGame='chase';}setupWorld(false);sendLobbyPreview();});
  $('startBtn').addEventListener('click',()=>setupWorld(true)); $('retryBtn').addEventListener('click',()=>setupWorld(true));
  $('swapRetryBtn').addEventListener('click',()=>{userCfg.hostRole=userCfg.hostRole==='cheese'?'human':'cheese';sendLobbyPreview();setupWorld(true);});
  $('nextFreeBtn').addEventListener('click',()=>{setupWorld(false);startFreeMatch();});
  const backMenu=()=>{ if(cfg.isOnline){if(cfg.onlineType==='free'||net.role==='guest')disconnectNet(false); else if(net.role==='host')broadcastToClients({t:'menu'});} setupWorld(false); };
  $('toMenuBtn').addEventListener('click',backMenu); $('menuBtn').addEventListener('click',backMenu);
  $('diveBtn').addEventListener('pointerdown',e=>{e.preventDefault();e.stopPropagation();if(game==='playing'&&!cfg.isRace&&!cfg.isTA&&cfg.myRole==='human'){diveRequested=true;if(cfg.isOnline&&net.role==='guest')sendGuestInput(true);}});

  let dragId=null, joyX0=0, joyY0=0;
  function input(){ return dragId!==null ? (Math.hypot(stick.x,stick.y)<JOY_DEAD?{x:0,y:0}:stick) : {x:(keys.right?1:0)-(keys.left?1:0),y:(keys.up?1:0)-(keys.down?1:0)}; }
  const endJoy=()=>{dragId=null;stick.x=stick.y=0;$('joy').classList.add('hidden');};
  cv.addEventListener('pointerdown',e=>{ if(game!=='playing'||dragId!==null)return; e.preventDefault(); cv.setPointerCapture(e.pointerId); dragId=e.pointerId; const g=$('game').getBoundingClientRect(); joyX0=e.clientX-g.left; joyY0=e.clientY-g.top; $('joy').style.left=joyX0+'px'; $('joy').style.top=joyY0+'px'; $('knob').style.transform=''; $('joy').classList.remove('hidden'); });
  cv.addEventListener('pointermove',e=>{ if(e.pointerId!==dragId)return; const g=$('game').getBoundingClientRect(); let dx=e.clientX-g.left-joyX0, dy=e.clientY-g.top-joyY0, m=Math.hypot(dx,dy); if(m>JOY_R){dx*=JOY_R/m;dy*=JOY_R/m;} stick.x=dx/JOY_R; stick.y=-dy/JOY_R; $('knob').style.transform=`translate(${dx}px,${dy}px)`; });
  const endDrag=e=>{if(e.pointerId===dragId)endJoy();}; cv.addEventListener('pointerup',endDrag); cv.addEventListener('pointercancel',endDrag);
  document.addEventListener('keydown',e=>{
    if(document.activeElement&&document.activeElement.tagName==='INPUT')return;
    if(game==='over'&&(e.key==='Enter'||e.key===' ')){if(!cfg.isOnline){e.preventDefault();setupWorld(true);}return;}
    if(game==='menu'&&e.key==='Enter'){if(userCfg.mode!=='online'||(userCfg.onlineType==='room'&&!$('startBtn').disabled)){e.preventDefault();setupWorld(true);}return;}
    if(game==='playing'&&!cfg.isRace&&!cfg.isTA&&cfg.myRole==='human'&&(e.key===' '||e.code==='Space')){e.preventDefault();diveRequested=true;if(cfg.isOnline&&net.role==='guest')sendGuestInput(true);return;}
    if(e.key==='ArrowLeft'||e.key==='a')keys.left=true; if(e.key==='ArrowRight'||e.key==='d')keys.right=true; if(e.key==='ArrowUp'||e.key==='w')keys.up=true; if(e.key==='ArrowDown'||e.key==='s')keys.down=true;
  });
  document.addEventListener('keyup',e=>{ if(e.key==='ArrowLeft'||e.key==='a')keys.left=false; if(e.key==='ArrowRight'||e.key==='d')keys.right=false; if(e.key==='ArrowUp'||e.key==='w')keys.up=false; if(e.key==='ArrowDown'||e.key==='s')keys.down=false; });
  addEventListener('blur',()=>{keys.left=keys.right=keys.up=keys.down=false;});
  document.addEventListener('touchstart',e=>{if(e.target===cv)e.preventDefault();},{passive:false});
  document.addEventListener('touchmove',e=>{if(dragId!==null)e.preventDefault();},{passive:false});
  document.addEventListener('contextmenu',e=>{if(e.target===cv)e.preventDefault();});
  let lastTouchEnd=0; document.addEventListener('touchend',e=>{if(e.target instanceof Element&&e.target.closest('button,input,.card'))return;const now=Date.now();if(now-lastTouchEnd<350)e.preventDefault();lastTouchEnd=now;},{passive:false});

  function resize(){
    const w=$('game').clientWidth, h=$('game').clientHeight; renderer.setSize(w,h); camera.aspect=w/h;
    camera.fov=clamp(2*Math.atan(Math.tan(CAM_HFOV/2*Math.PI/180)/camera.aspect)*180/Math.PI, 50, cfg.camVfovMax); camera.updateProjectionMatrix();
  }
  addEventListener('resize', resize);

  $('playerNameInput').value = profile.name;
  saveProfile();
  const inviteRoom = new URLSearchParams(location.search).get('room');
  if (inviteRoom){
    Object.assign(userCfg, { mode:'online', onlineType:'room', onlineGame:'chase', onlineCourse:'normal', ...PRESETS.online });
    $('roomCode').value = inviteRoom; setNetStatus('', `招待された合言葉「${inviteRoom}」が入りました。「部屋に入る」を押してください`);
  } else $('roomCode').value = String(Math.floor(1000 + Math.random() * 9000));

  setupWorld(false);
  requestAnimationFrame(loop);
})();
</script>
</body>
</html>
