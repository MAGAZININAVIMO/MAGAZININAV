<html lang="tg">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>MAGAZINI NAV</title>
<style>
body{font-family:Arial,sans-serif;margin:0;background:#f0f0f0;}
header{background:#008080;color:#fff;text-align:center;padding:15px;font-size:22px;position:relative;}
#admin-toggle{position:absolute;top:12px;right:12px;padding:5px 10px;background:#fff;color:#008080;border:none;border-radius:4px;cursor:pointer;}
#password-popup{position:absolute;top:50px;right:12px;background:#fff;padding:8px;border:1px solid #ccc;border-radius:6px;display:none;width:220px;z-index:100;}
#password-popup input{width:100%;padding:6px;margin-bottom:6px;border-radius:4px;border:1px solid #ccc;}
#password-popup .row{display:flex;gap:6px;}
#password-popup button{flex:1;padding:6px;border:none;border-radius:4px;cursor:pointer;}
#password-popup .ok{background:#008080;color:#fff;}
#password-popup .close{background:#f2f2f2;color:#333;}
#pw-error{color:#c33;font-size:13px;display:none;margin-top:4px;}
#admin-panel{max-width:400px;margin:12px auto;padding:12px;background:#fff;border-radius:8px;box-shadow:0 3px 12px rgba(0,0,0,0.06);display:none;}
#admin-panel input[type="text"],#admin-panel textarea,#admin-panel input[type="file"]{width:100%;padding:8px;margin-bottom:8px;border-radius:6px;border:1px solid #ccc;box-sizing:border-box;}
#admin-panel button{width:100%;padding:8px;border:none;border-radius:6px;background:#008080;color:#fff;font-weight:600;cursor:pointer;margin-bottom:6px;}
#admin-logout{background:#c33;}
#product-list{max-width:960px;margin:16px auto;display:grid;grid-template-columns:repeat(auto-fill,minmax(160px,1fr));gap:12px;padding:0 12px;}
.product{
  position: relative;
  background:#fff;
  border-radius:8px;
  padding:0;
  box-shadow:0 2px 6px rgba(0,0,0,0.06);
  cursor:pointer;
  display:flex;
  flex-direction:column;
  align-items:center;
  aspect-ratio:1/1;
  overflow:hidden;
}
.product img{
  width:100%;
  height:100%;
  object-fit:cover;
  display:block;
}
.product .info{
  position:absolute;
  bottom:0;
  left:0;
  width:100%;
  background: rgba(255,255,255,0.9);
  color:#000;
  padding:6px;
  text-align:center;
  box-sizing: border-box;
}
.product .info .title{font-weight:600;font-size:14px;}
.product .info .price{font-weight:600;font-size:13px;margin-top:2px;}
.delete-btn{
  position:absolute;
  top:6px;
  right:6px;
  background:#c33;
  color:#fff;
  border:none;
  padding:4px 6px;
  border-radius:4px;
  cursor:pointer;
}
#product-detail{max-width:600px;margin:16px auto;background:#fff;padding:12px;border-radius:8px;box-shadow:0 3px 12px rgba(0,0,0,0.06);display:none;}
#product-detail img{width:100%;border-radius:6px;margin-bottom:8px;}
#back-btn{display:inline-block;margin-bottom:8px;padding:6px 10px;border-radius:6px;border:none;background:#eee;color:#333;cursor:pointer;}
#detail-links{display:flex;gap:6px;margin-top:6px;}
#detail-links a{display:inline-block;width:32px;height:32px;}
#detail-links a img{width:100%;height:100%;}
#search-input{width:90%;max-width:300px;padding:6px;border-radius:4px;border:1px solid #ccc;margin-top:12px;}
</style>
</head>
<body>

<header>
MAGAZINI NAV
<button id="admin-toggle">Админ</button>
<div id="password-popup">
  <input id="admin-password-input" type="password" placeholder="Паролро ворид кунед" />
  <div class="row">
    <button class="ok" id="pw-ok">Ворид</button>
    <button class="close" id="pw-close">Бастан</button>
  </div>
  <div id="pw-error">Парол нодуруст аст</div>
</div>
<div>
  <input type="text" id="search-input" placeholder="Номи молро ҷустуҷӯ кунед...">
</div>
</header>

<div id="admin-panel">
<h2>Иловаи мол</h2>
<input id="p-name" type="text" placeholder="Номи мол" />
<input id="p-price" type="text" placeholder="Нарх" />
<textarea id="p-desc" rows="3" placeholder="Тавсифи мол"></textarea>
<input id="p-file" type="file" accept="image/*" />
<input id="p-whatsapp" type="text" placeholder="WhatsApp силка" />
<input id="p-inst" type="text" placeholder="Instagram силка" />
<input id="p-tiktok" type="text" placeholder="TikTok силка" />
<button id="add-btn">Илова кардан</button>
<button id="admin-logout">Баромадан</button>
</div>

<div id="product-list"></div>

<div id="product-detail">
<button id="back-btn" onclick="closeDetail()">← Бозгашт</button>
<h2 id="detail-name"></h2>
<img id="detail-image" src="" alt="" />
<p id="detail-price"></p>
<p id="detail-desc"></p>
<div id="detail-links">
<a id="detail-whatsapp" href="#" target="_blank"><img src="https://cdn-icons-png.flaticon.com/512/733/733585.png"></a>
<a id="detail-inst" href="#" target="_blank"><img src="https://cdn-icons-png.flaticon.com/512/2111/2111463.png"></a>
<a id="detail-tiktok" href="#" target="_blank"><img src="https://cdn-icons-png.flaticon.com/512/3046/3046121.png"></a>
</div>
</div>

<script>
const ADMIN_PASSWORD="MANHABIB*^*2007";
let products=JSON.parse(localStorage.getItem('hb_products')||"[]");
let isAdmin=false;

const adminToggle=document.getElementById('admin-toggle');
const passwordPopup=document.getElementById('password-popup');
const pwInput=document.getElementById('admin-password-input');
const pwOk=document.getElementById('pw-ok');
const pwClose=document.getElementById('pw-close');
const pwError=document.getElementById('pw-error');
const adminPanel=document.getElementById('admin-panel');
const addBtn=document.getElementById('add-btn');
const logoutBtn=document.getElementById('admin-logout');
const productList=document.getElementById('product-list');
const detailBox=document.getElementById('product-detail');
const fileInput=document.getElementById('p-file');
const searchInput=document.getElementById('search-input');

adminToggle.addEventListener('click',()=>{
  passwordPopup.style.display=(passwordPopup.style.display==='block')?'none':'block';
  pwError.style.display='none';pwInput.value='';pwInput.focus();
});
pwClose.addEventListener('click',()=>{passwordPopup.style.display='none';pwError.style.display='none';});
pwOk.addEventListener('click',()=>{
  if(pwInput.value.trim()===ADMIN_PASSWORD){
    isAdmin=true;adminPanel.style.display='block';passwordPopup.style.display='none';pwError.style.display='none';
    renderProducts();
  }else{pwError.style.display='block';}
});
logoutBtn.addEventListener('click',()=>{isAdmin=false;adminPanel.style.display='none';renderProducts();});

addBtn.addEventListener('click',()=>{
  const name=document.getElementById('p-name').value.trim();
  const price=document.getElementById('p-price').value.trim();
  const desc=document.getElementById('p-desc').value.trim();
  const whatsapp=document.getElementById('p-whatsapp').value.trim();
  const inst=document.getElementById('p-inst').value.trim();
  const tiktok=document.getElementById('p-tiktok').value.trim();
  const file=fileInput.files[0];

  if(!name || !price || !file){
    alert('Лутфан номи мол, нарх ва аксро интихоб кунед');
    return;
  }

  const reader=new FileReader();
  reader.onload=function(e){
    const id=Date.now().toString();
    products.push({id,name,price,desc,image:e.target.result,whatsapp,inst,tiktok});
    localStorage.setItem('hb_products',JSON.stringify(products));

    // Тоза кардани inputҳо
    document.getElementById('p-name').value='';
    document.getElementById('p-price').value='';
    document.getElementById('p-desc').value='';
    document.getElementById('p-whatsapp').value='';
    document.getElementById('p-inst').value='';
    document.getElementById('p-tiktok').value='';
    fileInput.value='';

    renderProducts(searchInput.value.trim().toLowerCase());
  };
  reader.readAsDataURL(file);
});

searchInput.addEventListener('input', () => {
  renderProducts(searchInput.value.trim().toLowerCase());
});

function renderProducts(search=''){
  productList.innerHTML='';
  detailBox.style.display='none';
  products.forEach((p,index)=>{
    if(search && !p.name.toLowerCase().includes(search)) return;
    const div=document.createElement('div');
    div.className='product';
    div.innerHTML=`
      <img src="${p.image}" alt="${p.name}">
      <div class="info">
        <div class="title">${p.name}</div>
        <div class="price">${p.price}</div>
      </div>
      ${isAdmin?'<button class="delete-btn">Удалить</button>':''}
    `;
    div.onclick=()=>openDetail(p);
    if(isAdmin){
      div.querySelector('.delete-btn').addEventListener('click',(e)=>{
        e.stopPropagation();
        if(confirm('Оё шумо воқеан мехоҳед ин молро ҳазф кунед?')){
          products.splice(index,1);
          localStorage.setItem('hb_products',JSON.stringify(products));
          renderProducts(search);
        }
      });
    }
    productList.appendChild(div);
  });
}

function openDetail(p){
  detailBox.style.display='block';
  document.getElementById('detail-name').textContent=p.name;
  document.getElementById('detail-price').textContent='Нарх: '+p.price;
  document.getElementById('detail-desc').textContent=p.desc||'';
  document.getElementById('detail-image').src=p.image;
  document.getElementById('detail-whatsapp').href=p.whatsapp||'#';
  document.getElementById('detail-inst').href=p.inst||'#';
  document.getElementById('detail-tiktok').href=p.tiktok||'#';
}

function closeDetail(){detailBox.style.display='none';}

window.onload=()=>renderProducts();
</script>
</body>
</html>
