---
ID: 137
post_title: Magento 2 ORM 架構淺談 (3)
author: Grayson
post_excerpt: ""
layout: post
permalink: 'http://blog.magento.im/2017/12/29/magento-2-orm-%e6%9e%b6%e6%a7%8b%e6%b7%ba%e8%ab%87-3/'
published: true
post_date: 2017-12-29 17:17:41
---
在上一次的文章中，我們介紹了 Collection 中 <code>Select</code> 子句的應用，接下來要介紹的是使用 Model 操作新增（<code>Insert</code>）、修改（<code>Update</code>）及刪除（<code>delete</code>）的方法。

<h2>什麼是 Model  ：</h2>

Model 在 Magento 內可以說是一個 實體<code>Entity</code>，無論是新增、修改、及刪除的方法，都可以透過 <code>Entity</code> 來操作。

<h2>取得 Model 的方式：</h2>

在第一篇的文章裡面，我們有提到兩種方式可以取得 Collection ，一種是宣告 Model，透過 <code>getCollection</code> 的 Method 來取得，另外一種就是直接宣告 Colleciton 類別。而我們今天要使用的 Model ，就是透過宣告 Model 的方式來取得，請看以下的範例

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
        /*
         * Object Manager 是為了示範上較為方便
         * 官方是較不建議這麼做的
         * 在實作過程中，應該使用 DI 的方式注入
         * */
        /* @var $employeeEntity \Astralweb\ORM\Model\Employee */
        $objectManager = ObjectManager::getInstance();
        $employeeEntity = $objectManager-&gt;get('Astralweb\ORM\Model\Employee');
</code></pre>

<br><br>

<h2>使用 Model 新增資料</h2>

需要新增資料，使用 <code>addData()</code> 方法即可，最後要記得使用 <code>save()</code> 方法，這樣才會真正存入資料庫內（切記！）

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?pph
       /*
         * 新增 (Insert) 資料
         * */
        $employeeEntity = $objectManager-&gt;get('Astralweb\ORM\Model\Employee');
        $employeeEntity-&gt;addData([
            'name' =&gt; '王小明',
            'department' =&gt; '工程部',
            'age' =&gt; '18',
            'gender' =&gt; '男',
            'id' =&gt; 'A123456789',
            'phone' =&gt; '02-27926381',
        ])-&gt;save();
</code></pre>

<br>

<h2>使用 Model 刪除資料</h2>

刪除資料有兩種方式，如果已經知道 刪除資料的 <code>id</code> ，可以直接使用 <code>load()</code> 方法，帶入 <code>id</code> 的值，接著使用 <code>delete()</code> 方法刪除資料

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
       /*
        * 刪除 (delete) 資料
        * */
        $employeeEntity = $objectManager-&gt;get('Astralweb\ORM\Model\Employee');
        $employeeEntity-&gt;load(1)-&gt;delete();
</code></pre>

如果要刪除多筆資料，並且配合查詢子句的話，就要使用第二種方法，一樣是透過 Collection 下完查詢條件後刪除，程式範例如下：

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
    /* 使用collection 方法刪除*/
        /* @var $employees \Astralweb\ORM\Model\Employee[] */
        $employees = $employeeCollection
            -&gt;addFieldToSelect('*')
            -&gt;addFieldToSelect('gender', '男')
            -&gt;getItems();

        foreach ($employees as $employee) {
            $employee-&gt;delete();
        }
</code></pre>

<br><br>

<h2>使用 Model 更新資料</h2>

下面範例是使用Model 修改 <code>entityId</code> 等於 <code>1</code>  的資料，如果是多筆資料需要更改，也可以使用 Collection 的方法

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
       /*
         * 更新（update）資料
         * */
        $employeeEntity = $objectManager-&gt;get('Astralweb\ORM\Model\Employee');
        $employeeEntity
            -&gt;load(1)
            -&gt;setData([
                'name' =&gt; '王小明',
                'department' =&gt; '工程部',
            ])-&gt;save();
</code></pre>

大致上介紹到這邊，以上這三篇就是使用 Magento 透過 ORM  的方法來操作資料庫，如果你會了此方法，以後就可以不用純 SQL 來執行唷，不但增加了程式的閱讀性及維護性，也大幅提升了程式的安全性，是防止 SQL Injection 攻擊的好方法

<br><br>

<h2>參考資料</h2>

<ul>
<li><a href="https://github.com/AstralWebTW/ORM-module" title="程式碼 Github">程式碼 Github</a></li>
</ul>