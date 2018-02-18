---
ID: 232
post_title: 'Magento EAV Model 介紹 (2) &#8211; 使用程式新增 Attribute'
author: Grayson
post_excerpt: ""
layout: post
permalink: 'http://blog.magento.im/2018/02/18/magento-eav-model-%e4%bb%8b%e7%b4%b9-2-%e4%bd%bf%e7%94%a8%e7%a8%8b%e5%bc%8f%e6%96%b0%e5%a2%9e-attribute/'
published: true
post_date: 2018-02-18 19:37:16
---
上一個篇幅的文章內，我們介紹了 Eav Model 的關係。今天我們要透過程式的方式來教大家如何新增 Attribute 及 Attribute Set，利用程式的方式新增，不僅可以確保在安裝模組的時候開發機、測試伺服器及正式伺服器內都擁有一樣的 Attribute，也可以更有效的管理 Attribute 唷！

一般來說，在安裝一個新的 Module 的時候， 會使用 <code>upgrade</code> 指令，若讀到這邊不知道指令操作的朋友，可以參考這篇（傳送門）。
<code>Upgrade</code> 指令執行後，會去執行 InstallData 內的程式，而新增 Attribute 的部分就非常適合寫在這邊了。

<h2>Install Data</h2>

我們來新增一個空白的 <code>InsatallData.php</code> 在你所開設的模組內，路徑為：

<blockquote>
  {vendor}/{extension_name}/Setup/InstallData.php
</blockquote>

程式碼如下：

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php 

namespace Vendor\ExtensionName\Setup;

use Magento\Framework\Setup\InstallDataInterface;
use Magento\Framework\Setup\ModuleContextInterface;
use Magento\Framework\Setup\ModuleDataSetupInterface;

class InstallData implements InstallDataInterface
{
    /**
     * Function install
     * @param ModuleDataSetupInterface $setup
     * @param ModuleContextInterface $context
     * @return void
     */
    public function install(ModuleDataSetupInterface $setup, ModuleContextInterface $context)
    {
        //install data here
    }
}
</code></pre>

<h2>EavSetup</h2>

新增檔案後，我們就已經完成了第一步驟了，接下來是使用 <code>Factory</code> 方法產生 <code>eavSetup</code> 的 <code>class</code>，<code>eavSetup</code> 內有各種的 Method，無論是 <code>addAttribute</code>、<code>addAttributeSet</code> 都可以由這個 <code>class</code> 來操作。將以下程式碼貼至 <code>install</code> 的 function 內。

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
         /** @var EavSetup $eavSetup */
        $eavSetup = $this-&gt;eavSetupFactory
            -&gt;create([
                'setup' =&gt; $setup
            ]);
</code></pre>

<h2>新增 Attribute Set</h2>

我們使用剛剛透過 <code>factory</code> 方法產生出來的 <code>eavSetup</code> 來新增一個屬於 <code>catalog_product</code> 的 Attribute set，並且將其命名為 <code>features</code>，使用 <code>addAttributeSet</code> 的方法。

<code>catelog_product</code> 的 <code>namespace</code> 為 <code>\Magento\Catalog\Model\Product</code>，所以可以直接用 static property
靜態變數取得 <code>catelog_product</code> 的字串，不需要自己手動輸入，減少輸入錯誤的風險。

將以下程式碼貼至 <code>install</code> 的 function 內：

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php

$eavSetup-&gt;addAttributeSet(
    \Magento\Catalog\Model\Product::ENTITY,
    'features',
    '1'
);

</code></pre>

<h2>新增 Product Attribute</h2>

我們這邊要使用的是 <code>addAttribute</code> 這個 Method。假如我們要新增一個 <code>is_featured</code> 欄位，一樣使用 <code>eavSetup</code> 來新增。

<ul>
<li>第一個參數：為 <code>entity type</code>，這邊使用 <code>catalog_product</code> 的 <code>namespace</code> 靜態變數代入</li>
<li>第二個參數：為 <code>attribute code</code>，這是一個唯一值，在同一個 <code>entity type</code> 內不能夠被重複使用</li>
<li>第三個參數：為 <code>attribute</code> 屬性的陣列 ( array )，這邊介紹一下幾個重要欄位：

<ul>
<li>group：所屬群組</li>
<li>type：資料型別，決定資料存在哪一個資料表的關鍵</li>
<li>label：在前端顯示的文字</li>
<li>input：在後台的欄位呈現方式</li>
<li>class：對應到的 class ，沒有可以不用寫</li>
<li>default：預設資料為何</li>
<li>searchable：是否可搜尋</li>
<li>comparable：是否可比較</li>
<li>filterable：是否可過濾</li>
</ul></li>
</ul>

參考程式如下：

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
                $eavSetup-&gt;addAttribute(
                \Magento\Catalog\Model\Product::ENTITY,
                'is_featured',
                [
                    'group' =&gt; 'General', 
                    'type' =&gt; 'int', 
                    'backend' =&gt; '',
                    'frontend' =&gt; '',
                    'label' =&gt; 'Is Featured', 
                    'input' =&gt; 'boolean', 
                    'class' =&gt; '',
                    'source' =&gt; 'Magento\Eav\Model\Entity\Attribute\Source\Boolean',
                    'global' =&gt; \Magento\Eav\Model\Entity\Attribute\ScopedAttributeInterface::SCOPE_GLOBAL,
                    'visible' =&gt; true,
                    'required' =&gt; false,
                    'user_defined' =&gt; false,
                    'default' =&gt; '1',
                    'searchable' =&gt; false,
                    'filterable' =&gt; false,
                    'comparable' =&gt; false,
                    'visible_on_front' =&gt; false,
                    'used_in_product_listing' =&gt; false,
                    'unique' =&gt; false,
                    'apply_to' =&gt; ''
                ]
            );
</code></pre>

<h2>新增 Category Attribute</h2>

新增 Category Attribute 的方法跟 Product 的方法其實大同小異。唯一不同的是需要把 <code>Entity type</code> 置換掉。而 <code>catalog_category</code> 的 <code>namespace</code> 為 <code>\Magento\Catalog\Model\Category</code>，我們一樣可以使用靜態變數取得字串 ( String )。

參考程式如下：

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
                $eavSetup-&gt;addAttribute(
                \Magento\Catalog\Model\Category::ENTITY,
                'is_featured',
                [
                    'group' =&gt; 'General', 
                    'type' =&gt; 'int', 
                    'backend' =&gt; '',
                    'frontend' =&gt; '',
                    'label' =&gt; 'Is Featured', 
                    'input' =&gt; 'boolean', 
                    'class' =&gt; '',
                    'source' =&gt; 'Magento\Eav\Model\Entity\Attribute\Source\Boolean',
                    'global' =&gt; \Magento\Eav\Model\Entity\Attribute\ScopedAttributeInterface::SCOPE_GLOBAL,
                    'visible' =&gt; true,
                    'required' =&gt; false,
                    'user_defined' =&gt; false,
                    'default' =&gt; '1',
                    'searchable' =&gt; false,
                    'filterable' =&gt; false,
                    'comparable' =&gt; false,
                    'visible_on_front' =&gt; false,
                    'used_in_product_listing' =&gt; false,
                    'unique' =&gt; false,
                    'apply_to' =&gt; ''
                ]
            );
</code></pre>

<h2>注意事項</h2>

<ul>
<li>Attribute 或是 Attribute set 再新增的時候參數非常繁瑣，常常需要不斷的刪除 module 再 upgrade module 來驗證是否成功</li>
<li>刪除 module 時，要記得至 <code>eav_attribute</code> 資料表內將剛剛新增的 <code>attribute</code> 刪除</li>
<li>Catalog Product 及 Catalog Category 是 Magento 內最完整的 Eav model，若是需要參考，可以優先使用。</li>
</ul>