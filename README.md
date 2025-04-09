<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <title>Туркистан Жумгиряти</title>
  <style>
    body { font-family: Arial, sans-serif; background: #f0f0f0; padding: 20px; }
    h1 { text-align: center; }
    .post-form { margin-bottom: 20px; background: white; padding: 15px; border-radius: 10px; }
    textarea, input { width: 100%; padding: 10px; margin-top: 10px; }
    button { padding: 10px 20px; margin-top: 10px; }
    .posts { background: white; padding: 10px; border-radius: 10px; }
    .post { padding: 10px; border-bottom: 1px solid #ddd; }
    .post:last-child { border-bottom: none; }
    .meta { font-size: 0.9em; color: #555; margin-bottom: 5px; }
    img.post-image { max-width: 100%; margin-top: 10px; border-radius: 8px; }
  </style>
</head>
<body>
  <h1>Туркистан Жумгиряти</h1>
  <div class="post-form">
    <input type="text" id="author" placeholder="Ваше имя" />
    <textarea id="postContent" placeholder="Напишите ваш пост..."></textarea>
    <input type="file" id="imageInput" accept="image/*" />
    <button onclick="addPost()">Опубликовать</button>
  </div>
  <div class="posts" id="postsContainer"></div>

  <script>
    function loadPosts() {
      const saved = localStorage.getItem('posts');
      if (saved) {
        const posts = JSON.parse(saved);
        posts.reverse().forEach(post => {
          displayPost(post.author, post.content, post.date, post.image);
        });
      }
    }

    function addPost() {
      const author = document.getElementById('author').value.trim() || "Аноним";
      const content = document.getElementById('postContent').value.trim();
      const imageInput = document.getElementById('imageInput');
      if (content === '') return;

      const date = new Date().toLocaleString();

      if (imageInput.files.length > 0) {
        const reader = new FileReader();
        reader.onload = function (e) {
          const imageData = e.target.result;
          saveAndDisplayPost(author, content, date, imageData);
        };
        reader.readAsDataURL(imageInput.files[0]);
      } else {
        saveAndDisplayPost(author, content, date, null);
      }

      document.getElementById('author').value = '';
      document.getElementById('postContent').value = '';
      document.getElementById('imageInput').value = '';
    }

    function saveAndDisplayPost(author, content, date, image) {
      const post = { author, content, date, image };
      let posts = JSON.parse(localStorage.getItem('posts')) || [];
      posts.push(post);
      localStorage.setItem('posts', JSON.stringify(posts));
      displayPost(author, content, date, image, true);
    }

    function displayPost(author, content, date, image, prepend = false) {
      const postDiv = document.createElement('div');
      postDiv.className = 'post';
      postDiv.innerHTML = `
        <div class="meta"><strong>${author}</strong> — <em>${date}</em></div>
        <div>${content}</div>
        ${image ? `<img src="${image}" class="post-image" />` : ''}
      `;
      const container = document.getElementById('postsContainer');
      if (prepend) {
        container.prepend(postDiv);
      } else {
        container.appendChild(postDiv);
      }
    }

    window.onload = loadPosts;
  </script>
</body>
</html>
