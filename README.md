<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Full of Mercy — For the People, By the People</title>
<style>
:root{--primary:#708238;--bg:#f7f7f5;--card:#fff;--muted:#555;}
body{margin:0;font-family:system-ui,sans-serif;background:var(--bg);color:#111;}
.wrap{max-width:820px;margin:28px auto;padding:20px;}
h1{margin:0;font-size:1.8rem;color:var(--primary);}
p.lead{margin:6px 0 18px;color:var(--muted);}
.card{background:var(--card);padding:18px;border-radius:12px;box-shadow:0 6px 18px rgba(10,10,10,0.06);}
.big-btn{display:block;margin:18px auto;padding:28px 26px;border-radius:16px;background:var(--primary);color:white;font-size:20px;border:0;cursor:pointer;box-shadow:0 8px 24px rgba(112,130,56,0.18);}
.counter{text-align:center;margin-top:12px;color:var(--primary);font-weight:700;}
.choices{display:grid;grid-template-columns:1fr 1fr;gap:12px;margin-top:16px;}
.btn{padding:10px;border-radius:10px;border:1px solid #eee;background:#fafafa;cursor:pointer;}
.search{width:100%;padding:10px;border-radius:10px;border:1px solid #ddd;margin-bottom:8px;}
.list{max-height:260px;overflow:auto;border:1px solid #eee;padding:8px;border-radius:8px;background:#fff;}
.country{padding:8px;border-bottom:1px dashed #f0f0f0;cursor:pointer;}
.country:last-child{border-bottom:0;}
footer{margin-top:18px;text-align:center;color:var(--muted);font-size:13px;}
.modal{position:fixed;inset:0;display:none;align-items:center;justify-content:center;background:rgba(0,0,0,0.35);z-index:40;}
.modal .box{width:92%;max-width:760px;background:white;border-radius:12px;padding:16px;}
.tabs{display:flex;gap:8px;margin-bottom:8px;}
.tab{padding:8px;border-radius:8px;border:1px solid #eee;cursor:pointer;background:#fafafa;}
.profiles{display:grid;grid-template-columns:1fr 1fr;gap:8px;margin-top:8px;}
.profile{padding:10px;border-radius:8px;border:1px solid #f1f1f1;}
.map-info{font-size:13px;color:var(--muted);margin-top:8px;}
small.help{color:#666;display:block;margin-top:8px;}
</style>
</head>
<body>
<div class="wrap">
<h1>Full of Mercy</h1>
<p class="lead">For the People, By the People. Tap for Kindness — every 3 acts = 1 meal credit.</p>

<div class="card">
<button id="kindBtn" class="big-btn">Tap for Kindness 💚</button>
<div class="counter" id="counterText">0 / 3 acts</div>

<div class="choices">
  <div>
    <div style="font-weight:700;margin-bottom:8px">Credits</div>
    <div id="credits" style="font-size:20px;color:var(--primary)">0</div>
  </div>
  <div style="text-align:right">
    <div style="font-weight:700;margin-bottom:8px">Actions</div>
    <button id="openModal" class="btn">Redeem Credit</button>
    <small class="help">Click redeem when you have credits.</small>
  </div>
</div>

<hr style="margin:14px 0;border:none;border-top:1px solid #f0f0f0" />

<div style="display:flex;gap:8px;align-items:center;flex-wrap:wrap">
  <button id="demoGive" class="btn">Demo: Give 1 Credit</button>
  <button id="reset" class="btn">Reset Demo</button>
</div>
</div>

<footer>
<small>Made with ♥ — For the People, By the People</small>
</footer>
</div>

<!-- Modal -->
<div id="redeemModal" class="modal">
<div class="box">
<div style="display:flex;align-items:center;justify-content:space-between">
<h3 style="margin:0">Redeem Food Credit</h3>
<button id="closeModal" class="btn">Close</button>
</div>

<div class="tabs" style="margin-top:12px">
  <div id="tabList" class="tab" style="background:#fff;border:2px solid var(--primary);color:var(--primary)">Country List</div>
  <div id="tabMap" class="tab">Map (click)</div>
</div>

<div id="panelList">
<input id="search" class="search" placeholder="Search country"/>
<div class="list" id="countryList"></div>
</div>

<div id="panelMap" style="display:none">
<p class="map-info">Open the world map externally, paste country below.</p>
<div style="display:flex;gap:8px;align-items:center">
  <button id="openWorldMap" class="btn">Open World Map</button>
  <input id="mapCountry" class="search" placeholder="Paste country"/>
  <button id="mapChoose" class="btn">Choose</button>
</div>
</div>

<div style="display:flex;gap:10px;align-items:center;justify-content:space-between;margin-top:12px">
<div style="flex:1">
<div style="font-weight:700;margin-bottom:6px">Or choose a profile:</div>
<div class="profiles" id="profiles">
<div class="profile"><strong>Aya, 3</strong><div>Gaza — needs meal</div><button class="btn profileSelect">Feed Aya</button></div>
<div class="profile"><strong>Hassan, 6</strong><div>Somalia — needs food</div><button class="btn profileSelect">Feed Hassan</button></div>
</div>
</div>
<div style="width:220px">
<div style="font-weight:700;margin-bottom:6px">Or feed yourself</div>
<button id="feedSelf" class="btn">Feed Myself</button>
</div>
</div>
</div>
</div>

<script>
const countries=["Afghanistan","Albania","Algeria","Albania","Palestine","Gaza Strip","Iraq","Yemen","Somalia","Sudan","USA","India","Mexico","Brazil"]; 
const kindBtn=document.getElementById('kindBtn');
const counterText=document.getElementById('counterText');
const creditsEl=document.getElementById('credits');
const redeemModal=document.getElementById('redeemModal');
const openModal=document.getElementById('openModal');
const closeModal=document.getElementById('closeModal');
const countryListEl=document.getElementById('countryList');
const searchInput=document.getElementById('search');
const tabList=document.getElementById('tabList');
const tabMap=document.getElementById('tabMap');
const panelList=document.getElementById('panelList');
const panelMap=document.getElementById('panelMap');
const openWorldMap=document.getElementById('openWorldMap');
const mapCountryInput=document.getElementById('mapCountry');
const mapChoose=document.getElementById('mapChoose');
const demoGive=document.getElementById('demoGive');
const resetBtn=document.getElementById('reset');
const feedSelf=document.getElementById('feedSelf');
let state={acts:0,credits:0};
function render(){counterText.textContent=`${state.acts} / 3 acts`;creditsEl.textContent=state.credits;}
render();
kindBtn.addEventListener('click',()=>{
state.acts++;if(state.acts>=3){state.acts=0;state.credits++;setTimeout(openRedeemModal,300);}render();});
openModal.addEventListener('click',openRedeemModal);
closeModal.addEventListener('click',()=>{redeemModal.style.display='none';});
function openRedeemModal(){
if(state.credits<=0){alert('No credits yet. Tap more kindness acts.');return;}
populateCountries();redeemModal.style.display='flex';
}
function populateCountries(filter=''){
countryListEl.innerHTML='';countries.filter(c=>c.toLowerCase().includes(filter.toLowerCase())).forEach(c=>{
let el=document.createElement('div');el.className='country';el.textContent=c;el.addEventListener('click',()=>redeemToCountry(c));countryListEl.appendChild(el);});}
searchInput.addEventListener('input',()=>populateCountries(searchInput.value));
tabList.addEventListener('click',()=>{panelList.style.display='block';panelMap.style.display='none';tabList.style.background='#fff';tabList.style.border='2px solid var(--primary)';tabList.style.color='var(--primary)';tabMap.style.background='#fafafa';tabMap.style.border='1px solid #eee';tabMap.style.color='#333';});
tabMap.addEventListener('click',()=>{panelList.style.display='none';panelMap.style.display='block';tabMap.style.background='#fff';tabMap.style.border='2px solid var(--primary)';tabMap.style.color='var(--primary)';tabList.style.background='#fafafa';tabList.style.border='1px solid #eee';tabList.style.color='#333';});
openWorldMap.addEventListener('click',()=>{window.open('https://mapchart.net/world.html','_blank');});
mapChoose.addEventListener('click',()=>{const c=mapCountryInput.value.trim();if(!c){alert('Paste country name');return;}redeemToCountry(c);});
demoGive.addEventListener('click',()=>{state.credits++;render();alert('Demo credit added. Open Redeem to redeem it.');});
resetBtn.addEventListener('click',()=>{state.acts=0;state.credits=0;render();alert('Demo reset.');});
feedSelf.addEventListener('click',()=>{if(state.credits<=0)return alert('No credits');if(!confirm('Redeem 1 credit to feed yourself?'))return;redeemCredit({type:'self',target:'self'});});
document.addEventListener('click',(e)=>{if(e.target.classList.contains('profileSelect')){const p=e.target.closest('.profile').querySelector('strong').textContent;if(state.credits<=0)return alert('No credits');if(!confirm('Redeem 1 credit to feed '+p+'?'))return;redeemCredit({type:'person',target:p});}});
function redeemToCountry(c){if(state.credits<=0)return alert('No credits');if(!confirm('Redeem 1 credit to feed people in '+c+'?'))return;redeemCredit({type:'country',target:c});}
function redeemCredit(payload){state.credits--;render();redeemModal.style.display='none';alert('Food Credit redeemed: '+(payload.target||payload.type));}
</script>
</body>
</html>