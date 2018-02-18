---
ID: 226
post_title: Magento EAV Model 介紹 (1)
author: Grayson
post_excerpt: ""
layout: post
permalink: 'http://blog.magento.im/2018/02/18/magento-eav-model-%e4%bb%8b%e7%b4%b9-1/'
published: true
post_date: 2018-02-18 19:31:51
---
在 Magento 的系統裡面，許多東西都是使用 Attribute 所控制，舉凡像是 Catalog Product、Catalog Category 等等，都有 Attribute 可以操作，但是是什麼樣的原因可以達成這樣彈性強大且靈活的設計，它依靠的是背後的 EAV  ( Entity Attribute Value ) 的系統。今天，我們就來了解一下這強大的系統吧！

<h2>什麼是 EAV</h2>

在維基百科的定義：

<blockquote>
  Entity-Attribute-Value model (EAV), also known as
  object-attribute-value model and open schema is a data model
  that is used in circumstances where the number of attributes
  (properties, parameters) that can be used to describe a
  thing (an “entity” or “object”) is potentially very vast,
  but the number that will actually apply to a given entity is
  relatively modest. In mathematics, this model is known as a
  sparse matrix.
</blockquote>

Alan Storm 的解釋為：

<blockquote>
  經過一個正規化應用的資料庫結構
</blockquote>

所以，經過以上解釋，我們知道可以 Magento 透過 EAV Model 來存取各種格式的資料，至於資料欄位是如何開設的。我們以 Magento 內建的 <code>catalog_product</code> 為例，對應到的資料表有以下五張，不同形態的 Eav Attribute Value 也會分別存至不同的資料表內。

<ul>
<li><code>catalog_product_entity</code></li>
<li><code>catalog_product_entity_datetime</code></li>
<li><code>catalog_product_entity_int</code></li>
<li><code>catalog_product_entity_decimal</code></li>
<li><code>catalog_product_entity_varchar</code></li>
</ul>

<h2>Entity Type</h2>

<code>Entity</code> 是 Magento 內的一種<code>實體物件</code>，在內部程式中則定義為 <code>Entity Type</code>，而 Magento 內建的 <code>Entity Type</code> 有以下幾種，當然也可以自己手動新增，無論是內建的 Entity Type 或是新增進去的都會存在 <code>eav_entity_type</code> 這張資料表內

<table>
<thead>
<tr>
  <th>Entity Type</th>
  <th>Class</th>
</tr>
</thead>
<tbody>
<tr>
  <td>customer</td>
  <td>Magento\Customer\Model\ResourceModel\Customer</td>
</tr>
<tr>
  <td>customer_address</td>
  <td>Magento\Customer\Model\ResourceModel\Address</td>
</tr>
<tr>
  <td>catalog_category</td>
  <td>Magento\Catalog\Model\ResourceModel\Category</td>
</tr>
<tr>
  <td>catalog_product</td>
  <td>Magento\Catalog\Model\ResourceModel\Product</td>
</tr>
<tr>
  <td>order</td>
  <td>Magento\Sales\Model\ResourceModel\Order</td>
</tr>
<tr>
  <td>invoice</td>
  <td>Magento\Sales\Model\ResourceModel\Order</td>
</tr>
<tr>
  <td>creditmemo</td>
  <td>Magento\Sales\Model\ResourceModel\Order\Creditmemo</td>
</tr>
<tr>
  <td>shipment</td>
  <td>Magento\Sales\Model\ResourceModel\Order\Shipment</td>
</tr>
</tbody>
</table>

<h2>Attribute 屬性</h2>

Magento 內所有的 Attribute 皆會對應到 Entity Type，舉凡像是 Catalog Product 的 name、sku、price、inventory 或是 Customer 的 firstname、lastname、email 等等，皆是屬於 Attribute 的一種，所有的 Attribute 都會記錄在 <code>eav_attribute</code> 資料表內。

Attribute 新增的時候，可以透過模組 ( Module ) 內的 <code>InstallData</code> 來新增。而他的參數有以下這些，新增的方法我們將會在下一個篇內詳細的介紹。

<ul>
<li>group</li>
<li>type</li>
<li>backend</li>
<li>frontend</li>
<li>label</li>
<li>input</li>
<li>class</li>
<li>source</li>
<li>global</li>
<li>visible</li>
<li>required</li>
<li>user_defined</li>
<li>default</li>
<li>searchable</li>
<li>filterable</li>
<li>comparable</li>
<li>visible_on_front</li>
<li>used_in_product_listing</li>
<li>unique</li>
<li>apply_to</li>
</ul>

<h2>Attribute Set 屬性集</h2>

Attribute 會存在不同的屬性集 ( Attribute set ) 內，預設都會有 <code>Default</code> 的 Attribute Set，而 Attribute set 儲存在 <code>eav_attribute_set</code> 這張資料表內。

<h2>參考資料</h2>

<ul>
<li><a href="http://inchoo.net/magento/creating-an-eav-based-models-in-magento/" title="Inchoo">Inchoo</a></li>
<li><a href="https://alanstorm.com/magento_advanced_orm_entity_attribute_value_part_1/" title="alanstorm">Alanstorm</a></li>
</ul>