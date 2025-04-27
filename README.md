<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>QuickSell Store</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: linear-gradient(to right, #f0f2f5, #e3f2fd);
      margin: 0;
      padding: 0;
    }

    header {
      background: linear-gradient(to right, #00b4d8, #0077b6);
      color: white;
      padding: 20px;
      text-align: center;
      font-size: 32px;
      font-weight: bold;
      position: relative;
    }

    header img {
      width: 40px;
      position: absolute;
      left: 20px;
      top: 50%;
      transform: translateY(-50%);
    }

    .container {
      padding: 30px;
      max-width: 1000px;
      margin: auto;
    }

    .upload-form {
      background: #ffffff;
      padding: 25px;
      border-radius: 16px;
      box-shadow: 0 6px 18px rgba(0,0,0,0.1);
      margin-bottom: 50px;
    }

    .upload-form h2 {
      text-align: center;
      color: #023e8a;
    }

    input, textarea, button {
      width: 100%;
      margin: 10px 0;
      padding: 14px;
      font-size: 16px;
      border-radius: 10px;
      border: 1px solid #ccc;
    }

    textarea {
      resize: vertical;
    }

    button {
      background: linear-gradient(to right, #48cae4, #00b4d8);
      color: white;
      font-weight: bold;
      border: none;
      cursor: pointer;
      transition: background 0.3s ease;
    }

    button:hover {
      background: linear-gradient(to right, #0077b6, #0096c7);
    }

    .products {
      display: flex;
      flex-wrap: wrap;
      gap: 20px;
      justify-content: center;
    }

    .product-card {
      background: #ffffff;
      width: 260px;
      padding: 18px;
      border-radius: 14px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.1);
      transition: transform 0.2s ease;
    }

    .product-card:hover {
      transform: scale(1.03);
    }

    .product-card img {
      width: 100%;
      height: 180px;
      object-fit: cover;
      border-radius: 10px;
    }

    .product-card .info {
      margin-top: 10px;
    }

    .product-card .info p {
      margin: 5px 0;
      color: #333;
    }

    footer {
      background: linear-gradient(to right, #023e8a, #03045e);
      color: white;
      padding: 35px 20px;
      text-align: center;
      margin-top: 60px;
      border-top-left-radius: 30px;
      border-top-right-radius: 30px;
    }

    footer h3 {
      margin-bottom: 10px;
      font-size: 20px;
      color: #90e0ef;
    }

    footer p {
      margin: 4px 0;
      font-size: 16px;
    }

    footer a {
      color: #90e0ef;
      text-decoration: none;
    }

    footer a:hover {
      text-decoration: underline;
    }

  </style>
</head>
<body>

  <header>
    <img src="https://cdn-icons-png.flaticon.com/512/263/263142.png" alt="Shopping Cart">
    QuickSell - Your Smart Mini Store
  </header>

  <div class="container">

    <div class="upload-form">
      <h2>Upload a Product</h2>
      <input type="file" id="productImage" accept="image/*">
      <input type="text" id="productPrice" placeholder="Enter Price (e.g. $20)">
      <textarea id="productDescription" placeholder="Enter product description..."></textarea>
      <button onclick="uploadProduct()">Post Product</button>
    </div>

    <div class="products" id="productList"></div>

  </div>

  <footer>
    <h3>Contact Us</h3>
    <p>Email: ebbisadame21@gmail.com</p>
    <p>Phone: +251925303361</p>
    <p>Telegram: <a href="https://t.me/ebba2186" target="_blank">@ebba2186</a></p>
    <p>Address: Adama, Ethiopia</p>
    <p>&copy; 2025 QuickSell Store</p>
  </footer>

  <!-- Firebase SDKs -->
  <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-storage-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore-compat.js"></script>

  <script>
    // Replace with your Firebase config
    const firebaseConfig = {
      apiKey: "YOUR_API_KEY",
      authDomain: "YOUR_PROJECT.firebaseapp.com",
      projectId: "YOUR_PROJECT_ID",
      storageBucket: "YOUR_PROJECT.appspot.com",
      messagingSenderId: "SENDER_ID",
      appId: "APP_ID"
    };

    firebase.initializeApp(firebaseConfig);
    const storage = firebase.storage();
    const db = firebase.firestore();

    const adminPassword = "mypassword"; // Change this to your own password

    function uploadProduct() {
      const password = prompt("Enter the admin password:");

      if (password !== adminPassword) {
        alert("You are not authorized to upload.");
        return;
      }

      const file = document.getElementById('productImage').files[0];
      const price = document.getElementById('productPrice').value;
      const description = document.getElementById('productDescription').value;

      if (!file || !price || !description) {
        alert("Please fill all fields.");
        return;
      }

      const ref = storage.ref('products/' + file.name);
      ref.put(file).then(snapshot => snapshot.ref.getDownloadURL())
        .then(url => db.collection('products').add({
          image: url,
          price: price,
          description: description
        }))
        .then(() => {
          alert("Product posted!");
          loadProducts();
          document.getElementById('productImage').value = "";
          document.getElementById('productPrice').value = "";
          document.getElementById('productDescription').value = "";
        });
    }

    function loadProducts() {
      const container = document.getElementById('productList');
      container.innerHTML = "";
      db.collection('products').get().then(snapshot => {
        snapshot.forEach(doc => {
          const data = doc.data();
          const card = document.createElement('div');
          card.className = 'product-card';
          card.innerHTML = `
            <img src="${data.image}">
            <div class="info">
              <p><strong>Price:</strong> ${data.price}</p>
              <p><strong>Description:</strong> ${data.description}</p>
            </div>
          `;
          container.appendChild(card);
        });
      });
    }

    window.onload = loadProducts;
  </script>

</body>
</html>
