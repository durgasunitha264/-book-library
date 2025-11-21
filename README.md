<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Personal Book Library</title>
  <link rel="stylesheet" href="style.css" />
</head>
<body>

  <h1>📚 Personal Book Library</h1>

  <div class="controls">
    <input id="searchInput" type="text" placeholder="Search books..." />

    <select id="categoryFilter">
      <option value="all">All Categories</option>
    </select>
  </div>

  <div class="book-form">
    <input id="bookTitle" type="text" placeholder="Book Title" />
    <input id="bookAuthor" type="text" placeholder="Author" />
    <input id="bookCategory" type="text" placeholder="Category" />
    <button id="addBookBtn">Add Book</button>
  </div>

  <h2>Your Books</h2>
  <ul id="bookList"></ul>

  <h2>Borrowing History</h2>
  <ul id="historyList"></ul>

  <script src="app.js"></script>
</body>
</html>
🎨 style.css
css
Copy code
body {
  font-family: Arial, sans-serif;
  padding: 20px;
  background: #f7f7f7;
}

h1, h2 {
  text-align: center;
}

.controls, .book-form {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 20px;
}

input, select {
  padding: 8px;
  font-size: 16px;
}

button {
  padding: 8px 15px;
  background: #4CAF50;
  color: #fff;
  border: none;
  cursor: pointer;
}

button:hover {
  background: #45a049;
}

#bookList, #historyList {
  list-style: none;
  padding: 0;
  max-width: 600px;
  margin: auto;
}

.book-item, .history-item {
  background: white;
  padding: 12px;
  margin: 5px 0;
  border-radius: 5px;
  display: flex;
  justify-content: space-between;
}

.borrow-btn {
  background: #007bff;
  padding: 6px 10px;
}

.borrow-btn:hover {
  background: #0069d9;
}
⚙️ app.js
javascript
Copy code
// Load from localStorage or create empty lists
let books = JSON.parse(localStorage.getItem("books")) || [];
let history = JSON.parse(localStorage.getItem("history")) || [];

// DOM elements
const bookList = document.getElementById("bookList");
const historyList = document.getElementById("historyList");
const categoryFilter = document.getElementById("categoryFilter");

// Add a book
document.getElementById("addBookBtn").addEventListener("click", () => {
  const title = document.getElementById("bookTitle").value;
  const author = document.getElementById("bookAuthor").value;
  const category = document.getElementById("bookCategory").value;

  if (!title || !author || !category) return alert("Please fill all fields");

  const newBook = { title, author, category };
  books.push(newBook);
  saveBooks();
  updateUI();

  // Clear fields
  document.getElementById("bookTitle").value = "";
  document.getElementById("bookAuthor").value = "";
  document.getElementById("bookCategory").value = "";
});

// Save to localStorage
function saveBooks() {
  localStorage.setItem("books", JSON.stringify(books));
  localStorage.setItem("history", JSON.stringify(history));
}

// Render books
function updateUI() {
  bookList.innerHTML = "";
  historyList.innerHTML = "";

  let filteredBooks = books;

  // Apply search filter
  const searchTerm = document.getElementById("searchInput").value.toLowerCase();
  filteredBooks = filteredBooks.filter(book =>
    book.title.toLowerCase().includes(searchTerm)
  );

  // Apply category filter
  const filterValue = categoryFilter.value;
  if (filterValue !== "all") {
    filteredBooks = filteredBooks.filter(book => book.category === filterValue);
  }

  // Render books
  filteredBooks.forEach((book, index) => {
    const li = document.createElement("li");
    li.className = "book-item";
    li.innerHTML = `
      <span><strong>${book.title}</strong> by ${book.author} (${book.category})</span>
      <button class="borrow-btn" onclick="borrowBook(${index})">Borrow</button>
    `;
    bookList.appendChild(li);
  });

  // Render history
  history.forEach(item => {
    const li = document.createElement("li");
    li.className = "history-item";
    li.textContent = `${item.title} borrowed on ${item.date}`;
    historyList.appendChild(li);
  });

  loadCategories();
}

// Borrow a book
function borrowBook(index) {
  const book = books[index];
  const entry = {
    title: book.title,
    date: new Date().toLocaleDateString()
  };
  history.push(entry);
  saveBooks();
  updateUI();
}

// Load unique categories
function loadCategories() {
  const categories = [...new Set(books.map(b => b.category))];
  categoryFilter.innerHTML = `<option value="all">All Categories</option>`;
  categories.forEach(cat => {
    const option = document.createElement("option");
    option.value = cat;
    option.textContent = cat;
    categoryFilter.appendChild(option);
  });
}

// Search input event
document.getElementById("searchInput").addEventListener("input", updateUI);

// Category filter event
categoryFilter.addEventListener("change", updateUI);

// Initial load
updateUI();
