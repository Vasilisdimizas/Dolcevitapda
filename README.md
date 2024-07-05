<!DOCTYPE html>
<html lang="el">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>-----DOLCE VITA PDA-----</title>
    <style>
        /* Το CSS είναι ακριβώς το ίδιο όπως πριν */
        body {
            font-family: Arial, sans-serif;
            line-height: 1.6;
            margin: 20px;
            background-color: #000000;
            position: relative;
        }
        .category {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            margin-bottom: 10px;
        }
        .category button {
            background-color: #007b3a;
            color: white;
            border: none;
            padding: 10px;
            text-align: center;
            text-decoration: none;
            display: inline-block;
            font-size: 16px;
            cursor: pointer;
            width: 150px;
            border-radius: 5px;
        }
        .product {
            display: none;
        }
        .product.active {
            display: block;
            margin-bottom: 20px;
        }
        .product h2 {
            background-color: #333;
            color: white;
            padding: 10px;
            margin: 0;
        }
        .product ul {
            list-style-type: none;
            padding: 0;
        }
        .product li {
            padding: 10px;
            border-bottom: 1px solid #ccc;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .product li button {
            background-color: #4CAF50;
            color: white;
            border: none;
            padding: 5px 10px;
            text-align: center;
            text-decoration: none;
            display: inline-block;
            font-size: 14px;
            margin-left: 10px;
            cursor: pointer;
        }
        #cart {
            background-color: #fff;
            padding: 20px;
            border-radius: 5px;
            margin-top: 20px;
        }
        #cart h2 {
            color: #333;
            font-size: 24px;
            border-bottom: 2px solid #333;
            padding-bottom: 10px;
        }
        #cart-items {
            list-style-type: none;
            padding: 0;
        }
        #cart-items li {
            margin-bottom: 10px;
        }
        #cart-items li button {
            background-color: #f44336;
            color: white;
            border: none;
            padding: 5px 10px;
            text-align: center;
            text-decoration: none;
            display: inline-block;
            font-size: 14px;
            margin-left: 10px;
            cursor: pointer;
        }
        #total {
            font-weight: bold;
        }
        .buttons {
            display: flex;
            justify-content: space-between;
            margin-top: 20px;
        }
        #submit-order, #previous-orders-btn, #paid-orders-btn {
            background-color: #008CBA;
            color: white;
            padding: 10px 20px;
            border: none;
            cursor: pointer;
            font-size: 16px;
            border-radius: 5px;
            width: calc(33.33% - 10px); /* Adjust the width as per your design */
        }
        #submit-order {
            background-color: #008CBA;
        }
        #previous-orders-btn {
            background-color: #f39c12;
        }
        #paid-orders-btn {
            background-color: #4CAF50;
        }
        #order-history {
            display: none;
            background-color: #fff;
            padding: 20px;
            border-radius: 5px;
            margin-top: 20px;
        }
        #order-history h2 {
            color: #333;
            font-size: 24px;
            border-bottom: 2px solid #333;
            padding-bottom: 10px;
        }
        #order-history ul {
            list-style-type: none;
            padding: 0;
        }
        #order-history ul li {
            margin-bottom: 10px;
            border-bottom: 1px solid #ccc;
            padding-bottom: 10px;
        }
        .order {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .order input[type="checkbox"] {
            margin-left: 20px;
        }
        #paid-orders {
            display: none;
            background-color: #fff;
            padding: 20px;
            border-radius: 5px;
            margin-top: 20px;
        }
        #paid-orders h2 {
            color: #333;
            font-size: 24px;
            border-bottom: 2px solid #333;
            padding-bottom: 10px;
        }
        #paid-orders ul {
            list-style-type: none;
            padding: 0;
        }
        #paid-orders ul li {
            margin-bottom: 10px;
            border-bottom: 1px solid #ccc;
            padding-bottom: 10px;
        }
        #paid-total {
            font-weight: bold;
            margin-top: 10px;
        }
        body {
            background-color: #D6EAF8 /* Light yellow color */
        }
    </style>
 <script>
    var orders = []; // Array to store orders
    var paidOrders = []; // Array to store paid orders
    var totalPaidAmount = 0; // Variable to store total paid amount

    function togglePreviousOrders() {
        var previousOrders = document.getElementById('order-history');
        if (previousOrders.style.display === 'none' || previousOrders.style.display === '') {
            previousOrders.style.display = 'block';
            displayPreviousOrders();
        } else {
            previousOrders.style.display = 'none';
        }
    }

    function togglePaidOrders() {
        var paidOrdersDiv = document.getElementById('paid-orders');
        if (paidOrdersDiv.style.display === 'none' || paidOrdersDiv.style.display === '') {
            paidOrdersDiv.style.display = 'block';
            displayPaidOrders();
        } else {
            paidOrdersDiv.style.display = 'none';
        }
    }

    function toggleCategory(category) {
        var products = document.querySelectorAll('.product');
        products.forEach(function(product) {
            product.classList.remove('active');
        });
        var activeProduct = document.getElementById(category);
        if (activeProduct) {
            activeProduct.classList.add('active');
        }
    }

    function changeQuantity(productName, change) {
        let quantityField = document.getElementById('quantity_' + productName);
        let currentQuantity = parseInt(quantityField.textContent);
        let newQuantity = currentQuantity + change;
        if (newQuantity < 1) {
            newQuantity = 1; // Ελάχιστη ποσότητα είναι 1
        }
        quantityField.textContent = newQuantity;
    }

    function addToCart(productName, price) {
        let quantity = parseInt(document.getElementById('quantity_' + productName).textContent);
        let cartItems = document.getElementById('cart-items');
        let newItem = document.createElement('li');
        newItem.textContent = productName + ' - ' + price.toFixed(2) + '€ (Ποσότητα: ' + quantity + ')';
        let removeButton = document.createElement('button');
        removeButton.textContent = 'Αφαίρεση';
        removeButton.onclick = function() {
            cartItems.removeChild(newItem);
            updateTotal();
        };
        newItem.appendChild(removeButton);
        cartItems.appendChild(newItem);
        updateTotal();
    }

    function updateTotal() {
        var cartItems = document.querySelectorAll('#cart-items li');
        var total = 0;
        cartItems.forEach(function(item) {
            var price = parseFloat(item.textContent.split('-')[1].split('€')[0].trim());
            var quantity = parseInt(item.textContent.split('Ποσότητα: ')[1]);
            total += price * quantity;
        });
        document.getElementById('total').textContent = 'Σύνολο: ' + total.toFixed(2) + '€';
    }

    function submitOrder() {
        var cartItems = document.querySelectorAll('#cart-items li');
        var commentInput = document.getElementById('comment');
        var comment = commentInput.value.trim();
        if (cartItems.length > 0) {
            var order = [];
            var orderTotal = 0;
            cartItems.forEach(function(item) {
                var itemName = item.textContent.split(' - ')[0];
                var itemPrice = parseFloat(item.textContent.split(' - ')[1].split('€')[0].trim());
                var itemQuantity = parseInt(item.textContent.split('Ποσότητα: ')[1]);
                for (let i = 0; i < itemQuantity; i++) {
                    order.push({name: itemName, price: itemPrice});
                    orderTotal += itemPrice;
                }
            });
            orders.push({items: order, total: orderTotal, comment: comment});
            document.getElementById('cart-items').innerHTML = ''; // Clear cart
            commentInput.value = ''; // Clear comment input
            updateTotal();
            displayPreviousOrders();
        }
    }

    function displayPreviousOrders() {
        var previousOrdersList = document.querySelector('#order-history ul');
        previousOrdersList.innerHTML = ''; // Clear previous list items

        // Add each order from the orders array to the list
        orders.forEach(function(order, index) {
            var li = document.createElement('li');
            li.classList.add('order');
            var orderText = document.createElement('span');
            orderText.textContent = 'Παραγγελία ' + (index + 1) + ': ' + order.items.map(function(item) {
                return item.name + ' (' + item.price.toFixed(2) + '€)';
            }).join(', ') + ' - Σύνολο: ' + order.total.toFixed(2) + '€' + (order.comment ? ' - Σχόλιο: ' + order.comment : '');

            var checkbox = document.createElement('input');
            checkbox.type = 'checkbox';
            checkbox.onclick = function() {
                markAsPaid(index, checkbox);
            };

            li.appendChild(orderText);
            li.appendChild(checkbox);
            previousOrdersList.appendChild(li);
        });
    }

    function markAsPaid(orderIndex, checkbox) {
        if (checkbox.checked) {
            var order = orders[orderIndex];
            paidOrders.push(order);
            totalPaidAmount += order.total; // Update total paid amount
            orders.splice(orderIndex, 1); // Remove order from orders array
            displayPreviousOrders();
            displayPaidOrders();
        }
    }

    function displayPaidOrders() {
        var paidOrdersList = document.querySelector('#paid-orders ul');
        paidOrdersList.innerHTML = ''; // Clear previous list items

        // Add each paid order from the paidOrders array to the list
        paidOrders.forEach(function(order, index) {
            var li = document.createElement('li');
            li.textContent = 'Πληρωμένη Παραγγελία ' + (index + 1) + ': ' + order.items.map(function(item) {
                return item.name + ' (' + item.price.toFixed(2) + '€)';
            }).join(', ') + ' - Σύνολο: ' + order.total.toFixed(2) + '€' + (order.comment ? ' - Σχόλιο: ' + order.comment : '');
            paidOrdersList.appendChild(li);
        });

        document.getElementById('paid-total').textContent = 'Συνολικές Πληρωμές: ' + totalPaidAmount.toFixed(2) + '€';
    }
</script>
</head>
<body>
    <h1>-----DOLCE VITA PDA-----</h1>
    <!-- Υπόλοιπο περιεχόμενο της σελίδας παραμένει το ίδιο -->

   <!-- Κατηγορίες -->
    <div class="category">
          <button
onclick="toggleCategory('Α')">Α </button>
         <button
onclick="toggleCategory('Β')">Β </button>
         <button
onclick="toggleCategory('Γ')">Γ </button>
        <button onclick="toggleCategory('Φαγητά')">Φαγητά</button>
        <button onclick="toggleCategory('Κοκτέιλ')">Κοκτέιλ</button>
        <button onclick="toggleCategory('Γρανίτες')">Γρανίτες</button>
        <button onclick="toggleCategory('Τσάι')">Τσάι</button>
        <button onclick="toggleCategory('Παγωτά')">Παγωτά</button>
        <button onclick="toggleCategory('Καφέδες')">Καφέδες</button>
        <button onclick="toggleCategory('Μπύρες')">Μπύρες</button>
        <button onclick="toggleCategory('Αναψυκτικά')">Αναψυκτικά</button>
        <button onclick="toggleCategory('Χυμοί')">Χυμοί</button>
            <button
onclick="toggleCategory('Τσίπουρα')">Τσίπουρα </button>
     <button
onclick="toggleCategory('Νερά')">Νερά </button>
    </div>

 

        <!-- Α -->
    <div id="Α" class="product">
        <h2>Α</h2>
     <ul>
    <li>
        Α1 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Α1', -1)">-</button>
            <span id="quantity_Α1">1</span>
            <button onclick="changeQuantity('Α1', 1)">+</button>
        </div>
        <button onclick="addToCart('Α1', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Α2 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Α2', -1)">-</button>
            <span id="quantity_Α2">1</span>
            <button onclick="changeQuantity('Α2', 1)">+</button>
        </div>
        <button onclick="addToCart('Α2', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Α3 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Α3', -1)">-</button>
            <span id="quantity_Α3">1</span>
            <button onclick="changeQuantity('Α3', 1)">+</button>
        </div>
        <button onclick="addToCart('Α3', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Α4 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Α4', -1)">-</button>
            <span id="quantity_Α4">1</span>
            <button onclick="changeQuantity('Α4', 1)">+</button>
        </div>
        <button onclick="addToCart('Α4', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Α5 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Α5', -1)">-</button>
            <span id="quantity_Α5">1</span>
            <button onclick="changeQuantity('Α5', 1)">+</button>
        </div>
        <button onclick="addToCart('Α5', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Α6 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Α6', -1)">-</button>
            <span id="quantity_Α6">1</span>
            <button onclick="changeQuantity('Α6', 1)">+</button>
        </div>
        <button onclick="addToCart('Α6', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Α7 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Α7', -1)">-</button>
            <span id="quantity_Α7">1</span>
            <button onclick="changeQuantity('Α7', 1)">+</button>
        </div>
        <button onclick="addToCart('Α7', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Α8 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Α8', -1)">-</button>
            <span id="quantity_Α8">1</span>
            <button onclick="changeQuantity('Α8', 1)">+</button>
        </div>
        <button onclick="addToCart('Α8', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Α9 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Α9', -1)">-</button>
            <span id="quantity_Α9">1</span>
            <button onclick="changeQuantity('Α9', 1)">+</button>
        </div>
        <button onclick="addToCart('Α9', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Α10 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Α10', -1)">-</button>
            <span id="quantity_Α10">1</span>
            <button onclick="changeQuantity('Α10', 1)">+</button>
        </div>
        <button onclick="addToCart('Α10', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Α11 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Α11', -1)">-</button>
            <span id="quantity_Α11">1</span>
            <button onclick="changeQuantity('Α11', 1)">+</button>
        </div>
        <button onclick="addToCart('Α11', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Α12 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Α12', -1)">-</button>
            <span id="quantity_Α12">1</span>
            <button onclick="changeQuantity('Α12', 1)">+</button>
        </div>
        <button onclick="addToCart('Α12', 0.00)">Προσθήκη</button>
    </li>
</ul>
</div>
      
      <!-- Β -->
    <div id="Β" class="product">
        <h2>Β</h2>
        <ul>
    <li>
        Β1 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Β1', -1)">-</button>
            <span id="quantity_Β1">1</span>
            <button onclick="changeQuantity('Β1', 1)">+</button>
        </div>
        <button onclick="addToCart('Β1', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Β2 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Β2', -1)">-</button>
            <span id="quantity_Β2">1</span>
            <button onclick="changeQuantity('Β2', 1)">+</button>
        </div>
        <button onclick="addToCart('Β2', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Β3 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Β3', -1)">-</button>
            <span id="quantity_Β3">1</span>
            <button onclick="changeQuantity('Β3', 1)">+</button>
        </div>
        <button onclick="addToCart('Β3', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Β4 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Β4', -1)">-</button>
            <span id="quantity_Β4">1</span>
            <button onclick="changeQuantity('Β4', 1)">+</button>
        </div>
        <button onclick="addToCart('Β4', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Β5 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Β5', -1)">-</button>
            <span id="quantity_Β5">1</span>
            <button onclick="changeQuantity('Β5', 1)">+</button>
        </div>
        <button onclick="addToCart('Β5', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Β6 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Β6', -1)">-</button>
            <span id="quantity_Β6">1</span>
            <button onclick="changeQuantity('Β6', 1)">+</button>
        </div>
        <button onclick="addToCart('Β6', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Β7 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Β7', -1)">-</button>
            <span id="quantity_Β7">1</span>
            <button onclick="changeQuantity('Β7', 1)">+</button>
        </div>
        <button onclick="addToCart('Β7', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Β8 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Β8', -1)">-</button>
            <span id="quantity_Β8">1</span>
            <button onclick="changeQuantity('Β8', 1)">+</button>
        </div>
        <button onclick="addToCart('Β8', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Β9 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Β9', -1)">-</button>
            <span id="quantity_Β9">1</span>
            <button onclick="changeQuantity('Β9', 1)">+</button>
        </div>
        <button onclick="addToCart('Β9', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Β10 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Β10', -1)">-</button>
            <span id="quantity_Β10">1</span>
            <button onclick="changeQuantity('Β10', 1)">+</button>
        </div>
        <button onclick="addToCart('Β10', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Β11 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Β11', -1)">-</button>
            <span id="quantity_Β11">1</span>
            <button onclick="changeQuantity('Β11', 1)">+</button>
        </div>
        <button onclick="addToCart('Β11', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Β12 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Β12', -1)">-</button>
            <span id="quantity_Β12">1</span>
            <button onclick="changeQuantity('Β12', 1)">+</button>
        </div>
        <button onclick="addToCart('Β12', 0.00)">Προσθήκη</button>
    </li>
</ul>
</div>
      
      <!-- Γ -->
    <div id="Γ" class="product">
        <h2>Γ</h2>
       <ul>
    <li>
        Γ1 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Γ1', -1)">-</button>
            <span id="quantity_Γ1">1</span>
            <button onclick="changeQuantity('Γ1', 1)">+</button>
        </div>
        <button onclick="addToCart('Γ1', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Γ2 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Γ2', -1)">-</button>
            <span id="quantity_Γ2">1</span>
            <button onclick="changeQuantity('Γ2', 1)">+</button>
        </div>
        <button onclick="addToCart('Γ2', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Γ3 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Γ3', -1)">-</button>
            <span id="quantity_Γ3">1</span>
            <button onclick="changeQuantity('Γ3', 1)">+</button>
        </div>
        <button onclick="addToCart('Γ3', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Γ4 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Γ4', -1)">-</button>
            <span id="quantity_Γ4">1</span>
            <button onclick="changeQuantity('Γ4', 1)">+</button>
        </div>
        <button onclick="addToCart('Γ4', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Γ5 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Γ5', -1)">-</button>
            <span id="quantity_Γ5">1</span>
            <button onclick="changeQuantity('Γ5', 1)">+</button>
        </div>
        <button onclick="addToCart('Γ5', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Γ6 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Γ6', -1)">-</button>
            <span id="quantity_Γ6">1</span>
            <button onclick="changeQuantity('Γ6', 1)">+</button>
        </div>
        <button onclick="addToCart('Γ6', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Γ7 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Γ7', -1)">-</button>
            <span id="quantity_Γ7">1</span>
            <button onclick="changeQuantity('Γ7', 1)">+</button>
        </div>
        <button onclick="addToCart('Γ7', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Γ8 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Γ8', -1)">-</button>
            <span id="quantity_Γ8">1</span>
            <button onclick="changeQuantity('Γ8', 1)">+</button>
        </div>
        <button onclick="addToCart('Γ8', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Γ9 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Γ9', -1)">-</button>
            <span id="quantity_Γ9">1</span>
            <button onclick="changeQuantity('Γ9', 1)">+</button>
        </div>
        <button onclick="addToCart('Γ9', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Γ10 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Γ10', -1)">-</button>
            <span id="quantity_Γ10">1</span>
            <button onclick="changeQuantity('Γ10', 1)">+</button>
        </div>
        <button onclick="addToCart('Γ10', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Γ11 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Γ11', -1)">-</button>
            <span id="quantity_Γ11">1</span>
            <button onclick="changeQuantity('Γ11', 1)">+</button>
        </div>
        <button onclick="addToCart('Γ11', 0.00)">Προσθήκη</button>
    </li>
    <li>
        Γ12 <span class="price">0.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Γ12', -1)">-</button>
            <span id="quantity_Γ12">1</span>
            <button onclick="changeQuantity('Γ12', 1)">+</button>
        </div>
        <button onclick="addToCart('Γ12', 0.00)">Προσθήκη</button>
    </li>
</ul>
</div>
      
        <!-- Τσίπουρα -->
    <div id="Τσίπουρα" class="product">
        <h2>Τσίπουρα</h2>
        <ul>
          <li>
            Καβαρατζή με <span class="price">8.00€</span>
            <div class="quantity">
                <button onclick="changeQuantity('Καβαρατζή με', -1)">-</button>
                <span id="quantity_Καβαρατζή με">1</span>
                <button onclick="changeQuantity('Καβαρατζή με', 1)">+</button>
            </div>
            <button onclick="addToCart('Καβαρατζή με', 8.00)">Προσθήκη</button>
        </li>
        <li>
            Καβαρατζή χωρίς <span class="price">8.00€</span>
            <div class="quantity">
                <button onclick="changeQuantity('Καβαρατζή χωρίς', -1)">-</button>
                <span id="quantity_Καβαρατζή χωρίς">1</span>
                <button onclick="changeQuantity('Καβαρατζή χωρίς', 1)">+</button>
            </div>
            <button onclick="addToCart('Καβαρατζή χωρίς', 8.00)">Προσθήκη</button>
        </li>
        <li>
            Αποστολάκη με <span class="price">8.00€</span>
            <div class="quantity">
                <button onclick="changeQuantity('Αποστολάκη με', -1)">-</button>
                <span id="quantity_Αποστολάκη με">1</span>
                <button onclick="changeQuantity('Αποστολάκη με', 1)">+</button>
            </div>
            <button onclick="addToCart('Αποστολάκη με', 8.00)">Προσθήκη</button>
        </li>
        <li>
            Αποστολάκη χωρίς <span class="price">8.00€</span>
            <div class="quantity">
                <button onclick="changeQuantity('Αποστολάκη χωρίς', -1)">-</button>
                <span id="quantity_Αποστολάκη χωρίς">1</span>
                <button onclick="changeQuantity('Αποστολάκη χωρίς', 1)">+</button>
            </div>
            <button onclick="addToCart('Αποστολάκη χωρίς', 8.00)">Προσθήκη</button>
        </li>
        <li>
            Τυρνάβου με <span class="price">8.00€</span>
            <div class="quantity">
                <button onclick="changeQuantity('Τυρνάβου με', -1)">-</button>
                <span id="quantity_Τυρνάβου με">1</span>
                <button onclick="changeQuantity('Τυρνάβου με', 1)">+</button>
            </div>
            <button onclick="addToCart('Τυρνάβου με', 8.00)">Προσθήκη</button>
        </li>
        <li>
            Τυρνάβου χωρίς <span class="price">8.00€</span>
            <div class="quantity">
                <button onclick="changeQuantity('Τυρνάβου χωρίς', -1)">-</button>
                <span id="quantity_Τυρνάβου χωρίς">1</span>
                <button onclick="changeQuantity('Τυρνάβου χωρίς', 1)">+</button>
            </div>
            <button onclick="addToCart('Τυρνάβου χωρίς', 8.00)">Προσθήκη</button>
        </li>
        <li>
            Τσιλιλή με <span class="price">8.00€</span>
            <div class="quantity">
                <button onclick="changeQuantity('Τσιλιλή με', -1)">-</button>
                <span id="quantity_Τσιλιλή με">1</span>
                <button onclick="changeQuantity('Τσιλιλή με', 1)">+</button>
            </div>
            <button onclick="addToCart('Τσιλιλή με', 8.00)">Προσθήκη</button>
        </li>
        <li>
            Τσιλιλή χωρίς <span class="price">8.00€</span>
            <div class="quantity">
                <button onclick="changeQuantity('Τσιλιλή χωρίς', -1)">-</button>
                <span id="quantity_Τσιλιλή χωρίς">1</span>
                <button onclick="changeQuantity('Τσιλιλή χωρίς', 1)">+</button>
            </div>
            <button onclick="addToCart('Τσιλιλή χωρίς', 8.00)">Προσθήκη</button>
        </li>
        <li>
        Ιδωνικό με <span class="price">8.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Ιδωνικό με', -1)">-</button>
            <span id="quantity_Ιδωνικό με">1</span>
            <button onclick="changeQuantity('Ιδωνικό με', 1)">+</button>
        </div>
        <button onclick="addToCart('Ιδωνικό με', 8.00)">Προσθήκη</button>
    </li>
                 <li>
        Ιδωνικό χωρίς <span class="price">8.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Ιδωνικό χωρίς', -1)">-</button>
            <span id="quantity_Ιδωνικό χωρίς">1</span>
            <button onclick="changeQuantity('Ιδωνικό χωρίς', 1)">+</button>
        </div>
        <button onclick="addToCart('Ιδωνικό χωρίς', 8.00)">Προσθήκη</button>
    </li>
    <li>
        Θεσσαλικό με <span class="price">8.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Θεσσαλικό με', -1)">-</button>
            <span id="quantity_Θεσσαλικό με">1</span>
            <button onclick="changeQuantity('Θεσσαλικό με', 1)">+</button>
        </div>
        <button onclick="addToCart('Θεσσαλικό με', 8.00)">Προσθήκη</button>
    </li>
    <li>
        Θεσσαλικό χωρίς <span class="price">8.00€</span>
        <div class="quantity">
            <button onclick="changeQuantity('Θεσσαλικό χωρίς', -1)">-</button>
            <span id="quantity_Θεσσαλικό χωρίς">1</span>
            <button onclick="changeQuantity('Θεσσαλικό χωρίς', 1)">+</button>
        </div>
        <button onclick="addToCart('Θεσσαλικό χωρίς', 8.00)">Προσθήκη</button>
            </div>
            
    <!-- Φαγητά -->
    <div id="Φαγητά" class="product">
        <h2>Φαγητά</h2>
        <ul>
    <li>Burger <span class="price">7.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Burger', -1)">-</button>
            <span id="quantity_Burger">1</span>
            <button onclick="changeQuantity('Burger', 1)">+</button>
        </div>
        <button onclick="addToCart('Burger', 7.50)">Προσθήκη</button>
    </li>
   <li>Club sandwich <span class="price">6.50€</span> 
    <div class="quantity">
        <button onclick="changeQuantity('Club sandwich', -1)">-</button>
        <span id="quantity_Club sandwich">1</span>
        <button onclick="changeQuantity('Club sandwich', 1)">+</button>
    </div>
    <button onclick="addToCart('Club sandwich', 6.50)">Προσθήκη</button>
</li>
    <li>Καρμπονάρα <span class="price">9.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Καρμπονάρα', -1)">-</button>
            <span id="quantity_Καρμπονάρα">1</span>
            <button onclick="changeQuantity('Καρμπονάρα', 1)">+</button>
        </div>
        <button onclick="addToCart('Καρμπονάρα', 9.00)">Προσθήκη</button>
    </li>
    <li>Κοτοσαλάτα <span class="price">10.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Κοτοσαλάτα', -1)">-</button>
            <span id="quantity_Κοτοσαλάτα">1</span>
            <button onclick="changeQuantity('Κοτοσαλάτα', 1)">+</button>
        </div>
        <button onclick="addToCart('Κοτοσαλάτα', 10.00)">Προσθήκη</button>
    </li>
    <li>Κοτομπουκιές <span class="price">9.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Κοτομπουκιές', -1)">-</button>
            <span id="quantity_Κοτομπουκιές">1</span>
            <button onclick="changeQuantity('Κοτομπουκιές', 1)">+</button>
        </div>
        <button onclick="addToCart('Κοτομπουκιές', 9.00)">Προσθήκη</button>
    </li>
    <li>Μπαγκέτα <span class="price">4.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Μπαγκέτα', -1)">-</button>
            <span id="quantity_Μπαγκέτα">1</span>
            <button onclick="changeQuantity('Μπαγκέτα', 1)">+</button>
        </div>
        <button onclick="addToCart('Μπαγκέτα', 4.00)">Προσθήκη</button>
    </li>
    <li>Τοστ <span class="price">2.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Τοστ', -1)">-</button>
            <span id="quantity_Τοστ">1</span>
            <button onclick="changeQuantity('Τοστ', 1)">+</button>
        </div>
        <button onclick="addToCart('Τοστ', 2.50)">Προσθήκη</button>
    </li>
    <li>Πατάτες <span class="price">4.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Πατάτες', -1)">-</button>
            <span id="quantity_Πατάτες">1</span>
            <button onclick="changeQuantity('Πατάτες', 1)">+</button>
        </div>
        <button onclick="addToCart('Πατάτες', 4.50)">Προσθήκη</button>
    </li>
</ul>
</div>

    <!-- Κοκτέιλ -->
    <div id="Κοκτέιλ" class="product">
        <h2>Κοκτέιλ</h2>
     <ul>
    <li>Μοχίτο <span class="price">8.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Μοχίτο', -1)">-</button>
            <span id="quantity_Μοχίτο">1</span>
            <button onclick="changeQuantity('Μοχίτο', 1)">+</button>
        </div>
        <button onclick="addToCart('Μοχίτο', 8.50)">Προσθήκη</button>
    </li>
    <li>Aperol Spritz <span class="price">8.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Aperol Spritz', -1)">-</button>
            <span id="quantity_Aperol Spritz">1</span>
            <button onclick="changeQuantity('Aperol Spritz', 1)">+</button>
        </div>
        <button onclick="addToCart('Aperol Spritz', 8.50)">Προσθήκη</button>
    </li>
    <li>Ποτά απλά <span class="price">6.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Ποτά απλά', -1)">-</button>
            <span id="quantity_Ποτά απλά">1</span>
            <button onclick="changeQuantity('Ποτά απλά', 1)">+</button>
        </div>
        <button onclick="addToCart('Ποτά απλά', 6.00)">Προσθήκη</button>
    </li>
    <li>Ποτά Special <span class="price">8.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Ποτά Special', -1)">-</button>
            <span id="quantity_Ποτά Special">1</span>
            <button onclick="changeQuantity('Ποτά Special', 1)">+</button>
        </div>
        <button onclick="addToCart('Ποτά Special', 8.00)">Προσθήκη</button>
    </li>
    <li>Μοσχάτο D’asti <span class="price">7.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Μοσχάτο D’asti', -1)">-</button>
            <span id="quantity_Μοσχάτο D’asti">1</span>
            <button onclick="changeQuantity('Μοσχάτο D’asti', 1)">+</button>
        </div>
        <button onclick="addToCart('Μοσχάτο D’asti', 7.00)">Προσθήκη</button>
    </li>
    <li>Ποτήρι κρασί <span class="price">3.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Ποτήρι κρασί', -1)">-</button>
            <span id="quantity_Ποτήρι κρασί">1</span>
            <button onclick="changeQuantity('Ποτήρι κρασί', 1)">+</button>
        </div>
        <button onclick="addToCart('Ποτήρι κρασί', 3.50)">Προσθήκη</button>
    </li>
</ul>
</div>


    <!-- Γρανίτες -->
<div id="Γρανίτες" class="product">
    <h2>Γρανίτες</h2>
<ul>
    <li>Φράουλα <span class="price">4.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Γρανίτα Φράουλα', -1)">-</button>
            <span id="quantity_Γρανίτα Φράουλα">1</span>
            <button onclick="changeQuantity('Γρανίτα Φράουλα', 1)">+</button>
        </div>
        <button onclick="addToCart('Γρανίτα Φράουλα', 4.50)">Προσθήκη</button>
    </li>
    <li>Λεμόνι <span class="price">4.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Γρανίτα Λεμόνι', -1)">-</button>
            <span id="quantity_Γρανίτα Λεμόνι">1</span>
            <button onclick="changeQuantity('Γρανίτα Λεμόνι', 1)">+</button>
        </div>
        <button onclick="addToCart('Γρανίτα Λεμόνι', 4.50)">Προσθήκη</button>
    </li>
    <li>Τσιχλόφουσκα <span class="price">4.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Γρανίτα Τσιχλόφουσκα', -1)">-</button>
            <span id="quantity_Γρανίτα Τσιχλόφουσκα">1</span>
            <button onclick="changeQuantity('Γρανίτα Τσιχλόφουσκα', 1)">+</button>
        </div>
        <button onclick="addToCart('Γρανίτα Τσιχλόφουσκα', 4.50)">Προσθήκη</button>
    </li>
    <li>Φρούτα του Δάσους <span class="price">4.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Γρανίτα Φρούτα του Δάσους', -1)">-</button>
            <span id="quantity_Γρανίτα Φρούτα του Δάσους">1</span>
            <button onclick="changeQuantity('Γρανίτα Φρούτα του Δάσους', 1)">+</button>
        </div>
        <button onclick="addToCart('Γρανίτα Φρούτα του Δάσους', 4.50)">Προσθήκη</button>
    </li>
    <li>Καρπούζι <span class="price">4.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Γρανίτα Καρπούζι', -1)">-</button>
            <span id="quantity_Γρανίτα Καρπούζι">1</span>
            <button onclick="changeQuantity('Γρανίτα Καρπούζι', 1)">+</button>
        </div>
        <button onclick="addToCart('Γρανίτα Καρπούζι', 4.50)">Προσθήκη</button>
    </li>
    <li>+<span class="price">0.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('+', -1)">-</button>
            <span id="quantity_+">1</span>
            <button onclick="changeQuantity('+', 1)">+</button>
        </div>
        <button onclick="addToCart('+', 0.00)">Προσθήκη</button>
    </li>
    <li>Φράουλα <span class="price">0.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Γρανίτα Φράουλα', -1)">-</button>
            <span id="quantity_Γρανίτα Φράουλα">1</span>
            <button onclick="changeQuantity('Γρανίτα Φράουλα', 1)">+</button>
        </div>
        <button onclick="addToCart('Γρανίτα Φράουλα', 0.00)">Προσθήκη</button>
    </li>
    <li>Λεμόνι <span class="price">0.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Γρανίτα Λεμόνι', -1)">-</button>
            <span id="quantity_Γρανίτα Λεμόνι">1</span>
            <button onclick="changeQuantity('Γρανίτα Λεμόνι', 1)">+</button>
        </div>
        <button onclick="addToCart('Γρανίτα Λεμόνι', 0.00)">Προσθήκη</button>
    </li>
    <li>Τσιχλόφουσκα <span class="price">0.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Γρανίτα Τσιχλόφουσκα', -1)">-</button>
            <span id="quantity_Γρανίτα Τσιχλόφουσκα">1</span>
            <button onclick="changeQuantity('Γρανίτα Τσιχλόφουσκα', 1)">+</button>
        </div>
        <button onclick="addToCart('Γρανίτα Τσιχλόφουσκα', 0.00)">Προσθήκη</button>
    </li>
    <li>Φρούτα του Δάσους <span class="price">0.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Γρανίτα Φρούτα του Δάσους', -1)">-</button>
            <span id="quantity_Γρανίτα Φρούτα του Δάσους">1</span>
            <button onclick="changeQuantity('Γρανίτα Φρούτα του Δάσους', 1)">+</button>
        </div>
        <button onclick="addToCart('Γρανίτα Φρούτα του Δάσους', 0.00)">Προσθήκη</button>
    </li>
    <li>Καρπούζι <span class="price">0.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Γρανίτα Καρπούζι', -1)">-</button>
            <span id="quantity_Γρανίτα Καρπούζι">1</span>
            <button onclick="changeQuantity('Γρανίτα Καρπούζι', 1)">+</button>
        </div>
        <button onclick="addToCart('Γρανίτα Καρπούζι', 0.00)">Προσθήκη</button>
    </li>
</ul>
</div>

<!-- Τσάι -->
<div id="Τσάι" class="product">
    <h2>Τσάι</h2>
<ul>
    <li>Τσάι λεμόνι <span class="price">4.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Τσάι λεμόνι', -1)">-</button>
            <span id="quantity_Τσάι λεμόνι">1</span>
            <button onclick="changeQuantity('Τσάι λεμόνι', 1)">+</button>
        </div>
        <button onclick="addToCart('Τσάι λεμόνι', 4.50)">Προσθήκη</button>
    </li>
    <li>Τσάι λεμόνι του βουνού <span class="price">4.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Τσάι λεμόνι του βουνού', -1)">-</button>
            <span id="quantity_Τσάι λεμόνι του βουνού">1</span>
            <button onclick="changeQuantity('Τσάι λεμόνι του βουνού', 1)">+</button>
        </div>
        <button onclick="addToCart('Τσάι λεμόνι του βουνού', 4.50)">Προσθήκη</button>
    </li>
    <li>Ροδάκινο <span class="price">4.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Τσάι Ροδάκινο', -1)">-</button>
            <span id="quantity_Τσάι Ροδάκινο">1</span>
            <button onclick="changeQuantity('Τσάι Ροδάκινο', 1)">+</button>
        </div>
        <button onclick="addToCart('Τσάι Ροδάκινο', 4.50)">Προσθήκη</button>
    </li>
    <li>Φράουλα <span class="price">4.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Τσάι Φράουλα', -1)">-</button>
            <span id="quantity_Τσάι Φράουλα">1</span>
            <button onclick="changeQuantity('Τσάι Φράουλα', 1)">+</button>
        </div>
        <button onclick="addToCart('Τσάι Φράουλα', 4.50)">Προσθήκη</button>
    </li>
</ul>
</div>

<!-- Παγωτά -->
<div id="Παγωτά" class="product">
    <h2>Παγωτά</h2>
<ul>
    <li>Βανίλια <span class="price">3.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Παγωτό Βανίλια', -1)">-</button>
            <span id="quantity_Παγωτό Βανίλια">1</span>
            <button onclick="changeQuantity('Παγωτό Βανίλια', 1)">+</button>
        </div>
        <button onclick="addToCart('Παγωτό Βανίλια', 3.00)">Προσθήκη</button>
    </li>
    <li>Σοκολάτα <span class="price">3.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Παγωτό Σοκολάτα', -1)">-</button>
            <span id="quantity_Παγωτό Σοκολάτα">1</span>
            <button onclick="changeQuantity('Παγωτό Σοκολάτα', 1)">+</button>
        </div>
        <button onclick="addToCart('Παγωτό Σοκολάτα', 3.00)">Προσθήκη</button>
    </li>
    <li>Ανάμικτο <span class="price">3.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Παγωτό Ανάμικτο', -1)">-</button>
            <span id="quantity_Παγωτό Ανάμικτο">1</span>
            <button onclick="changeQuantity('Παγωτό Ανάμικτο', 1)">+</button>
        </div>
        <button onclick="addToCart('Παγωτό Ανάμικτο', 3.00)">Προσθήκη</button>
    </li>
    <li>Χωνάκι <span class="price">0.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Χωνάκι', -1)">-</button>
            <span id="quantity_Χωνάκι">1</span>
            <button onclick="changeQuantity('Χωνάκι', 1)">+</button>
        </div>
        <button onclick="addToCart('Χωνάκι', 0.00)">Προσθήκη</button>
    </li>
    <li>Κυπελλάκι <span class="price">0.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Κυπελλάκι', -1)">-</button>
            <span id="quantity_Κυπελλάκι">1</span>
            <button onclick="changeQuantity('Κυπελλάκι', 1)">+</button>
        </div>
        <button onclick="addToCart('Κυπελλάκι', 0.00)">Προσθήκη</button>
    </li>
</ul>
</div>

<!-- Καφέδες --> <div id="Καφέδες" class="product"> <h2>Καφέδες</h2> <ul> 
<li class="product">Σκέτος<span class="price">0.00€</span> <button onclick="changeQuantity('Σκέτος', -1)">-</button> <span id="quantity_Σκέτος" class="quantity">1</span> <button onclick="changeQuantity('Σκέτος', 1)">+</button> <button onclick="addToCart('Σκέτος', 0.00)">Προσθήκη</button> </li>
<li class="product">Μέτριος<span class="price">0.00€</span> <button onclick="changeQuantity('Μέτριος', -1)">-</button> <span id="quantity_Μέτριος" class="quantity">1</span> <button onclick="changeQuantity('Μέτριος', 1)">+</button> <button onclick="addToCart('Μέτριος', 0.00)">Προσθήκη</button> </li>
<li class="product">Γλυκός<span class="price">0.00€</span> <button onclick="changeQuantity('Γλυκός', -1)">-</button> <span id="quantity_Γλυκός" class="quantity">1</span> <button onclick="changeQuantity('Γλυκός', 1)">+</button> <button onclick="addToCart('Γλυκός', 0.00)">Προσθήκη</button> </li>
<li class="product">Φραπέ <span class="price">4.00€</span> <button onclick="changeQuantity('Φραπέ', -1)">-</button> <span id="quantity_Φραπέ" class="quantity">1</span> <button onclick="changeQuantity('Φραπέ', 1)">+</button> <button onclick="addToCart('Φραπέ', 4.00)">Προσθήκη</button> </li>
<li class="product">Freddo Εσπρέσο <span class="price">4.00€</span> <button onclick="changeQuantity('Freddo Εσπρέσο', -1)">-</button> <span id="quantity_Freddo Εσπρέσο" class="quantity">1</span> <button onclick="changeQuantity('Freddo Εσπρέσο', 1)">+</button> <button onclick="addToCart('Freddo Εσπρέσο', 4.00)">Προσθήκη</button> </li> <li class="product">Freddo Καπουτσίνο <span class="price">4.50€</span> <button onclick="changeQuantity('Freddo Καπουτσίνο', -1)">-</button> <span id="quantity_Freddo Καπουτσίνο" class="quantity">1</span> <button onclick="changeQuantity('Freddo Καπουτσίνο', 1)">+</button> <button onclick="addToCart('Freddo Καπουτσίνο', 4.50)">Προσθήκη</button> </li> <li class="product">Λάτε <span class="price">4.50€</span> <button onclick="changeQuantity('Λάτε', -1)">-</button> <span id="quantity_Λάτε" class="quantity">1</span> <button onclick="changeQuantity('Λάτε', 1)">+</button> <button onclick="addToCart('Λάτε', 4.50)">Προσθήκη</button> </li> <li class="product">Εσπρέσο <span class="price">3.50€</span> <button onclick="changeQuantity('Εσπρέσο', -1)">-</button> <span id="quantity_Εσπρέσο" class="quantity">1</span> <button onclick="changeQuantity('Εσπρέσο', 1)">+</button> <button onclick="addToCart('Εσπρέσο', 3.50)">Προσθήκη</button> </li> <li class="product">Κρύα Σοκολάτα <span class="price">4.50€</span> <button onclick="changeQuantity('Κρύα Σοκολάτα', -1)">-</button> <span id="quantity_Κρύα Σοκολάτα" class="quantity">1</span> <button onclick="changeQuantity('Κρύα Σοκολάτα', 1)">+</button> <button onclick="addToCart('Κρύα Σοκολάτα', 4.50)">Προσθήκη</button> </li> <li class="product">Ελληνικός <span class="price">3.50€</span> <button onclick="changeQuantity('Ελληνικός', -1)">-</button> <span id="quantity_Ελληνικός" class="quantity">1</span> <button onclick="changeQuantity('Ελληνικός', 1)">+</button> <button onclick="addToCart('Ελληνικός', 3.50)">Προσθήκη</button> </li> <li class="product">Cappuccino <span class="price">4.00€</span> <button onclick="changeQuantity('Cappuccino', -1)">-</button> <span id="quantity_Cappuccino" class="quantity">1</span> <button onclick="changeQuantity('Cappuccino', 1)">+</button> <button onclick="addToCart('Cappuccino', 4.00)">Προσθήκη</button> </li> <li class="product">Milkshake <span class="price">5.00€</span> <button onclick="changeQuantity('Milkshake', -1)">-</button> <span id="quantity_Milkshake" class="quantity">1</span> <button onclick="changeQuantity('Milkshake', 1)">+</button> <button onclick="addToCart('Milkshake', 5.00)">Προσθήκη</button> </ul> </div>

<!-- Μπύρες -->
<div id="Μπύρες" class="product">
    <h2>Μπύρες</h2>
<ul>
    <li>ΑΛΦΑ <span class="price">5.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('ΑΛΦΑ', -1)">-</button>
            <span id="quantity_ΑΛΦΑ">1</span>
            <button onclick="changeQuantity('ΑΛΦΑ', 1)">+</button>
        </div>
        <button onclick="addToCart('ΑΛΦΑ', 5.00)">Προσθήκη</button>
    </li>
    <li>Heineken <span class="price">5.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Heineken', -1)">-</button>
            <span id="quantity_Heineken">1</span>
            <button onclick="changeQuantity('Heineken', 1)">+</button>
        </div>
        <button onclick="addToCart('Heineken', 5.00)">Προσθήκη</button>
    </li>
    <li>Fischer <span class="price">5.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Fischer', -1)">-</button>
            <span id="quantity_Fischer">1</span>
            <button onclick="changeQuantity('Fischer', 1)">+</button>
        </div>
        <button onclick="addToCart('Fischer', 5.00)">Προσθήκη</button>
    </li>
    <li>Amstel radler <span class="price">5.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Amstel radler', -1)">-</button>
            <span id="quantity_Amstel radler">1</span>
            <button onclick="changeQuantity('Amstel radler', 1)">+</button>
        </div>
        <button onclick="addToCart('Amstel radler', 5.00)">Προσθήκη</button>
    </li>
    <li>Kaiser <span class="price">5.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Kaiser', -1)">-</button>
            <span id="quantity_Kaiser">1</span>
            <button onclick="changeQuantity('Kaiser', 1)">+</button>
        </div>
        <button onclick="addToCart('Kaiser', 5.00)">Προσθήκη</button>
    </li>
    <li>FIX <span class="price">5.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('FIX', -1)">-</button>
            <span id="quantity_FIX">1</span>
            <button onclick="changeQuantity('FIX', 1)">+</button>
        </div>
        <button onclick="addToCart('FIX', 5.00)">Προσθήκη</button>
    </li>
    <li>Βεργίνα <span class="price">5.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Βεργίνα', -1)">-</button>
            <span id="quantity_Βεργίνα">1</span>
            <button onclick="changeQuantity('Βεργίνα', 1)">+</button>
        </div>
        <button onclick="addToCart('Βεργίνα', 5.00)">Προσθήκη</button>
    </li>
    <li>Erdinger <span class="price">5.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Erdinger', -1)">-</button>
            <span id="quantity_Erdinger">1</span>
            <button onclick="changeQuantity('Erdinger', 1)">+</button>
        </div>
        <button onclick="addToCart('Erdinger', 5.00)">Προσθήκη</button>
    </li>
    <li>Corona <span class="price">5.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Corona', -1)">-</button>
            <span id="quantity_Corona">1</span>
            <button onclick="changeQuantity('Corona', 1)">+</button>
        </div>
        <button onclick="addToCart('Corona', 5.00)">Προσθήκη</button>
    </li>
    <li>Βαρελίσια <span class="price">5.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Βαρελίσια', -1)">-</button>
            <span id="quantity_Βαρελίσια">1</span>
            <button onclick="changeQuantity('Βαρελίσια', 1)">+</button>
        </div>
        <button onclick="addToCart('Βαρελίσια', 5.50)">Προσθήκη</button>
    </li>
</ul>
</div>

<!-- Αναψυκτικά -->
<div id="Αναψυκτικά" class="product">
    <h2>Αναψυκτικά</h2>
    <ul>
    <li>Κόκα Κόλα <span class="price">3.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Κόκα Κόλα', -1)">-</button>
            <span id="quantity_Κόκα Κόλα">1</span>
            <button onclick="changeQuantity('Κόκα Κόλα', 1)">+</button>
        </div>
        <button onclick="addToCart('Κόκα Κόλα', 3.50)">Προσθήκη</button>
    </li>
    <li>Πορτοκαλάδα <span class="price">3.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Fanta Πορτοκαλάδα', -1)">-</button>
            <span id="quantity_Fanta Πορτοκαλάδα">1</span>
            <button onclick="changeQuantity('Fanta Πορτοκαλάδα', 1)">+</button>
        </div>
        <button onclick="addToCart('Fanta Πορτοκαλάδα', 3.50)">Προσθήκη</button>
    </li>
    <li>Λεμονάδα <span class="price">3.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Fanta Λεμονάδα', -1)">-</button>
            <span id="quantity_Fanta Λεμονάδα">1</span>
            <button onclick="changeQuantity('Fanta Λεμονάδα', 1)">+</button>
        </div>
        <button onclick="addToCart('Fanta Λεμονάδα', 3.50)">Προσθήκη</button>
    </li>
    <li>Σόδα Schweppes <span class="price">3.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Σόδα Schweppes', -1)">-</button>
            <span id="quantity_Σόδα Schweppes">1</span>
            <button onclick="changeQuantity('Σόδα Schweppes', 1)">+</button>
        </div>
        <button onclick="addToCart('Σόδα Schweppes', 3.50)">Προσθήκη</button>
    </li>
    <li>Βυσσινάδα Αναψυκτικό<span class="price">3.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Βυσσινάδα Αναψυκτικό', -1)">-</button>
            <span id="quantity_Βυσσινάδα Αναψυκτικό">1</span>
            <button onclick="changeQuantity('Βυσσινάδα Αναψυκτικό', 1)">+</button>
        </div>
        <button onclick="addToCart('Βυσσινάδα Αναψυκτικό', 3.50)">Προσθήκη</button>
    </li>
    <li>Sprite <span class="price">3.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Sprite', -1)">-</button>
            <span id="quantity_Sprite">1</span>
            <button onclick="changeQuantity('Sprite', 1)">+</button>
        </div>
        <button onclick="addToCart('Sprite', 3.50)">Προσθήκη</button>
    </li>
</ul>
</div>

<!-- Χυμοί -->
<div id="Χυμοί" class="product">
    <h2>Χυμοί</h2>
    <ul>
    <li>Χυμός Πορτοκάλι <span class="price">4.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Χυμός Πορτοκάλι', -1)">-</button>
            <span id="quantity_Χυμός Πορτοκάλι">1</span>
            <button onclick="changeQuantity('Χυμός Πορτοκάλι', 1)">+</button>
        </div>
        <button onclick="addToCart('Χυμός Πορτοκάλι', 4.00)">Προσθήκη</button>
    </li>
    <li>Χυμός Φράουλα <span class="price">4.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Χυμός Φράουλα', -1)">-</button>
            <span id="quantity_Χυμός Φράουλα">1</span>
            <button onclick="changeQuantity('Χυμός Φράουλα', 1)">+</button>
        </div>
        <button onclick="addToCart('Χυμός Φράουλα', 4.00)">Προσθήκη</button>
    </li>
    <li>Χυμός Μήλο <span class="price">4.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Χυμός Μήλο', -1)">-</button>
            <span id="quantity_Χυμός Μήλο">1</span>
            <button onclick="changeQuantity('Χυμός Μήλο', 1)">+</button>
        </div>
        <button onclick="addToCart('Χυμός Μήλο', 4.00)">Προσθήκη</button>
    </li>
    <li>Χυμός Βύσσινο <span class="price">4.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Χυμός Βύσσινο', -1)">-</button>
            <span id="quantity_Χυμός Βύσσινο">1</span>
            <button onclick="changeQuantity('Χυμός Βύσσινο', 1)">+</button>
        </div>
        <button onclick="addToCart('Χυμός Βύσσινο', 4.00)">Προσθήκη</button>
    </li>
    <li>Χυμός Ροδάκινο <span class="price">4.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Χυμός Ροδάκινο', -1)">-</button>
            <span id="quantity_Χυμός Ροδάκινο">1</span>
            <button onclick="changeQuantity('Χυμός Ροδάκινο', 1)">+</button>
        </div>
        <button onclick="addToCart('Χυμός Ροδάκινο', 4.00)">Προσθήκη</button>
    </li>
    <li>Χυμός 9 Φρούτων <span class="price">4.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Χυμός 9 Φρούτων', -1)">-</button>
            <span id="quantity_Χυμός 9 Φρούτων">1</span>
            <button onclick="changeQuantity('Χυμός 9 Φρούτων', 1)">+</button>
        </div>
        <button onclick="addToCart('Χυμός 9 Φρούτων', 4.00)">Προσθήκη</button>
    </li>
    <li>Χυμός 9 Κόκκινων Φρούτων <span class="price">4.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Χυμός 9 Κόκκινων Φρούτων', -1)">-</button>
            <span id="quantity_Χυμός 9 Κόκκινων Φρούτων">1</span>
            <button onclick="changeQuantity('Χυμός 9 Κόκκινων Φρούτων', 1)">+</button>
        </div>
        <button onclick="addToCart('Χυμός 9 Κόκκινων Φρούτων', 4.00)">Προσθήκη</button>
    </li>
    <li>Χυμός Μπανάνα <span class="price">4.00€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Χυμός Μπανάνα', -1)">-</button>
            <span id="quantity_Χυμός Μπανάνα">1</span>
            <button onclick="changeQuantity('Χυμός Μπανάνα', 1)">+</button>
        </div>
        <button onclick="addToCart('Χυμός Μπανάνα', 4.00)">Προσθήκη</button>
    </li>
    <li>Φυσικός Χυμός <span class="price">5.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Φυσικός Χυμός', -1)">-</button>
            <span id="quantity_Φυσικός Χυμός">1</span>
            <button onclick="changeQuantity('Φυσικός Χυμός', 1)">+</button>
        </div>
        <button onclick="addToCart('Φυσικός Χυμός', 5.50)">Προσθήκη</button>
    </li>
</ul>
</div>


  <!-- Νερά -->
    <div id="Νερά" class="product">
        <h2>Νερά</h2>
        <ul>
    <li>Μικρό Νερό <span class="price">0.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Μικρό νερό', -1)">-</button>
            <span id="quantity_Μικρό νερό">1</span>
            <button onclick="changeQuantity('Μικρό νερό', 1)">+</button>
        </div>
        <button onclick="addToCart('Μικρό νερό', 0.50)">Προσθήκη</button>
    </li>
    <li>Μεγάλο Νερό <span class="price">1.50€</span> 
        <div class="quantity">
            <button onclick="changeQuantity('Μεγάλο νερό', -1)">-</button>
            <span id="quantity_Μεγάλο νερό">1</span>
            <button onclick="changeQuantity('Μεγάλο νερό', 1)">+</button>
        </div>
        <button onclick="addToCart('Μεγάλο νερό', 1.50)">Προσθήκη</button>
    </li>
</ul>
</div>
     <div id="cart">
    <h2>Καλάθι</h2>
    <ul id="cart-items"></ul>
    <p id="total">Σύνολο: 0.00€</p>
    <textarea id="comment" placeholder="Σχόλιο για την παραγγελία"></textarea>
    <div class="buttons">
        <button id="submit-order" onclick="submitOrder()">Υποβολή Παραγγελίας</button>
        <button id="previous-orders-btn" onclick="togglePreviousOrders()">Προηγούμενες Παραγγελίες</button>
        <button id="paid-orders-btn" onclick="togglePaidOrders()">Πληρωμένες Παραγγελίες</button>
    </div>
</div>

<div id="order-history">
    <h2>Προηγούμενες Παραγγελίες</h2>
    <ul></ul>
</div>

<div id="paid-orders">
    <h2>Πληρωμένες Παραγγελίες</h2>
    <ul></ul>
    <p id="paid-total">Συνολικές Πληρωμές: 0.00€</p>
</div>
    </div>

</body>
</html>
