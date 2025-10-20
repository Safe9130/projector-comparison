# projector-comparison

<!doctype html>
<html lang="zh-Hant">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Kyleの投影機規格比較</title>
<style>
:root{
  color-scheme:light dark;
  --bg:#0b0d10;--surface:#151a21;--surface-2:#1d2430;
  --text:#e8eef5;--muted:#9fb0c3;--accent:#2eaadc;--good:#44d17a;
  --border:rgba(255,255,255,.1);--shadow:0 6px 18px rgba(0,0,0,.28);--radius:16px;
}
:root[data-theme="light"]{--bg:#f5f7fb;--surface:#fff;--surface-2:#eff3f8;--text:#0e1621;--muted:#5c708a;--border:rgba(16,24,40,.1);--shadow:0 6px 18px rgba(16,24,40,.08);}
body{margin:0;background:var(--bg);color:var(--text);font:14px/1.45 system-ui,-apple-system,Segoe UI,Roboto,Noto Sans,Arial;}
.container{max-width:1200px;margin:20px auto;padding:0 16px;}
header{display:flex;align-items:center;justify-content:space-between;gap:12px;margin-bottom:16px;}
h1{margin:0;font-size:26px}
.sub{color:var(--muted)}
.grid{display:grid;grid-template-columns:280px 1fr;gap:16px;}
@media(max-width:900px){.grid{grid-template-columns:1fr}}
.card{background:var(--surface);border:1px solid var(--border);border-radius:var(--radius);box-shadow:var(--shadow);}
.card h2{margin:0;padding:14px 16px;border-bottom:1px solid var(--border);color:var(--muted);}
.card .content{padding:14px 16px;}
.row{display:flex;align-items:center;flex-wrap:wrap;gap:10px;}
select,input{background:var(--surface-2);color:var(--text);border:1px solid var(--border);border-radius:8px;padding:8px 10px;}
.btn{background:var(--accent);color:#04222c;border:none;padding:10px 14px;border-radius:12px;cursor:pointer;font-weight:600;box-shadow:0 4px 10px rgba(46,170,220,.35);}
.btn.secondary{background:var(--surface-2);color:var(--text);border:1px solid var(--border);}
.btn.ghost{background:transparent;color:var(--muted);border:1px dashed var(--border);}
table{width:100%;border-collapse:separate;border-spacing:0;font-size:14px;}
th,td{padding:10px;border-bottom:1px solid var(--border);vertical-align:middle;}
thead th{position:sticky;top:0;background:var(--surface-2);color:var(--muted);text-align:left;}
tbody tr:nth-child(even){background:color-mix(in oklab,var(--surface) 90%,var(--text) 2%);}
.is-best{outline:2px solid var(--good);outline-offset:-2px;border-radius:6px;}
.vol-ratio{display:flex;flex-wrap:wrap;gap:10px;margin:8px 0 12px;}
.chip{display:inline-flex;align-items:center;gap:6px;padding:6px 10px;border-radius:999px;background:var(--surface-2);border:1px solid var(--border);}
.toast{position:fixed;right:16px;bottom:16px;background:var(--surface);border:1px solid var(--border);padding:10px 14px;border-radius:10px;box-shadow:var(--shadow);}
</style>
</head>
<body>
<div class="container">
<header>
  <div style="display:flex;align-items:center;gap:8px;">
    <h1>Kyleの投影機規格比較</h1>
    <div class="sub">多語系、自定義尺寸、體積比、匯出/匯入</div>
  </div>
  <div class="row">
    <select id="langSel">
      <option value="zh">中文</option>
      <option value="en">English</option>
      <option value="ja">日本語</option>
    </select>
    <button id="themeToggle" class="btn secondary" type="button">🌙 / ☀️</button>
    <button id="exportHtmlBtn" class="btn secondary" type="button">下載完整HTML</button>
    <button id="exportBtn" class="btn secondary" type="button">匯出 JSON</button>
    <label class="btn secondary">匯入 JSON<input id="importInput" type="file" accept="application/json" style="display:none"></label>
  </div>
</header>

<div class="grid">
  <aside class="card">
    <h2>篩選與選擇</h2>
    <div class="content">
      <div class="row">
        <div><label>品牌</label><br><select id="brandSel"><option value="">全部</option></select></div>
        <div><label>技術</label><br><select id="techSel"><option value="">全部</option></select></div>
        <div><label>解析度</label><br><select id="resSel"><option value="">全部</option></select></div>
      </div><hr>
      <div class="row">
        <select id="modelSel" style="flex:1"></select>
        <button id="addColBtn" class="btn">加入</button>
      </div>
      <hr>
      <details>
        <summary style="color:var(--muted)">➕ 自定義尺寸（輸入 W×D×H 即可加入比較）</summary>
        <div class="row" style="margin-top:8px">
          <input id="cd_name" type="text" placeholder="名稱（可留白）" style="flex:1">
        </div>
        <div class="row" style="margin-top:8px">
          <input id="cd_w" type="number" min="1" step="1" placeholder="寬 W (mm)">
          <input id="cd_d" type="number" min="1" step="1" placeholder="深 D (mm)">
          <input id="cd_h" type="number" min="1" step="1" placeholder="高 H (mm)">
          <button id="cd_add" class="btn secondary" type="button">加入自定義</button>
        </div>
        <div class="sub" style="margin-top:6px;color:var(--muted)">其他規格會顯示「—」，但仍可比較體積與尺寸。</div>
      </details>
    </div>
  </aside>

  <main class="card">
    <h2>比較結果</h2>
    <div class="content">
      <div class="row" style="justify-content:space-between;">
        <div>已選欄位：<strong id="selCount">0</strong> / 4</div>
        <button id="clearCols" class="btn ghost" type="button">清除全部</button>
      </div>
      <div id="volumeCompare" class="vol-ratio" style="display:none"></div>
      <div class="table-wrap">
        <table>
          <thead><tr id="theadRow"><th>指標</th></tr></thead>
          <tbody id="tbody"></tbody>
        </table>
      </div>
    </div>
  </main>
</div>
</div>
<div id="toast" class="toast" style="display:none"></div>

<script>
const $=(s,e=document)=>e.querySelector(s);
const $$=(s,e=document)=>Array.from(e.querySelectorAll(s));

let DATA=[
{id:'CHR-4K22-HS',brand:'Christie',model:'4K22-HS',tech:'1DLP',resolution_class:'UHD',brightness_lm:22000,power_w:1700,weight_kg:49.5,noise_db:42,dims_mm:{w:690,d:770,h:210}},
{id:'PNC-RQ25K',brand:'Panasonic',model:'PT-RQ25K',tech:'3DLP',resolution_class:'4K+',brightness_lm:20000,power_w:2100,weight_kg:35,noise_db:46,dims_mm:{w:560,d:600,h:250}},
{id:'NEC-PX2201',brand:'Sharp/NEC',model:'PX2201UL',tech:'1DLP',resolution_class:'WUXGA',brightness_lm:20000,power_w:1600,weight_kg:28,noise_db:43,dims_mm:{w:600,d:500,h:210}}
];

const I18N={
 zh:{ui:{ind:'指標',added:'已加入',exists:'已在比較',need:'請先選擇',clear:'已清空',imported:'匯入完成'},metrics:{brand:"品牌",model:"型號",tech:"技術",resolution_class:"解析度",brightness_lm:"亮度(lm)",power_w:"功耗(W)",lm_per_w:"效率(lm/W)",dims:"尺寸(mm)",volume_L:"體積(L)",lm_per_L:"亮度體積比",weight_kg:"重量(kg)",lm_per_kg:"亮度重量比",noise_db:"噪音(dB)"}},
 en:{ui:{ind:'Metric',added:'Added',exists:'Already added',need:'Please select',clear:'Cleared',imported:'Import done'},metrics:{brand:"Brand",model:"Model",tech:"Technology",resolution_class:"Resolution",brightness_lm:"Brightness(lm)",power_w:"Power(W)",lm_per_w:"Efficiency(lm/W)",dims:"Dimensions(mm)",volume_L:"Volume(L)",lm_per_L:"Lumens/L",weight_kg:"Weight(kg)",lm_per_kg:"Lumens/kg",noise_db:"Noise(dB)"}},
 ja:{ui:{ind:'指標',added:'追加済み',exists:'すでに追加',need:'選択してください',clear:'クリアしました',imported:'インポート完了'},metrics:{brand:"ブランド",model:"型番",tech:"方式",resolution_class:"解像度",brightness_lm:"明るさ(lm)",power_w:"消費電力(W)",lm_per_w:"効率(lm/W)",dims:"サイズ(mm)",volume_L:"体積(L)",lm_per_L:"輝度体積比",weight_kg:"重量(kg)",lm_per_kg:"輝度重量比",noise_db:"騒音(dB)"}}
};

function initTheme(){
  const root = document.documentElement;
  if (!root.dataset.theme) {
    root.dataset.theme = window.matchMedia('(prefers-color-scheme: dark)').matches
      ? 'dark' : 'light';
  }
}

function derive(p){
 const v=(p.dims_mm.w*p.dims_mm.d*p.dims_mm.h)/1e6;
 return {...p,volume_L:v,lm_per_w:p.power_w?p.brightness_lm/p.power_w:NaN,lm_per_L:v?p.brightness_lm/v:NaN,lm_per_kg:p.weight_kg?p.brightness_lm/p.weight_kg:NaN};
}

const show=v=>(v===null||v===undefined|| (typeof v==='number' && !Number.isFinite(v)))?'—':v;
const state={lang:'zh',selected:[]};
function toast(t){const x=$('#toast');x.textContent=t;x.style.display='block';setTimeout(()=>x.style.display='none',1100);}
function uniq(a){return [...new Set(a)].sort();}

function setCascade(){
 const b=$('#brandSel').value||'',t=$('#techSel').value||'',r=$('#resSel').value||'';
 const techs=uniq(DATA.filter(d=>!b||d.brand===b).map(d=>d.tech));
 $('#techSel').innerHTML='<option value="">全部</option>'+techs.map(x=>`<option${x===t?' selected':''}>${x}</option>`).join('');
 const res=uniq(DATA.filter(d=>(!b||d.brand===b)&&(!t||d.tech===t)).map(d=>d.resolution_class));
 $('#resSel').innerHTML='<option value="">全部</option>'+res.map(x=>`<option${x===r?' selected':''}>${x}</option>`).join('');
 const list=DATA.filter(d=>(!b||d.brand===b)&&(!t||d.tech===t)&&(!r||d.resolution_class===r));
 $('#modelSel').innerHTML=list.map(d=>`<option value="${d.id}">${d.model}</option>`).join('');
 if(list.length>0)$('#modelSel').selectedIndex=0;
}

function populate(){
 // ⬇️ 這裡把 Custom 從品牌清單中移除
 const brands = uniq(DATA.filter(x=>x.brand!=='Custom').map(x=>x.brand));
 $('#brandSel').innerHTML = '<option value="">全部</option>' + brands.map(x=>`<option>${x}</option>`).join('');
 setCascade();
}

function render(){
 const dict=(I18N[state.lang]||I18N.zh).metrics;
 const ui=(I18N[state.lang]||I18N.zh).ui;
 const keys=['brand','model','tech','resolution_class','brightness_lm','power_w','lm_per_w','dims','volume_L','lm_per_L','weight_kg','lm_per_kg','noise_db'];

 // Header
 $('#theadRow').innerHTML=`<th>${ui.ind}</th>` + state.selected.map(id=>{
   const p=derive(DATA.find(x=>x.id===id));
   return `<th>
     <div style="display:flex;align-items:center;justify-content:space-between;gap:8px;">
       <strong>${p.brand} ${p.model}</strong>
       <button class="btn ghost" data-remove="${p.id}" title="移除" aria-label="移除">✕</button>
     </div>
   </th>`;
 }).join('');

 // Body
 const tb=$('#tbody');tb.innerHTML='';
 keys.forEach(k=>{
   const tr=document.createElement('tr');
   tr.innerHTML = `<td>${dict[k]}</td>` + state.selected.map(id=>{
     const p=derive(DATA.find(x=>x.id===id));
     let v, raw = NaN;
     switch(k){
       case 'brightness_lm': v=show(p.brightness_lm); raw=p.brightness_lm; break;
       case 'power_w':      v=show(p.power_w);       raw=p.power_w;      break;
       case 'lm_per_w':     v=show(p.lm_per_w?.toFixed(2)); raw=p.lm_per_w; break;
       case 'dims':         v=show(`${p.dims_mm.w}×${p.dims_mm.d}×${p.dims_mm.h}`); break;
       case 'volume_L':     v=show(p.volume_L?.toFixed(2)); raw=p.volume_L; break;
       case 'lm_per_L':     v=show(p.lm_per_L?.toFixed(0)); raw=p.lm_per_L; break;
       case 'weight_kg':    v=show(p.weight_kg);  raw=p.weight_kg; break;
       case 'lm_per_kg':    v=show(p.lm_per_kg?.toFixed(0)); raw=p.lm_per_kg; break;
       case 'noise_db':     v=show(p.noise_db);   raw=p.noise_db;  break;
       default:             v=show(p[k]);
     }
     return `<td data-metric="${k}" data-val="${Number(raw)}">${v}</td>`;
   }).join('');
   tb.appendChild(tr);
 });

 // Best highlight
 $$('#tbody tr').forEach(tr=>{
   const cells=$$('td[data-val]',tr);
   if(!cells.length) return;
   const nums=cells.map(c=>Number(c.dataset.val)).filter(n=>Number.isFinite(n));
   if(!nums.length) return;
   const metric=cells[0].dataset.metric;
   const higherBetter=['brightness_lm','lm_per_w','lm_per_L','lm_per_kg'].includes(metric);
   const lowerBetter=['power_w','weight_kg','noise_db','volume_L'].includes(metric);
   let targetVal = null;
   if(higherBetter) targetVal=Math.max(...nums);
   else if(lowerBetter) targetVal=Math.min(...nums);
   else return;
   cells.forEach(c=>{
     const val=Number(c.dataset.val);
     c.classList.toggle('is-best', val===targetVal);
   });
 });

 // Volume ratio
 if(state.selected.length>=2){
   const a=derive(DATA.find(x=>x.id===state.selected[0]));
   const html=state.selected.slice(1).map(id=>{
     const p=derive(DATA.find(x=>x.id===id));
     const r=a.volume_L?(p.volume_L/a.volume_L).toFixed(2):'—';
     return `<span class="chip"><b>${p.brand} ${p.model}</b> 相對於 <b>${a.brand} ${a.model}</b>：${r}×</span>`;
   }).join('');
   $('#volumeCompare').style.display='flex';
   $('#volumeCompare').innerHTML=html;
 }else{
   $('#volumeCompare').style.display='none';
 }

 // remove events
 $$('[data-remove]').forEach(btn=>{
   btn.onclick=()=>{ const id=btn.getAttribute('data-remove'); state.selected=state.selected.filter(x=>x!==id); render(); };
 });

 $('#selCount').textContent=state.selected.length;
}

function addSelected(id){
 const ui=(I18N[state.lang]||I18N.zh).ui;
 if(!id) return toast(ui.need);
 if(state.selected.includes(id)) return toast(ui.exists);
 if(state.selected.length>=4) return alert('最多 4 欄');
 state.selected.push(id);
 render();
 toast(ui.added);
}

function addCustomDims(){
 const name=($('#cd_name')?.value||'').trim();
 const w=Number($('#cd_w')?.value), d=Number($('#cd_d')?.value), h=Number($('#cd_h')?.value);
 if(!Number.isFinite(w)||!Number.isFinite(d)||!Number.isFinite(h)||w<=0||d<=0||h<=0){
   alert('請輸入正確的尺寸（W/D/H，單位 mm）'); return;
 }
 const id=`CUSTOM-${Date.now()}`;
 const modelName=name||`${w}×${d}×${h}`;
 const custom = {
   id, brand:'Custom', model:modelName,
   tech:'', resolution_class:'',
   brightness_lm:NaN, power_w:NaN, weight_kg:NaN, noise_db:NaN,
   dims_mm:{w,d,h}
 };
 DATA.push(custom);
 populate(); $('#modelSel').value=id; addSelected(id);
 ['cd_name','cd_w','cd_d','cd_h'].forEach(i=>{ const el=$('#'+i); if(el) el.value=''; });
}

function importJSON(file){
 const reader=new FileReader();
 reader.onload=e=>{
   try{
     const arr=JSON.parse(e.target.result);
     if(!Array.isArray(arr)) throw new Error('格式錯誤');
     const normalized=arr.map(x=>{
       const dims = x.dims_mm && Number.isFinite(x.dims_mm.w) ? x.dims_mm :
                   { w:Number(x.w_mm ?? x.w), d:Number(x.d_mm ?? x.d), h:Number(x.h_mm ?? x.h) };
       return {
         id: x.id || `${x.brand||'X'}-${x.model||'Y'}-${Math.random().toString(36).slice(2,6)}`,
         brand: x.brand ?? '—',
         model: x.model ?? '—',
         tech: x.tech ?? '',
         resolution_class: x.resolution_class ?? '',
         brightness_lm: Number(x.brightness_lm),
         power_w: Number(x.power_w),
         weight_kg: Number(x.weight_kg),
         noise_db: Number(x.noise_db),
         dims_mm: dims
       };
     }).filter(x=>x.dims_mm && Number.isFinite(x.dims_mm.w) && Number.isFinite(x.dims_mm.d) && Number.isFinite(x.dims_mm.h));
     if(!normalized.length) throw new Error('沒有有效資料列');
     const map=new Map(DATA.map(d=>[d.id,d]));
     normalized.forEach(n=>map.set(n.id,n));
     DATA=Array.from(map.values());
     populate(); render();
     toast((I18N[state.lang]||I18N.zh).ui.imported);
   }catch(err){ alert('匯入失敗：'+err.message); }
 };
 reader.readAsText(file);
}

function exportJSON(){
 const blob=new Blob([JSON.stringify(DATA,null,2)],{type:'application/json'});
 const a=document.createElement('a'); a.href=URL.createObjectURL(blob); a.download='projectors.json'; a.click();
}

function exportHTML(){
  const serialized = JSON.stringify(DATA, null, 2);
  let html = document.documentElement.outerHTML;

  // ✅ 關鍵：把 <html> 上殘留的 data-theme="light|dark" 拿掉，避免新檔被鎖死在某個主題
  html = html.replace(/\sdata-theme=(["'])(?:light|dark)\1/gi, '');

  // 仍然把目前記憶體中的 DATA 直接嵌入
  html = html.replace(/(let|var|const)\s+DATA\s*=\s*\[[\s\S]*?\];/, `let DATA = ${serialized};`);

  const blob = new Blob([html], { type: 'text/html' });
  const a = document.createElement('a');
  a.href = URL.createObjectURL(blob);
  a.download = 'projector_comparator.html';
  a.click();
}

function init(){
  initTheme(); populate(); render();
 // Events
 $('#addColBtn').onclick=()=>addSelected($('#modelSel').value);
 $('#clearCols').onclick=()=>{ state.selected=[]; render(); toast((I18N[state.lang]||I18N.zh).ui.clear); };
 $('#cd_add').onclick=addCustomDims;
 $('#importInput').onchange=e=>{ const f=e.target.files?.[0]; if(f) importJSON(f); e.target.value=''; };
 $('#exportBtn').onclick=exportJSON;
 $('#exportHtmlBtn').onclick=exportHTML;
 $('#themeToggle').onclick=()=>{ const c=document.documentElement.dataset.theme; document.documentElement.dataset.theme = (c==='light')?'dark':'light'; };
 ['brandSel','techSel','resSel'].forEach(id=> $('#'+id).onchange=setCascade );
 $('#langSel').onchange=e=>{ state.lang=e.target.value; render(); };
}

if(document.readyState==='loading'){ document.addEventListener('DOMContentLoaded', init); }
else { init(); }
</script>
</body>
</html>
