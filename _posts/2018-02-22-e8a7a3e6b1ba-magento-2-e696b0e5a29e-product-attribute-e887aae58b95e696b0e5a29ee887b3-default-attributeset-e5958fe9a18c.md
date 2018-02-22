---
ID: 271
post_title: >
  解決 Magento 2 新增 Product
  Attribute 自動新增至 Default
  AttributeSet 問題
author: Grayson
post_excerpt: ""
layout: post
permalink: 'http://blog.magento.im/2018/02/22/%e8%a7%a3%e6%b1%ba-magento-2-%e6%96%b0%e5%a2%9e-product-attribute-%e8%87%aa%e5%8b%95%e6%96%b0%e5%a2%9e%e8%87%b3-default-attributeset-%e5%95%8f%e9%a1%8c/'
published: true
post_date: 2018-02-22 20:11:14
---
當 Magento 使用上有時候需要多個 AttributeSet 來管理多種類的 Product，但是使用內建的 <code>addAttribute</code> 方法帶入 <code>group</code> 參數的時候，會自動新增至每一個 <code>AttributeSet</code>， 可是偏偏不是每一個 <code>AttributeSet</code> 都需要此屬性。我們今天就來看看到底是怎麼回事！

<h3>適用版本：</h3>

<ul>
<li>Magento 2.0 以上</li>
</ul>

<br>

<h2>新增 Attribute</h2>

通常會寫在 <code>InstallData.php</code> 內，今天我們就不特別說明新增的部分但是在新增的時候，我們首先要把 <code>group</code> 選項移除掉，並且加上 <code>user_defined</code> 選項，並給予 <code>true</code> 值。

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">        $eavSetup-&gt;addAttribute(
           \Magento\Catalog\Model\Product::ENTITY,
            'datetime_example',
            [
                'label' =&gt; 'datetime example',

                //這項要移除～
                'group'=&gt; 'example_group'

                //這項要新增
                'user_defined'=&gt; true,

                'type' =&gt; 'datetime',
                'input' =&gt; 'date',
                'backend' =&gt; Startdate::class,
                'required' =&gt; false,
                'global' =&gt; ScopedAttributeInterface::SCOPE_GLOBAL,
                'visible' =&gt; true,
                'searchable' =&gt; false,
                'filterable' =&gt; false,
                'filterable_in_search' =&gt; false,
                'visible_in_advanced_search' =&gt; false,
                'comparable' =&gt; false,
                'visible_on_front' =&gt; false,
                'used_in_product_listing' =&gt; false,
                'unique' =&gt; false
            ]
        );
</code></pre>

<br>

<h2>原始碼追蹤</h2>

大家一定會很好奇為什麼會這樣自動新增至 <code>Default AttributeSet</code>，原來是因為官方有段程式碼這樣寫：

<blockquote>
  <a href="https://github.com/magento/magento2/blob/2.2-develop/app/code/Magento/Eav/Setup/EavSetup.php#L826">vendor/magento/module-eav/Setup/EavSetup.php:826</a>
</blockquote>

裡面有一個 <code>foreach</code>，會自動新增至所有的 <code>AttributeSet</code>，所以不要官方幫我們新增的話，記得要把 <code>group</code> 選項拿掉即可。

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php"><br />if (!empty($attr['group']) || empty($attr['user_defined'])) {

            $select = $this-&gt;setup-&gt;getConnection()-&gt;select()-&gt;from(
                $this-&gt;setup-&gt;getTable('eav_attribute_set')
            )-&gt;where(
                'entity_type_id = :entity_type_id'
            );

            $sets = $this-&gt;setup-&gt;getConnection()-&gt;fetchAll($select, ['entity_type_id' =&gt; $entityTypeId]);

            foreach ($sets as $set) {
                if (!empty($attr['group'])) {
                    $this-&gt;addAttributeGroup($entityTypeId, $set['attribute_set_id'], $attr['group']);
                    $this-&gt;addAttributeToSet(
                        $entityTypeId,
                        $set['attribute_set_id'],
                        $attr['group'],
                        $code,
                        $sortOrder
                    );
                } else {
                    $this-&gt;addAttributeToSet(
                        $entityTypeId,
                        $set['attribute_set_id'],
                        $this-&gt;_generalGroupName,
                        $code,
                        $sortOrder
                    );
                }
            }
        }
</code></pre>