<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Çöp Adam RPG</title>
<link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&family=VT323&display=swap" rel="stylesheet">
<style>
:root{
  --gold:#f0c040;--gold-d:#c89a20;--deep:#0a0818;
  --panel:rgba(10,8,24,0.97);--glow:0 0 18px rgba(240,192,64,.5);
  --red:#e87060;--green:#4caf50;--blue:#60a8e8;
}
*{box-sizing:border-box;margin:0;padding:0}
html,body{width:100%;height:100%;background:var(--deep);overflow:hidden;font-family:'VT323',monospace}

/* ─ PIXEL FONT ─ */
.px{font-family:'Press Start 2P',monospace}

/* ─ SCREENS ─ */
.screen{position:fixed;inset:0;display:flex;flex-direction:column;align-items:center;justify-content:center;z-index:100;transition:opacity .4s,visibility .4s}
.screen.hidden{opacity:0;visibility:hidden;pointer-events:none}
.scroll-screen{position:fixed;inset:0;z-index:100;overflow-y:auto;display:flex;flex-direction:column;align-items:center;padding:20px 14px;transition:opacity .4s,visibility .4s;background:rgba(10,8,24,.95)}
.scroll-screen.hidden{opacity:0;visibility:hidden;pointer-events:none}

/* ─ STARS ─ */
.stars-bg{position:absolute;inset:0;overflow:hidden;pointer-events:none}
.stars-bg span{position:absolute;border-radius:50%;background:#fff;animation:twinkle 3s infinite alternate}
@keyframes twinkle{from{opacity:.06}to{opacity:.9}}

/* ══════════════════════════════
   SPLASH / LOADING
══════════════════════════════ */
#splashScreen{background:var(--deep);z-index:200}
.splash-logo{font-family:'Press Start 2P',monospace;font-size:clamp(1.4rem,5vw,3rem);color:var(--gold);text-shadow:0 0 40px rgba(240,192,64,.7),4px 4px 0 #5a3c00;text-align:center;animation:titlePulse 2s ease-in-out infinite;margin-bottom:12px}
.splash-sub{font-size:1.4rem;color:rgba(255,255,255,.35);letter-spacing:6px;margin-bottom:40px}
.splash-bar-wrap{width:260px;height:8px;background:rgba(255,255,255,.1);border-radius:4px;overflow:hidden;margin-bottom:10px}
.splash-bar{height:100%;background:linear-gradient(90deg,#c89a20,#f0c040);border-radius:4px;width:0%;transition:width .3s}
.splash-tip{font-size:.95rem;color:rgba(255,255,255,.3);text-align:center;max-width:280px}
@keyframes titlePulse{0%,100%{text-shadow:0 0 40px rgba(240,192,64,.6),4px 4px 0 #5a3c00}50%{text-shadow:0 0 70px rgba(240,192,64,.95),4px 4px 0 #5a3c00}}

/* ══════════════════════════════
   USER + CLASS SELECT
══════════════════════════════ */
#userScreen{background:radial-gradient(ellipse at 50% 55%,#18103a 0%,#0a0818 70%)}
.section-title{font-family:'Press Start 2P',monospace;font-size:clamp(.65rem,2.5vw,1rem);color:var(--gold);text-align:center;margin-bottom:6px;text-shadow:2px 2px 0 #5a3c00}
.section-sub{font-size:1.15rem;color:rgba(255,255,255,.3);letter-spacing:4px;text-align:center;margin-bottom:22px}
.user-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:10px;margin-bottom:20px;max-width:500px;width:100%}
.user-card{background:rgba(255,255,255,.04);border:2px solid rgba(240,192,64,.2);border-radius:10px;padding:14px 8px;text-align:center;cursor:pointer;transition:all .2s;position:relative}
.user-card:hover,.user-card.sel{background:rgba(240,192,64,.1);border-color:var(--gold);box-shadow:var(--glow)}
.user-card .av{font-size:1.8rem;margin-bottom:6px}
.user-card .un{font-family:'Press Start 2P',monospace;font-size:.48rem;color:var(--gold);word-break:break-all;line-height:1.6}
.user-card .ul{font-size:.95rem;color:rgba(255,255,255,.35);margin-top:3px}
.user-card .class-badge{font-size:.7rem;margin-top:4px}
.edit-btn{position:absolute;top:5px;right:5px;background:rgba(240,192,64,.2);border:1px solid rgba(240,192,64,.4);border-radius:3px;color:var(--gold);font-size:.45rem;padding:2px 4px;cursor:pointer;font-family:'Press Start 2P',monospace}

.class-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:8px;margin-bottom:18px;max-width:500px;width:100%}
.class-card{background:rgba(255,255,255,.04);border:2px solid rgba(255,255,255,.1);border-radius:8px;padding:12px 6px;text-align:center;cursor:pointer;transition:all .2s}
.class-card:hover,.class-card.sel{border-color:var(--gold);background:rgba(240,192,64,.1)}
.class-card .ci{font-size:1.5rem;margin-bottom:4px}
.class-card .cn{font-family:'Press Start 2P',monospace;font-size:.42rem;color:var(--gold);margin-bottom:4px}
.class-card .cd{font-size:.85rem;color:rgba(255,255,255,.35);line-height:1.3}

.confirm-btn{font-family:'Press Start 2P',monospace;padding:13px 38px;background:linear-gradient(135deg,#c89a20,#f0c040,#c89a20);border:none;border-radius:6px;color:#0d0a1a;font-size:.68rem;cursor:pointer;box-shadow:0 6px 28px rgba(240,192,64,.4);transition:transform .2s,box-shadow .2s}
.confirm-btn:hover{transform:translateY(-3px);box-shadow:0 10px 36px rgba(240,192,64,.6)}

/* ─ EDIT MODAL ─ */
#editModal{position:fixed;inset:0;z-index:300;background:rgba(0,0,0,.75);backdrop-filter:blur(4px);display:flex;align-items:center;justify-content:center}
#editModal.hidden{display:none}
.modal-box{background:var(--panel);border:2px solid rgba(240,192,64,.4);border-radius:12px;padding:28px;text-align:center;max-width:300px;width:90%}
.modal-title{font-family:'Press Start 2P',monospace;font-size:.75rem;color:var(--gold);margin-bottom:18px}
.modal-input{font-family:'Press Start 2P',monospace;font-size:.6rem;width:100%;padding:10px;background:rgba(255,255,255,.05);border:2px solid rgba(240,192,64,.3);border-radius:5px;color:var(--gold);outline:none;text-align:center;margin-bottom:14px}
.modal-input:focus{border-color:var(--gold)}
.modal-btns{display:flex;gap:8px;justify-content:center}
.modal-btn{font-family:'Press Start 2P',monospace;font-size:.55rem;padding:9px 16px;border-radius:5px;border:none;cursor:pointer;transition:all .2s}
.modal-btn.ok{background:linear-gradient(135deg,#c89a20,#f0c040);color:#0d0a1a}
.modal-btn.cancel{background:rgba(255,255,255,.08);color:rgba(255,255,255,.4);border:1px solid rgba(255,255,255,.15)}

/* ══════════════════════════════
   HUB (ANA EKRAN)
══════════════════════════════ */
#hubScreen{background:radial-gradient(ellipse at 50% 60%,#1a1030 0%,#0a0818 70%);flex-direction:column;gap:0;overflow:hidden}
.hub-top{width:100%;max-width:680px;display:flex;align-items:center;justify-content:space-between;padding:16px 20px 10px;flex-shrink:0}
.hub-user-info{display:flex;align-items:center;gap:10px}
.hub-avatar{font-size:1.8rem}
.hub-name{font-family:'Press Start 2P',monospace;font-size:.6rem;color:var(--gold);display:block}
.hub-class{font-size:.9rem;color:rgba(255,255,255,.4)}
.hub-tokens{font-family:'Press Start 2P',monospace;font-size:.8rem;color:var(--gold);background:rgba(240,192,64,.1);border:1px solid rgba(240,192,64,.3);border-radius:20px;padding:6px 14px}

.hub-title{font-family:'Press Start 2P',monospace;font-size:clamp(.9rem,3.5vw,1.8rem);color:var(--gold);text-shadow:0 0 30px rgba(240,192,64,.5),3px 3px 0 #5a3c00;text-align:center;margin:4px 0 2px;animation:titlePulse 3s infinite}
.hub-tagline{font-size:1.1rem;color:rgba(255,255,255,.25);letter-spacing:5px;text-align:center;margin-bottom:16px}

/* nav tiles */
.hub-nav{display:grid;grid-template-columns:repeat(3,1fr);gap:10px;max-width:560px;width:100%;padding:0 14px;margin-bottom:12px}
.hub-tile{background:rgba(255,255,255,.04);border:2px solid rgba(255,255,255,.08);border-radius:10px;padding:16px 10px;text-align:center;cursor:pointer;transition:all .22s;position:relative;overflow:hidden}
.hub-tile:hover{border-color:rgba(240,192,64,.5);background:rgba(240,192,64,.08);transform:translateY(-3px);box-shadow:0 8px 24px rgba(240,192,64,.2)}
.hub-tile .ti{font-size:1.8rem;margin-bottom:6px}
.hub-tile .tn{font-family:'Press Start 2P',monospace;font-size:.45rem;color:var(--gold);letter-spacing:1px}
.hub-tile .td{font-size:.9rem;color:rgba(255,255,255,.3);margin-top:3px}
.hub-tile .badge{position:absolute;top:6px;right:6px;background:var(--red);color:#fff;font-family:'Press Start 2P',monospace;font-size:.4rem;padding:2px 5px;border-radius:8px}

.diff-row{display:flex;gap:8px;justify-content:center;margin-bottom:14px}
.diff-btn{font-family:'Press Start 2P',monospace;padding:8px 16px;border:2px solid rgba(240,192,64,.3);border-radius:4px;background:transparent;color:rgba(240,192,64,.4);font-size:.5rem;cursor:pointer;transition:all .2s}
.diff-btn:hover,.diff-btn.active{background:rgba(240,192,64,.15);border-color:var(--gold);color:var(--gold);box-shadow:var(--glow)}
.play-btn{font-family:'Press Start 2P',monospace;padding:14px 48px;background:linear-gradient(135deg,#c89a20,#f0c040,#c89a20);border:none;border-radius:6px;color:#0d0a1a;font-size:.8rem;cursor:pointer;box-shadow:0 6px 28px rgba(240,192,64,.4);transition:transform .2s,box-shadow .2s;letter-spacing:1px}
.play-btn:hover{transform:translateY(-3px);box-shadow:0 10px 36px rgba(240,192,64,.6)}

/* ── QUESTS ── */
.quest-item{background:rgba(255,255,255,.04);border:1px solid rgba(255,255,255,.08);border-radius:8px;padding:12px 14px;margin-bottom:10px}
.quest-header{display:flex;align-items:center;gap:10px;margin-bottom:6px}
.quest-icon{font-size:1.3rem}
.quest-name{font-family:'Press Start 2P',monospace;font-size:.52rem;color:var(--gold);flex:1}
.quest-reward{font-family:'Press Start 2P',monospace;font-size:.48rem;color:#8fdc8f}
.quest-prog-wrap{height:5px;background:rgba(255,255,255,.08);border-radius:3px;overflow:hidden;margin-bottom:4px}
.quest-prog{height:100%;background:linear-gradient(90deg,#c89a20,#f0c040);border-radius:3px;transition:width .5s}
.quest-desc{font-size:.9rem;color:rgba(255,255,255,.35)}
.quest-claim{font-family:'Press Start 2P',monospace;font-size:.45rem;padding:5px 10px;background:linear-gradient(135deg,#c89a20,#f0c040);border:none;border-radius:4px;color:#0d0a1a;cursor:pointer;margin-top:6px}
.quest-done{font-family:'Press Start 2P',monospace;font-size:.45rem;color:#8fdc8f;margin-top:6px}
.quest-reset-btn{font-family:'Press Start 2P',monospace;font-size:.45rem;padding:7px 14px;background:rgba(192,57,43,.2);border:1px solid rgba(192,57,43,.4);border-radius:4px;color:#e87060;cursor:pointer;width:100%;margin-top:8px}

/* ── ACHIEVEMENTS ── */
.ach-grid{display:grid;grid-template-columns:1fr 1fr;gap:10px}
.ach-item{background:rgba(255,255,255,.04);border:1px solid rgba(255,255,255,.07);border-radius:8px;padding:12px;display:flex;align-items:center;gap:10px;transition:all .2s}
.ach-item.unlocked{border-color:rgba(240,192,64,.4);background:rgba(240,192,64,.06)}
.ach-item.locked{opacity:.4;filter:grayscale(1)}
.ach-icon{font-size:1.6rem}
.ach-name{font-family:'Press Start 2P',monospace;font-size:.42rem;color:var(--gold);margin-bottom:3px}
.ach-desc{font-size:.85rem;color:rgba(255,255,255,.35)}

/* ── SHOP ── */
.shop-grid{display:grid;grid-template-columns:1fr 1fr;gap:10px;max-width:520px;width:100%;margin-bottom:14px}
.shop-item{background:rgba(255,255,255,.03);border:2px solid rgba(255,255,255,.08);border-radius:10px;padding:12px;display:flex;flex-direction:column;gap:5px;transition:border-color .2s}
.shop-item:hover{border-color:rgba(240,192,64,.3)}
.si-icon{font-size:1.5rem}
.si-tier{font-family:'Press Start 2P',monospace;font-size:.38rem;letter-spacing:1px;padding:2px 5px;border-radius:6px;width:fit-content}
.tier-normal{background:rgba(100,200,100,.2);color:#8fdc8f}
.tier-rare{background:rgba(240,192,64,.2);color:var(--gold)}
.tier-epic{background:rgba(192,57,43,.2);color:#e87060}
.si-name{font-family:'Press Start 2P',monospace;color:rgba(255,255,255,.9);font-size:.48rem}
.si-desc{font-size:.9rem;color:rgba(255,255,255,.35);line-height:1.4}
.si-dur{font-size:.85rem;color:rgba(100,200,255,.65)}
.lvl-dots{display:flex;gap:3px;flex-wrap:wrap;margin:4px 0}
.lvl-dot{width:8px;height:8px;border-radius:2px}
.si-btn{font-family:'Press Start 2P',monospace;padding:6px 10px;border-radius:4px;border:none;cursor:pointer;font-size:.44rem;margin-top:4px;transition:all .2s}
.si-btn.can{background:linear-gradient(135deg,#c89a20,#f0c040);color:#0d0a1a}
.si-btn.can:hover{transform:translateY(-1px);box-shadow:0 4px 14px rgba(240,192,64,.4)}
.si-btn.no{background:rgba(255,255,255,.06);color:rgba(255,255,255,.22);cursor:default}

/* ── LEADERBOARD ── */
.leader-row{display:flex;align-items:center;gap:10px;padding:8px 0;border-bottom:1px solid rgba(255,255,255,.06)}
.leader-rank{font-family:'Press Start 2P',monospace;color:var(--gold);width:20px;text-align:right;font-size:.55rem}
.leader-name{flex:1;font-size:1.1rem;font-style:italic;color:rgba(255,255,255,.8)}
.leader-score{font-family:'Press Start 2P',monospace;color:var(--gold);font-size:.58rem}
.diff-badge{font-family:'Press Start 2P',monospace;font-size:.38rem;padding:2px 6px;border-radius:8px}
.diff-badge.easy{background:rgba(100,200,100,.2);color:#8fdc8f}
.diff-badge.normal{background:rgba(240,192,64,.2);color:var(--gold)}
.diff-badge.hard{background:rgba(192,57,43,.2);color:#e87060}

/* ── PROFILE ── */
.stat-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:8px;margin-bottom:16px}
.stat-card{background:rgba(255,255,255,.03);border:1px solid rgba(255,255,255,.06);border-radius:7px;padding:10px;text-align:center}
.stat-val{font-family:'Press Start 2P',monospace;color:var(--gold);font-size:.85rem}
.stat-lbl{font-size:.85rem;color:rgba(255,255,255,.3);text-transform:uppercase;margin-top:2px}
.chart-title{font-family:'Press Start 2P',monospace;color:rgba(240,192,64,.55);font-size:.4rem;letter-spacing:1px;margin-bottom:5px;margin-top:10px}
canvas.mini-chart{width:100%;height:75px;border-radius:5px;background:rgba(0,0,0,.3);display:block}

/* ── PANEL SHARED ── */
.panel{background:var(--panel);border:2px solid rgba(240,192,64,.18);border-radius:12px;padding:24px 30px;box-shadow:0 0 50px rgba(240,192,64,.06)}
.panel-title{font-family:'Press Start 2P',monospace;color:var(--gold);font-size:.85rem;text-align:center;margin-bottom:18px;letter-spacing:2px}
.back-btn{font-family:'Press Start 2P',monospace;margin-top:16px;width:100%;padding:10px;background:transparent;border:2px solid rgba(240,192,64,.3);border-radius:5px;color:var(--gold);font-size:.5rem;cursor:pointer;transition:all .2s}
.back-btn:hover{background:rgba(240,192,64,.1)}
.shop-token-bar{font-family:'Press Start 2P',monospace;color:var(--gold);font-size:.65rem;text-align:center;margin-bottom:12px}

/* ── HUD ── */
#hud{position:fixed;inset:0;pointer-events:none;z-index:10}
#scoreDisplay{position:absolute;top:16px;right:20px;font-family:'Press Start 2P',monospace;font-size:1.4rem;color:var(--gold);text-shadow:0 0 18px rgba(240,192,64,.5),2px 2px 0 #5a3c00}
#levelDisplay{position:absolute;top:16px;left:20px;font-family:'Press Start 2P',monospace;font-size:.48rem;color:var(--gold);display:flex;flex-direction:column;gap:3px}
.xp-bar-wrap{width:95px;height:4px;background:rgba(255,255,255,.1);border-radius:2px;overflow:hidden}
#xpBar{height:100%;background:linear-gradient(90deg,#c89a20,#f0c040);border-radius:2px;transition:width .4s}
#tokenHud{position:absolute;top:16px;left:50%;transform:translateX(-50%);font-family:'Press Start 2P',monospace;font-size:.55rem;color:var(--gold);background:rgba(0,0,0,.65);border:2px solid rgba(240,192,64,.3);border-radius:20px;padding:5px 13px;display:flex;gap:5px;align-items:center}
#diffBadge{position:absolute;bottom:16px;left:50%;transform:translateX(-50%);font-family:'Press Start 2P',monospace;font-size:.44rem;letter-spacing:2px;padding:4px 11px;border-radius:4px}
#diffBadge.easy{background:rgba(100,200,100,.2);color:#8fdc8f;border:1px solid rgba(100,200,100,.3)}
#diffBadge.normal{background:rgba(240,192,64,.15);color:var(--gold);border:1px solid rgba(240,192,64,.3)}
#diffBadge.hard{background:rgba(192,57,43,.2);color:#e87060;border:1px solid rgba(192,57,43,.3)}
#powerupHud{position:absolute;bottom:16px;right:18px;display:flex;gap:5px}
.pu-icon{width:44px;height:44px;border-radius:6px;border:2px solid rgba(255,255,255,.2);display:flex;flex-direction:column;align-items:center;justify-content:center;font-size:1.1rem;background:rgba(0,0,0,.65);position:relative;transition:border-color .3s,opacity .3s}
.pu-icon.pu-inactive{opacity:.2;border-color:rgba(255,255,255,.1)}
.pu-icon.pu-active{border-color:var(--gold);box-shadow:0 0 10px rgba(240,192,64,.4)}
.pu-timer{font-family:'Press Start 2P',monospace;font-size:.32rem;color:#fff;position:absolute;bottom:2px;right:3px}
.pu-bar{position:absolute;bottom:0;left:0;height:3px;border-radius:0 0 4px 4px;background:var(--gold)}

/* ── EVENT BANNER ── */
#eventBanner{position:absolute;top:60px;left:50%;transform:translateX(-50%);font-family:'Press Start 2P',monospace;font-size:.58rem;padding:8px 20px;border-radius:6px;border:2px solid;text-align:center;opacity:0;transition:opacity .5s;pointer-events:none;white-space:nowrap}
#eventBanner.show{opacity:1}

/* ── BOSS INDICATOR ── */
#bossIndicator{position:absolute;top:110px;left:50%;transform:translateX(-50%);font-family:'Press Start 2P',monospace;font-size:.5rem;color:#e87060;background:rgba(192,57,43,.25);border:2px solid rgba(192,57,43,.5);border-radius:6px;padding:6px 14px;opacity:0;transition:opacity .5s;pointer-events:none;animation:bossGlow 1s infinite alternate}
#bossIndicator.show{opacity:1}
@keyframes bossGlow{from{box-shadow:0 0 8px rgba(192,57,43,.4)}to{box-shadow:0 0 22px rgba(192,57,43,.8)}}

/* ── DAY/NIGHT INDICATOR ── */
#timeDisplay{position:absolute;top:16px;left:50%;transform:translateX(-50%);margin-left:80px;font-size:1.1rem;opacity:.6}

/* ── TOASTS ── */
#toastArea{position:absolute;top:42%;left:50%;transform:translate(-50%,-50%);display:flex;flex-direction:column;align-items:center;gap:7px;pointer-events:none}
.toast{font-family:'Press Start 2P',monospace;padding:6px 20px;border-radius:4px;font-size:.55rem;animation:toastIn .3s ease,toastOut .5s ease 1.1s forwards;opacity:0}
.toast.perfect{background:rgba(240,192,64,.25);color:var(--gold);border:2px solid rgba(240,192,64,.5);font-size:.8rem;box-shadow:0 0 26px rgba(240,192,64,.4)}
.toast.combo{background:rgba(192,57,43,.25);color:#e87060;border:2px solid rgba(192,57,43,.5)}
.toast.levelup{background:rgba(100,200,100,.2);color:#8fdc8f;border:2px solid rgba(100,200,100,.4);font-size:.85rem}
.toast.token{background:rgba(240,192,64,.1);color:var(--gold);border:1px solid rgba(240,192,64,.3);font-size:.48rem}
.toast.pickup{background:rgba(33,150,243,.18);color:#90caf9;border:1px solid rgba(33,150,243,.35)}
.toast.event{background:rgba(156,39,176,.25);color:#ce93d8;border:2px solid rgba(156,39,176,.5);font-size:.7rem}
.toast.boss{background:rgba(192,57,43,.3);color:#e87060;border:2px solid rgba(192,57,43,.6);font-size:.75rem}
.toast.ach{background:rgba(240,192,64,.2);color:var(--gold);border:2px solid rgba(240,192,64,.5);font-size:.62rem}
@keyframes toastIn{from{opacity:0;transform:translateY(10px)}to{opacity:1;transform:translateY(0)}}
@keyframes toastOut{from{opacity:1}to{opacity:0;transform:translateY(-16px)}}

/* ── PARTICLES ── */
.particle{position:absolute;border-radius:50%;pointer-events:none;animation:particleFly .8s ease-out forwards}
@keyframes particleFly{from{opacity:1;transform:translate(0,0) scale(1)}to{opacity:0;transform:var(--tx,0) scale(0)}}

#introduction{position:fixed;bottom:10%;left:50%;transform:translateX(-50%);font-family:'Press Start 2P',monospace;color:rgba(240,192,64,.6);font-size:.5rem;letter-spacing:2px;text-align:center;transition:opacity 1.5s;pointer-events:none;z-index:20}

/* ── GAME OVER ── */
#gameOverScreen{background:rgba(10,8,24,.9);backdrop-filter:blur(6px)}
.go-panel{background:var(--panel);border:2px solid rgba(240,192,64,.2);border-radius:12px;padding:38px 48px;text-align:center;box-shadow:0 0 70px rgba(192,57,43,.2);min-width:260px}
.go-title{font-family:'Press Start 2P',monospace;color:#e87060;font-size:.95rem;letter-spacing:2px;margin-bottom:10px;text-shadow:3px 3px 0 #6a1010}
.go-score{font-family:'Press Start 2P',monospace;color:var(--gold);font-size:2.4rem;font-weight:900;line-height:1;text-shadow:3px 3px 0 #5a3c00}
.go-label{font-size:1rem;color:rgba(255,255,255,.3);letter-spacing:3px;margin-bottom:6px}
.go-earned{font-family:'Press Start 2P',monospace;color:var(--gold);font-size:.55rem;margin-bottom:5px}
.go-best{font-size:1.1rem;color:rgba(240,192,64,.5);margin-bottom:22px}
.go-new{color:var(--gold);animation:titlePulse 1s infinite}
.go-btns{display:flex;gap:10px;flex-direction:column}
.go-btn{font-family:'Press Start 2P',monospace;padding:11px 26px;border-radius:5px;font-size:.55rem;cursor:pointer;transition:all .2s}
.go-btn.primary{background:linear-gradient(135deg,#c89a20,#f0c040,#c89a20);border:none;color:#0d0a1a}
.go-btn.primary:hover{transform:translateY(-2px);box-shadow:0 8px 28px rgba(240,192,64,.5)}
.go-btn.secondary{background:transparent;border:2px solid rgba(240,192,64,.3);color:var(--gold)}
.go-btn.secondary:hover{background:rgba(240,192,64,.08)}

#game{position:fixed;inset:0;display:block}
</style>
</head>
<body>
<canvas id="game"></canvas>

<!-- HUD -->
<div id="hud">
  <div id="levelDisplay">
    <span id="levelText">LVL 1</span>
    <div class="xp-bar-wrap"><div id="xpBar" style="width:0%"></div></div>
  </div>
  <div id="tokenHud">🪙 <span id="tokenCount">0</span></div>
  <div id="scoreDisplay">0</div>
  <div id="diffBadge" class="normal">NORMAL</div>
  <div id="timeDisplay">🌙</div>
  <div id="eventBanner"></div>
  <div id="bossIndicator">💀 BOSS PLATFORM!</div>
  <div id="powerupHud">
    <div class="pu-icon pu-inactive" id="pu-shield"><span>🛡️</span><span class="pu-timer" id="pt-shield"></span><div class="pu-bar" id="pb-shield" style="width:0%"></div></div>
    <div class="pu-icon pu-inactive" id="pu-magnet"><span>🧲</span><span class="pu-timer" id="pt-magnet"></span><div class="pu-bar" id="pb-magnet" style="width:0%"></div></div>
    <div class="pu-icon pu-inactive" id="pu-speed"><span>⚡</span><span class="pu-timer" id="pt-speed"></span><div class="pu-bar" id="pb-speed" style="width:0%"></div></div>
    <div class="pu-icon pu-inactive" id="pu-double"><span>✨</span><span class="pu-timer" id="pt-double"></span><div class="pu-bar" id="pb-double" style="width:0%"></div></div>
    <div class="pu-icon pu-inactive" id="pu-life"><span>❤️</span></div>
  </div>
  <div id="toastArea"></div>
</div>
<div id="introduction">BASMAYA BAŞLA</div>

<!-- SPLASH -->
<div class="screen" id="splashScreen">
  <div class="splash-logo">ÇÖP ADAM</div>
  <div class="splash-sub">EFSANE YOLCULUĞU</div>
  <div class="splash-bar-wrap"><div class="splash-bar" id="splashBar"></div></div>
  <div class="splash-tip" id="splashTip">Yükleniyor...</div>
</div>

<!-- USER SELECT -->
<div class="scroll-screen hidden" id="userScreen">
  <div style="max-width:500px;width:100%;margin:auto;padding-bottom:20px">
    <div class="section-title" style="margin-top:10px">KİM OYNUYOR?</div>
    <div class="section-sub">KAHRAMAN SEÇ</div>
    <div class="user-grid" id="userGrid"></div>
    <div class="section-title" style="margin-bottom:10px">SINIF SEÇ</div>
    <div class="class-grid" id="classGrid"></div>
    <div style="text-align:center"><button class="confirm-btn" id="userConfirmBtn">► DEVAM ET</button></div>
  </div>
</div>

<!-- EDIT MODAL -->
<div id="editModal" class="hidden">
  <div class="modal-box">
    <div class="modal-title">İSİM GİR</div>
    <input class="modal-input" id="modalInput" maxlength="10" placeholder="İSİM...">
    <div class="modal-btns">
      <button class="modal-btn ok" id="modalOk">TAMAM</button>
      <button class="modal-btn cancel" id="modalCancel">İPTAL</button>
    </div>
  </div>
</div>

<!-- HUB -->
<div class="screen hidden" id="hubScreen">
  <div class="stars-bg" id="hubStars"></div>
  <div class="hub-top" style="max-width:560px;width:100%">
    <div class="hub-user-info" id="hubUserInfo" style="cursor:pointer" onclick="goToUserSelect()">
      <div class="hub-avatar" id="hubAvatar">⚔️</div>
      <div>
        <span class="hub-name" id="hubName">-</span>
        <span class="hub-class" id="hubClass">-</span>
      </div>
    </div>
    <div class="hub-tokens">🪙 <span id="hubTokens">0</span></div>
  </div>
  <div class="hub-title">ÇÖP ADAM</div>
  <div class="hub-tagline">EFSANE YOLCULUĞU</div>
  <div class="hub-nav" id="hubNav">
    <div class="hub-tile" onclick="showScreen('profileScreen');renderProfile()"><div class="ti">👤</div><div class="tn">PROFİL</div><div class="td">İstatistikler</div></div>
    <div class="hub-tile" onclick="showScreen('shopScreen');renderShop()"><div class="ti">🏪</div><div class="tn">MAĞAZA</div><div class="td">Özellikler</div></div>
    <div class="hub-tile" onclick="showScreen('questScreen');renderQuests()"><div class="ti">🎯</div><div class="tn">GÖREVLER</div><div class="td">Günlük</div><div class="badge" id="questBadge" style="display:none">!</div></div>
    <div class="hub-tile" onclick="showScreen('achScreen');renderAch()"><div class="ti">🏆</div><div class="tn">BAŞARIMLAR</div><div class="td">Rozetler</div></div>
    <div class="hub-tile" onclick="showScreen('leaderScreen');renderLeader()"><div class="ti">📊</div><div class="tn">SKOR</div><div class="td">Efsaneler</div></div>
    <div class="hub-tile" onclick="goToUserSelect()"><div class="ti">🔄</div><div class="tn">KULLANICI</div><div class="td">Değiştir</div></div>
  </div>
  <div style="display:flex;gap:10px;align-items:center;margin-bottom:10px">
    <button class="diff-btn" data-diff="easy">KOLAY</button>
    <button class="diff-btn active" data-diff="normal">NORMAL</button>
    <button class="diff-btn active" data-diff="hard" style="display:none" id="hardBtnHub"></button>
  </div>
  <div class="diff-row" id="diffRow"></div>
  <button class="play-btn" id="playBtn">► OYNA</button>
</div>

<!-- QUESTS -->
<div class="scroll-screen hidden" id="questScreen">
  <div class="panel" style="max-width:440px;width:100%;margin:auto">
    <div class="panel-title">🎯 GÜNLÜK GÖREVLER</div>
    <div id="questList"></div>
    <button class="quest-reset-btn" id="questResetBtn">🔄 GÖREVLERİ SIFIRLA</button>
    <button class="back-btn" onclick="showScreen('hubScreen')">◄ GERİ</button>
  </div>
</div>

<!-- ACHIEVEMENTS -->
<div class="scroll-screen hidden" id="achScreen">
  <div class="panel" style="max-width:460px;width:100%;margin:auto">
    <div class="panel-title">🏆 BAŞARIMLAR</div>
    <div class="ach-grid" id="achGrid"></div>
    <button class="back-btn" onclick="showScreen('hubScreen')">◄ GERİ</button>
  </div>
</div>

<!-- PROFILE -->
<div class="scroll-screen hidden" id="profileScreen">
  <div class="panel" style="max-width:440px;width:100%;margin:auto">
    <div class="panel-title">👤 PROFİL</div>
    <div style="display:flex;align-items:center;gap:12px;margin-bottom:18px">
      <div style="width:52px;height:52px;border-radius:50%;background:rgba(240,192,64,.1);border:2px solid rgba(240,192,64,.4);display:flex;align-items:center;justify-content:center;font-size:1.5rem" id="profAv">⚔️</div>
      <div><div style="font-family:'Press Start 2P',monospace;color:var(--gold);font-size:.65rem;margin-bottom:3px" id="profName">-</div><div style="font-size:.95rem;color:rgba(255,255,255,.35)" id="profSub">-</div></div>
    </div>
    <div class="stat-grid" id="statGrid"></div>
    <div class="chart-title">KOLAY</div><canvas class="mini-chart" id="easyChart"></canvas>
    <div class="chart-title">NORMAL</div><canvas class="mini-chart" id="normalChart"></canvas>
    <div class="chart-title">ZOR</div><canvas class="mini-chart" id="hardChart"></canvas>
    <button class="back-btn" onclick="showScreen('hubScreen')">◄ GERİ</button>
  </div>
</div>

<!-- SHOP -->
<div class="scroll-screen hidden" id="shopScreen">
  <div style="max-width:520px;width:100%;margin:auto">
    <div class="panel-title">🏪 MAĞAZA</div>
    <div class="shop-token-bar">🪙 <span id="shopTokens">0</span></div>
    <div class="shop-grid" id="shopGrid"></div>
    <button class="back-btn" onclick="showScreen('hubScreen')">◄ GERİ</button>
  </div>
</div>

<!-- LEADERBOARD -->
<div class="scroll-screen hidden" id="leaderScreen">
  <div class="panel" style="min-width:280px;max-width:420px;width:100%;margin:auto">
    <div class="panel-title">📊 EFSANELER</div>
    <div id="leaderList"></div>
    <button class="back-btn" onclick="showScreen('hubScreen')">◄ GERİ</button>
  </div>
</div>

<!-- GAME OVER -->
<div class="screen hidden" id="gameOverScreen">
  <div class="go-panel">
    <div class="go-title">GAME OVER</div>
    <div class="go-score" id="goScore">0</div>
    <div class="go-label">PUAN</div>
    <div class="go-earned" id="goEarned"></div>
    <div class="go-best" id="goBest"></div>
    <div class="go-btns">
      <button class="go-btn primary" id="goRestart">► TEKRAR</button>
      <button class="go-btn secondary" id="goHub">◄ ANA EKRAN</button>
    </div>
  </div>
</div>

<script>
'use strict';
// ═══════════════════════════════════════════════════════
//  STORAGE
// ═══════════════════════════════════════════════════════
const LS={
  get:(k,d)=>{try{const v=localStorage.getItem(k);return v!=null?JSON.parse(v):d}catch{return d}},
  set:(k,v)=>{try{localStorage.setItem(k,JSON.stringify(v))}catch{}}
};

// ═══════════════════════════════════════════════════════
//  AUDIO ENGINE
// ═══════════════════════════════════════════════════════
let audioCtx=null;
function getAudioCtx(){
  if(!audioCtx)audioCtx=new(window.AudioContext||window.webkitAudioContext)();
  return audioCtx;
}
function playTone(freq,type,dur,vol=0.3,delay=0){
  try{
    const ac=getAudioCtx();
    const o=ac.createOscillator();const g=ac.createGain();
    o.connect(g);g.connect(ac.destination);
    o.type=type;o.frequency.setValueAtTime(freq,ac.currentTime+delay);
    g.gain.setValueAtTime(vol,ac.currentTime+delay);
    g.gain.exponentialRampToValueAtTime(0.001,ac.currentTime+delay+dur);
    o.start(ac.currentTime+delay);o.stop(ac.currentTime+delay+dur+0.05);
  }catch{}
}
function playJump(){playTone(440,'square',.08,.15);playTone(660,'square',.08,.1,.08);}
function playPerfect(){
  [523,659,784,1047].forEach((f,i)=>playTone(f,'sine',.15,.2,i*.08));
}
function playCombo(){playTone(880,'sawtooth',.12,.25);playTone(1100,'sawtooth',.12,.2,.1);}
function playToken(){playTone(880,'sine',.06,.12);}
function playLevelUp(){[523,659,784,1047,1319].forEach((f,i)=>playTone(f,'triangle',.2,.3,i*.1));}
function playGameOver(){[440,330,220,110].forEach((f,i)=>playTone(f,'sawtooth',.3,.25,i*.18));}
function playBoss(){playTone(110,'sawtooth',.5,.35);playTone(82,'sawtooth',.5,.3,.1);}
function playPickup(){playTone(660,'sine',.1,.18);playTone(880,'sine',.1,.15,.08);}
function playStretch(){
  try{
    const ac=getAudioCtx();
    const o=ac.createOscillator();const g=ac.createGain();
    o.connect(g);g.connect(ac.destination);
    o.type='triangle';o.frequency.setValueAtTime(200,ac.currentTime);
    o.frequency.linearRampToValueAtTime(600,ac.currentTime+0.5);
    g.gain.setValueAtTime(0.05,ac.currentTime);
    g.gain.setValueAtTime(0.05,ac.currentTime+0.5);
    o.start();stretchOsc=o;stretchGain=g;
  }catch{}
}
function stopStretch(){
  try{if(stretchOsc){stretchGain.gain.exponentialRampToValueAtTime(0.001,audioCtx.currentTime+0.1);stretchOsc.stop(audioCtx.currentTime+0.15);stretchOsc=null;}}catch{}
}
let stretchOsc=null,stretchGain=null;

// ═══════════════════════════════════════════════════════
//  CLASSES
// ═══════════════════════════════════════════════════════
const CLASSES=[
  {id:'warrior',icon:'⚔️',name:'SAVAŞÇI',  desc:'Kalkan 2x güçlü\n+50% koruma',   bonus:{shieldMul:2, perfectBonus:0, speedMul:1, xpMul:1}},
  {id:'mage',   icon:'🧙',name:'BÜYÜCÜ',   desc:'Çift puan\nsüresi +30%',          bonus:{shieldMul:1, perfectBonus:1, speedMul:1, xpMul:1.3}},
  {id:'archer', icon:'🏹',name:'OKÇU',     desc:'Mükemmel alan\n2x geniş',         bonus:{shieldMul:1, perfectBonus:0, speedMul:1, xpMul:1, widerPerfect:true}},
  {id:'rogue',  icon:'💨',name:'HIZ',      desc:'Hız boost\n3x güçlü',             bonus:{shieldMul:1, perfectBonus:0, speedMul:0.3, xpMul:1}},
];

// ═══════════════════════════════════════════════════════
//  USERS
// ═══════════════════════════════════════════════════════
const AVATARS=['⚔️','🧙','🏹','💨','💎','🔥'];
const DNAMES=['Kahraman','Büyücü','Okçu','Savaşçı','Kral','Ejder'];
function defUser(i){return{name:DNAMES[i],classId:'warrior',level:1,xp:0,tokens:0,shopData:{},scoreHistory:{easy:[],normal:[],hard:[]},bestCombo:0,achievements:[],questProgress:{},questDate:'',questClaimed:[]};}
function loadUsers(){const u=LS.get('cop4_users',null);if(u&&u.length===6)return u;return Array.from({length:6},(_,i)=>defUser(i));}
let USERS=loadUsers();
let currentUserIdx=LS.get('cop4_curidx',0);
function CU(){return USERS[currentUserIdx];}
function saveUsers(){LS.set('cop4_users',USERS);}
let selectedClassId='warrior';

// ═══════════════════════════════════════════════════════
//  SHOP ITEMS
// ═══════════════════════════════════════════════════════
const ITEMS=[
  {id:'shield', icon:'🛡️',name:'KALKAN',    desc:'Düşmekten korur',   baseCost:20,maxLv:10,tier:'normal',mulCost:1.8},
  {id:'magnet', icon:'🧲',name:'MIKNATISMIK',desc:'Jetonları çeker',  baseCost:20,maxLv:10,tier:'normal',mulCost:1.8},
  {id:'speed',  icon:'⚡',name:'HIZ',       desc:'Geçici hız artışı', baseCost:25,maxLv:10,tier:'rare',  mulCost:2.0},
  {id:'pillar', icon:'🏛️',name:'DESTEK',    desc:'Sütun hızlı uzar',  baseCost:30,maxLv:10,tier:'rare',  mulCost:2.0},
  {id:'double', icon:'✨',name:'2X PUAN',   desc:'Puanlar 2x olur',   baseCost:40,maxLv:10,tier:'epic',  mulCost:2.2},
  {id:'life',   icon:'❤️',name:'CAN',       desc:'1 ekstra şans',     baseCost:50,maxLv:10,tier:'epic',  mulCost:2.5},
];
function itemLv(id){return CU().shopData[id]||0;}
function itemCost(it){const lv=itemLv(it.id);if(lv>=it.maxLv)return null;return Math.round(it.baseCost*Math.pow(it.mulCost,lv));}
function itemOwned(id){return itemLv(id)>0;}
function itemDuration(id){return Math.min(90,itemLv(id)*10);}
function buyItem(id){
  const it=ITEMS.find(i=>i.id===id);if(!it)return;
  const lv=itemLv(id);if(lv>=it.maxLv){showToast('MAX SEVİYE!','token');return;}
  const cost=itemCost(it);if(CU().tokens<cost){showToast('YETERSİZ JETON','token');return;}
  CU().tokens-=cost;CU().shopData[id]=(lv+1);saveUsers();renderShop();refreshTok();
  showToast(it.icon+' YÜKSELTİLDİ! LV'+(lv+1),'pickup');playPickup();
}

// ═══════════════════════════════════════════════════════
//  DIFFICULTY
// ═══════════════════════════════════════════════════════
const DIFF={
  easy:  {ss:6,ws:5,gMin:30, gMax:160,wMin:25,wMax:110,label:'KOLAY', xpM:1,  tokM:1,  freq:10},
  normal:{ss:4,ws:4,gMin:40, gMax:200,wMin:20,wMax:100,label:'NORMAL',xpM:1.5,tokM:1.5,freq:15},
  hard:  {ss:2,ws:3,gMin:50, gMax:240,wMin:15,wMax:80, label:'ZOR',   xpM:2,  tokM:2,  freq:22},
};
let difficulty='normal';

// ═══════════════════════════════════════════════════════
//  QUESTS
// ═══════════════════════════════════════════════════════
const QUEST_POOL=[
  {id:'q_perfect5',  icon:'✨',name:'MÜKEMMEL x5',  desc:'5 mükemmel vuruş yap',   goal:5,  stat:'perfects', reward:30},
  {id:'q_score20',   icon:'🎯',name:'SKOR 20',       desc:'Tek oyunda 20 puan al',  goal:20, stat:'score',    reward:25},
  {id:'q_token50',   icon:'🪙',name:'50 JETON',      desc:'50 jeton kazan',         goal:50, stat:'tokens',   reward:40},
  {id:'q_combo3',    icon:'🔥',name:'KOMBO x3',      desc:'3 ardışık mükemmel vur', goal:3,  stat:'combo',    reward:35},
  {id:'q_play3',     icon:'🕹️',name:'3 OYUN',        desc:'3 oyun oyna',            goal:3,  stat:'games',    reward:20},
  {id:'q_boss',      icon:'💀',name:'BOSS',           desc:'1 boss platform geç',   goal:1,  stat:'boss',     reward:50},
  {id:'q_score50',   icon:'🏆',name:'SKOR 50',       desc:'Tek oyunda 50 puan al',  goal:50, stat:'score',    reward:60},
  {id:'q_perfect10', icon:'⭐',name:'MÜKEMMEL x10',  desc:'10 mükemmel vuruş yap',  goal:10, stat:'perfects', reward:55},
];
function getActiveQuests(){
  // pick 3 from pool based on user seed
  const seed=currentUserIdx*3;
  return [QUEST_POOL[seed%QUEST_POOL.length],QUEST_POOL[(seed+1)%QUEST_POOL.length],QUEST_POOL[(seed+2)%QUEST_POOL.length]];
}
function questProg(qid){return CU().questProgress[qid]||0;}
function questClaimed(qid){return (CU().questClaimed||[]).includes(qid);}
function addQuestProgress(stat,amount=1){
  const quests=getActiveQuests();
  quests.forEach(q=>{
    if(q.stat===stat&&!questClaimed(q.id)){
      CU().questProgress[q.id]=(CU().questProgress[q.id]||0)+amount;
      if(CU().questProgress[q.id]>=q.goal){
        CU().questProgress[q.id]=q.goal;
        // show badge
        const b=document.getElementById('questBadge');if(b)b.style.display='block';
      }
    }
  });
  saveUsers();
}
function claimQuest(qid){
  const q=QUEST_POOL.find(x=>x.id===qid);if(!q)return;
  if(questProg(qid)<q.goal||questClaimed(qid))return;
  CU().tokens+=q.reward;if(!CU().questClaimed)CU().questClaimed=[];
  CU().questClaimed.push(qid);saveUsers();refreshTok();
  showToast('🎯 GÖREV TAMAMLANDI! +'+q.reward+' 🪙','ach');playLevelUp();
  renderQuests();
}
function resetQuests(){
  CU().questProgress={};CU().questClaimed=[];saveUsers();renderQuests();
  const b=document.getElementById('questBadge');if(b)b.style.display='none';
  showToast('GÖREVLER SIFIRLANDI','token');
}
function renderQuests(){
  const quests=getActiveQuests();
  const list=document.getElementById('questList');
  list.innerHTML=quests.map(q=>{
    const prog=questProg(q.id);const claimed=questClaimed(q.id);
    const pct=Math.min(100,Math.round(prog/q.goal*100));
    return`<div class="quest-item">
      <div class="quest-header">
        <span class="quest-icon">${q.icon}</span>
        <span class="quest-name">${q.name}</span>
        <span class="quest-reward">+${q.reward}🪙</span>
      </div>
      <div class="quest-prog-wrap"><div class="quest-prog" style="width:${pct}%"></div></div>
      <div class="quest-desc">${q.desc} (${prog}/${q.goal})</div>
      ${claimed?'<div class="quest-done">✓ TAMAMLANDI</div>':prog>=q.goal?`<button class="quest-claim" onclick="claimQuest('${q.id}')">► AL</button>`:''}
    </div>`;
  }).join('');
  // update badge
  const anyReady=quests.some(q=>questProg(q.id)>=q.goal&&!questClaimed(q.id));
  const b=document.getElementById('questBadge');if(b)b.style.display=anyReady?'block':'none';
}

// ═══════════════════════════════════════════════════════
//  ACHIEVEMENTS
// ═══════════════════════════════════════════════════════
const ACHS=[
  {id:'first_game',  icon:'🕹️',name:'İLK OYUN',      desc:'İlk oyunu oyna'},
  {id:'first_perf',  icon:'✨',name:'İLK MÜKEMMEL',  desc:'İlk mükemmel vuruş'},
  {id:'combo5',      icon:'🔥',name:'KOMBO USTASI',  desc:'5 ardışık mükemmel'},
  {id:'score100',    icon:'💯',name:'YÜZ PUAN',      desc:'100 puan kazan'},
  {id:'boss_first',  icon:'💀',name:'BOSS KATILI',   desc:'İlk boss platformu geç'},
  {id:'level5',      icon:'⭐',name:'SEVİYE 5',      desc:'Seviye 5 ulaş'},
  {id:'tokens100',   icon:'🪙',name:'ZENGİN',        desc:'100 jeton biriktir'},
  {id:'shop_first',  icon:'🏪',name:'ALIŞVERIŞ',     desc:'İlk özellik satın al'},
];
function hasAch(id){return (CU().achievements||[]).includes(id);}
function unlockAch(id){
  if(hasAch(id))return;
  if(!CU().achievements)CU().achievements=[];
  CU().achievements.push(id);saveUsers();
  const a=ACHS.find(x=>x.id===id);
  if(a)showToast('🏆 '+a.name+' AÇILDI!','ach');
}
function renderAch(){
  document.getElementById('achGrid').innerHTML=ACHS.map(a=>{
    const un=hasAch(a.id);
    return`<div class="ach-item ${un?'unlocked':'locked'}">
      <div class="ach-icon">${a.icon}</div>
      <div><div class="ach-name">${a.name}</div><div class="ach-desc">${a.desc}</div></div>
    </div>`;
  }).join('');
}

// ═══════════════════════════════════════════════════════
//  RANDOM EVENTS
// ═══════════════════════════════════════════════════════
const EVENTS=[
  {id:'double_tokens', icon:'🪙',name:'ALTIN YAĞMURU',color:'#f0c040',borderColor:'rgba(240,192,64,.5)', duration:20, desc:'20 saniye 2x jeton!'},
  {id:'extra_life',    icon:'❤️',name:'ŞANS GECESİ',  color:'#e87060',borderColor:'rgba(232,112,96,.5)', duration:0,  desc:'Ekstra can hakkı!'},
  {id:'score_boost',   icon:'⭐',name:'YILDIZ ANLIK', color:'#90caf9',borderColor:'rgba(144,202,249,.5)',duration:15, desc:'15 saniye 3x puan!'},
];
let activeEvent=null;
let eventTokenMul=1,eventScoreMul=1;

function triggerRandomEvent(){
  if(Math.random()>0.08)return; // ~8% chance per platform
  const ev=EVENTS[Math.floor(Math.random()*EVENTS.length)];
  activeEvent=ev;
  const banner=document.getElementById('eventBanner');
  banner.textContent=ev.icon+' '+ev.name+' - '+ev.desc;
  banner.style.color=ev.color;banner.style.borderColor=ev.borderColor;banner.style.background='rgba(0,0,0,.7)';
  banner.classList.add('show');
  showToast(ev.icon+' '+ev.name+'!','event');
  if(ev.id==='double_tokens'){eventTokenMul=2;setTimeout(()=>{eventTokenMul=1;activeEvent=null;banner.classList.remove('show');},ev.duration*1000);}
  else if(ev.id==='extra_life'){lifeUsed=false;setTimeout(()=>{activeEvent=null;banner.classList.remove('show');},3000);}
  else if(ev.id==='score_boost'){eventScoreMul=3;setTimeout(()=>{eventScoreMul=1;activeEvent=null;banner.classList.remove('show');},ev.duration*1000);}
}

// ═══════════════════════════════════════════════════════
//  DAY / NIGHT
// ═══════════════════════════════════════════════════════
let dayPhase=0; // 0=night,1=dawn,2=day,3=dusk  cycles every 20 platforms
let platformCount=0;
const SKY_PHASES=[
  {sky0:'#0d0820',sky1:'#1a1535',sky2:'#2d3a1a',icon:'🌙',hill1:'#2a5c0a',hill2:'#1e4206'},
  {sky0:'#1a0a20',sky1:'#2d1555',sky2:'#4a2a1a',icon:'🌅',hill1:'#3a5c0a',hill2:'#2e4206'},
  {sky0:'#1a3060',sky1:'#2a5090',sky2:'#1a5c2a',icon:'☀️',hill1:'#4a7c1a',hill2:'#3a6210'},
  {sky0:'#2a1520',sky1:'#3d2535',sky2:'#2a3a1a',icon:'🌇',hill1:'#3a6010',hill2:'#2a4806'},
];
function currentSkyPhase(){return SKY_PHASES[dayPhase%4];}

// ═══════════════════════════════════════════════════════
//  RPG
// ═══════════════════════════════════════════════════════
let score=0,xp=0,level=1,consecutivePerfect=0;
const xpPL=lv=>5+lv*3;
function syncFromUser(){xp=CU().xp;level=CU().level;}
function addXP(a){
  const cls=CLASSES.find(c=>c.id===CU().classId)||CLASSES[0];
  xp+=Math.floor(a*DIFF[difficulty].xpM*cls.bonus.xpMul);
  while(xp>=xpPL(level)){xp-=xpPL(level);level++;const b=level*5;CU().tokens+=b;playLevelUp();showToast('🌟 LVL '+level+'! +'+b+'🪙','levelup');if(level>=5)unlockAch('level5');}
  CU().level=level;CU().xp=xp;saveUsers();updateHUD();
}
function earnTokens(n,msg){
  const t=Math.ceil(n*DIFF[difficulty].tokM*eventTokenMul);
  CU().tokens+=t;saveUsers();updateHUD();
  if(msg){showToast('+'+t+'🪙 '+msg,'token');playToken();}
  addQuestProgress('tokens',t);
  if(CU().tokens>=100)unlockAch('tokens100');
}
function updateHUD(){
  document.getElementById('scoreDisplay').textContent=score;
  document.getElementById('levelText').textContent='LVL '+level;
  document.getElementById('xpBar').style.width=Math.min(100,Math.floor(xp/xpPL(level)*100))+'%';
  document.getElementById('tokenCount').textContent=CU().tokens;
  refreshTok();
  document.getElementById('timeDisplay').textContent=currentSkyPhase().icon;
}
function refreshTok(){
  document.getElementById('hubTokens').textContent=CU().tokens;
  const s=document.getElementById('shopTokens');if(s)s.textContent=CU().tokens;
}

// ═══════════════════════════════════════════════════════
//  POWERUPS
// ═══════════════════════════════════════════════════════
let activePU={},shieldUsed=false,lifeUsed=false,doubleActive=false;
let puInterval=null;
function activatePU(id){
  const dur=itemDuration(id);if(dur===0)return;
  activePU[id]={remaining:dur,total:dur};
  document.getElementById('pu-'+id)?.classList.remove('pu-inactive');
  document.getElementById('pu-'+id)?.classList.add('pu-active');
  const it=ITEMS.find(i=>i.id===id);
  showToast((it?.icon||'')+(it?.name||id)+' AKTİF! ('+dur+'s)','pickup');playPickup();
}
function startPUTick(){
  if(puInterval)clearInterval(puInterval);
  puInterval=setInterval(()=>{
    Object.keys(activePU).forEach(id=>{
      const st=activePU[id];st.remaining=Math.max(0,st.remaining-1);
      const pt=document.getElementById('pt-'+id);if(pt)pt.textContent=st.remaining>0?st.remaining:'';
      const pb=document.getElementById('pb-'+id);if(pb)pb.style.width=(st.remaining/st.total*100)+'%';
      if(st.remaining<=0){
        delete activePU[id];
        document.getElementById('pu-'+id)?.classList.remove('pu-active');
        document.getElementById('pu-'+id)?.classList.add('pu-inactive');
        const pt2=document.getElementById('pt-'+id);if(pt2)pt2.textContent='';
        const pb2=document.getElementById('pb-'+id);if(pb2)pb2.style.width='0%';
      }
    });
    doubleActive=!!activePU['double'];
  },1000);
}
function stopPUTick(){if(puInterval){clearInterval(puInterval);puInterval=null;}}
function resetPU(){
  activePU={};doubleActive=false;shieldUsed=false;lifeUsed=false;
  ['shield','magnet','speed','pillar','double','life'].forEach(id=>{
    document.getElementById('pu-'+id)?.classList.remove('pu-active');
    document.getElementById('pu-'+id)?.classList.add('pu-inactive');
    const pt=document.getElementById('pt-'+id);if(pt)pt.textContent='';
    const pb=document.getElementById('pb-'+id);if(pb)pb.style.width='0%';
  });
  stopPUTick();
}
function magnetRange(){const lv=itemLv('magnet');return 18+lv*18;}

// ═══════════════════════════════════════════════════════
//  FIELD PICKUPS
// ═══════════════════════════════════════════════════════
let fieldPickups=[];
const PTYPES=['🪙','🪙','🧲','⚡','✨'];
function maybeSpawnPickup(){
  if(Math.random()<1/DIFF[difficulty].freq){
    const last=platforms[platforms.length-1];
    fieldPickups.push({x:last.x+last.w/2,collected:false,type:PTYPES[Math.floor(Math.random()*PTYPES.length)]});
  }
}
function checkPickups(){
  fieldPickups.forEach(p=>{
    if(p.collected)return;
    const range=itemOwned('magnet')&&activePU['magnet']?magnetRange():26;
    if(Math.abs(heroX-p.x)<range){
      p.collected=true;
      if(p.type==='🪙'){earnTokens(1,'');showToast('+1🪙','token');}
      else if(p.type==='🧲'&&itemOwned('magnet'))activatePU('magnet');
      else if(p.type==='⚡'&&itemOwned('speed'))activatePU('speed');
      else if(p.type==='✨'&&itemOwned('double'))activatePU('double');
    }
  });
  fieldPickups=fieldPickups.filter(p=>!p.collected);
}

// ═══════════════════════════════════════════════════════
//  PARTICLES
// ═══════════════════════════════════════════════════════
function spawnParticles(x,y,color='#f0c040',count=12){
  const hud=document.getElementById('hud');
  for(let i=0;i<count;i++){
    const el=document.createElement('div');el.className='particle';
    const angle=Math.random()*Math.PI*2;const dist=30+Math.random()*60;
    const tx=Math.cos(angle)*dist+'px',ty=Math.sin(angle)*dist+'px';
    el.style.cssText=`width:5px;height:5px;background:${color};left:${x}px;top:${y}px;--tx:translate(${tx},${ty});`;
    hud.appendChild(el);
    setTimeout(()=>el.remove(),900);
  }
}

// ═══════════════════════════════════════════════════════
//  SCREENS
// ═══════════════════════════════════════════════════════
function showScreen(id){
  ['splashScreen','userScreen','hubScreen','profileScreen','shopScreen','leaderScreen','questScreen','achScreen','gameOverScreen'].forEach(s=>{
    document.getElementById(s)?.classList.toggle('hidden',s!==id);
  });
}
function goToUserSelect(){renderUserGrid();renderClassGrid();showScreen('userScreen');}

// ═══════════════════════════════════════════════════════
//  STARS
// ═══════════════════════════════════════════════════════
function buildStars(containerId){
  const c=document.getElementById(containerId);if(!c)return;
  for(let i=0;i<80;i++){const s=document.createElement('span');const sz=Math.random()*2+0.4;s.style.cssText=`width:${sz}px;height:${sz}px;top:${Math.random()*100}%;left:${Math.random()*100}%;animation-delay:${Math.random()*4}s;animation-duration:${2+Math.random()*3}s`;c.appendChild(s);}
}
buildStars('hubStars');

// ═══════════════════════════════════════════════════════
//  USER GRID
// ═══════════════════════════════════════════════════════
let editSlot=-1;
function renderUserGrid(){
  document.getElementById('userGrid').innerHTML=USERS.map((u,i)=>{
    const cls=CLASSES.find(c=>c.id===u.classId)||CLASSES[0];
    return`<div class="user-card ${i===currentUserIdx?'sel':''}" id="uc-${i}" onclick="selectUser(${i})">
      <button class="edit-btn" onclick="event.stopPropagation();openEdit(${i})">✏️</button>
      <div class="av">${AVATARS[i]}</div>
      <div class="un">${u.name}</div>
      <div class="ul">LV${u.level} 🪙${u.tokens}</div>
      <div class="class-badge">${cls.icon}</div>
    </div>`;
  }).join('');
}
function renderClassGrid(){
  document.getElementById('classGrid').innerHTML=CLASSES.map(c=>`
    <div class="class-card ${selectedClassId===c.id?'sel':''}" onclick="selectClass('${c.id}')">
      <div class="ci">${c.icon}</div>
      <div class="cn">${c.name}</div>
      <div class="cd">${c.desc}</div>
    </div>`).join('');
}
function selectUser(i){currentUserIdx=i;LS.set('cop4_curidx',i);selectedClassId=USERS[i].classId||'warrior';renderUserGrid();renderClassGrid();}
function selectClass(id){selectedClassId=id;renderClassGrid();}
function openEdit(i){editSlot=i;document.getElementById('modalInput').value=USERS[i].name;document.getElementById('editModal').classList.remove('hidden');document.getElementById('modalInput').focus();}
function closeEdit(){document.getElementById('editModal').classList.add('hidden');editSlot=-1;}
document.getElementById('modalOk').addEventListener('click',()=>{
  if(editSlot<0)return;
  const v=document.getElementById('modalInput').value.trim().toUpperCase();
  if(v){USERS[editSlot].name=v;saveUsers();renderUserGrid();}closeEdit();
});
document.getElementById('modalCancel').addEventListener('click',closeEdit);
document.getElementById('modalInput').addEventListener('keydown',e=>{if(e.key==='Enter')document.getElementById('modalOk').click();});
document.getElementById('userConfirmBtn').addEventListener('click',()=>{
  CU().classId=selectedClassId;saveUsers();
  syncFromUser();updateHUD();
  document.getElementById('hubName').textContent=CU().name;
  const cls=CLASSES.find(c=>c.id===CU().classId)||CLASSES[0];
  document.getElementById('hubClass').textContent=cls.icon+' '+cls.name;
  document.getElementById('hubAvatar').textContent=AVATARS[currentUserIdx];
  refreshTok();renderQuests();checkQuestBadge();
  showScreen('hubScreen');
});

function checkQuestBadge(){
  const quests=getActiveQuests();
  const anyReady=quests.some(q=>questProg(q.id)>=q.goal&&!questClaimed(q.id));
  const b=document.getElementById('questBadge');if(b)b.style.display=anyReady?'block':'none';
}

// ═══════════════════════════════════════════════════════
//  LEADERBOARD
// ═══════════════════════════════════════════════════════
function getScores(){return LS.get('cop4_scores',[]);}
function saveScore(s,d){const sc=getScores();sc.push({score:s,diff:d,name:CU().name,date:new Date().toLocaleDateString('tr-TR')});sc.sort((a,b)=>b.score-a.score);sc.splice(12);LS.set('cop4_scores',sc);}
function getBest(d){const a=getScores().filter(s=>s.diff===d);return a.length?a[0].score:0;}
function renderLeader(){
  const scores=getScores();
  if(!scores.length){document.getElementById('leaderList').innerHTML='<div style="color:rgba(255,255,255,.3);text-align:center;padding:20px;font-size:1.1rem">Henüz kayıt yok</div>';return;}
  document.getElementById('leaderList').innerHTML=scores.map((s,i)=>`<div class="leader-row"><span class="leader-rank">${i+1}</span><span class="leader-name">${s.name||'?'} ${s.date}</span><span class="leader-score">${s.score}</span><span class="diff-badge ${s.diff}">${DIFF[s.diff].label}</span></div>`).join('');
}

// ═══════════════════════════════════════════════════════
//  PROFILE
// ═══════════════════════════════════════════════════════
function renderProfile(){
  const u=CU();const cls=CLASSES.find(c=>c.id===u.classId)||CLASSES[0];
  document.getElementById('profAv').textContent=AVATARS[currentUserIdx];
  document.getElementById('profName').textContent=u.name;
  document.getElementById('profSub').textContent=cls.icon+' '+cls.name+' • LV'+u.level+' • 🪙'+u.tokens;
  const sc=getScores().filter(s=>s.name===u.name);
  const total=sc.length,best=sc.length?sc[0].score:0,avg=sc.length?Math.round(sc.reduce((a,b)=>a+b.score,0)/sc.length):0;
  document.getElementById('statGrid').innerHTML=`
    <div class="stat-card"><div class="stat-val">${total}</div><div class="stat-lbl">Oyun</div></div>
    <div class="stat-card"><div class="stat-val">${best}</div><div class="stat-lbl">En İyi</div></div>
    <div class="stat-card"><div class="stat-val">${avg}</div><div class="stat-lbl">Ort.</div></div>
    <div class="stat-card"><div class="stat-val">${u.tokens}</div><div class="stat-lbl">Jeton</div></div>
    <div class="stat-card"><div class="stat-val">${u.level}</div><div class="stat-lbl">Seviye</div></div>
    <div class="stat-card"><div class="stat-val">${u.bestCombo||0}</div><div class="stat-lbl">Kombo</div></div>
  `;
  ['easy','normal','hard'].forEach(d=>drawChart(d+'Chart',u.scoreHistory[d]||[],d));
}
function drawChart(cid,data,diff){
  const c=document.getElementById(cid);if(!c)return;
  const W=Math.max(c.offsetWidth,260);c.width=W*2;c.height=150;
  const x2=c.getContext('2d');x2.clearRect(0,0,c.width,c.height);
  const color={easy:'#4caf50',normal:'#f0c040',hard:'#e87060'}[diff];
  if(!data||data.length<2){x2.fillStyle='rgba(255,255,255,.2)';x2.font='20px VT323';x2.textAlign='center';x2.fillText('Veri yok',c.width/2,c.height/2);return;}
  const max=Math.max(...data,1),pad=16,H=c.height,CW=c.width;
  x2.strokeStyle='rgba(255,255,255,.05)';x2.lineWidth=1;
  for(let i=0;i<=4;i++){const y=pad+(H-pad*2)*(1-i/4);x2.beginPath();x2.moveTo(pad,y);x2.lineTo(CW-pad,y);x2.stroke();}
  const g=x2.createLinearGradient(0,pad,0,H);g.addColorStop(0,color+'88');g.addColorStop(1,color+'00');
  const pts=data.map((v,i)=>({x:pad+(CW-pad*2)*(i/(data.length-1)),y:pad+(H-pad*2)*(1-v/max)}));
  x2.beginPath();x2.moveTo(pts[0].x,H);pts.forEach(p=>x2.lineTo(p.x,p.y));x2.lineTo(pts[pts.length-1].x,H);x2.closePath();x2.fillStyle=g;x2.fill();
  x2.beginPath();x2.moveTo(pts[0].x,pts[0].y);pts.forEach(p=>x2.lineTo(p.x,p.y));x2.strokeStyle=color;x2.lineWidth=3;x2.lineJoin='round';x2.stroke();
  pts.forEach(p=>{x2.beginPath();x2.arc(p.x,p.y,4,0,Math.PI*2);x2.fillStyle=color;x2.fill();});
  const avgV=data.reduce((a,b)=>a+b,0)/data.length;
  const avgY=pad+(H-pad*2)*(1-avgV/max);
  x2.setLineDash([5,4]);x2.strokeStyle='rgba(255,255,255,.28)';x2.lineWidth=1.5;x2.beginPath();x2.moveTo(pad,avgY);x2.lineTo(CW-pad,avgY);x2.stroke();x2.setLineDash([]);
}

// ═══════════════════════════════════════════════════════
//  SHOP
// ═══════════════════════════════════════════════════════
function dotColor(idx,lv){
  if(idx>=lv)return'rgba(255,255,255,.1)';
  const frac=(idx+1)/10;
  return`rgb(${Math.round(220*(1-frac))},${Math.round(180*frac+60)},40)`;
}
function renderShop(){
  document.getElementById('shopTokens').textContent=CU().tokens;
  document.getElementById('shopGrid').innerHTML=ITEMS.map(it=>{
    const lv=itemLv(it.id),cost=itemCost(it),maxed=lv>=it.maxLv,canBuy=cost!==null&&CU().tokens>=cost;
    const dur=itemDuration(it.id);
    const dots=Array.from({length:10},(_,i)=>`<div class="lvl-dot" style="width:8px;height:8px;border-radius:2px;background:${dotColor(i,lv)}"></div>`).join('');
    return`<div class="shop-item">
      <div class="si-icon">${it.icon}</div>
      <span class="si-tier tier-${it.tier}">${it.tier.toUpperCase()}</span>
      <div class="si-name">${it.name}</div>
      <div class="si-desc">${it.desc}</div>
      <div class="lvl-dots">${dots}</div>
      <div class="si-dur">${lv>0?'⏱ '+dur+'s':'⏱ --'} LV${lv}/10</div>
      <button class="si-btn ${canBuy?'can':'no'}" ${canBuy?`onclick="buyItem('${it.id}')"`:''}>
        ${maxed?'✓ MAX':canBuy?'🪙 '+cost:'🪙 '+(cost||'MAX')}
      </button>
    </div>`;
  }).join('');
  unlockAch('shop_first');
}

// ═══════════════════════════════════════════════════════
//  TOAST
// ═══════════════════════════════════════════════════════
function showToast(text,type){
  const el=document.createElement('div');el.className='toast '+type;el.textContent=text;
  document.getElementById('toastArea').appendChild(el);
  void el.offsetWidth;el.style.opacity='1';
  setTimeout(()=>el.remove(),1800);
}

// ═══════════════════════════════════════════════════════
//  HUB EVENTS
// ═══════════════════════════════════════════════════════
document.querySelectorAll('.diff-btn').forEach(btn=>{
  btn.addEventListener('click',()=>{document.querySelectorAll('.diff-btn').forEach(b=>b.classList.remove('active'));btn.classList.add('active');difficulty=btn.dataset.diff;});
});

// rebuild diff row in hub
const diffRow=document.getElementById('diffRow');
['easy','normal','hard'].forEach(d=>{
  const b=document.createElement('button');b.className='diff-btn'+(d==='normal'?' active':'');
  b.dataset.diff=d;b.textContent=DIFF[d].label;
  b.addEventListener('click',()=>{document.querySelectorAll('#diffRow .diff-btn').forEach(x=>x.classList.remove('active'));b.classList.add('active');difficulty=d;});
  diffRow.appendChild(b);
});

document.getElementById('playBtn').addEventListener('click',()=>{showScreen(null);startGame();});
document.getElementById('questResetBtn').addEventListener('click',resetQuests);
document.getElementById('goRestart').addEventListener('click',()=>{showScreen(null);startGame();});
document.getElementById('goHub').addEventListener('click',()=>{refreshTok();checkQuestBadge();showScreen('hubScreen');});

// ═══════════════════════════════════════════════════════
//  GAME ENGINE
// ═══════════════════════════════════════════════════════
Array.prototype.last=function(){return this[this.length-1];};
Math.sinus=deg=>Math.sin((deg/180)*Math.PI);

let phase,lastTimestamp,heroX,heroY,sceneOffset,platforms,sticks,trees;
let isBossPlatform=false,bossCleared=false;

const CW=375,CH=375,PH=100,HDE=10,PX=100;
let PAS=10; // perfect area size, modified by archer class
const BGS=0.2,H1H=100,H1A=10,H1S=1,H2H=70,H2A=20,H2S=0.5;
const TS=4,TRS=2,FS=2,HW=17,HHero=30;

const canvas=document.getElementById('game');
canvas.width=window.innerWidth;canvas.height=window.innerHeight;
const ctx=canvas.getContext('2d');
const introEl=document.getElementById('introduction');

function startGame(){
  syncFromUser();score=0;consecutivePerfect=0;fieldPickups=[];platformCount=0;dayPhase=0;
  isBossPlatform=false;bossCleared=false;eventTokenMul=1;eventScoreMul=1;
  document.getElementById('eventBanner').classList.remove('show');
  document.getElementById('bossIndicator').classList.remove('show');
  resetPU();startPUTick();
  const cls=CLASSES.find(c=>c.id===CU().classId)||CLASSES[0];
  PAS=cls.bonus.widerPerfect?20:10;
  const badge=document.getElementById('diffBadge');
  badge.textContent=DIFF[difficulty].label;badge.id='diffBadge';badge.className=difficulty;
  addQuestProgress('games',1);unlockAch('first_game');
  resetGame();updateHUD();
}

function resetGame(){
  phase='waiting';lastTimestamp=undefined;sceneOffset=0;score=0;
  introEl.style.opacity=1;updateHUD();
  platforms=[{x:50,w:50,boss:false}];
  for(let i=0;i<4;i++)generatePlatform();
  sticks=[{x:platforms[0].x+platforms[0].w,length:0,rotation:0}];
  trees=[];for(let i=0;i<10;i++)generateTree();
  heroX=platforms[0].x+platforms[0].w-HDE;heroY=0;draw();
}

function generateTree(){
  const last=trees[trees.length-1];const fx=last?last.x:0;
  const x=fx+30+Math.floor(Math.random()*120);
  trees.push({x,color:["#6D8821","#8FAC34","#98B333"][Math.floor(Math.random()*3)]});
}
function generatePlatform(){
  platformCount++;
  const d=DIFF[difficulty],last=platforms[platforms.length-1],fx=last.x+last.w;
  const isBoss=(platformCount%15===0);
  // boss platforms: wide but narrow landing zone handled in drawing
  const x=fx+d.gMin+Math.floor(Math.random()*(d.gMax-d.gMin));
  const w=isBoss?140:(d.wMin+Math.floor(Math.random()*(d.wMax-d.wMin)));
  platforms.push({x,w,boss:isBoss});
  maybeSpawnPickup();
  triggerRandomEvent();
  // day/night
  if(platformCount%20===0){dayPhase=(dayPhase+1)%4;updateHUD();}
}

window.addEventListener('mousedown',()=>{
  if(phase==='waiting'){lastTimestamp=undefined;introEl.style.opacity=0;phase='stretching';window.requestAnimationFrame(animate);playStretch();}
});
window.addEventListener('mouseup',()=>{if(phase==='stretching'){phase='turning';stopStretch();}});
window.addEventListener('touchstart',e=>{e.preventDefault();if(phase==='waiting'){lastTimestamp=undefined;introEl.style.opacity=0;phase='stretching';window.requestAnimationFrame(animate);playStretch();}},{passive:false});
window.addEventListener('touchend',e=>{e.preventDefault();if(phase==='stretching'){phase='turning';stopStretch();}},{passive:false});
window.addEventListener('resize',()=>{canvas.width=window.innerWidth;canvas.height=window.innerHeight;draw();});
window.requestAnimationFrame(animate);

function animate(ts){
  if(!lastTimestamp){lastTimestamp=ts;window.requestAnimationFrame(animate);return;}
  const d=DIFF[difficulty];
  const cls=CLASSES.find(c=>c.id===CU().classId)||CLASSES[0];
  const ws=activePU['speed']?(d.ws*cls.bonus.speedMul):d.ws;
  const ss=itemOwned('pillar')&&activePU['pillar']?d.ss*0.4:d.ss;
  const dt=ts-lastTimestamp;

  switch(phase){
    case 'waiting':return;
    case 'stretching':sticks.last().length+=dt/ss;break;
    case 'turning':
      sticks.last().rotation+=dt/TS;
      if(sticks.last().rotation>90){
        sticks.last().rotation=90;
        const[np,perf]=thePlatformHit();
        if(np){
          const sMul=(doubleActive?2:1)*eventScoreMul*(perf?1:1);
          let pts=(perf?2:1)*sMul+cls.bonus.perfectBonus*(perf?1:0);
          pts=Math.round(pts);
          score+=pts;earnTokens(1,'');
          if(perf){
            consecutivePerfect++;
            CU().bestCombo=Math.max(CU().bestCombo||0,consecutivePerfect);
            addQuestProgress('perfects',1);addQuestProgress('combo',consecutivePerfect>=3?1:0);
            const bPts=consecutivePerfect>1?consecutivePerfect*sMul:0;
            const bTok=consecutivePerfect>1?consecutivePerfect:0;
            score+=Math.round(bPts);if(bTok>0)earnTokens(bTok,'MÜKEMMEL');
            addXP(pts+bPts+2);showToast('✨ MÜKEMMEL!','perfect');playPerfect();
            // particles
            const cx=window.innerWidth/2,cy=window.innerHeight/2;
            spawnParticles(cx,cy,'#f0c040',16);
            if(consecutivePerfect>1){showToast('🔥 x'+consecutivePerfect+' COMBO','combo');playCombo();}
            unlockAch('first_perf');
            if(consecutivePerfect>=5)unlockAch('combo5');
          } else {consecutivePerfect=0;addXP(pts);}
          addQuestProgress('score',pts);
          if(score>=100)unlockAch('score100');
          updateHUD();
          // boss clear
          if(np.boss&&!bossCleared){
            bossCleared=true;earnTokens(20,'BOSS!');showToast('💀 BOSS GEÇİLDİ! +20🪙','boss');playBoss();
            addQuestProgress('boss',1);unlockAch('boss_first');
            document.getElementById('bossIndicator').classList.remove('show');
          }
          generatePlatform();generateTree();generateTree();
          // show boss indicator if next is boss
          if(platforms[platforms.length-1].boss){
            document.getElementById('bossIndicator').classList.add('show');playBoss();
          } else {
            document.getElementById('bossIndicator').classList.remove('show');
          }
          saveUsers();
        } else consecutivePerfect=0;
        phase='walking';
      }
      break;
    case 'walking':
      heroX+=dt/ws;checkPickups();
      const[np2]=thePlatformHit();
      if(np2){const mx=np2.x+np2.w-HDE;if(heroX>mx){heroX=mx;phase='transitioning';}}
      else{const mx=sticks.last().x+sticks.last().length+HW;if(heroX>mx){heroX=mx;phase='falling';}}
      break;
    case 'transitioning':
      sceneOffset+=dt/TRS;
      const[np3]=thePlatformHit();
      if(sceneOffset>np3.x+np3.w-PX){sticks.push({x:np3.x+np3.w,length:0,rotation:0});phase='waiting';}
      break;
    case 'falling':
      if(sticks.last().rotation<180)sticks.last().rotation+=dt/TS;
      heroY+=dt/FS;
      if(heroY>PH+100+(window.innerHeight-CH)/2){
        const shieldMul=cls.bonus.shieldMul;
        if(itemOwned('shield')&&!shieldUsed&&shieldMul>=1){
          shieldUsed=true;showToast('🛡️ KALKAN KORUDI!','pickup');
          const lp=platforms[platforms.length-2]||platforms[0];
          heroX=lp.x+lp.w-HDE;heroY=0;sticks.last().rotation=90;phase='waiting';break;
        }
        if(itemOwned('life')&&!lifeUsed){
          lifeUsed=true;showToast('❤️ CAN HAKKI!','pickup');
          const lp=platforms[platforms.length-2]||platforms[0];
          heroX=lp.x+lp.w-HDE;heroY=0;sticks.last().rotation=90;phase='waiting';break;
        }
        gameOver();return;
      }
      break;
    default:throw Error('Wrong phase');
  }
  draw();window.requestAnimationFrame(animate);lastTimestamp=ts;
}

function thePlatformHit(){
  if(sticks.last().rotation!==90)throw Error('Stick not 90');
  const fx=sticks.last().x+sticks.last().length;
  const p=platforms.find(p=>{
    if(p.boss){
      // boss platform: landing zone is left third or right third only (middle is hole)
      const hole_start=p.x+p.w*0.35,hole_end=p.x+p.w*0.65;
      return(p.x<fx&&fx<hole_start)||(hole_end<fx&&fx<p.x+p.w);
    }
    return p.x<fx&&fx<p.x+p.w;
  });
  // perfect zone (center for normal, avoid boss hole center)
  if(p&&!p.boss&&p.x+p.w/2-PAS/2<fx&&fx<p.x+p.w/2+PAS/2)return[p,true];
  return[p,false];
}

function gameOver(){
  stopPUTick();playGameOver();
  CU().scoreHistory[difficulty].push(score);
  if(CU().scoreHistory[difficulty].length>20)CU().scoreHistory[difficulty].shift();
  saveScore(score,difficulty);
  const tokEarned=Math.floor(score/2);CU().tokens+=tokEarned;
  saveUsers();checkQuestBadge();
  const best=getBest(difficulty);const isNew=score>=best&&score>0;
  document.getElementById('goScore').textContent=score;
  document.getElementById('goEarned').textContent='+'+tokEarned+'🪙 KAZANILDI';
  document.getElementById('goBest').innerHTML=isNew?'<span class="go-new">🏆 YENİ REKOR!</span>':'EN YÜKSEK: '+best;
  showScreen('gameOverScreen');
}

// ═══════════════════════════════════════════════════════
//  DRAW
// ═══════════════════════════════════════════════════════
function draw(){
  ctx.save();ctx.clearRect(0,0,window.innerWidth,window.innerHeight);
  drawBG();
  ctx.translate((window.innerWidth-CW)/2-sceneOffset,(window.innerHeight-CH)/2);
  drawPlatforms();drawPickups();drawHero();drawSticks();
  ctx.restore();
}

function drawPlatforms(){
  platforms.forEach(({x,w,boss})=>{
    if(boss){
      // boss platform: gold/dark with hole in middle
      const holeS=x+w*0.35,holeE=x+w*0.65;
      // left part
      ctx.fillStyle='#3d1010';ctx.fillRect(x,CH-PH,holeS-x,PH+(window.innerHeight-CH)/2);
      ctx.fillStyle='rgba(232,112,96,.4)';ctx.fillRect(x,CH-PH,holeS-x,2);
      // right part
      ctx.fillStyle='#3d1010';ctx.fillRect(holeE,CH-PH,x+w-holeE,PH+(window.innerHeight-CH)/2);
      ctx.fillStyle='rgba(232,112,96,.4)';ctx.fillRect(holeE,CH-PH,x+w-holeE,2);
      // skull decoration
      ctx.font='18px serif';ctx.textAlign='center';ctx.globalAlpha=0.7;
      ctx.fillText('💀',(holeS+holeE)/2,CH-PH-8);ctx.globalAlpha=1;
    } else {
      const g=ctx.createLinearGradient(x,0,x+w,0);
      g.addColorStop(0,'#2a1f0a');g.addColorStop(.5,'#3d2e10');g.addColorStop(1,'#2a1f0a');
      ctx.fillStyle=g;ctx.fillRect(x,CH-PH,w,PH+(window.innerHeight-CH)/2);
      ctx.fillStyle='rgba(240,192,64,0.35)';ctx.fillRect(x,CH-PH,w,2);
      if(sticks.last().x<x){
        const pg=ctx.createLinearGradient(0,CH-PH,0,CH-PH+PAS);
        pg.addColorStop(0,'#f0c040');pg.addColorStop(1,'#c89a20');
        ctx.fillStyle=pg;ctx.fillRect(x+w/2-PAS/2,CH-PH,PAS,PAS);
      }
    }
  });
}

function drawPickups(){
  const t=Date.now()/400;
  fieldPickups.forEach(p=>{
    if(p.collected)return;
    const y=CH-PH-42+Math.sin(t+p.x)*8;
    ctx.save();ctx.font='19px serif';ctx.textAlign='center';ctx.globalAlpha=0.88;
    ctx.fillText(p.type,p.x,y);ctx.restore();
  });
}

function drawHero(){
  ctx.save();ctx.translate(heroX-HW/2,heroY+CH-PH-HHero/2);
  const cls=CLASSES.find(c=>c.id===CU().classId)||CLASSES[0];
  // Shield aura
  if(itemOwned('shield')&&!shieldUsed){
    const lv=itemLv('shield')/10;
    ctx.save();ctx.beginPath();ctx.arc(0,0,18+lv*12,0,Math.PI*2);
    ctx.fillStyle=`rgba(100,180,255,${0.04+lv*0.07})`;
    ctx.strokeStyle=`rgba(100,180,255,${0.15+lv*0.25})`;ctx.lineWidth=1+lv*2;
    ctx.fill();ctx.stroke();ctx.restore();
  }
  // Class aura
  if(cls.id==='mage'){ctx.save();ctx.beginPath();ctx.arc(0,0,22,0,Math.PI*2);ctx.strokeStyle='rgba(156,39,176,.3)';ctx.lineWidth=1.5;ctx.stroke();ctx.restore();}
  if(cls.id==='archer'){ctx.save();ctx.beginPath();ctx.arc(0,0,22,0,Math.PI*2);ctx.strokeStyle='rgba(76,175,80,.3)';ctx.lineWidth=1.5;ctx.stroke();ctx.restore();}

  ctx.fillStyle='rgba(0,0,0,0.2)';ctx.beginPath();ctx.ellipse(0,HHero/2+4,10,3,0,0,Math.PI*2);ctx.fill();
  ctx.fillStyle='#c0c0c0';drawRR(-HW/2,-HHero/2,HW,HHero-4,5);
  ctx.fillStyle='rgba(255,255,255,0.13)';drawRR(-HW/2,-HHero/2,HW/2,HHero-4,5);
  ctx.fillStyle='#888';ctx.beginPath();ctx.arc(5,11.5,3,0,Math.PI*2);ctx.fill();ctx.beginPath();ctx.arc(-5,11.5,3,0,Math.PI*2);ctx.fill();
  ctx.beginPath();ctx.fillStyle='white';ctx.arc(5,-7,3,0,Math.PI*2);ctx.fill();
  ctx.beginPath();ctx.fillStyle='#222';ctx.arc(6,-7,1.5,0,Math.PI*2);ctx.fill();
  ctx.fillStyle='#c89a20';ctx.fillRect(-HW/2-1,-12,HW+2,4.5);
  ctx.fillStyle='#f0c040';
  ctx.beginPath();ctx.moveTo(-9,-14.5);ctx.lineTo(-17,-18.5);ctx.lineTo(-14,-8.5);ctx.fill();
  ctx.beginPath();ctx.moveTo(-10,-10.5);ctx.lineTo(-15,-3.5);ctx.lineTo(-5,-7);ctx.fill();
  ctx.restore();
}

function drawRR(x,y,w,h,r){
  ctx.beginPath();ctx.moveTo(x,y+r);ctx.lineTo(x,y+h-r);ctx.arcTo(x,y+h,x+r,y+h,r);
  ctx.lineTo(x+w-r,y+h);ctx.arcTo(x+w,y+h,x+w,y+h-r,r);
  ctx.lineTo(x+w,y+r);ctx.arcTo(x+w,y,x+w-r,y,r);
  ctx.lineTo(x+r,y);ctx.arcTo(x,y,x,y+r,r);ctx.fill();
}

function drawSticks(){
  sticks.forEach(s=>{
    ctx.save();ctx.translate(s.x,CH-PH);ctx.rotate((Math.PI/180)*s.rotation);
    const g=ctx.createLinearGradient(0,0,3,-s.length);g.addColorStop(0,'#c89a20');g.addColorStop(1,'#f0c040');
    ctx.strokeStyle=g;ctx.lineWidth=3;ctx.lineCap='round';ctx.beginPath();ctx.moveTo(0,0);ctx.lineTo(0,-s.length);ctx.stroke();
    ctx.restore();
  });
}

function drawBG(){
  const sp=currentSkyPhase();
  const sky=ctx.createLinearGradient(0,0,0,window.innerHeight);
  sky.addColorStop(0,sp.sky0);sky.addColorStop(.5,sp.sky1);sky.addColorStop(1,sp.sky2);
  ctx.fillStyle=sky;ctx.fillRect(0,0,window.innerWidth,window.innerHeight);
  // Moon or sun
  if(dayPhase===0||dayPhase===1){
    ctx.save();ctx.beginPath();ctx.arc(window.innerWidth*.85,80,26,0,Math.PI*2);
    ctx.fillStyle='rgba(240,220,140,0.9)';ctx.shadowColor='rgba(240,220,140,0.5)';ctx.shadowBlur=30;ctx.fill();ctx.restore();
  } else {
    ctx.save();ctx.beginPath();ctx.arc(window.innerWidth*.85,80,32,0,Math.PI*2);
    ctx.fillStyle='rgba(255,230,80,0.95)';ctx.shadowColor='rgba(255,230,80,0.6)';ctx.shadowBlur=40;ctx.fill();ctx.restore();
  }
  ctx.save();ctx.fillStyle='rgba(255,255,255,'+(dayPhase<2?'0.8':'0.2')+')';
  [[50,40],[120,25],[200,60],[300,30],[400,50],[500,20],[600,45],[700,35],[800,80],[900,55]].forEach(([x,y])=>{ctx.beginPath();ctx.arc(x,y,1,0,Math.PI*2);ctx.fill();});
  ctx.restore();
  drawHill(H1H,H1A,H1S,sp.hill1);drawHill(H2H,H2A,H2S,sp.hill2);
  trees.forEach(t=>drawTree(t.x,t.color));
}

function drawHill(bH,amp,str,col){
  ctx.beginPath();ctx.moveTo(0,window.innerHeight);ctx.lineTo(0,getHillY(0,bH,amp,str));
  for(let i=0;i<window.innerWidth;i++)ctx.lineTo(i,getHillY(i,bH,amp,str));
  ctx.lineTo(window.innerWidth,window.innerHeight);ctx.fillStyle=col;ctx.fill();
}
function drawTree(x,color){
  ctx.save();ctx.translate((-sceneOffset*BGS+x)*H1S,getTreeY(x,H1H,H1A));
  ctx.fillStyle='#4a3a1a';ctx.fillRect(-1,-5,2,5);
  ctx.beginPath();ctx.moveTo(-8,-5);ctx.lineTo(0,-30);ctx.lineTo(8,-5);
  ctx.fillStyle=color+'cc';ctx.fill();ctx.restore();
}
function getHillY(wx,bH,amp,str){return Math.sinus((sceneOffset*BGS+wx)*str)*amp+(window.innerHeight-bH);}
function getTreeY(x,bH,amp){return Math.sinus(x)*amp+(window.innerHeight-bH);}

// ═══════════════════════════════════════════════════════
//  SPLASH LOADING
// ═══════════════════════════════════════════════════════
const TIPS=['Savaşçı: Kalkan 2x güçlü','Okçu: Mükemmel alan geniş','Büyücü: XP 1.3x fazla','Hız: Speed 3x güçlü','Boss platformlarda orta delik var!','Kombo yapınca bonus jeton kazanırsın','Mağazadan özellik satın alabilirsin'];
let splashProgress=0;
const splashBar=document.getElementById('splashBar');
const splashTip=document.getElementById('splashTip');
splashTip.textContent=TIPS[Math.floor(Math.random()*TIPS.length)];

const splashInterval=setInterval(()=>{
  splashProgress+=Math.random()*15+8;
  if(splashProgress>=100){splashProgress=100;clearInterval(splashInterval);
    setTimeout(()=>{
      renderUserGrid();renderClassGrid();
      showScreen('userScreen');
    },400);
  }
  splashBar.style.width=splashProgress+'%';
},120);
</script>
</body>
</html>
