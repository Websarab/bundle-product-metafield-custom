# bundle-product-metafield-custom
# HTML code
{% if product.metafields.bundle.products.size > 0 %}
  <div id="bundle-products">
    <h3>Frequently Bought Together</h3>
    <ul>
      {% for bundled_product in product.metafields.bundle.products.value %}
        <li class="bundle-item" data-id="{{ bundled_product.variants.first.id }}">
          <img src="{{ bundled_product.featured_image | img_url: '100x' }}" alt="{{ bundled_product.title }}" />
          <span>{{ bundled_product.title }}</span>
          <input type="number" value="1" min="1" class="bundle-qty" />
        </li>
      {% endfor %}
    </ul>
    <button id="add-bundle-to-cart">Add Bundle to Cart</button>
  </div>
{% endif %}

// jquery script with ajax

<script>
  $(document).ready(function () {
    $('#add-bundle-to-cart').click(function () {
      var items = [];

      $('#bundle-products .bundle-item').each(function () {
        var id = $(this).data('id');
        var qty = parseInt($(this).find('.bundle-qty').val());
        items.push({ id: id, quantity: qty });
      });

      $.ajax({
        type: 'POST',
        url: '/cart/add.js',
        data: JSON.stringify({ items: items }),
        dataType: 'json',
        contentType: 'application/json',
        success: function (data) {
          alert('Bundle products added to cart!');
          console.log('Added:', data);
        },
        error: function (xhr, status, error) {
          console.error('Add to cart failed:', error);
        }
      });
    });
  });
</script>
