<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>Avengers vs Thanos</title>
  <style>
    body { font-family: Arial, sans-serif; background:#111; color:#eee; text-align:center; }
    h1 { color:#f39c12; }
    .choice { margin:20px; }
    button { padding:10px 20px; margin:5px; cursor:pointer; font-size:16px; }
    .bar { height:20px; background:#333; border-radius:10px; margin:10px auto; width:300px; position:relative; }
    .fill { height:100%; border-radius:10px; }
    #log { border:1px solid #555; padding:10px; height:200px; overflow-y:auto; background:#222; text-align:left; margin-top:20px; }
    .avatar { font-size:50px; }
    .floating-dmg { position:absolute; animation: floatUp 1s ease-out forwards; font-weight:bold; }
    @keyframes floatUp { from { opacity:1; transform:translateY(0); } to { opacity:0; transform:translateY(-50px); } }
  </style>
</head>
<body>
  <h1>🦸 Avengers vs Thanos 🟣</h1>

  <div id="setup">
    <div class="choice">
      <h2>Choisis ton héros</h2>
      <button onclick="selectHero('Iron Man','🤖')">🤖 Iron Man</button>
      <button onclick="selectHero('Captain America','🛡️')">🛡️ Captain</button>
      <button onclick="selectHero('Thor','⚡')">⚡ Thor</button>
      <button onclick="selectHero('Black Widow','🕷️')">🕷️ Black Widow</button>
      <button onclick="selectHero('Hawkeye','🏹')">🏹 Hawkeye</button>
      <button onclick="selectHero('Hulk','🟢')">🟢 Hulk</button>
    </div>
    <div class="choice">
      <h2>Choisis ton arme</h2>
      <button onclick="selectWeapon('Répulseurs',4,7)">🔫 Répulseurs (4-7)</button>
      <button onclick="selectWeapon('Bouclier',3,6)">🛡️ Bouclier (3-6)</button>
      <button onclick="selectWeapon('Mjolnir',5,9)">⚒️ Mjolnir (5-9)</button>
      <button onclick="selectWeapon('Bâtons',3,6)">🥷 Bâtons (3-6)</button>
      <button onclick="selectWeapon('Arc',4,7)">🏹 Arc (4-7)</button>
      <button onclick="selectWeapon('Poings',5,8)">👊 Poings (5-8)</button>
    </div>
    <div class="choice">
      <h2>Choisis ton pouvoir offensif</h2>
      <button onclick="selectPower('Explosion',8,12)">💥 Explosion (8-12)</button>
      <button onclick="selectPower('Frappe divine',8,12)">⚡ Frappe divine (8-12)</button>
      <button onclick="selectPower('Rage',8,12)">🔥 Rage (8-12)</button>
    </div>
    <div class="choice">
      <h2>Choisis la difficulté</h2>
      <button onclick="startGame('Facile')">Facile</button>
      <button onclick="startGame('Normal')">Normal</button>
      <button onclick="startGame('Difficile')">Difficile</button>
      <button onclick="startGame('Cauchemar')">Cauchemar</button>
    </div>
  </div>

  <div id="battle" style="display:none;">
    <div>
      <div class="avatar" id="heroAvatar"></div>
      <div id="heroName"></div>
      <div class="bar"><div id="player-bar" class="fill" style="background:#2ecc71; width:100%"></div></div>
      <div>PV: <span id="player-pv"></span></div>
    </div>
    <hr>
    <div>
      <div class="avatar">🟣</div>
      <div>Thanos</div>
      <div class="bar"><div id="enemy-bar" class="fill" style="background:#e74c3c; width:100%"></div></div>
      <div>PV: <span id="enemy-pv"></span></div>
    </div>
    <hr>
    <div id="actions"></div>
    <button id="continueBtn" style="display:none;" onclick="continueBattle()">Continuer</button>
    <div id="log"></div>
  </div>

<script>
let state = {
  hero:null, avatar:null, weapon:null, power:null,
  difficulty:"Normal",
  player:{pv:30, max:30, usedPower:false, shieldAvailable:true, shieldActive:false},
  enemy:{pv:50, max:50, rounds:999, dmg:[4,8]},
  turn:"player"
};

function selectHero(name, emoji){ state.hero=name; state.avatar=emoji; }
function selectWeapon(name,min,max){ state.weapon={name,min,max}; }
function selectPower(name,min,max){ state.power={name,min,max}; }

function startGame(diff){
  state.difficulty=diff;
  if(diff==="Facile"){ state.enemy.pv=40; state.enemy.max=40; state.enemy.dmg=[3,6]; }
  if(diff==="Normal"){ state.enemy.pv=50; state.enemy.max=50; state.enemy.dmg=[4,8]; }
  if(diff==="Difficile"){ state.enemy.pv=60; state.enemy.max=60; state.enemy.dmg=[6,12]; }
  if(diff==="Cauchemar"){ state.enemy.pv=75; state.enemy.max=75; state.enemy.dmg=[8,15]; }
  document.getElementById("setup").style.display="none";
  document.getElementById("battle").style.display="block";
  state.player.pv=30; state.player.max=30; state.player.usedPower=false;
  state.player.shieldAvailable=true; state.player.shieldActive=false;
  document.getElementById("heroAvatar").textContent=state.avatar;
  document.getElementById("heroName").textContent=state.hero;
  render();
  playerAttackPrompt();
}

function rnd(min,max){ return Math.floor(Math.random()*(max-min+1))+min; }

function render(){
  document.getElementById("player-pv").textContent=state.player.pv+"/"+state.player.max;
  document.getElementById("enemy-pv").textContent=state.enemy.pv+"/"+state.enemy.max;
  document.getElementById("player-bar").style.width=(state.player.pv/state.player.max*100)+"%";
  document.getElementById("enemy-bar").style.width=(state.enemy.pv/state.enemy.max*100)+"%";
}

function pushLog(msg){
  const log=document.getElementById("log");
  log.innerHTML+="<div>"+msg+"</div>";
  log.scrollTop=log.scrollHeight;
}

function showFloatingDmg(target, dmg, color){
  const bar=document.getElementById(target);
  const span=document.createElement("span");
  span.className="floating-dmg";
  span.textContent=(dmg>0? "-"+dmg: dmg);
  span.style.color=color;
  span.style.left=(bar.offsetLeft+100)+"px";
  span.style.top=(bar.offsetTop-20)+"px";
  document.body.appendChild(span);
  setTimeout(()=>span.remove(),1000);
}

function playerAttackPrompt(){
  document.getElementById("actions").innerHTML=`
    <button onclick="applyPlayerDamage('Rapide')">⚡ Rapide (3-5)</button>
    <button onclick="applyPlayerDamage('Lourde')">💥 Lourde (5-9, risque raté)</button>
    <button onclick="applyPlayerDamage('Arme')">${state.weapon?state.weapon.name:"Arme"} (${state.weapon.min}-${state.weapon.max})</button>
    <button ${state.player.usedPower?"disabled":""} onclick="applyPlayerDamage('Pouvoir')">${state.power?state.power.name:"Pouvoir"} (8-12, unique)</button>
    <button ${state.player.shieldAvailable?"":"disabled"} onclick="applyPlayerDamage('Bouclier')">🛡️ Bouclier (bloque 1 attaque)</button>
  `;
}

function applyPlayerDamage(type){
  let dmg=0, miss=false, crit=false;
  if(type==="Rapide") dmg=rnd(3,5);
  if(type==="Lourde"){ if(Math.random()<0.2){ miss=true; } else dmg=rnd(5,9); }
  if(type==="Arme") dmg=rnd(state.weapon.min,state.weapon.max);
  if(type==="Pouvoir"){ dmg=rnd(8,12); state.player.usedPower=true; }
  if(type==="Bouclier"){ state.player.shieldActive=true; state.player.shieldAvailable=false; pushLog("🛡️ Bouclier activé ! La prochaine attaque de Thanos sera bloquée."); dmg=0; }
  if(type!=="Bouclier" && !miss && Math.random()<0.2){ crit=true; dmg*=2; }
  if(miss) dmg=0;

  if(type!=="Bouclier"){
    state.enemy.pv=Math.max(0,state.enemy.pv-dmg);
    render();
    if(miss){ pushLog("❌ Ton attaque a échoué !"); }
    else if(crit){ pushLog("💥 Coup critique ! Tu infliges "+dmg+" PV"); showFloatingDmg("enemy-bar",dmg,"yellow"); }
    else { pushLog("✅ Tu infliges "+dmg+" PV"); showFloatingDmg("enemy-bar",dmg,"red"); }
  }

  document.getElementById("actions").innerHTML="";
  document.getElementById("continueBtn").style.display="block";
  state.turn="enemy";
  if(state.enemy.pv<=0){ pushLog("🎉 Thanos est vaincu !"); endGame(); }
}

function continueBattle(){
  document.getElementById("continueBtn").style.display="none";
  if(state.turn==="enemy"){ enemyTurn(); }
  else { playerAttackPrompt(); }
}

function enemyTurn(){
  if(state.enemy.pv<=0){ endGame(); return; }

  if(state.player.shieldActive){
    pushLog("🛡️ Ton bouclier bloque complètement l'attaque de Thanos !");
    state.player.shieldActive=false;
  } else {
    let dmg=0;
    if((state.difficulty==="Difficile" || state.difficulty==="Cauchemar") && Math.random()<0.2){
      dmg=rnd(10,20);
      state.player.pv=Math.max(0,state.player.pv-dmg);
      pushLog("✨🫰 Thanos claque des doigts ! Il t'arrache "+dmg+" PV !");
      showFloatingDmg("player-bar",dmg,"purple");
    } else {
      dmg=rnd(state.enemy.dmg[0],state.enemy.dmg[1]);
      state.player.pv=Math.max(0,state.player.pv-dmg);
      pushLog("⚔️ Thanos attaque et inflige "+dmg+" PV !");
      showFloatingDmg("player-bar",dmg,"red");
    }
  }

  render();
  state.turn="player";
  if(state.player.pv<=0){ pushLog("💀 "+state.hero+" est vaincu..."); endGame(); }
  else { document.getElementById("continueBtn").style.display="block"; }
}

function endGame(){ document.getElementById("actions").innerHTML=""; document.getElementById("continueBtn").style.display="none"; }
</script>
</body>
</html>
