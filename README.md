<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>QuickNotes</title>
  <link rel="stylesheet" href="style.css">
  <script src="script.js" defer></script>
</head>
<body>
  <header>
    <h1>QuickNotes</h1>
    <p>Organize your thoughts simply and efficiently</p>
  </header>

  <main>
    <!-- Task 1 Section 1: Add a note -->
    <section class="card-section">
      <h2>Add a Note</h2>
      <form id="note-form">
        <div class="form-group">
          <label for="note-input">Note Text:</label>
          <input type="text" id="note-input" placeholder="Type your note here...">
        </div>
        <div class="form-group">
          <label for="note-category">Category:</label>
          <select id="note-category">
            <option value="Personal">Personal</option>
            <option value="Work">Work</option>
            <option value="Study">Study</option>
          </select>
        </div>
        <button type="submit" id="add-btn">Add Note</button>
      </form>
      <p id="error-message" class="error"></p>
    </section>

    <!-- Task 1 Section 2: Your notes -->
    <section class="card-section">
      <h2>Your Notes</h2>
      <div class="search-container">
        <input type="text" id="search-input" placeholder="Search notes...">
        <button id="clear-all-btn" type="button">Clear All</button>
      </div>
      <p id="note-count">You have no notes yet.</p>
      <ul id="notes-list"></ul>
    </section>
  </main>

  <footer>
    <p>&copy; 2026 QuickNotes App. Built with Vanilla JavaScript.</p>
  </footer>
</body>
</html>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: Arial, sans-serif;
  background-color: #f4f6f9;
  color: #333333;
  line-height: 1.6;
}

header {
  background-color: #2c3e50;
  color: #ffffff;
  text-align: center;
  padding: 2rem 1rem;
}

header h1 {
  margin-bottom: 0.5rem;
}

main {
  max-width: 700px;
  margin: 2rem auto;
  padding: 0 1rem;
}

.card-section {
  background-color: #ffffff;
  padding: 1.5rem;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  margin-bottom: 2rem;
}

.card-section h2 {
  margin-bottom: 1rem;
  color: #2c3e50;
}

#note-form {
  display: flex;
  gap: 1rem;
  align-items: flex-end;
  flex-wrap: wrap;
}

.form-group {
  display: flex;
  flex-direction: column;
  flex: 1;
  min-width: 150px;
}

.form-group label {
  font-weight: bold;
  margin-bottom: 0.3rem;
}

#note-input,
#note-category,
#search-input {
  padding: 0.6rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 1rem;
  width: 100%;
}

.search-container {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

button {
  padding: 0.6rem 1.2rem;
  background-color: #3498db;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.2s ease;
}

button:hover {
  background-color: #2980b9;
}

#clear-all-btn {
  background-color: #e74c3c;
}

#clear-all-btn:hover {
  background-color: #c0392b;
}

.error {
  color: #e74c3c;
  font-weight: bold;
  margin-top: 0.5rem;
  min-height: 1.2rem;
}

#note-count {
  font-style: italic;
  margin-bottom: 1rem;
  color: #666;
}

#notes-list {
  list-style: none;
}

.note-card {
  padding: 1rem;
  border: 1px solid #e0e0e0;
  border-radius: 6px;
  margin-bottom: 1rem;
  background-color: #fafafa;
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
}

/* Category Left Border Styles */
.category-personal {
  border-left: 6px solid #1abc9c; /* Teal */
}

.category-work {
  border-left: 6px solid #c0392b; /* Dark Red */
}

.category-study {
  border-left: 6px solid #2980b9; /* Blue */
}

.note-content {
  flex: 1;
  margin-right: 1rem;
}

.note-text {
  font-size: 1.05rem;
  margin-bottom: 0.5rem;
  word-break: break-word;
}

.note-meta {
  font-size: 0.85rem;
  color: #7f8c8d;
  display: flex;
  gap: 0.8rem;
}

.note-category-badge {
  font-weight: bold;
  text-transform: uppercase;
}

.delete-btn {
  background-color: #e74c3c;
  padding: 0.4rem 0.8rem;
  font-size: 0.85rem;
}

.delete-btn:hover {
  background-color: #c0392b;
}

footer {
  text-align: center;
  padding: 1.5rem;
  color: #7f8c8d;
  font-size: 0.9rem;
}

/* Responsive Layout */
@media (max-width: 600px) {
  #note-form {
    flex-direction: column;
    align-items: stretch;
  }

  .search-container {
    flex-direction: column;
  }
// DOM Element Selection using querySelector / querySelectorAll
const noteForm = document.querySelector("#note-form");
const noteInput = document.querySelector("#note-input");
const noteCategory = document.querySelector("#note-category");
const searchInput = document.querySelector("#search-input");
const notesList = document.querySelector("#notes-list");
const noteCount = document.querySelector("#note-count");
const errorMessage = document.querySelector("#error-message");
const clearAllBtn = document.querySelector("#clear-all-btn");

// Application State
let notes = JSON.parse(localStorage.getItem("quicknotes_data")) || [];

// Save notes to localStorage
function saveNotes() {
  localStorage.setItem("quicknotes_data", JSON.stringify(notes));
}

// Update Note Count Display
function updateNoteCount(displayedCount) {
  if (displayedCount === 0) {
    noteCount.textContent = "You have no notes yet.";
  } else if (displayedCount === 1) {
    noteCount.textContent = "You have 1 note.";
  } else {
    noteCount.textContent = `You have ${displayedCount} notes.`;
  }
}

// Render Function
function render() {
  notesList.textContent = "";
  const query = searchInput.value.trim().toLowerCase();

  const filteredNotes = notes.filter((note) =>
    note.text.toLowerCase().includes(query)
  );

  updateNoteCount(filteredNotes.length);

  if (filteredNotes.length === 0 && query !== "") {
    const noResultsMsg = document.createElement("li");
    noResultsMsg.textContent = "No notes match your search.";
    noResultsMsg.style.fontStyle = "italic";
    noResultsMsg.style.color = "#666";
    notesList.appendChild(noResultsMsg);
    return;
  }

  filteredNotes.forEach((note) => {
    const li = document.createElement("li");
    li.className = `note-card category-${note.category.toLowerCase()}`;

    const contentDiv = document.createElement("div");
    contentDiv.className = "note-content";

    const textP = document.createElement("p");
    textP.className = "note-text";
    textP.textContent = note.text; // Use textContent for XSS safety

    const metaDiv = document.createElement("div");
    metaDiv.className = "note-meta";

    const categorySpan = document.createElement("span");
    categorySpan.className = "note-category-badge";
    categorySpan.textContent = note.category;

    const dateSpan = document.createElement("span");
    dateSpan.textContent = note.createdAt;

    metaDiv.appendChild(categorySpan);
    metaDiv.appendChild(dateSpan);

    contentDiv.appendChild(textP);
    contentDiv.appendChild(metaDiv);

    const deleteBtn = document.createElement("button");
    deleteBtn.className = "delete-btn";
    deleteBtn.textContent = "Delete";
    deleteBtn.addEventListener("click", () => deleteNote(note.id));

    li.appendChild(contentDiv);
    li.appendChild(deleteBtn);

    notesList.appendChild(li);
  });
}

// Add Note Handler
function handleAddNote(e) {
  e.preventDefault();
  const text = noteInput.value.trim();
  const category = noteCategory.value;

  // Validation
  if (text === "") {
    errorMessage.textContent = "Please type a note first.";
    return;
  }

  if (text.length > 200) {
    errorMessage.textContent = "Notes must be 200 characters or fewer.";
    return;
  }

  errorMessage.textContent = "";

  const newNote = {
    id: Date.now().toString(),
    text: text,
    category: category,
    createdAt: new Date().toLocaleString()
  };

  notes.unshift(newNote);
  saveNotes();
  render();

  noteInput.value = "";
}

// Delete Single Note
function deleteNote(id) {
  notes = notes.filter((note) => note.id !== id);
  saveNotes();
  render();
}

// Bonus: Clear All Notes
function handleClearAll() {
  if (notes.length === 0) return;
  
  if (confirm("Delete all notes?")) {
    notes = [];
    saveNotes();
    render();
  }
}

// Event Listeners
noteForm.addEventListener("submit", handleAddNote);
searchInput.addEventListener("input", render);
clearAllBtn.addEventListener("click", handleClearAll);

// Initial Render on Load
render();
}
