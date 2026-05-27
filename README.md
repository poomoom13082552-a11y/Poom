<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Poom Lost & Found</title>

<style>
body{
margin:0;
font-family:sans-serif;
background:linear-gradient(135deg,#3b82f6,#ec4899);
min-height:100vh;
color:white;
}

header{
padding:18px 25px;
background:rgba(255,255,255,.15);
backdrop-filter:blur(10px);
display:flex;
justify-content:space-between;
align-items:center;
}

button{
padding:10px 14px;
border:none;
border-radius:12px;
cursor:pointer;
}

.container{
width:92%;
max-width:1200px;
margin:auto;
padding:20px 0;
}

.panel,.card{
background:rgba(0,0,0,.25);
border-radius:20px;
padding:20px;
margin-bottom:20px;
}

.card img{
width:100%;
height:280px;
object-fit:cover;
border-radius:16px;
}

.badge{
display:inline-block;
padding:6px 12px;
border-radius:999px;
font-size:13px;
margin-right:6px;
}

.red{background:#ef4444;}
.yellow{background:#eab308;}
.green{background:#22c55e;}
.blue{background:#3b82f6;}

input,textarea,select{
width:100%;
padding:14px;
border:none;
border-radius:12px;
margin-bottom:12px;
}

.actions{
display:flex;
gap:10px;
margin-top:10px;
flex-wrap:wrap;
}

.comment{
background:rgba(255,255,255,.15);
padding:8px;
border-radius:10px;
margin-top:6px;
}
</style>
</head>

<body>

<header>
<h2>📦 Poom Lost & Found</h2>
<button onclick="openAdmin()">🔐 Admin</button>
</header>

<div class="container">

<div class="panel" id="adminPanel" style="display:none">
<h3>เพิ่มรายการ</h3>

<input id="itemName" placeholder="ชื่อรายการ">
<input type="date" id="itemDate">
<input type="file" id="itemImage" accept="image/*">

<textarea id="itemDesc" placeholder="รายละเอียด"></textarea>

<select id="itemCategory">
<option>อุปกรณ์</option>
<option>เอกสาร</option>
<option>เสื้อผ้า</option>
<option>อิเล็กทรอนิกส์</option>
<option>อื่น ๆ</option>
</select>

<select id="itemStatus">
<option>ของหาย</option>
<option>พบของ</option>
<option>กำลังตรวจสอบ</option>
<option>รอเจ้าของติดต่อ</option>
<option>ส่งคืนแล้ว</option>
<option>ปิดเคส</option>
</select>

<button onclick="addPost()">➕ เพิ่ม</button>
</div>

<div id="posts"></div>

</div>

<script>
let isAdmin=false;

let posts = JSON.parse(localStorage.getItem("poom_posts")) || [];

function save(){
localStorage.setItem("poom_posts",JSON.stringify(posts));
}

function fileToBase64(file,callback){
const reader=new FileReader();
reader.onload=()=>callback(reader.result);
reader.readAsDataURL(file);
}

function badgeColor(status){
if(status==="ของหาย") return "red";
if(status==="กำลังตรวจสอบ"||status==="รอเจ้าของติดต่อ") return "yellow";
if(status==="พบของ") return "blue";
return "green";
}

function render(){
const box=document.getElementById("posts");
box.innerHTML="";

posts.forEach((p,i)=>{
box.innerHTML+=`
<div class="card">
<img src="${p.image}">
<h3>${p.name}</h3>
<p>📅 ${p.date}</p>

<div>
<span class="badge blue">${p.category}</span>
<span class="badge ${badgeColor(p.status)}">${p.status}</span>
</div>

<p>${p.desc}</p>

<div class="actions">
<button onclick="likePost(${i})">👍 ${p.likes}</button>
${isAdmin?`<button onclick="deletePost(${i})">🗑 ลบ</button>`:""}
</div>

<h4>💬 ความคิดเห็น</h4>
${p.comments.map(c=>`<div class="comment">${c}</div>`).join("")}
<input placeholder="พิมพ์แล้วกด Enter"
onkeypress="if(event.key==='Enter'){addComment(${i},this.value);this.value=''}">
</div>
`;
});
}

function addPost(){
const file=itemImage.files[0];
if(!file){alert("เลือกรูปภาพ");return;}

fileToBase64(file,function(base64){
posts.unshift({
name:itemName.value,
date:itemDate.value,
image:base64,
desc:itemDesc.value,
category:itemCategory.value,
status:itemStatus.value,
likes:0,
comments:[]
});
save();
render();
itemName.value="";
itemDate.value="";
itemImage.value="";
itemDesc.value="";
});
}

function likePost(i){
posts[i].likes++;
save();render();
}

function addComment(i,text){
if(text.trim()==="")return;
posts[i].comments.push(text);
save();render();
}

function deletePost(i){
if(confirm("ลบรายการนี้?")){
posts.splice(i,1);
save();render();
}
}

function openAdmin(){
const pass=prompt("ใส่รหัสผ่าน");
if(pass==="poom"){
isAdmin=true;
adminPanel.style.display="block";
render();
}else{
alert("รหัสไม่ถูกต้อง");
}
}

render();
</script>

</body>
</html>
