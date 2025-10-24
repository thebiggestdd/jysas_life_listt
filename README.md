<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Vanity Gallery — The Biggest D of All (Locked)</title>
<title>Vanity Gallery — The Biggest D of All</title>
<style>
  body {
    margin: 0;
    font-family: Georgia, "Times New Roman", serif;
    background: #fafafa;
    color: #111;
    display: flex;
    flex-direction: column;
@@ -14,11 +14,17 @@
    align-items: center;
    padding: 40px 20px;
  }
  h1 { margin-bottom: 10px; font-size: 2rem; }
  p { color: #555; margin-bottom: 30px; }
  h1 {
    margin-bottom: 10px;
    font-size: 2rem;
@@ -24,29 +22,41 @@
  }

  .controls {
    display: none; /* Hidden until unlocked */
    display: flex;
    display: none; /* hidden until password unlock */
    flex-direction: column;
    align-items: center;
    background: white;
    padding: 20px;
    border-radius: 10px;
@@ -27,250 +33,232 @@
    box-shadow: 0 4px 15px rgba(0,0,0,0.1);
    margin-bottom: 40px;
    width: min(600px, 90%);
    gap:8px;
  }

  textarea { width: 100%; height: 100px; resize: vertical; padding: 10px;
    font-family: Georgia, "Times New Roman", serif; font-size: 1rem; border: 1px solid #ccc;
    border-radius: 6px; margin-top: 10px; }
  input[type="file"], input[type="password"], input[type="text"] {
    margin: 6px 0;
    padding: 8px;
    border-radius: 6px;
    border: 1px solid #ccc;
  textarea {
    width: 100%;
    height: 100px;
    resize: vertical;
    padding: 10px;
    font-family: Georgia, "Times New Roman", serif;
    width: 90%;
    box-sizing: border-box;
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 6px;
    margin-top: 10px;
  }

  .unlock-row { display:flex; gap:8px; width:100%; justify-content:center; }
  input[type="file"] {
    margin: 10px 0;
  }
@@ -55,25 +65,27 @@
    background: black;
    color: white;
    border: none;
    padding: 10px 18px;
    padding: 10px 20px;
    border-radius: 6px;
    cursor: pointer;
    font-family: Georgia, "Times New Roman", serif;
    margin: 5px;
  }
  button.secondary { background:#555; }
  button:hover { opacity:0.9; }

  .gallery { display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 20px; width: 100%; max-width: 1100px; }
  button:hover {
    background: #333;
  }
  .gallery {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 20px;
    width: 100%;
    max-width: 1100px;
  }
  .card {
    background: white;
    border-radius: 10px;
@@ -83,29 +95,27 @@
    flex-direction: column;
  }

  .card { background: white; border-radius: 10px; overflow: hidden;
    box-shadow: 0 6px 18px rgba(0,0,0,0.1); display: flex; flex-direction: column; }
  .card img {
    width: 100%;
    height: 200px;
    object-fit: cover;
  }
  .card-content {
    padding: 15px;
    flex: 1;
  }
  .card-content p {
    margin: 0;
    font-size: 0.95rem;
    line-height: 1.5;
  }
  .date {
    color: #777;
    font-size: 0.8rem;
    margin-top: 8px;
  }
  .delete-btn {
    background: #e74c3c;
    color: white;
@@ -116,11 +126,9 @@
    font-size: 0.8rem;
    margin-top: 10px;
  }
  .delete-btn:hover {
    background: #c0392b;
  }
  #login {
    background: white;
    padding: 20px;
@@ -130,48 +138,140 @@
    text-align: center;
  }

  .card img { width: 100%; height: 200px; object-fit: cover; }
  .card-content { padding: 15px; flex: 1; }
  .card-content p { margin: 0; font-size: 0.95rem; line-height: 1.5; }
  .date { color: #777; font-size: 0.8rem; margin-top: 8px; }
  #login input {
    padding: 10px;
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 6px;
  }

  #logoutBtn {
    background: #b33a3a;
  }
  #logoutBtn:hover {
    background: #922d2d;
  }
  .delete-btn { background: #e74c3c; color: white; border: none; padding: 5px 10px;
    border-radius: 4px; cursor: pointer; font-size: 0.8rem; margin-top: 10px; }
</style>
</head>
<body>

<h1>Vanity Gallery</h1>
<p>Capture and list your daily masterpieces, photo and thought included. :)</p>
<p>Where only the biggest D may post their thoughts and pictures.</p>
<div id="login">
  <p>Enter password to unlock editor access:</p>
  <input type="password" id="passwordInput" placeholder="Enter password">
  <button id="loginBtn">Unlock</button>
</div>
<div class="controls" id="controls">
  <input type="file" id="imageInput" accept="image/*">
  <textarea id="textInput" placeholder="Write your vanity thought here..."></textarea>
  <button id="saveBtn">Save Card</button>
  <button id="logoutBtn">Lock / Logout</button>
</div>

<div class="controls" id="controls"></div>
<div class="gallery" id="gallery"></div>

<script>
/* ==================== CONFIG ==================== */
/* Change this to your actual password (keep it secret). */
const PASSWORD = "OnlyTheRealDKnows"; // ← SET YOUR PASSWORD HERE
/* ================================================= */
const storageKey = "vanity_gallery_cards";
const adminKey = "vanity_gallery_admin_mode";
const controls = document.getElementById("controls");
const gallery = document.getElementById("gallery");
let cards = JSON.parse(localStorage.getItem(storageKey)) || [];
let isAdmin = localStorage.getItem(adminKey) === "true";
// Trim helper
function trimSafe(s){ return (s || "").toString().trim(); }
// Render the controls area depending on admin state
function renderControls() {
  controls.innerHTML = "";
  if (!isAdmin) {
    // Show unlock UI
    controls.innerHTML = `
      <div style="width:100%;max-width:520px">
        <div style="text-align:center;color:#666;margin-bottom:6px">Enter password to unlock editing</div>
        <div class="unlock-row">
          <input id="passwordInput" type="password" placeholder="Password" />
          <button id="unlockBtn">Unlock</button>
        </div>
      </div>
      <div style="width:100%;max-width:520px;text-align:center;color:#888;margin-top:6px">View-only mode — visitors can't edit.</div>
    `;
    document.getElementById("unlockBtn").onclick = () => tryUnlock(false);
    // Also prompt automatically (friendly prompt)
    setTimeout(() => {
      if (!isAdmin) {
        const p = prompt("Enter password to enable edit mode (or Cancel to view only):");
        if (p !== null) tryUnlock(true, p);
      }
    }, 250);
  } else {
    // Admin UI: image + text + save + lock
    controls.innerHTML = `
      <input type="file" id="imageInput" accept="image/*" />
      <textarea id="textInput" placeholder="Write your vanity thought here..."></textarea>
      <div style="display:flex;gap:8px;flex-wrap:wrap;justify-content:center;width:100%;">
        <button id="saveBtn">Save Card</button>
        <button id="lockBtn" class="secondary">Lock Editing</button>
        <button id="exportBtn" class="secondary">Export JSON</button>
        <button id="importBtn" class="secondary">Import JSON</button>
        <input id="importFile" type="file" accept="application/json" style="display:none"/>
      </div>
    `;
    document.getElementById("saveBtn").onclick = saveCard;
    document.getElementById("lockBtn").onclick = lockAdmin;
    document.getElementById("exportBtn").onclick = exportJSON;
    document.getElementById("importBtn").onclick = () => document.getElementById("importFile").click();
    document.getElementById("importFile").onchange = importJSON;
  }
  console.log("Admin mode:", isAdmin);
}
// Try to unlock — either from prompt or UI
function tryUnlock(fromPrompt=false, providedValue=null) {
  const raw = fromPrompt ? providedValue : (document.getElementById("passwordInput") || {}).value;
  const input = trimSafe(raw);
  if (input === "") return; // nothing entered
  if (input === PASSWORD) {
    isAdmin = true;
    localStorage.setItem(adminKey, "true");
    renderControls();
    renderCards();
    alert("Edit mode unlocked. Welcome back, The Biggest D of All.");
  } else {
    alert("Incorrect password. Access denied.");
  }
}
  const storageKey = "vanity_gallery_cards";
  const accessKey = "vanity_gallery_access";
  const ADMIN_PASSWORD = "OnlytheRealDKnows"; // 👈 change this to your own password
  const ADMIN_PASSWORD = "OnlytheRealDKnows"; // 👈 change this to your password

// Lock editing
function lockAdmin() {
  localStorage.removeItem(adminKey);
  isAdmin = false;
  renderControls();
  renderCards();
  alert("Editing locked.");
}
// Save a card (admin only)
function saveCard() {
  if (!isAdmin) return alert("Editing disabled.");
  const file = (document.getElementById("imageInput") || {}).files?.[0];
  const text = trimSafe((document.getElementById("textInput") || {}).value);
  if (!file && !text) return alert("Add text or an image, mighty D.");
  const reader = new FileReader();
  reader.onload = function(e){
    const imageData = file ? e.target.result : null;
    const newCard = { image: imageData, text, date: new Date().toISOString() };
    cards.push(newCard);
    localStorage.setItem(storageKey, JSON.stringify(cards));
    renderCards();
    // clear inputs
    if (document.getElementById("imageInput")) document.getElementById("imageInput").value = "";
    if (document.getElementById("textInput")) document.getElementById("textInput").value = "";
  let cards = JSON.parse(localStorage.getItem(storageKey)) || [];
  const gallery = document.getElementById("gallery");
  const saveBtn = document.getElementById("saveBtn");
@@ -181,39 +281,60 @@
  const loginDiv = document.getElementById("login");
  const loginBtn = document.getElementById("loginBtn");
  const passwordInput = document.getElementById("passwordInput");
  const logoutBtn = document.getElementById("logoutBtn");
  // 🔐 Login handler
  // Login handler
  loginBtn.onclick = () => {
    if (passwordInput.value === ADMIN_PASSWORD) {
      localStorage.setItem(accessKey, "granted");
      loginDiv.style.display = "none";
      controls.style.display = "flex";
      renderCards();
    } else {
      alert("Wrong password. Access denied, pretender of D.");
      alert("Wrong password, mortal.");
    }
  };
  // 🔒 Logout handler
  logoutBtn.onclick = () => {
    localStorage.removeItem(accessKey);
    alert("Locked! Only the chosen D can unlock again.");
    location.reload();
  };
  // Auto-login check
  if (file) reader.readAsDataURL(file);
  else reader.onload();
}
// Render cards
function renderCards() {
  gallery.innerHTML = "";
  if (!cards || cards.length === 0) {
    gallery.innerHTML = "<p style='color:#888;text-align:center;'>No vanity cards yet. Add your first brilliance.</p>";
    return;
  // If previously logged in
  if (localStorage.getItem(accessKey) === "granted") {
    loginDiv.style.display = "none";
    controls.style.display = "flex";
  }
  cards.slice().reverse().forEach((card, idx) => {
    const div = document.createElement("div");
    div.className = "card";
    div.style.display = "flex";
    div.style.flexDirection = "column";
    div.innerHTML = `
      ${card.image ? `<img src="${card.image}" alt="Vanity Image">` : ""}
      <div class="card-content">
        <p>${escapeHtml(card.text)}</p>
        <div class="date">${new Date(card.date).toLocaleString()}</div>
        ${isAdmin ? `<button class="delete-btn" data-index="${cards.length - 1 - idx}">Delete</button>` : ""}
      </div>
    `;
    gallery.appendChild(div);
  });
  if (isAdmin) {
    document.querySelectorAll(".delete-btn").forEach(btn => {
      btn.onclick = () => {
        const idx = parseInt(btn.dataset.index);
        if (confirm("Delete this card?")) {
          cards.splice(idx, 1);
          localStorage.setItem(storageKey, JSON.stringify(cards));
          renderCards();
        }
  // Save new card
  saveBtn.onclick = () => {
    const file = imageInput.files[0];
    const text = textInput.value.trim();
    if (!file && !text) return alert("At least write something or add a photo, the biggest D.");
    const reader = new FileReader();
    reader.onload = function (e) {
      const imageData = file ? e.target.result : null;
@@ -222,26 +343,64 @@
        text: text,
        date: new Date().toISOString()
      };
    });
  }
}
// Utilities
function escapeHtml(text) {
  if (!text) return "";
  return text.replace(/[&<>"']/g, m => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[m]));
}
// Export / Import JSON helpers (backup)
function exportJSON() {
  const blob = new Blob([JSON.stringify(cards, null, 2)], {type: 'application/json'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = 'vanity_cards_export.json';
  document.body.appendChild(a);
  a.click();
  a.remove();
  URL.revokeObjectURL(url);
}
function importJSON(e) {
  const f = e.target.files?.[0];
  if (!f) return;
  const reader = new FileReader();
  reader.onload = function(ev){
    try {
      const imported = JSON.parse(ev.target.result || '[]');
      if (!Array.isArray(imported)) throw new Error("Invalid");
      // simple merge: append imported
      cards = cards.concat(imported);
      cards.push(newCard);
      localStorage.setItem(storageKey, JSON.stringify(cards));
      renderCards();
      alert("Import successful.");
    } catch (err) {
      alert("Invalid JSON file.");
    }
    document.getElementById("importFile").value = "";
      imageInput.value = "";
      textInput.value = "";
    };
    if (file) reader.readAsDataURL(file);
    else reader.onload(); // For text-only cards
  };
  reader.readAsText(f);
}

/* Initialize UI */
renderControls();
renderCards();
  // Render cards
  function renderCards() {
    gallery.innerHTML = "";
    if (cards.length === 0) {
      gallery.innerHTML = "<p style='color:#888;text-align:center;'>No vanity cards yet. Time to add your first brilliance, the biggest D of all.</p>";
      return;
    }
    const isOwner = localStorage.getItem(accessKey) === "granted";
    cards.slice().reverse().forEach((card, i) => {
      const div = document.createElement("div");
      div.className = "card";
@@ -255,7 +414,6 @@
      `;
      gallery.appendChild(div);
    });
    if (isOwner) {
      document.querySelectorAll(".delete-btn").forEach(btn => {
        btn.onclick = () => {
@@ -269,15 +427,12 @@
      });
    }
  }
  function escapeHtml(text) {
    return text.replace(/[&<>"']/g, m => ({
      '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;', "'": '&#39;'
    }[m]));
  }
  renderCards();
</script>

</body>
</html>
