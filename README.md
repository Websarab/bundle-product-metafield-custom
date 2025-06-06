# bundle-product-metafield-custom
# HTML code
<div class="custom-bundle">
  {% if product.metafields.custom.bundle_products != blank %}
  <h3>Frequently Bought Together</h3>
  <div class="bundle-products">
    <ul>
    {% for bundled_product in product.metafields.custom.bundle_products.value %}
   <li class="bundle-card" data-id="{{ bundled_product.variants.first.id }}" data-available="{{ bundled_product.variants.first.available }}">

  <a href="{{ bundled_product.url }}">
    {% if bundled_product.featured_image %}
      <img src="{{ bundled_product.featured_image | image_url: width: 300 }}" alt="{{ bundled_product.title }}">
    {% else %}
      <img src="{{ 'placeholder-image.png' | asset_url }}" alt="No Image">
    {% endif %}
    <p>{{ bundled_product.title }}</p>
    <p>{{ bundled_product.price | money }}</p>
  </a>
  <input type="number" value="1" min="1" class="bundle-qty" />
</li>

    {% endfor %}
    </ul>
     <button id="add-bundle-to-cart">Add Bundle to Cart</button>
  </div>
{% endif %}

</div>

// jquery script with ajax

<script>
$(document).ready(function () {
  $('#add-bundle-to-cart').click(function () {
    alert("Product added to the cart");

    var items = [];

    // Loop through each bundle product card
    $('.bundle-card').each(function () {
      var id = $(this).data('id');  // Get the variant ID from the data-id attribute
      var qty = parseInt($(this).find('.bundle-qty').val());  // Get the quantity from the input field
      
      // Validate that the quantity is greater than 0
      if (qty <= 0) {
        alert('Please select a valid quantity for each item.');
        return;  // Skip this product if quantity is not valid
      }

      // If the product has an ID and quantity is valid, push it to the items array
      if (id && qty > 0) {
        items.push({ id: id, quantity: qty });
      }
    });

    // Log the items array to see if everything is correct
    console.log(items);

    // Make the AJAX request to add items to the cart
    $.ajax({
      type: 'POST',
      url: '/cart/add.js',
      data: JSON.stringify({ items: items }),  // Send the items in JSON format
      dataType: 'json',
      contentType: 'application/json',
      success: function (data) {
        alert('Bundle products added to cart!');
        console.log('Added:', data);

       // Update the cart icon bubble with the latest item count
        $.ajax({
          type: 'GET',
          url: '/cart.js',
          dataType: 'json',
          success: function(cart) {
            // Update the cart icon and item count
            $('#cart-icon-bubble').html(`
              <div class="icon-cart">
                <span class="svg-wrapper">
                  <svg xmlns="http://www.w3.org/2000/svg" fill="none" class="icon icon-cart" viewBox="0 0 40 40">
                    <path fill="currentColor" fill-rule="evenodd" d="M20.5 6.5a4.75 4.75 0 0 0-4.75 4.75v.56h-3.16l-.77 11.6a5 5 0 0 0 4.99 5.34h7.38a5 5 0 0 0 4.99-5.33l-.77-11.6h-3.16v-.57A4.75 4.75 0 0 0 20.5 6.5m3.75 5.31v-.56a3.75 3.75 0 1 0-7.5 0v.56zm-7.5 1h7.5v.56a3.75 3.75 0 1 1-7.5 0zm-1 0v.56a4.75 4.75 0 1 0 9.5 0v-.56h2.22l.71 10.67a4 4 0 0 1-3.99 4.27h-7.38a4 4 0 0 1-4-4.27l.72-10.67z"></path>
                  </svg>
                </span>
                <div class="cart-count-bubble">${cart.item_count}</div>
              </div>
            `);
          }
        });
      },
    error: function() {
        console.error('Add to cart failed:', error);
        alert('There was an error adding the bundle to the cart.');
      }
    });
  });

  
});
</script>
