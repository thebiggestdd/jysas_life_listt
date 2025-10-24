<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Vanity Gallery — The Biggest D of All</title>
<style>
  body {
    margin: 0;
    font-family: Georgia, "Times New Roman", serif;
    background: #fafafa;
    color: #111;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 40px 20px;
  }
  h1 {
    margin-bottom: 10px;
    font-size: 2rem;
  }
  p {
    color: #555;
    margin-bottom: 30px;
  }

  .controls {
    display: flex;
    flex-direction: column;
    align-items: center;
    background: white;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 4px 15px rgba(0,0,0,0.1);
    margin-bottom: 40px;
    width: min(600px, 90%);
  }

  textarea {
    width: 100%;
    height: 100px;
    resize: vertical;
    padding: 10px;
    font-family: Georgia, "Times New Roman", serif;
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 6px;
    margin-top: 10px;
  }

  input[type="file"], input[type="password"] {
    margin: 10px 0;
    padding: 8px;
    border-radius: 6px;
    border: 1px solid #ccc;
    font-family: Georgia, "Times New Roman", serif;
  }

  button {
    background: black;
    color: white;
    border: none;
    padding: 10px 20px;
    border-radius: 6px;
    cursor: pointer;
    font-family: Georgia, "Times New Roman", serif;
  }

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
    overflow: hidden;
    box-shadow: 0 6px 18px rgba(0,0,0,0.1);
    display: flex;
    flex-direction: column;
  }

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
    border: none;
    padding: 5px 10px;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.8rem;
    margin-top: 10px;
  }

  .delete-btn:hover {
    background: #c0392b;
  }
</style>
</head>
<body>

<h1>Vanity Gallery</h1>
<p>Capture and list your daily masterpieces, photo and thought included. :)</p>

<div class="controls"></div>
<div class="gallery" id="gallery"></div>

<script>
  const storageKey = "vanity_gallery_cards";
  const adminKey = "vanity_gallery_admin_mode";
  
  // 🔐 CHANGE THIS to your secret password
  const PASSWORD = "biggestDpassword"; // Example password — change this!

  let cards = JSON.parse(localStorage.getItem(storageKey)) || [];
  const gallery = document.getElementById("gallery");
  const controls = document.querySelector(".controls");

  let isAdmin = localStorage.getItem(adminKey) === "true";

  function renderControls() {
    if (!isAdmin) {
      controls.innerHTML = `
        <input type="password" id="passwordInput" placeholder="Enter password to unlock editing">
        <button id="unlockBtn">Unlock</button>
      `;
      document.getElementById("unlockBtn").onclick = checkPassword;
    } else {
      controls.innerHTML = `
        <input type="file" id="imageInput" accept="image/*">
        <textarea id="textInput" placeholder="Write your vanity thought here..."></textarea>
        <button id="saveBtn">Save Card</button>
        <button id="lockBtn" style="background:#555;">Lock Editing</button>
      `;
      document.getElementById("saveBtn").onclick = saveCard;
      document.getElementById("lockBtn").onclick = lockAdmin;
    }
  }

  function checkPassword() {
    const input = document.getElementById("passwordInput").value;
    if (input === PASSWORD) {
      isAdmin = true;
      localStorage.setItem(adminKey, "true");
      renderControls();
      renderCards();
      alert("Editing unlocked, The Biggest D of All!");
    } else {
      alert("Incorrect password. Begone, fake D.");
    }
  }

  function lockAdmin() {
    localStorage.removeItem(adminKey);
    isAdmin = false;
    renderControls();
    renderCards();
    alert("Editing locked.");
  }

  function saveCard() {
    const file = document.getElementById("imageInput").files[0];
    const text = document.getElementById("textInput").value.trim();
    if (!file && !text) return alert("At least write something or add a photo, The Biggest D.");

    const reader = new FileReader();
    reader.onload = function (e) {
      const imageData = file ? e.target.result : null;
      const newCard = {
        image: imageData,
        text: text,
        date: new Date().toISOString()
      };
      cards.push(newCard);
      localStorage.setItem(storageKey, JSON.stringify(cards));
      renderCards();
      document.getElementById("imageInput").value = "";
      document.getElementById("textInput").value = "";
    };
    if (file) reader.readAsDataURL(file);
    else reader.onload();
  }

  function renderCards() {
    gallery.innerHTML = "";
    if (cards.length === 0) {
      gallery.innerHTML = "<p style='color:#888;text-align:center;'>No vanity cards yet. Time to add your first brilliance, The Biggest D of All.</p>";
      return;
    }
    cards.slice().reverse().forEach((card, i) => {
      const div = document.createElement("div");
      div.className = "card";
      div.innerHTML = `
        ${card.image ? `<img src="${card.image}" alt="Vanity Image">` : ""}
        <div class="card-content">
          <p>${escapeHtml(card.text)}</p>
          <div class="date">${new Date(card.date).toLocaleString()}</div>
          ${isAdmin ? `<button class="delete-btn" data-index="${cards.length - 1 - i}">Delete</button>` : ""}
        </div>
      `;
      gallery.appendChild(div);
    });

    if (isAdmin) {
      document.querySelectorAll(".delete-btn").forEach(btn => {
        btn.onclick = () => {
          const idx = btn.dataset.index;
          if (confirm("Delete this card?")) {
            cards.splice(idx, 1);
            localStorage.setItem(storageKey, JSON.stringify(cards));
            renderCards();
          }
        };
      });
    }
  }

  function escapeHtml(text) {
    return text.replace(/[&<>"']/g, m => ({
      '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;', "'": '&#39;'
    }[m]));
  }

  renderControls();
  renderCards();
</script>

</body>
</html>
