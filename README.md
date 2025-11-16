<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Free Dropshipping Store</title>
  <meta name="description" content="Free single-file dropshipping store - deploy on any static host" />
  <style>
    :root{--accent:#0b74de;--muted:#666;--bg:#f7f8fb} *{box-sizing:border-box} 
    body{font-family:Inter,system-ui,Segoe UI,Roboto,"Helvetica Neue",Arial; margin:0;background:var(--bg);color:#111} 
    header{display:flex;align-items:center;justify-content:space-between;padding:18px 24px;background:#fff;box-shadow:0 1px 6px rgba(20,20,30,.06)} 
    .logo{display:flex;gap:12px;align-items:center} 
    .logo .mark{width:40px;height:40px;background:linear-gradient(135deg,var(--accent),#4fd1c5);border-radius:8px;display:flex;align-items:center;justify-content:center;color:#fff;font-weight:700} 
    .search{flex:1;margin:0 18px} 
    .search input{width:100%;padding:10px 12px;border-radius:8px;border:1px solid #e5e7eb} 
    .controls{display:flex;gap:12px;align-items:center} 
    main{max-width:1200px;margin:28px auto;padding:0 18px} 
    .grid{display:grid;grid-template-columns:repeat(3,1fr);gap:18px} 
    @media(max-width:980px){.grid{grid-template-columns:repeat(2,1fr)}} 
    @media(max-width:640px){.grid{grid-template-columns:1fr}.search{display:none}} 
    .card{background:#fff;border-radius:12px;padding:12px;box-shadow:0 6px 18px rgba(15,15,25,.04);display:flex;flex-direction:column} 
    .card img{width:100%;height:200px;object-fit:cover;border-radius:8px} 
    .meta{padding:10px 2px;display:flex;justify-content:space-between;align-items:center} 
    .title{font-weight:600} 
    .price{font-weight:700} 
    .btn{background:var(--accent);color:#fff;padding:8px 12px;border-radius:8px;border:0;cursor:pointer} 
    .btn.ghost{background:transparent;color:var(--accent);border:1px solid rgba(11,116,222,.12)} 
    .sidebar{position:fixed;right:18px;top:80px;width:320px;background:#fff;border-radius:12px;padding:14px;box-shadow:0 12px 40px rgba(20,20,40,.08)} 
    .cart-items{max-height:420px;overflow:auto;margin-top:8px} 
    .cart-row{display:flex;gap:8px;align-items:center;padding:8px 0;border-bottom:1px dashed #eee} 
    .cart-row img{width:56px;height:56px;border-radius:6px;object-fit:cover} 
    .qty{display:flex;gap:6px;align-items:center} 
    .total{display:flex;justify-content:space-between;padding-top:10px;font-weight:700} 
    footer{padding:24px;text-align:center;color:var(--muted)} 
    .topbar{display:flex;gap:12px;align-items:center} 
    .tags{display:flex;gap:8px;flex-wrap:wrap} 
    .tag{padding:6px 10px;background:#fff;border-radius:999px;border:1px solid #eee;cursor:pointer} 
    .modal{position:fixed;inset:0;background:rgba(10,10,20,.4);display:none;align-items:center;justify-content:center;padding:20px} 
    .modal .panel{width:960px;max-width:100%;background:#fff;border-radius:12px;padding:18px;display:flex;gap:18px} 
    .modal img{width:420px;max-width:44%;height:360px;object-fit:cover;border-radius:8px} 
    .flexcol{display:flex;flex-direction:column} 
    .muted{color:var(--muted);font-size:13px}
  </style>
</head>
<body>
  <header>
    <div class="logo">
      <div class="mark">FD</div>
      <div>
        <div style="font-weight:700">FreeDropship</div>
        <div style="font-size:12px;color:var(--muted)">Static store — free to host</div>
      </div>
    </div>

    <div class="search"><input id="searchInput" placeholder="Search products, e.g. 'watch'" /></div>

    <div class="controls">
      <div class="topbar">
        <div class="tags" id="categories"></div>
      </div>
      <button class="btn ghost" id="viewStore">Store</button>
      <button class="btn" id="openCart">Cart (<span id="cartCount">0</span>)</button>
    </div>
  </header>

  <main>
    <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:12px">
      <h1 style="margin:0">Featured Products</h1>
      <div class="muted">Static store — no payment integration</div>
    </div>

    <div class="grid" id="productGrid"></div>
  </main>

  <aside class="sidebar" id="cartPanel">
    <h3 style="margin:0">Cart</h3>
    <div class="cart-items" id="cartItems"></div>
    <div class="total"><span>Subtotal</span><span id="subtotal">$0.00</span></div>
    <div style="margin-top:12px;display:flex;gap:8px">
      <button class="btn ghost" id="clearCart">Clear</button>
    </div>
  </aside>

  <div class="modal" id="productModal" aria-hidden="true">
    <div class="panel">
      <img id="modalImg" src="" alt="">
      <div class="flexcol" style="flex:1">
        <h2 id="modalTitle"></h2>
        <div class="muted" id="modalCategory"></div>
        <p id="modalDesc"></p>
        <div style="display:flex;gap:10px;align-items:center;margin-top:auto">
          <div style="font-size:20px;font-weight:700" id="modalPrice"></div>
          <div style="margin-left:auto;display:flex;gap:8px;align-items:center">
            <input id="modalQty" type="number" value="1" min="1" style="width:72px;padding:8px;border-radius:8px;border:1px solid #eee" />
            <button class="btn" id="modalAdd">Add to cart</button>
            <button class="btn ghost" id="modalClose">Close</button>
          </div>
        </div>
      </div>
    </div>
  </div>

  <footer>
    Built with ❤️ • Deploy on GitHub Pages or Netlify for free
  </footer>

  <script>
    const PRODUCTS = [
      {id:'p1',title:'Minimalist Smartwatch',price:29.99,category:'Electronics',image:'https://picsum.photos/seed/p1/800/600',desc:'Water-resistant smartwatch with step counter and 7-day battery.'},
      {id:'p2',title:'Ceramic Coffee Mug',price:12.49,category:'Home',image:'https://picsum.photos/seed/p2/800/600',desc:'12oz ceramic mug with ergonomic handle.'},
      {id:'p3',title:'Travel Backpack',price:39.95,category:'Bags',image:'https://picsum.photos/seed/p3/800/600',desc:'Lightweight, durable backpack with laptop sleeve.'},
      {id:'p4',title:'LED Ring Light',price:18.5,category:'Electronics',image:'https://picsum.photos/seed/p4/800/600',desc:'Adjustable brightness for streaming and selfies.'},
      {id:'p5',title:'Yoga Mat',price:22.0,category:'Fitness',image:'https://picsum.photos/seed/p5/800/600',desc:'Non-slip yoga mat, 6mm thick.'},
      {id:'p6',title:'Sunglasses',price:14.9,category:'Accessories',image:'https://picsum.photos/seed/p6/800/600',desc:'UV400 protection sunglasses.'}
    ];

    const store = { products:PRODUCTS.slice(), cart: JSON.parse(localStorage.getItem('fd_cart')||'{}') };
    function saveCart(){ localStorage.setItem('fd_cart', JSON.stringify(store.cart)); renderCart(); }

    function renderProducts(list=store.products){ const grid = document.getElementById('productGrid'); grid.innerHTML=''; list.forEach(p=>{ const el = document.createElement('div'); el.className='card'; el.innerHTML = `<img src="${p.image}" alt="${p.title}" loading="lazy" /><div class="meta"><div><div class="title">${p.title}</div><div class="muted" style="font-size:13px">${p.category}</div></div><div style="text-align:right"><div class="price">$${p.price.toFixed(2)}</div><div style="margin-top:8px;display:flex;gap:6px;justify-content:flex-end"><button class="btn ghost" data-id="${p.id}" data-action="view">View</button><button class="btn" data-id="${p.id}" data-action="add">Add</button></div></div></div>`; grid.appendChild(el); }); }

    function renderCategories(){ const cats = Array.from(new Set(PRODUCTS.map(p=>p.category))); const el = document.getElementById('categories'); el.innerHTML=''; const all = document.createElement('div'); all.className='tag'; all.textContent='All'; all.onclick=()=>renderProducts(); el.appendChild(all); cats.forEach(c=>{ const t = document.createElement('div'); t.className='tag'; t.textContent=c; t.onclick=()=>renderProducts(PRODUCTS.filter(p=>p.category===c)); el.appendChild(t) }) }

    function addToCart(id,qty=1){ const p = PRODUCTS.find(x=>x.id===id); if(!p) return; store.cart[id] = (store.cart[id]||0) + Number(qty); saveCart(); }
    function removeFromCart(id){ delete store.cart[id]; saveCart(); }
    function setQty(id,qty){ if(qty<=0) removeFromCart(id); else store.cart[id]=Number(qty); saveCart(); }

    function renderCart(){ const area = document.getElementById('cartItems'); area.innerHTML=''; const ids = Object.keys(store.cart); let subtotal = 0; let count = 0; ids.forEach(id=>{ const p = PRODUCTS.find(x=>x.id===id); const q = store.cart[id]; if(!p) return; subtotal += p.price * q; count += q; const row = document.createElement('div'); row.className='cart-row'; row.innerHTML = `<img src="${p.image}" alt="${p.title}" /><div style="flex:1"><div style="font-weight:600">${p.title}</div><div class="muted">${p.category} • $${p.price.toFixed(2)}</div><div style="margin-top:6px" class="qty"><button data-action="dec" data-id="${id}" class="btn ghost">-</button><input type="number" min="1" value="${q}" data-id="${id}" style="width:56px;padding:6px;border-radius:6px;border:1px solid #eee" /><button data-action="inc" data-id="${id}" class="btn ghost">+</button><button data-action="rm" data-id="${id}" class="btn ghost">Remove</button></div></div>`; area.appendChild(row); }); document.getElementById('subtotal').textContent = '$' + subtotal.toFixed(2); document.getElementById('cartCount').textContent = count; }

    document.addEventListener('click', e=>{ const a = e.target.closest('[data-action]'); if(a){ const action=a.dataset.action; const id=a.dataset.id; if(action==='add'){ addToCart(id); } else if(action==='view'){ openModal(id); } else if(action==='dec'){ setQty(id, (store.cart[id]||1)-1 ); } else if(action==='inc'){ setQty(id, (store.cart[id]||0)+1 ); } else if(action==='rm'){ removeFromCart(id) } } });
    document.getElementById('openCart').addEventListener('click', ()=>{ const panel = document.getElementById('cartPanel'); panel.style.display = panel.style.display === 'none' || !panel.style.display ? 'block' : 'none'; });
    document.getElementById('clearCart').addEventListener('click', ()=>{ store.cart={}; saveCart(); });
    document.getElementById('searchInput').addEventListener('input', e=>{ const q = e.target.value.toLowerCase().trim(); if(!q) return renderProducts(); renderProducts(PRODUCTS.filter(p=>p.title.toLowerCase().includes(q) || p.desc.toLowerCase().includes(q) || p.category.toLowerCase().includes(q))); });

    function openModal(id){ const p = PRODUCTS.find(x=>x.id===id); if(!p) return; document.getElementById('modalImg').src = p.image; document.getElementById('modalTitle').textContent=p.title; document.getElementById('modalDesc').textContent=p.desc; document.getElementById('modalPrice').textContent='$'+p.price.toFixed(2); document.getElementById('modalCategory').textContent=p.category; document.getElementById('modalQty').value=1; document.getElementById('productModal').style.display='flex'; document.getElementById('modalAdd').dataset.id = id; }
    document.getElementById('modalClose').addEventListener('click', ()=>document.getElementById('productModal').style.display='none');
    document.getElementById('modalAdd').addEventListener('click', e=>{ const id=e.target.dataset.id; const q = Number(document.getElementById('modalQty').value||1); addToCart(id,q); document.getElementById('productModal').style.display='none'; });

    (function(){ if(window.innerWidth>900) document.getElementById('cartPanel').style.display='block'; else document.getElementById('cartPanel').style.display='none'; }());

    renderCategories(); renderProducts(); renderCart();
  </script>
</body>
</html>
# haithe.github.io
