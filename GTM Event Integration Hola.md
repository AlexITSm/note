<?xml version="1.0" encoding="utf-8"?>
<modification>
    <name>GTM Event Integration_02</name>
    <version>1.0</version>
    <author>Google GTM</author>
    <code>gtm_events_integration_02</code>
    <description>Google Tag Manager events integrations</description>

  <file path="catalog/view/theme/*/template/common/header.twig">
        <operation>
            <search><![CDATA[{% for link in links %}]]></search>
            <add position="before"><![CDATA[
                

                <!-- Google Tag Manager Events -->
                <script>
                $(function(){
                    
                    $('body').on('click', '.button-cart, .us-acc-product-btn', function(e) {
                        var parent=$(this).closest(".us-module-item");
                        var quantity = 1;
                        var price = parent.find(".us-module-price-new").text();
                        price = price.replace(",",".");
                        price = price.replace(" ","");
                        var numericPrice = parseFloat(price);
                        var value = numericPrice * quantity;
                        var formattedValue = value.toFixed(2) + " lei";
                        dataLayer.push({
                            'event': 'add_to_cart',
                            'ecommerce': {
                                'value': formattedValue,
                                'currency': 'MDL', // Currency code
                                'items': [
                                    {
                                        'item_name': parent.find(".gtm-name").val(), 
                                        'item_id': parent.find(".gtm-sku").val(), 
                                        'price': formattedValue,  
                                        'currency': 'MDL', 
                                        'quantity': quantity, 
                                        'item_brand': parent.find(".gtm-brand").val(), 
                                        'item_category': parent.find(".gtm-category").val(),  
                                        'index': parent.find('.gtm-index').val() 
                                     }
                                    ]
                            }
                        });
                    });
                    $('body').on('click', '.us-module-buttons-wishlist', function(e) {
                        var parent=$(this).closest(".us-module-item");
                        var quantity = 1;
                        var price = parent.find(".us-module-price-new").text();
                        price = price.replace(",",".");
                        price = price.replace(" ","");
                        var numericPrice = parseFloat(price);
                        var value = numericPrice * quantity;
                        var formattedValue = value.toFixed(2) + " lei";
                        dataLayer.push({
                            'event': 'add_to_wishlist',
                            'ecommerce': {
                                'value': formattedValue,
                                'currency': 'MDL', // Currency code
                                'items': [
                                    {
                                        'item_name': parent.find(".gtm-name").val(), 
                                        'item_id': parent.find(".gtm-sku").val(), 
                                        'price': formattedValue,  
                                        'currency': 'MDL', 
                                        'quantity': quantity, 
                                        'item_brand': parent.find(".gtm-brand").val(), 
                                        'item_category': parent.find(".gtm-category").val(),  
                                        'index': parent.find('.gtm-index').val() 
                                     }
                                    ]
                            }
                        });
                    });
                    $('body').on('click', '.us-cart-del', function(e) {
                        var prod_id=$(this).parent().find('[name="product_id_q"]').val();
                        var quantity=$(this).parent().find('[name="quantity"]').val();
                        $.ajax({
                          url: 'index.php?route=product/product/getGtmData', 
                          type: 'post',
                          data: {
                            'product_id': prod_id
                          },
                          dataType: 'json',
                          success: function(json) {
                            dataLayer.push({
                                'event': 'remove_from_cart',
                                'ecommerce': {
                                    'value': (json.product_info.special?parseFloat(json.product_info.special).toFixed(2):parseFloat(json.product_info.price).toFixed(2))*quantity,
                                    'currency': 'MDL', // Currency code
                                    'items': [
                                        {
                                            'item_name': json.product_names_ro, 
                                            'item_id': json.product_info.sku, 
                                            'price':  (json.product_info.special?parseFloat(json.product_info.special).toFixed(2):parseFloat(json.product_info.price).toFixed(2)),  
                                            'currency': 'MDL', 
                                            'quantity': quantity, 
                                            'item_brand': json.product_info.manufacturer, 
                                            'item_category': json.category_names[0],  
                                            'index': 1 
                                         }
                                        ]
                                    }
                                });
                            }
                        })
                        
                    });
                    $("body").on('click',"#simplecheckout_cart .btn-danger",function(){
                        var data={};
                        var parent=$(this).closest("tr");
                        var prod_id=parent.find('.prod_id').val();
                        var quantity=parent.find('[name*="quantity"]').val();
                        $.ajax({
                          url: 'index.php?route=product/product/getGtmData', 
                          type: 'post',
                          async: false,
                          data: {
                            'product_id': prod_id
                          },
                          dataType: 'json',
                          success: function(json) {
                            json.quantity=quantity;
                              data=json;
                           }
                        })
                        dataLayer.push({
                            'event': 'remove_from_cart',
                            'ecommerce': {
                                'value': (data.product_info.special?parseFloat(data.product_info.special).toFixed(2):parseFloat(data.product_info.price).toFixed(2))*quantity,
                                'currency': 'MDL', // Currency code
                                'items': [
                                    {
                                        'item_name': data.product_names_ro, 
                                        'item_id': data.product_info.sku, 
                                        'price':  (data.product_info.special?parseFloat(data.product_info.special).toFixed(2):parseFloat(data.product_info.price).toFixed(2)),  
                                        'currency': 'MDL', 
                                        'quantity': quantity, 
                                        'item_brand': data.product_info.manufacturer, 
                                        'item_category': data.category_names[0],  
                                        'index': 1 
                                     }
                                    ]
                                }
                          });
                    })

                    $.ajax({
                        type: 'get',
                        dataType: 'html',
                        url: 'index.php?route=octemplates/module/oct_popup_cart',
                        cache: false,
                        success: function(data) {
                            masked('body', false);
                            $(".modal-holder").html(data);
                        }
                    });
                    $('.us-cart-img').on('click', function(e) {
                        dataArray=[];
                        $(".us-cart-item").not(":first").each(function(){
                            var prod_id=$(this).find('[name="product_id_q"]').val();
                            var quantity=$(this).find('[name="quantity"]').val();
                            $.ajax({
                              url: 'index.php?route=product/product/getGtmData', 
                              type: 'post',
                              async: false,
                              data: {
                                'product_id': prod_id
                              },
                              dataType: 'json',
                              success: function(json) {
                                json.quantity=quantity;
                                dataArray.push(json);
                               }

                            })
                        
                        });
                        var total=0; items=[];
                        $.each(dataArray,function(a,b){
                            total += b.quantity*(b.product_info.special?parseFloat(b.product_info.special):parseFloat(b.product_info.price));
                            item={
                                    'item_name': b.product_names_ro, 
                                    'item_id': b.product_info.sku, 
                                    'price':  (b.product_info.special?parseFloat(b.product_info.special).toFixed(2):parseFloat(b.product_info.price).toFixed(2)),  
                                    'currency': 'MDL', 
                                    'quantity': b.quantity, 
                                    'item_brand': b.product_info.manufacturer, 
                                    'item_category': b.category_names[0],  
                                    'index': a+1 
                                 };
                             items.push(item);
                        })
                        dataLayer.push({
                            'event': 'view_cart',
                            'ecommerce': {
                                'value': total,
                                'currency': 'MDL', // Currency code
                                'items': items
                             }
                        });
                    });
                    
                    $('body').on('click', '#us-cart-modal .us-module-btn-green:first', function(e) {
                        dataArray=[];
                        $(".us-cart-item").not(":first").each(function(){
                            var prod_id=$(this).find('[name="product_id_q"]').val();
                            var quantity=$(this).find('[name="quantity"]').val();
                            $.ajax({
                              url: 'index.php?route=product/product/getGtmData', 
                              type: 'post',
                              async: false,
                              data: {
                                'product_id': prod_id
                              },
                              dataType: 'json',
                              success: function(json) {
                                json.quantity=quantity;
                                dataArray.push(json);
                               }
                            })
                        });
                        var total=0; items=[];
                        $.each(dataArray,function(a,b){
                            total += b.quantity*(b.product_info.special?parseFloat(b.product_info.special):parseFloat(b.product_info.price));
                            item={
                                    'item_name': b.product_names_ro, 
                                    'item_id': b.product_info.sku, 
                                    'price':  (b.product_info.special?parseFloat(b.product_info.special).toFixed(2):parseFloat(b.product_info.price).toFixed(2)),  
                                    'currency': 'MDL', 
                                    'quantity': b.quantity, 
                                    'item_brand': b.product_info.manufacturer, 
                                    'item_category': b.category_names[0],  
                                    'index': a+1 
                                 };
                             items.push(item);
                        })
                        dataLayer.push({
                            'event': 'begin_checkout',
                            'ecommerce': {
                                'value': total,
                                'currency': 'MDL', // Currency code
                                'items': items
                             }
                        });
                    });
                    // $('body').on('click', '.us-module-btn-green', function(e) {
                    $("#simplecheckout_customer input").on('blur',function(){
                        var blank=0;
                        $("#simplecheckout_customer input").each(function(){
                            if($(this).val()=="") blank++;
                        })
                        if(blank==0)
                        {
                            dataArray=[];
                            $(".simplecheckout-cart tbody tr").each(function(){
                                var prod_id=$(this).find('.prod_id').val();
                                var quantity=$(this).find('[name*="quantity"]').val();
                                $.ajax({
                                  url: 'index.php?route=product/product/getGtmData', 
                                  type: 'post',
                                  async: false,
                                  data: {
                                    'product_id': prod_id
                                  },
                                  dataType: 'json',
                                  success: function(json) {
                                    json.quantity=quantity;
                                    dataArray.push(json);
                                   }
                                })
                            });
                            var total=0; items=[];
                            $.each(dataArray,function(a,b){
                                total += b.quantity*(b.product_info.special?parseFloat(b.product_info.special):parseFloat(b.product_info.price));
                                item={
                                        'item_name': b.product_names_ro, 
                                        'item_id': b.product_info.sku, 
                                        'price':  (b.product_info.special?parseFloat(b.product_info.special).toFixed(2):parseFloat(b.product_info.price).toFixed(2)),  
                                        'currency': 'MDL', 
                                        'quantity': b.quantity, 
                                        'item_brand': b.product_info.manufacturer, 
                                        'item_category': b.category_names[0],  
                                        'index': a+1 
                                     };
                                 items.push(item);
                            })
                            dataLayer.push({
                                'event': 'add_personal_info',
                                'ecommerce': {
                                    'value': total,
                                    'currency': 'MDL', // Currency code
                                    'items': items
                                 }
                            });
                        // });

                        }
                    })
                    $("body").on('click',"#simplecheckout_shipping input[type=radio]",function(){
                        dataArray=[];
                        $(".simplecheckout-cart tbody tr").each(function(){
                            var prod_id=$(this).find('.prod_id').val();
                            var quantity=$(this).find('[name*="quantity"]').val();
                            $.ajax({
                              url: 'index.php?route=product/product/getGtmData', 
                              type: 'post',
                              async: false,
                              data: {
                                'product_id': prod_id
                              },
                              dataType: 'json',
                              success: function(json) {
                                json.quantity=quantity;
                                dataArray.push(json);
                               }
                            })
                        });
                        var total=0; items=[];
                        $.each(dataArray,function(a,b){
                            total += b.quantity*(b.product_info.special?parseFloat(b.product_info.special):parseFloat(b.product_info.price));
                            item={
                                    'item_name': b.product_names_ro, 
                                    'item_id': b.product_info.sku, 
                                    'price':  (b.product_info.special?parseFloat(b.product_info.special).toFixed(2):parseFloat(b.product_info.price).toFixed(2)),  
                                    'currency': 'MDL', 
                                    'quantity': b.quantity, 
                                    'item_brand': b.product_info.manufacturer, 
                                    'item_category': b.category_names[0],  
                                    'index': a+1 
                                 };
                             items.push(item);
                        })

                        dataLayer.push({
                            'event': 'add_shipping_info',
                            'shipping_tier': $("input[name=shipping_method]:checked").parent().text().trim(), // Shipping method
                            'ecommerce': {
                                'value': total,
                                'currency': 'MDL', // Currency code
                                'items': items
                             }
                        });
                    })
                    $("body").on('change',"#simplecheckout_payment input",function(){                        
                            dataArray=[];
                            $(".simplecheckout-cart tbody tr").each(function(){
                                var prod_id=$(this).find('.prod_id').val();
                                var quantity=$(this).find('[name*="quantity"]').val();
                                $.ajax({
                                  url: 'index.php?route=product/product/getGtmData', 
                                  type: 'post',
                                  async: false,
                                  data: {
                                    'product_id': prod_id
                                  },
                                  dataType: 'json',
                                  success: function(json) {
                                    json.quantity=quantity;
                                    dataArray.push(json);
                                   }
                                })
                            });
                            var total=0; items=[];
                            $.each(dataArray,function(a,b){
                                total += b.quantity*(b.product_info.special?parseFloat(b.product_info.special):parseFloat(b.product_info.price));
                                item={
                                        'item_name': b.product_names_ro, 
                                        'item_id': b.product_info.sku, 
                                        'price':  (b.product_info.special?parseFloat(b.product_info.special).toFixed(2):parseFloat(b.product_info.price).toFixed(2)),  
                                        'currency': 'MDL', 
                                        'quantity': b.quantity, 
                                        'item_brand': b.product_info.manufacturer, 
                                        'item_category': b.category_names[0],  
                                        'index': a+1 
                                     };
                                 items.push(item);
                            })
                            dataLayer.push({
                                'event': 'add_payment_info',
                                'ecommerce': {
                                    'value': total,
                                    'currency': 'MDL', // Currency code
                                    'payment_type': $("#simplecheckout_payment input[name=payment_method]:checked").parent().text().trim(),
                                    'items': items
                                 }
                            });
                    })
                    $("#simplecheckout_button_confirm").on('click',function(){
                        var blank=0;
                        // $("#simplecheckout_payment input").each(function(){
                        //     if($(this).val()=="") blank++;
                        // })
                        if(blank==0)
                        {
                            dataArray=[];
                            $(".simplecheckout-cart tbody tr").each(function(){
                                var prod_id=$(this).find('.prod_id').val();
                                var quantity=$(this).find('[name*="quantity"]').val();
                                $.ajax({
                                  url: 'index.php?route=product/product/getGtmData', 
                                  type: 'post',
                                  async: false,
                                  data: {
                                    'product_id': prod_id
                                  },
                                  dataType: 'json',
                                  success: function(json) {
                                    json.quantity=quantity;
                                    dataArray.push(json);
                                   }
                                })
                            });
                            var total=0; items=[];
                            $.each(dataArray,function(a,b){
                                total += b.quantity*(b.product_info.special?parseFloat(b.product_info.special):parseFloat(b.product_info.price));
                                item={
                                        'item_name': b.product_names_ro, 
                                        'item_id': b.product_info.sku, 
                                        'price':  (b.product_info.special?parseFloat(b.product_info.special).toFixed(2):parseFloat(b.product_info.price).toFixed(2)),  
                                        'currency': 'MDL', 
                                        'quantity': b.quantity, 
                                        'item_brand': b.product_info.manufacturer, 
                                        'item_category': b.category_names[0],  
                                        'index': a+1 
                                     };
                                 items.push(item);
                            })
                            dataLayer.push({
                                'event': 'purchase',
                                'ecommerce': {
                                    'value': total,
                                    'currency': 'MDL', // Currency code
                                    'items': items
                                 }
                            });
                        // });

                        }
                    })
                })
                </script>


                ]]></add>
        </operation>
    </file>

    <file path="catalog/view/theme/*/template/product/product.twig">
        <operation>
            <search><![CDATA[<div id="product-product" class="container">]]></search>
            <add position="after"><![CDATA[
                <script>
                  $(document).ready(function() {
                    dataLayer.push({
                      'event': 'view_item',
                      'ecommerce': {
                        'items': [
                          {
                            'name': '{{ product_names_ro }}',
                            'item_id': '{{ sku }}', 
                            {% if not special %}
                            'price': '{{ price }}',
                            {% else %}
                            'price': '{{ special }}',
                            {% endif %}
                            'currency': '{{ currency }}',
                            'quantity': '1',
                            {% if manufacturer %}
                            "brand": '{{ manufacturer }}',
                            {% endif %}
                            {% if category_names %}
                            {% for category_name in category_names %}
                             'item_category': '{{ category_name }}',
                            {% endfor %}
                            {% endif %}   
                            'index': '{{ product_views }}'
                          }
                        ]
                      }
                    });
                  });

                $('body').on('click', '#button-cart', function(e) {
                    var quantity = $('#input-quantity').val();
                    {% if not special %}
                     var price = '{{ price|replace({'lei': ''})|replace({' ': ''}) }}';
                     var numericPrice = parseFloat(price);
                     var value = numericPrice * quantity;
                     var formattedValue = value.toFixed(2) + " lei";
                     {% else %}
                     var special = '{{ special|replace({'lei': ''})|replace({' ': ''}) }}';
                     var numericSpecial = parseFloat(special);
                     var valueSpecial = numericSpecial * quantity;
                     var formattedValueSpecial = valueSpecial.toFixed(2) + " lei";
                     var quantity=$("#input-quantity").val();
                     {% endif %}
                         dataLayer.push({
                         'event': 'add_to_cart',
                         'ecommerce': {
                            {% if not special %}
                                'value': formattedValue,
                                {% else %}
                                'value': formattedValueSpecial,
                                {% endif %}
                                'currency': 'EUR', // Currency code
                                'items': [
                                    {
                                       'name': '{{ product_names_ro }}',
                                       'item_id': '{{ sku }}', 
                                       {% if not special %}
                                       'price': '{{ price }}',
                                       {% else %}
                                       'price': '{{ special }}',
                                       {% endif %}
                                       'currency': '{{ currency }}',
                                       'quantity': quantity,
                                      {% if manufacturer %}
                                       "brand": '{{ manufacturer }}',
                                       {% endif %}
                                       {% if category_names %}
                                       {% for category_name in category_names %}
                                        'item_category': '{{ category_name }}',
                                       {% endfor %}
                                       {% endif %}   
                                       'index': '{{ product_views }}'
                                     }
                                ]
                            }
                        });
                    });
                    $('body').on('click', '.compare-wishlist-btn', function(e) {
                    var quantity = $('#input-quantity').val();
                    {% if not special %}
                     var price = '{{ price|replace({'lei': ''})|replace({' ': ''}) }}';
                     var numericPrice = parseFloat(price);
                     var value = numericPrice * quantity;
                     var formattedValue = value.toFixed(2) + " lei";
                     {% else %}
                     var special = '{{ special|replace({'lei': ''})|replace({' ': ''}) }}';
                     var numericSpecial = parseFloat(special);
                     var valueSpecial = numericSpecial * quantity;
                     var formattedValueSpecial = valueSpecial.toFixed(2) + " lei";
                     var quantity=$("#input-quantity").val();
                     {% endif %}
                         dataLayer.push({
                         'event': 'add_to_wishlist',
                         'ecommerce': {
                            {% if not special %}
                                'value': formattedValue,
                                {% else %}
                                'value': formattedValueSpecial,
                                {% endif %}
                                'currency': 'EUR', // Currency code
                                'items': [
                                    {
                                       'name': '{{ product_names_ro }}',
                                       'item_id': '{{ sku }}', 
                                       {% if not special %}
                                       'price': '{{ price }}',
                                       {% else %}
                                       'price': '{{ special }}',
                                       {% endif %}
                                       'currency': '{{ currency }}',
                                       'quantity': quantity,
                                      {% if manufacturer %}
                                       "brand": '{{ manufacturer }}',
                                       {% endif %}
                                       {% if category_names %}
                                       {% for category_name in category_names %}
                                        'item_category': '{{ category_name }}',
                                       {% endfor %}
                                       {% endif %}   
                                       'index': '{{ product_views }}'
                                     }
                                ]
                            }
                        });
                    });
                </script>
                ]]></add>
        </operation>
    </file>
    <file path="catalog/controller/product/product.php">
        <operation>
            <search><![CDATA[$data['rating'] = (int)$product_info['rating'];]]></search>
            <add position="after"><![CDATA[
                    $data['product_names_ro'] = $this->model_catalog_product->getProductNames($product_id);

                    $data['currency'] = $this->session->data['currency'];

                    $data['cantitatea'] = $product_info['quantity'];

                
                    $category_names = $this->model_catalog_product->getProductCategoryNames($product_id);
                    $data['category_names'] = $category_names;

                    
                   
                  
                    $product_views = $this->model_catalog_product->getProductViews($product_id);
                   
                    $data['product_views'] = $product_views;
                ]]></add>
        </operation>
        <operation>
            <search><![CDATA[class ControllerProductProduct extends Controller {]]></search>
            <add position="after"><![CDATA[
                public function getGtmData() {
                    $product_id=$_POST['product_id'];
                    $data['product_info']=$this->model_catalog_product->getProduct($product_id);
                    $data['category_names']=$this->model_catalog_product->getProductCategoryNames($product_id);
                    $data['product_names_ro']=$this->model_catalog_product->getProductNames($product_id);
                    echo json_encode($data);
                }
                ]]></add>
        </operation>
    </file>
    <file path="catalog/model/catalog/product.php">
        <operation>
            <search><![CDATA[public function getProductDescriptions($product_id) {]]></search>
            <add position="before"><![CDATA[

                    public function getProductNames($product_id) {
                        $query = $this->db->query("SELECT `name` FROM " . DB_PREFIX . "product_description WHERE product_id = '". (int)$product_id . "' AND language_id = 4");
                        
                        if ($query->num_rows) {
                            return $query->row['name'];
                        } else {
                            return false;
                        }
                    }
                    // var_dump($query->num_rows);
                    public function getProductCategoryNames($product_id) {
                        $query = $this->db->query("SELECT cd.`name` FROM " . DB_PREFIX . "product_to_category p2c LEFT JOIN " . DB_PREFIX . "category_description cd ON (p2c.category_id = cd.category_id) WHERE p2c.product_id = '". (int)$product_id . "' AND cd.language_id = 4");

                        
                        if ($query->num_rows) {
                            $category_names = array();
                            
                            foreach ($query->rows as $row) {
                                $category_names[] = $row['name'];
                            }
                            
                            return $category_names;
                        } else {
                            return false;
                        }
                    }
                    public function getProductViews($product_id) {
                        $query = $this->db->query("SELECT viewed FROM " . DB_PREFIX . "product WHERE product_id = '" . (int)$product_id . "'");
                        if ($query->num_rows) {
                            return $query->row['viewed'];
                        } else {
                            return 0;
                        }
                    }
                ]]></add>
        </operation>
    </file>
    <file path="catalog/view/theme/*/template/product/category.twig">
        <operation>
            <search><![CDATA[{% if product.oct_stickers is not empty%}]]></search>
            <add position="before"><![CDATA[
                <input type="hidden" value="{{loop.index}}" class="gtm-index">
                <input type="hidden" value="{{product.prod_name_ro}}" class="gtm-name">
                <input type="hidden" value="{{product.product_info.sku}}" class="gtm-sku">
                <input type="hidden" value="{{product.product_info.maunfacturer}}" class="gtm-brand">
                <input type="hidden" value="{{product.category_names.0}}" class="gtm-category">
                ]]></add>
        </operation>
    </file>
    <file path="catalog/controller/product/category.php">
        <operation>
            <search><![CDATA[$data['products'][] = array(]]></search>
            <add position="after"><![CDATA[
                'product_info'=> $result,
                    'prod_name_ro'=> $this->model_catalog_product->getProductNames($result['product_id']),
                    'category_names'=>$this->model_catalog_product->getProductCategoryNames($result['product_id']),
                ]]></add>
        </operation>
    </file>

    <file path="catalog/view/theme/*/template/octemplates/module/oct_products_modules.twig">
        <operation>
            <search><![CDATA[<ul class="us-module-buttons-list">]]></search>
            <add position="before"><![CDATA[
                <input type="hidden" value="{{loop.index}}" class="gtm-index">
                <input type="hidden" value="{{product.prod_name_ro}}" class="gtm-name">
                <input type="hidden" value="{{product.product_info.sku}}" class="gtm-sku">
                <input type="hidden" value="{{product.product_info.maunfacturer}}" class="gtm-brand">
                <input type="hidden" value="{{product.category_names.0}}" class="gtm-category">
                ]]></add>
        </operation>
    </file>
    <file path="catalog/controller/extension/module/oct_product_modules.php">
        <operation>
            <search><![CDATA[$data['products'][] = []]></search>
            <add position="after"><![CDATA[
                    'prod_name_ro'=> $this->model_catalog_product->getProductNames($product_info['product_id']),
                    'category_names'=>$this->model_catalog_product->getProductCategoryNames($product_info['product_id']),
                ]]></add>
        </operation>
    </file>


    <file path="catalog/controller/checkout/simplecheckout_cart.php">
        <operation>
            <search><![CDATA['thumb'     => $image,]]></search>
            <add position="after"><![CDATA[
                    'prod_id'   => $product['product_id'],
                ]]></add>
        </operation>
    </file>

    <file path="catalog/view/theme/oct_ultrastore/template/checkout/simplecheckout_cart.twig">
        <operation>
            <search><![CDATA[<td class="name">]]></search>
            <add position="after"><![CDATA[
                    <input type="hidden" value="{{product['prod_id']}}" class="prod_id">
                ]]></add>
        </operation>
    </file>
</modification>