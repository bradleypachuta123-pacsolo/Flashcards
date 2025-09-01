<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Flashcards with Groups</title>
  <style>
    body { font-family: Arial, sans-serif; text-align: center; margin: 20px; background: #f5f5f5; }
    h1 { color: #333; }

    .card {
      width: 300px; height: 180px;
      margin: 20px auto;
      perspective: 1000px;
      cursor: pointer;
    }
    .card-inner {
      position: relative;
      width: 100%; height: 100%;
      text-align: center;
      transition: transform 0.6s;
      transform-style: preserve-3d;
    }
    .card.flipped .card-inner { transform: rotateY(180deg); }
    .card-front, .card-back {
      position: absolute; width: 100%; height: 100%;
      backface-visibility: hidden;
      display: flex; align-items: center; justify-content: center;
      border: 2px solid #333; border-radius: 10px;
      font-size: 1.2em; padding: 10px;
      background: white;
    }
    .card-back { transform: rotateY(180deg); background: #eaeaea; }

    .form-container { margin: 20px 0; }
    input, select, button { padding: 10px; margin: 5px; font-size: 1em; }
    #cards-container { display: flex; flex-wrap: wrap; justify-content: center; }
    #group-list { margin: 15px 0; }
    .group-btn { margin: 5px; padding: 8px 15px; }
  </style>
</head>
<body>
  <h1>Flashcards with Groups</h1>

  <!-- Group creation -->
  <div class="form-container">
    <input id="group-name" placeholder="Enter new group name">
    <button onclick="addGroup()">Add Group</button>
  </div>

  <!-- Group selector -->
  <div id="group-list"></div>

  <!-- Flashcard creation -->
  <div class="form-container">
    <input id="question" placeholder="Enter question">
    <input id="answer" placeholder="Enter answer">
    <button onclick="addFlashcard()">Add Flashcard</button>
  </div>

  <!-- Flashcards display -->
  <div id="cards-container"></div>

  <script>
    let groups = JSON.parse(localStorage.getItem("flashcardGroups")) || {};
    let currentGroup = null;

    function saveGroups() {
      localStorage.setItem("flashcardGroups", JSON.stringify(groups));
    }

    function addGroup() {
      const groupName = document.getElementById("group-name").value.trim();
      if (!groupName) return alert("Please enter a group name");
      if (groups[groupName]) return alert("Group already exists!");
      groups[groupName] = [];
      saveGroups();
      document.getElementById("group-name").value = "";
      renderGroups();
    }

    function selectGroup(name) {
      currentGroup = name;
      renderFlashcards();
    }

    function addFlashcard() {
      if (!currentGroup) return alert("Please select a group first!");
      const question = document.getElementById("question").value.trim();
      const answer = document.getElementById("answer").value.trim();
      if (!question || !answer) return alert("Please enter both question and answer");
      groups[currentGroup].push({ question, answer });
      saveGroups();
      renderFlashcards();
      document.getElementById("question").value = "";
      document.getElementById("answer").value = "";
    }

    function renderGroups() {
      const groupList = document.getElementById("group-list");
      groupList.innerHTML = "<h3>Select a Group:</h3>";
      Object.keys(groups).forEach(name => {
        const btn = document.createElement("button");
        btn.className = "group-btn";
        btn.textContent = name;
        btn.onclick = () => selectGroup(name);
        groupList.appendChild(btn);
      });
    }

    function renderFlashcards() {
      const container = document.getElementById("cards-container");
      container.innerHTML = "";
      if (!currentGroup) return;
      groups[currentGroup].forEach((card, index) => {
        const cardDiv = document.createElement("div");
        cardDiv.className = "card";
        cardDiv.innerHTML = `
          <div class="card-inner">
            <div class="card-front">${card.question}</div>
            <div class="card-back">${card.answer}</div>
          </div>
        `;
        cardDiv.addEventListener("click", () => cardDiv.classList.toggle("flipped"));
        container.appendChild(cardDiv);
      });
    }

    // Initial render
    renderGroups();
  </script>
</body>
</html>
