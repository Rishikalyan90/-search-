 
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>SOLO SEARCH AI</title>

<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Rajdhani:wght@400;600&display=swap" rel="stylesheet">

<style>

:root{
    --accent:#00eaff;
    --bg:#02030a;
    --card:#07101f;
    --text:#ffffff;
}

*{
    margin:0;
    padding:0;
    box-sizing:border-box;
}

body{
    background:radial-gradient(circle at top,#0b1025,#02030a 70%);
    color:var(--text);
    font-family:'Rajdhani',sans-serif;
    min-height:100vh;
    overflow-x:hidden;
}

#galaxy{
    position:fixed;
    inset:0;
    z-index:-1;
}

/* HEADER */

header{
    text-align:center;
    padding:70px 20px 30px;
}

.logo{
    font-family:'Orbitron',sans-serif;
    font-size:58px;
    color:var(--accent);
    letter-spacing:5px;
    animation:glow 3s infinite alternate;
}

@keyframes glow{
    from{
        text-shadow:0 0 10px var(--accent);
    }
    to{
        text-shadow:0 0 40px var(--accent);
    }
}

.sub{
    margin-top:10px;
    opacity:.8;
    letter-spacing:2px;
}

/* SEARCH */

.searchWrap{
    display:flex;
    justify-content:center;
    margin-top:35px;
    padding:0 15px;
}

.searchBox{
    width:100%;
    max-width:850px;
    display:flex;
    gap:12px;
    flex-wrap:wrap;
}

.searchBox input{
    flex:1;
    min-width:250px;
    padding:18px;
    border-radius:15px;
    background:#040915;
    border:1px solid rgba(0,234,255,.25);
    color:white;
    font-size:18px;
    outline:none;
}

.searchBox input:focus{
    border-color:var(--accent);
    box-shadow:0 0 15px rgba(0,234,255,.35);
}

.searchBox button{
    padding:16px 28px;
    border:none;
    border-radius:15px;
    background:var(--accent);
    color:black;
    font-weight:bold;
    cursor:pointer;
    transition:.3s;
    font-size:16px;
}

.searchBox button:hover{
    transform:translateY(-2px);
    box-shadow:0 0 20px var(--accent);
}

/* LOADER */

.loader{
    width:60px;
    height:60px;
    border:5px solid rgba(255,255,255,.1);
    border-top:5px solid var(--accent);
    border-radius:50%;
    animation:spin 1s linear infinite;
    margin:40px auto;
}

@keyframes spin{
    100%{
        transform:rotate(360deg);
    }
}

/* AI BOX */

#aiBox{
    width:92%;
    max-width:1100px;
    margin:30px auto;
    padding:25px;
    border-radius:18px;
    background:rgba(0,0,0,.45);
    border:1px solid rgba(0,234,255,.2);
    display:none;
    backdrop-filter:blur(10px);
}

#aiBox h2{
    color:var(--accent);
    margin-bottom:12px;
}

/* RESULTS */

.grid{
    width:92%;
    max-width:1400px;
    margin:35px auto;
    display:grid;
    grid-template-columns:repeat(auto-fit,minmax(280px,1fr));
    gap:22px;
}

.card{
    background:rgba(0,0,0,.4);
    border:1px solid rgba(255,255,255,.08);
    padding:24px;
    border-radius:18px;
    transition:.3s;
    cursor:pointer;
    backdrop-filter:blur(8px);
}

.card:hover{
    transform:translateY(-6px);
    box-shadow:0 0 25px rgba(0,234,255,.35);
    border-color:rgba(0,234,255,.35);
}

.card h3{
    color:var(--accent);
    margin-bottom:12px;
}

/* IMAGES */

#images{
    width:92%;
    max-width:1400px;
    margin:50px auto;
    display:grid;
    grid-template-columns:repeat(auto-fit,minmax(220px,1fr));
    gap:18px;
}

#images img{
    width:100%;
    height:220px;
    object-fit:cover;
    border-radius:18px;
    transition:.35s;
    border:2px solid transparent;
    background:#111;
}

#images img:hover{
    transform:scale(1.03);
    border-color:var(--accent);
}

/* MODAL */

.modal{
    position:fixed;
    inset:0;
    background:rgba(0,0,0,.95);
    z-index:999;
    overflow-y:auto;
    display:none;
    padding:90px 20px 40px;
}

.modalContent{
    max-width:1100px;
    margin:auto;
    background:#050b18;
    border-radius:20px;
    padding:30px;
    border:1px solid rgba(0,234,255,.2);
}

.closeBtn{
    position:fixed;
    top:20px;
    right:30px;
    font-size:40px;
    cursor:pointer;
}

footer{
    text-align:center;
    padding:30px;
    opacity:.7;
}

@media(max-width:700px){

    .logo{
        font-size:36px;
    }

    .searchBox{
        flex-direction:column;
    }

}

</style>
</head>

<body>

<canvas id="galaxy"></canvas>

<header>

    <h1 class="logo">SOLO SEARCH</h1>

    <p class="sub">UNIVERSAL AI SEARCH ENGINE</p>

    <div class="searchWrap">

        <div class="searchBox">

            <input
                type="text"
                id="searchInput"
                placeholder="Search anything in the universe..."
                autocomplete="off"
            >

            <button onclick="searchWikipedia()">
                SEARCH
            </button>

        </div>

    </div>

</header>

<div id="aiBox">

    <h2>AI SUMMARY</h2>

    <p id="aiText"></p>

</div>

<div id="results" class="grid"></div>

<div id="images"></div>

<div class="modal" id="modal">

    <div class="closeBtn" onclick="closeModal()">
        ✕
    </div>

    <div class="modalContent">

        <h2 id="articleTitle"></h2>

        <p id="articleContent"></p>

    </div>

</div>

<footer>
    POWERED BY RISHI KALYAN ⚡
</footer>

<script>

/* =========================
   GALAXY BACKGROUND
========================= */

const canvas = document.getElementById("galaxy");
const ctx = canvas.getContext("2d");

let stars = [];

function resizeCanvas(){

    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;

}

window.addEventListener("resize", resizeCanvas);

resizeCanvas();

for(let i = 0; i < 250; i++){

    stars.push({
        x:Math.random() * canvas.width,
        y:Math.random() * canvas.height,
        radius:Math.random() * 2,
        speed:Math.random() * 0.5 + 0.1
    });

}

function animateStars(){

    ctx.clearRect(0,0,canvas.width,canvas.height);

    ctx.fillStyle = "white";

    stars.forEach(star => {

        ctx.beginPath();

        ctx.arc(
            star.x,
            star.y,
            star.radius,
            0,
            Math.PI * 2
        );

        ctx.fill();

        star.y += star.speed;

        if(star.y > canvas.height){
            star.y = 0;
        }

    });

    requestAnimationFrame(animateStars);

}

animateStars();

/* =========================
   SEARCH
========================= */

const input = document.getElementById("searchInput");
const results = document.getElementById("results");
const images = document.getElementById("images");
const aiBox = document.getElementById("aiBox");

input.addEventListener("keydown",(e)=>{

    if(e.key === "Enter"){
        searchWikipedia();
    }

});

async function searchWikipedia(){

    const query = input.value.trim();

    if(!query){
        alert("Please enter something!");
        return;
    }

    results.innerHTML = `<div class="loader"></div>`;
    images.innerHTML = "";
    aiBox.style.display = "none";

    try{

        const url =
        `https://en.wikipedia.org/w/api.php?action=query&list=search&srsearch=${encodeURIComponent(query)}&format=json&origin=*`;

        const response = await fetch(url);

        const data = await response.json();

        renderResults(data.query.search);

        loadImages(query);

    }
    catch(error){

        results.innerHTML =
        `<div style="text-align:center;color:#ff5555;">
            Search Failed
        </div>`;

    }

}

function renderResults(arr){

    results.innerHTML = "";

    if(arr.length === 0){

        results.innerHTML =
        `<div style="text-align:center;color:var(--accent);">
            No Results Found
        </div>`;

        return;
    }

    arr.forEach(item => {

        const card = document.createElement("div");

        card.className = "card";

        const cleanSnippet =
        item.snippet.replace(/<\/?[^>]+(>|$)/g,"");

        card.innerHTML = `
            <h3>${item.title}</h3>
            <p>${cleanSnippet}...</p>
        `;

        card.onclick = () =>
        openArticle(item.pageid,item.title);

        results.appendChild(card);

    });

}

/* =========================
   IMAGE SEARCH
========================= */

async function loadImages(query){

    images.innerHTML =
    `<div class="loader" style="grid-column:1/-1"></div>`;

    try{

        const url =
        `https://en.wikipedia.org/w/api.php?action=query&prop=pageimages&format=json&piprop=thumbnail&pithumbsize=600&generator=search&gsrsearch=${encodeURIComponent(query)}&origin=*`;

        const response = await fetch(url);

        const data = await response.json();

        images.innerHTML = "";

        if(data.query && data.query.pages){

            const pages = Object.values(data.query.pages);

            pages.forEach(page => {

                if(page.thumbnail){

                    const img = document.createElement("img");

                    img.src = page.thumbnail.source;
                    img.alt = page.title;

                    images.appendChild(img);

                }

            });

        }
        else{

            images.innerHTML =
            `<p style="grid-column:1/-1;text-align:center;opacity:.5;">
                No images found.
            </p>`;

        }

    }
    catch(error){

        images.innerHTML = "";

    }

}

/* =========================
   ARTICLE MODAL
========================= */

async function openArticle(pageId,title){

    const modal = document.getElementById("modal");

    modal.style.display = "block";

    document.body.style.overflow = "hidden";

    document.getElementById("articleTitle").innerText = title;

    document.getElementById("articleContent").innerText = "Loading...";

    try{

        const url =
        `https://en.wikipedia.org/w/api.php?action=query&prop=extracts&pageids=${pageId}&explaintext=1&format=json&origin=*`;

        const response = await fetch(url);

        const data = await response.json();

        const text =
        data.query.pages[pageId].extract;

        document.getElementById("articleContent")
        .innerText = text.substring(0,5000);

        aiBox.style.display = "block";

        document.getElementById("aiText")
        .innerText = text.substring(0,400) + "...";

    }
    catch(error){

        document.getElementById("articleContent")
        .innerText = "Failed to load content.";

    }

}

function closeModal(){

    document.getElementById("modal").style.display = "none";

    document.body.style.overflow = "auto";

}

window.onclick = (e) => {

    if(e.target.id === "modal"){
        closeModal();
    }

};

</script>

</body>
</html>