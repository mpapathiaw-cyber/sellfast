<!doctype html>
<html lang="fr">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>SellFast Yoff - Annonces</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#f6f7fb; --card:#fff; --accent:#16a34a; --muted:#6b7280;
  }
  *{box-sizing:border-box;font-family:Inter,system-ui,-apple-system,Segoe UI,Roboto,"Helvetica Neue",Arial}
  body{margin:0;background:var(--bg);color:#111}
  header{background:linear-gradient(90deg,#fff 0,#fff 60%, rgba(255,255,255,0.9));padding:18px 20px;display:flex;align-items:center;gap:16px;border-bottom:1px solid #e6e9ee}
  .logo{font-weight:700;color:var(--accent);font-size:18px}
  .searchbar{flex:1;display:flex;gap:8px;align-items:center}
  input[type="search"]{flex:1;padding:10px 12px;border-radius:8px;border:1px solid #e1e4ea;background:#fff}
  .btn{background:var(--accent);color:#fff;padding:10px 14px;border-radius:8px;border:0;cursor:pointer}
  .container{max-width:1100px;margin:20px auto;padding:0 16px}
  .filters{display:flex;gap:8px;flex-wrap:wrap;margin-bottom:14px}
  .select, .input{padding:8px;border-radius:8px;border:1px solid #e1e4ea;background:#fff}
  .grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(260px,1fr));gap:14px}
  .card{background:var(--card);border-radius:10px;overflow:hidden;box-shadow:0 6px 18px rgba(16,24,40,0.06)}
  .card img{width:100%;height:160px;object-fit:cover;display:block}
  .card .body{padding:12px}
  .title{font-weight:600;margin:0 0 6px}
  .price{color:var(--accent);font-weight:700}
  .meta{font-size:13px;color:var(--muted);margin-top:6px}
  .footer{display:flex;justify-content:space-between;align-items:center;padding:14px;border-top:1px solid #f1f3f6;background:#fafafa}
  .post-btn{background:#0ea5a4;padding:10px 12px;border-radius:8px;color:#fff;border:0;cursor:pointer}
  /* modal */
  .modal-back{position:fixed;inset:0;background:rgba(2,6,23,0.5);display:none;align-items:center;justify-content:center;padding:20px}
  .modal{background:#fff;border-radius:10px;max-width:900px;width:100%;overflow:auto;max-height:90vh}
  .modal .head{display:flex;justify-content:space-between;align-items:center;padding:12px 16px;border-bottom:1px solid #eee}
  .close{background:transparent;border:0;font-size:20px;cursor:pointer}
  .modal .content{padding:16px;display:grid;grid-template-columns:1fr 320px;gap:16px}
  .list{list-style:none;padding:0;margin:0;font-size:14px}
  @media (max-width:880px){.modal .content{grid-template-columns:1fr}}
</style>
</head>
<body>
<header>
  <div class="logo">SellFast.Yoff</div>
  <div class="searchbar">
    <input id="q" type="search" placeholder="Rechercher: téléphone, appartement, voiture..." />
    <button class="btn" onclick="doSearch()">Rechercher</button>
  </div>
  <button class="btn" onclick="openPost()">Déposer une annonce</button>
</header>

<div class="container">
  <div class="filters">
    <select id="cat" class="select">
      <option value="">Toutes catégories</option>
      <option>Propriété</option><option>Véhicules</option><option>Téléphones</option><option>Électronique</option>
    </select>
    <select id="prix" class="select">
      <option value="">Tous prix</option>
      <option value="lt100000">Moins de 100 000</option>
      <option value="100k-500k">100k - 500k</option>
      <option value="gt500k">Plus de 500k</option>
    </select>
    <select id="sort" class="select">
      <option value="recent">Les plus récents</option>
      <option value="price-asc">Prix croissant</option>
      <option value="price-desc">Prix décroissant</option>
    </select>
    <input id="loc" class="input" placeholder="Yoff, Virage, Ouest Foire..." />
  </div>

  <div id="list" class="grid"></div>

  <div class="footer">
    <div id="count">0 annonces trouvées</div>
    <div>
      <button class="post-btn" onclick="openPost()">+ Publier une annonce</button>
    </div>
  </div>
</div>

<!-- Modal détail -->
<div id="modalBack" class="modal-back">
  <div class="modal" role="dialog" aria-modal="true">
    <div class="head">
      <strong id="mdTitle">Titre annonce</strong>
      <button class="close" onclick="closeModal()">✕</button>
    </div>
    <div class="content">
      <div>
        <img id="mdImg" src="" alt="" style="width:100%;height:300px;object-fit:cover;border-radius:6px"/>
        <p id="mdDesc" style="margin-top:12px"></p>
        <ul class="list" id="mdMeta"></ul>
      </div>
      <aside style="background:#fff;border-radius:8px;padding:12px;box-shadow:0 6px 18px rgba(16,24,40,0.04)">
        <div style="font-weight:700;font-size:20px" id="mdPrice"></div>
        <div style="color:var(--muted);margin-top:8px" id="mdLoc"></div>
        <button style="width:100%;margin-top:14px;padding:10px;border-radius:8px;border:0;background:var(--accent);color:#fff;cursor:pointer" onclick="contactSeller()">Contacter le vendeur</button>
        <div style="margin-top:12px;font-size:13px;color:var(--muted)">Téléphone: <span id="mdPhone">+221 ...</span></div>
      </aside>
    </div>
  </div>
</div>

<!-- Simple form modal (simulate dépôt) -->
<div id="postBack" class="modal-back">
  <div class="modal" style="max-width:720px">
    <div class="head"><strong>Déposer une annonce</strong><button class="close" onclick="closePost()">✕</button></div>
    <div style="padding:16px">
      <div style="display:grid;gap:8px">
        <input id="p_title" placeholder="Titre" class="input"/>
        <input id="p_price" placeholder="Prix (ex: 300000)" class="input"/>
        <input id="p_loc" placeholder="Localisation (ex: Yoff Virage)" class="input"/>
        <select id="p_cat" class="select">
          <option>Propriété</option><option>Véhicules</option><option>Téléphones</option><option>Électronique</option>
        </select>
        <textarea id="p_desc" class="input" placeholder="Description" style="min-height:100px"></textarea>
        <button class="btn" onclick="submitPost()">Publier (simulate)</button>
      </div>
    </div>
  </div>
</div>

<script>
/* Données d'exemple : remplacer par fetch() depuis Firestore ou API */
let listings = [
  {id:1, title:"Appartement F2 - Yoff Virage", price:300000, img:"https://picsum.photos/seed/a/800/600", cat:"Propriété", loc:"Yoff Virage", phone:"+221 77 000 0000", desc:"Appartement F2 propre, proche route."},
  {id:2, title:"iPhone 12 - 128GB", price:350000, img:"https://picsum.photos/seed/b/800/600", cat:"Téléphones", loc:"Yoff Ouest Foire", phone:"+221 78 111 2222", desc:"Très bon état, chargeur fourni."},
  {id:3, title:"Toyota Corolla 2015", price:1200000, img:"https://picsum.photos/seed/c/800/600", cat:"Véhicules", loc:"Yoff", phone:"+221 76 333 4444", desc:"Entretien fait, pneus neufs."},
  {id:4, title:"Studio Meublé - proche aéroport", price:75000, img:"https://picsum.photos/seed/d/800/600", cat:"Propriété", loc:"Yoff Aéroport", phone:"+221 77 999 8888", desc:"Petit studio meublé, idéal pour étudiant."}
];

function render(list){
  const container = document.getElementById('list'); container.innerHTML='';
  document.getElementById('count').innerText = list.length + ' annonces trouvées';
  list.forEach(it=>{
    const div = document.createElement('div'); div.className='card';
    div.innerHTML = `<img src="${it.img}" alt="">
      <div class="body">
        <h3 class="title">${it.title}</h3>
        <div class="price">${formatPrice(it.price)}</div>
        <div class="meta">${it.cat} • ${it.loc}</div>
      </div>
      <div class="footer">
        <small style="color:var(--muted)">${new Date().toLocaleDateString()}</small>
        <button style="background:transparent;border:0;color:var(--accent);cursor:pointer" onclick="openDetail(${it.id})">Voir</button>
      </div>`;
    container.appendChild(div);
  });
}

function formatPrice(p){
  return p ? p.toString().replace(/\B(?=(\d{3})+(?!\d))/g, " ") + " CFA" : "N/C";
}

function doSearch(){
  const q = (document.getElementById('q').value||'').toLowerCase();
  const cat = document.getElementById('cat').value;
  const loc = (document.getElementById('loc').value||'').toLowerCase();
  const priceFilter = document.getElementById('prix').value;
  let list = listings.filter(i=>{
    if(cat && i.cat!==cat) return false;
    if(q && !(i.title.toLowerCase().includes(q) || i.desc.toLowerCase().includes(q))) return false;
    if(loc && !i.loc.toLowerCase().includes(loc)) return false;
    if(priceFilter){
      if(priceFilter==='lt100000' && !(i.price < 100000)) return false;
      if(priceFilter==='100k-500k' && !(i.price >=100000 && i.price<=500000)) return false;
      if(priceFilter==='gt500k' && !(i.price > 500000)) return false;
    }
    return true;
  });
  const sort = document.getElementById('sort').value;
  if(sort==='price-asc') list.sort((a,b)=>a.price-b.price);
  if(sort==='price-desc') list.sort((a,b)=>b.price-a.price);
  render(list);
}

function openDetail(id){
  const it = listings.find(x=>x.id===id);
  if(!it) return;
  document.getElementById('mdTitle').innerText = it.title;
  document.getElementById('mdImg').src = it.img;
  document.getElementById('mdDesc').innerText = it.desc;
  document.getElementById('mdPrice').innerText = formatPrice(it.price);
  document.getElementById('mdLoc').innerText = it.loc;
  document.getElementById('mdPhone').innerText = it.phone;
  document.getElementById('modalBack').style.display = 'flex';
}

function closeModal(){document.getElementById('modalBack').style.display='none'}
function contactSeller(){ alert('Appel/WhatsApp: ' + document.getElementById('mdPhone').innerText) }

/* Post simulate */
function openPost(){document.getElementById('postBack').style.display='flex'}
function closePost(){document.getElementById('postBack').style.display='none'}
function submitPost(){
  const t = document.getElementById('p_title').value || 'Sans titre';
  const p = Number(document.getElementById('p_price').value)||0;
  const l = document.getElementById('p_loc').value || 'Yoff';
  const c = document.getElementById('p_cat').value || 'Autre';
  const d = document.getElementById('p_desc').value || '';
  const id = Math.floor(Math.random()*100000)+100;
  listings.unshift({id,title:t,price:p,img:'https://picsum.photos/seed/new/800/600',cat:c,loc:l,phone:'+221 77 000 0000',desc:d});
  closePost(); doSearch();
  alert('Annonce publiée (simulate). Pour la rendre réelle, connecte-toi à un backend.');
}

/* initial render */
render(listings);
</script>
</body>
</html>
