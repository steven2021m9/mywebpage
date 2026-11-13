<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>My Life Dashboard</title>
<style>
/* ========== 1. 全局变量：改这里就能换主题色 ========== */
:root{--blue:#2563eb;--blue-d:#1d4ed8;--blue-l:#eff6ff;--line:#e5e7eb;--text:#1f2937;--mute:#6b7280;--red:#dc2626;--green:#16a34a}
*{box-sizing:border-box;margin:0}
body{font-family:"Segoe UI","Microsoft YaHei",sans-serif;background:#fff;color:var(--text);line-height:1.5}

/* ========== 2. 整体布局：左侧导航 + 右侧主区域 ========== */
.sidebar{position:fixed;inset:0 auto 0 0;width:232px;background:#fff;border-right:1px solid var(--line);padding:20px 12px;z-index:30;transition:transform .25s}
.logo{font-size:18px;font-weight:700;color:var(--blue);padding:4px 12px 20px}
.nav-btn{display:block;width:100%;text-align:left;padding:11px 14px;margin-bottom:4px;border:0;border-radius:8px;background:none;font-size:15px;color:var(--text);cursor:pointer}
.nav-btn:hover{background:var(--blue-l)}
.nav-btn.active{background:var(--blue);color:#fff}
.main{margin-left:232px}
.topbar{position:sticky;top:0;z-index:20;display:flex;align-items:center;gap:12px;padding:14px 28px;background:#fff;border-bottom:1px solid var(--line)}
.topbar h1{font-size:20px;flex:1}
.date{color:var(--mute);font-size:14px}
.menu-btn{display:none;border:1px solid var(--line);background:#fff;border-radius:8px;padding:6px 12px;font-size:18px;cursor:pointer}
.content{padding:28px;background:#f8fafc;min-height:calc(100vh - 62px)}
.page{display:none}.page.show{display:block}
.page>h2{font-size:22px;margin-bottom:16px}

/* ========== 3. 卡片与表单 ========== */
.grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(290px,1fr));gap:18px}
.card{background:#fff;border:1px solid var(--line);border-radius:12px;padding:20px}
.card h3{font-size:16px;margin-bottom:12px}
.stat{font-size:30px;font-weight:700;color:var(--blue)}
.mute{color:var(--mute);font-size:13px}
.row{display:flex;gap:8px;flex-wrap:wrap;margin-top:10px}
input,select{padding:9px 10px;border:1px solid var(--line);border-radius:8px;font-size:14px;flex:1;min-width:110px}
input:focus{outline:2px solid var(--blue);border-color:transparent}
.btn{padding:9px 16px;border:0;border-radius:8px;background:var(--blue);color:#fff;font-size:14px;cursor:pointer}
.btn:hover{background:var(--blue-d)}
.btn.ghost{background:var(--blue-l);color:var(--blue)}
.btn.ghost:hover{background:#dbeafe}
.btn.del{background:#fee2e2;color:var(--red);padding:4px 10px}
.btn.ok{background:var(--green)}
.list{list-style:none;padding:0;margin-top:12px}
.list li{display:flex;justify-content:space-between;align-items:center;gap:8px;padding:10px 0;border-top:1px solid var(--line);font-size:14px}
.empty{color:var(--mute);font-size:14px;padding:10px 0}

/* 单词卡 */
.flash{display:flex;align-items:center;justify-content:center;flex-direction:column;min-height:150px;border-radius:12px;background:var(--blue-l);color:var(--blue);font-size:30px;font-weight:700;cursor:pointer;text-align:center;padding:12px}
.progress{height:8px;background:var(--line);border-radius:4px;overflow:hidden;margin:12px 0 4px}
.progress i{display:block;height:100%;background:var(--blue);transition:width .3s}
/* 柱状图 */
.bars{display:flex;align-items:flex-end;gap:10px;height:150px;margin-top:14px;overflow-x:auto}
.bar{flex:1;min-width:38px;background:var(--blue);border-radius:6px 6px 0 0;position:relative;transition:height .3s}
.bar b{position:absolute;top:-20px;left:0;right:0;text-align:center;font-size:12px;color:var(--text)}
.bar span{position:absolute;bottom:-20px;left:0;right:0;text-align:center;font-size:11px;color:var(--mute)}
.bars-wrap{padding-bottom:22px}

/* ========== 4. 响应式：平板和手机 ========== */
@media(max-width:900px){
 .sidebar{transform:translateX(-100%)}
 .sidebar.open{transform:none;box-shadow:0 0 40px rgba(0,0,0,.2)}
 .main{margin-left:0}
 .menu-btn{display:block}
 .topbar{padding:12px 16px}.content{padding:16px}
 .date{display:none}
}
</style>
</head>
<body>

<!-- ========== HTML 结构 ========== -->
<aside class="sidebar" id="sidebar">
  <div class="logo">📊 My Life</div>
  <button class="nav-btn active" data-page="home">🏠 总览</button>
  <button class="nav-btn" data-page="spanish">🇪🇸 西班牙语学习</button>
  <button class="nav-btn" data-page="car">🚗 汽车维修管理</button>
  <button class="nav-btn" data-page="energy">⚡ 家庭能源管理</button>
  <button class="nav-btn" data-page="power">🔋 户外电源计算器</button>
</aside>

<div class="main">
  <header class="topbar">
    <button class="menu-btn" id="menuBtn" aria-label="打开菜单">☰</button>
    <h1>My Life Dashboard</h1>
    <span class="date" id="today"></span>
  </header>

  <main class="content">

    <!-- 总览 -->
    <section class="page show" id="home">
      <h2>总览</h2>
      <div class="grid">
        <div class="card"><h3>🇪🇸 西班牙语</h3><div class="stat" id="hSpanish">0</div><p class="mute" id="hSpanishSub"></p><div class="row"><button class="btn" onclick="go('spanish')">去学习</button></div></div>
        <div class="card"><h3>🚗 汽车维修</h3><div class="stat" id="hCar">0 €</div><p class="mute" id="hCarSub"></p><div class="row"><button class="btn" onclick="go('car')">查看记录</button></div></div>
        <div class="card"><h3>⚡ 家庭能源</h3><div class="stat" id="hEnergy">0 €</div><p class="mute" id="hEnergySub"></p><div class="row"><button class="btn" onclick="go('energy')">查看用电</button></div></div>
        <div class="card"><h3>🔋 户外电源</h3><div class="stat" id="hPower">0 h</div><p class="mute" id="hPowerSub"></p><div class="row"><button class="btn" onclick="go('power')">去计算</button></div></div>
      </div>
    </section>

    <!-- 西班牙语 -->
    <section class="page" id="spanish">
      <h2>西班牙语学习</h2>
      <div class="grid">
        <div class="card">
          <h3>单词卡片（点击卡片翻面）</h3>
          <div class="flash" id="flash" onclick="flip()"></div>
          <div class="progress"><i id="prog"></i></div>
          <p class="mute" id="progText"></p>
          <div class="row">
            <button class="btn ok" onclick="mark(true)">✔ 记住了</button>
            <button class="btn ghost" onclick="mark(false)">再复习</button>
            <button class="btn ghost" onclick="flip()">翻面</button>
          </div>
        </div>
        <div class="card">
          <h3>添加新单词</h3>
          <div class="row"><input id="wEs" placeholder="西班牙语，如 gracias"><input id="wZh" placeholder="中文，如 谢谢"></div>
          <div class="row"><button class="btn" onclick="addWord()">添加单词</button><button class="btn ghost" onclick="resetWords()">重置学习进度</button></div>
          <p class="mute" id="wCount" style="margin-top:12px"></p>
        </div>
      </div>
    </section>

    <!-- 汽车维修 -->
    <section class="page" id="car">
      <h2>汽车维修管理</h2>
      <div class="grid">
        <div class="card">
          <h3>添加维修 / 保养记录</h3>
          <div class="row"><input type="date" id="cDate"><input id="cItem" placeholder="项目，如 换机油"></div>
          <div class="row"><input type="number" id="cCost" placeholder="费用 (€)" min="0"><input type="number" id="cKm" placeholder="里程 (km)" min="0"></div>
          <div class="row"><button class="btn" onclick="addCar()">保存记录</button><button class="btn ghost" onclick="clearCar()">清空全部</button></div>
        </div>
        <div class="card">
          <h3>统计</h3>
          <div class="stat" id="cTotal">0 €</div>
          <p class="mute" id="cStat"></p>
        </div>
      </div>
      <div class="card" style="margin-top:18px"><h3>历史记录</h3><ul class="list" id="cList"></ul></div>
    </section>

    <!-- 家庭能源 -->
    <section class="page" id="energy">
      <h2>家庭能源管理</h2>
      <div class="grid">
        <div class="card">
          <h3>记录每月用电</h3>
          <div class="row"><input type="month" id="eMonth"><input type="number" id="eKwh" placeholder="用电量 (kWh)" min="0"></div>
          <div class="row"><input type="number" id="ePrice" placeholder="电价 (€/kWh)" step="0.01" min="0" value="0.18"></div>
          <div class="row"><button class="btn" onclick="addEnergy()">保存本月</button><button class="btn ghost" onclick="clearEnergy()">清空全部</button></div>
        </div>
        <div class="card">
          <h3>统计</h3>
          <div class="stat" id="eTotal">0 €</div>
          <p class="mute" id="eStat"></p>
        </div>
      </div>
      <div class="card" style="margin-top:18px"><h3>用电量趋势 (kWh)</h3><div class="bars-wrap"><div class="bars" id="eBars"></div></div><ul class="list" id="eList"></ul></div>
    </section>

    <!-- 户外电源 -->
    <section class="page" id="power">
      <h2>户外电源计算器</h2>
      <div class="grid">
        <div class="card">
          <h3>电源参数</h3>
          <div class="row"><input type="number" id="pBat" placeholder="电池容量 (Wh)" min="1" oninput="calcPower()"><input type="number" id="pEff" placeholder="逆变效率 (%)" min="1" max="100" oninput="calcPower()"></div>
          <h3 style="margin-top:18px">添加设备</h3>
          <div class="row"><input id="dName" placeholder="设备名称"><input type="number" id="dW" placeholder="功率 (W)" min="1"><input type="number" id="dH" placeholder="每天使用 (小时)" min="0.1" step="0.1"></div>
          <div class="row"><button class="btn" onclick="addDevice()">添加设备</button><button class="btn ghost" onclick="clearDevices()">清空设备</button></div>
          <p class="mute" style="margin-top:14px">快速添加常用设备：</p>
          <div class="row">
            <button class="btn ghost" onclick="preset('手机充电',15,2)">手机</button>
            <button class="btn ghost" onclick="preset('笔记本电脑',60,5)">笔记本</button>
            <button class="btn ghost" onclick="preset('LED 灯',10,6)">LED 灯</button>
            <button class="btn ghost" onclick="preset('车载冰箱',45,12)">冰箱</button>
          </div>
        </div>
        <div class="card">
          <h3>计算结果</h3>
          <div class="stat" id="pRun">0 h</div>
          <p class="mute" id="pRes"></p>
          <ul class="list" id="pList"></ul>
        </div>
      </div>
    </section>

  </main>
</div>

<script>
/* ========== 5. 工具函数 ========== */
// 保存与读取数据：存在浏览器 localStorage 中，刷新页面数据不会丢
const store = {
  get(k, d){ try{ const v = JSON.parse(localStorage.getItem('mld_'+k)); return v ?? d; }catch(e){ return d; } },
  set(k, v){ try{ localStorage.setItem('mld_'+k, JSON.stringify(v)); }catch(e){} }
};
const $ = id => document.getElementById(id);
const esc = s => String(s).replace(/[&<>"']/g, c => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c]));
const uid = () => Date.now() + Math.random();
const eur = n => n.toFixed(2) + ' €';

/* ========== 6. 导航与日期 ========== */
function go(page){
  document.querySelectorAll('.page').forEach(p => p.classList.toggle('show', p.id === page));
  document.querySelectorAll('.nav-btn').forEach(b => b.classList.toggle('active', b.dataset.page === page));
  $('sidebar').classList.remove('open');
  renderAll(); window.scrollTo(0,0);
}
document.querySelectorAll('.nav-btn').forEach(b => b.onclick = () => go(b.dataset.page));
$('menuBtn').onclick = () => $('sidebar').classList.toggle('open');
function showDate(){ $('today').textContent = new Date().toLocaleDateString('zh-CN',{year:'numeric',month:'long',day:'numeric',weekday:'long'}); }
showDate(); setInterval(showDate, 60000);

/* ========== 7. 西班牙语学习 ========== */
const defaultWords = [['hola','你好'],['gracias','谢谢'],['por favor','请'],['adiós','再见'],['buenos días','早上好'],['agua','水'],['casa','家'],['comer','吃'],['¿cuánto cuesta?','多少钱？'],['no entiendo','我不明白'],['la cuenta','账单'],['coche','汽车']];
let words = store.get('words', defaultWords);
let known = store.get('known', []);
let showBack = false, current = null;

function nextWord(){
  const left = words.filter(w => !known.includes(w[0]));
  current = left.length ? left[Math.floor(Math.random()*left.length)] : null;
  showBack = false;
}
function renderSpanish(){
  if(!current || known.includes(current[0]) || !words.includes(current)) nextWord();
  $('flash').innerHTML = current ? esc(showBack ? current[1] : current[0]) : '🎉 全部掌握了！<small class="mute">可以添加新单词或重置进度</small>';
  const pct = words.length ? Math.round(known.length/words.length*100) : 0;
  $('prog').style.width = pct + '%';
  $('progText').textContent = `已掌握 ${known.length} / ${words.length}（${pct}%）`;
  $('wCount').textContent = `当前词库共 ${words.length} 个单词`;
}
function flip(){ if(!current) return; showBack = !showBack; renderSpanish(); }
function mark(ok){
  if(!current) return;
  if(ok && !known.includes(current[0])) known.push(current[0]);
  store.set('known', known); nextWord(); renderSpanish();
}
function addWord(){
  const es = $('wEs').value.trim(), zh = $('wZh').value.trim();
  if(!es || !zh) return alert('请同时填写西班牙语和中文');
  if(words.some(w => w[0].toLowerCase() === es.toLowerCase())) return alert('这个单词已经存在');
  words.push([es, zh]); store.set('words', words);
  $('wEs').value = $('wZh').value = ''; renderSpanish();
}
function resetWords(){
  if(!confirm('确定重置学习进度吗？（单词不会被删除）')) return;
  known = []; store.set('known', known); nextWord(); renderSpanish();
}

/* ========== 8. 汽车维修 ========== */
let cars = store.get('cars', []);
$('cDate').value = new Date().toISOString().slice(0,10);
function addCar(){
  const item = $('cItem').value.trim(), date = $('cDate').value;
  const cost = parseFloat($('cCost').value) || 0, km = parseInt($('cKm').value) || 0;
  if(!item || !date) return alert('请填写日期和维修项目');
  cars.push({id:uid(), date, item, cost, km});
  cars.sort((a,b) => b.date.localeCompare(a.date));
  store.set('cars', cars);
  $('cItem').value = $('cCost').value = $('cKm').value = ''; renderCar();
}
function delCar(id){ cars = cars.filter(c => c.id !== id); store.set('cars', cars); renderCar(); }
function clearCar(){ if(cars.length && confirm('确定清空所有维修记录吗？')){ cars = []; store.set('cars', cars); renderCar(); } }
function renderCar(){
  const total = cars.reduce((s,c) => s + c.cost, 0);
  const maxKm = Math.max(0, ...cars.map(c => c.km));
  $('cTotal').textContent = eur(total);
  $('cStat').textContent = cars.length ? `共 ${cars.length} 条记录，最近里程 ${maxKm} km` : '还没有记录，先添加一条吧';
  $('cList').innerHTML = cars.length ? cars.map(c =>
    `<li><span><b>${esc(c.item)}</b><br><span class="mute">${c.date}${c.km ? ' · ' + c.km + ' km' : ''}</span></span>
     <span>${eur(c.cost)} <button class="btn del" onclick="delCar(${c.id})">删除</button></span></li>`).join('')
    : '<li class="empty">暂无记录</li>';
}

/* ========== 9. 家庭能源 ========== */
let energy = store.get('energy', []);
$('eMonth').value = new Date().toISOString().slice(0,7);
function addEnergy(){
  const month = $('eMonth').value, kwh = parseFloat($('eKwh').value), price = parseFloat($('ePrice').value);
  if(!month || !(kwh >= 0) || isNaN(kwh) || !(price >= 0)) return alert('请填写月份、用电量和电价');
  energy = energy.filter(e => e.month !== month);   // 同月份覆盖
  energy.push({month, kwh, price});
  energy.sort((a,b) => a.month.localeCompare(b.month));
  store.set('energy', energy); $('eKwh').value = ''; renderEnergy();
}
function delEnergy(m){ energy = energy.filter(e => e.month !== m); store.set('energy', energy); renderEnergy(); }
function clearEnergy(){ if(energy.length && confirm('确定清空所有用电记录吗？')){ energy = []; store.set('energy', energy); renderEnergy(); } }
function renderEnergy(){
  const cost = energy.reduce((s,e) => s + e.kwh*e.price, 0);
  const kwh = energy.reduce((s,e) => s + e.kwh, 0);
  $('eTotal').textContent = eur(cost);
  $('eStat').textContent = energy.length ? `累计 ${kwh.toFixed(0)} kWh，月均 ${(kwh/energy.length).toFixed(0)} kWh` : '还没有记录，先添加一个月吧';
  const max = Math.max(1, ...energy.map(e => e.kwh));
  $('eBars').innerHTML = energy.map(e => `<div class="bar" style="height:${Math.max(4, e.kwh/max*100)}%"><b>${e.kwh}</b><span>${e.month.slice(2)}</span></div>`).join('');
  $('eList').innerHTML = energy.length ? [...energy].reverse().map(e =>
    `<li><span><b>${e.month}</b><br><span class="mute">${e.kwh} kWh × ${e.price} €</span></span>
     <span>${eur(e.kwh*e.price)} <button class="btn del" onclick="delEnergy('${e.month}')">删除</button></span></li>`).join('')
    : '<li class="empty">暂无记录</li>';
}

/* ========== 10. 户外电源计算器 ========== */
let devices = store.get('devices', []);
let pBat = store.get('bat', 1000), pEff = store.get('eff', 85);
$('pBat').value = pBat; $('pEff').value = pEff;
function addDevice(){
  const name = $('dName').value.trim(), w = parseFloat($('dW').value), h = parseFloat($('dH').value);
  if(!name || !(w > 0) || !(h > 0)) return alert('请填写设备名称、功率和使用时间');
  preset(name, w, h); $('dName').value = $('dW').value = $('dH').value = '';
}
function preset(name, w, h){ devices.push({id:uid(), name, w, h}); store.set('devices', devices); calcPower(); }
function delDevice(id){ devices = devices.filter(d => d.id !== id); store.set('devices', devices); calcPower(); }
function clearDevices(){ if(devices.length && confirm('确定清空所有设备吗？')){ devices = []; store.set('devices', devices); calcPower(); } }
function calcPower(){
  pBat = parseFloat($('pBat').value) || 0;
  pEff = Math.min(100, parseFloat($('pEff').value) || 0);
  store.set('bat', pBat); store.set('eff', pEff);
  const dailyWh = devices.reduce((s,d) => s + d.w*d.h, 0);          // 每天需要的电量 Wh
  const usable = pBat * pEff / 100;                                   // 实际可用电量 Wh
  const days = dailyWh ? usable/dailyWh : 0;
  $('pRun').textContent = dailyWh ? days.toFixed(1) + ' 天' : '— 天';
  $('pRes').textContent = dailyWh
    ? `每天耗电 ${dailyWh.toFixed(0)} Wh；可用电量 ${usable.toFixed(0)} Wh（约 ${(days*24).toFixed(0)} 小时）`
    : '添加设备后显示可用天数';
  $('pList').innerHTML = devices.length ? devices.map(d =>
    `<li><span><b>${esc(d.name)}</b><br><span class="mute">${d.w} W × ${d.h} h = ${(d.w*d.h).toFixed(0)} Wh/天</span></span>
     <button class="btn del" onclick="delDevice(${d.id})">删除</button></li>`).join('')
    : '<li class="empty">还没有设备</li>';
}

/* ========== 11. 总览页 + 启动 ========== */
function renderHome(){
  $('hSpanish').textContent = known.length + ' 词';
  $('hSpanishSub').textContent = `词库共 ${words.length} 个单词`;
  $('hCar').textContent = eur(cars.reduce((s,c) => s + c.cost, 0));
  $('hCarSub').textContent = `${cars.length} 条维修记录`;
  const last = energy[energy.length-1];
  $('hEnergy').textContent = last ? eur(last.kwh*last.price) : '0.00 €';
  $('hEnergySub').textContent = last ? `${last.month} 电费（${last.kwh} kWh）` : '还没有用电记录';
  const dailyWh = devices.reduce((s,d) => s + d.w*d.h, 0);
  $('hPower').textContent = dailyWh ? (pBat*pEff/100/dailyWh).toFixed(1) + ' 天' : '— 天';
  $('hPowerSub').textContent = `${devices.length} 个设备，电池 ${pBat} Wh`;
}
function renderAll(){ renderHome(); renderSpanish(); renderCar(); renderEnergy(); calcPower(); }
renderAll();
</script>
</body>
</html>
