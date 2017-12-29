---
ID: 133
post_title: Magento 2 ORM 架構淺談 (2)
author: Grayson
post_excerpt: ""
layout: post
permalink: 'http://magento.im/2017/12/29/magento-2-orm-%e6%9e%b6%e6%a7%8b%e6%b7%ba%e8%ab%87-2/'
published: true
post_date: 2017-12-29 17:12:00
---
在上一次的文章中，我們稍微介紹了 ORM 在Magento 內部的關係，也教大家怎麼實作了一個 包含 ORM 的 Model，今天我們就針對 Collection 的部分，介紹一些好用的方法，讓大家在操作資料表的時候，更能夠得心應手唷！
<br>

<h2>什麼是 Collection  ：</h2>

Collection 是 Magento 內的操作資料庫的一種類別，裡面實作了許多資料庫的查詢語句的方法（ Ｍethod ），讓我們用很直觀的方式取得資料庫內資料，並且也不用使用落落長的 SQL 語法，在使用上會比 SQL 方便許多。
<br>

<h2>取得 Collection 的方式：</h2>

在上一篇的文章裡面，我們有提到兩種方式可以取得 Collection ，一種是宣告 Model，透過 getCollection() 的 Method 來取得，另外一種就是直接宣告 Colleciton 類別。如果上一篇沒有看過的朋友，可以先去看完上一篇介紹再過來喲。（<a href="http://magento.im/2017/12/29/magento-2-orm-%E6%9E%B6%E6%A7%8B%E6%B7%BA%E8%AB%87-1/" title="上一篇傳送門">上一篇傳送門</a>)
<br>

<h2>Collection 方法介紹：</h2>

<ul>
<li><code>addFieldToSelect</code> 方法
這是最常用的方法沒有之一，在每個查詢語句的一開始，都會接這個方法來使用，說明在 select 語句中要選擇什麼欄位，或是設定 Alias 都可以在這個方法中操作：</li>
</ul>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
      /*選擇全部欄位*/
        $employeeCollection-&gt;addFieldToSelect('*');

        /*選擇單一欄位*/
        $employeeCollection-&gt;addFieldToSelect('entity_id');

        /*選擇單一欄位，給予別名*/
        $employeeCollection-&gt;addFieldToSelect('entity_id as id');
</code></pre>

<ul>
<li><code>addFieldToFilter</code> 方法
這是在開發中第二常用到的查詢語句，相當於 SQL 的 <code>where</code> 子句，接下來介紹幾種操作的方式，基本上這些方法可以 Cover 掉決大部分的查詢，如果不夠使用，你想要知道更多，可以參考<a href="http://www.tutorialmines.net/addattributetofilter-conditions-in-magento/" title="此篇">此篇</a></li>
</ul>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
     /*查詢 entity_id 等於 (Equals) 1 的資料*/
        $employeeCollection-&gt;addFieldToFilter('entity_id', 1);

        /*查詢 entity_id  Not Equals 1 的資料 */
        $employeeCollection-&gt;addFieldToFilter('entity_id', ['neq' =&gt; '1']);

        /*查詢 entity_id 存在陣列中的資料*/
        $array = [1, 2, 3, 4, 5];
        $employeeCollection-&gt;addFieldToFilter('entity_id', ['in' =&gt; $array]);

        /*查詢 entity_id  Not In  陣列中的資料 */
        $array = [1, 2, 3, 4, 5];
        $employeeCollection-&gt;addFieldToFilter('entity_id', ['nin' =&gt; $array]);

        /*查詢 name  Not null 的資料 */
        $employeeCollection-&gt;addFieldToFilter('name', ['notnull' =&gt; true]);

        /*查詢 entity_id  Greater Than 5 的資料 */
        $employeeCollection-&gt;addFieldToFilter('entity_id', ['gt' =&gt; 5]);

        /*查詢 entity_id  Less Than  5 的資料 */
        $employeeCollection-&gt;addFieldToFilter('entity_id', ['lt' =&gt; 5]);
</code></pre>

<br>

<ul>
<li>Sort 排序的方法</li>
</ul>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
       /* 排序 entity_id  做排序 */
        $employeeCollection-&gt;setOrder('entity_id');

        /* 排序 entity_id  做倒序 */
        $employeeCollection-&gt;setOrder('entity_id','desc');
</code></pre>

<br>

<ul>
<li>分頁的方法
在查詢過程中往往會使用分頁的功能，但是如果是純 SQL 語句，需要自己撰寫 limit 、offset 等等，Collection 裡面也包含了分頁的功能，讓我們不用自己去實作。</li>
</ul>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
       /*設定 每頁顯示幾筆 */
        $employeeCollection-&gt;setPageSize(3);

        /*設定 每頁顯示幾筆 */
        $employeeCollection-&gt;setCurPage(1);
</code></pre>

<br>

<ul>
<li>取得資料
在查詢語句的最後，通常都是以取得 <code>Entity</code> 做結尾，但是除了可以取得 <code>Entity</code> 之外，還有幾種方法如下：</li>
</ul>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
       /**
         * 取得 Collection
         * 若是不使用此方法也可以直接取得 Collection
         **/
        $employeeCollection-&gt;getItems();

        /**
         *  取得 Entity 物件
         * 以本篇為例，會回傳 employee 的 model Entity
         **/
        $employeeCollection-&gt;getFirstItem();

        /* 取得 全部的 ID */
        $employeeCollection-&gt;getAllIds();
</code></pre>

<br>

<h2>Collection 串接方法：</h2>

在看完上述幾種查詢語句之後，是不是覺得在查詢上變的非常方便呢？我們來看看如果多個條件語句組合再一起的時候的實作方式吧！另外在實作的時候，因為 <code>Collecion</code> 大部分的 Method 都是回傳 自己本身 <code>$this</code>  ，所以可以用串接的方式取得資料，這不僅程式看起來非常美觀，而且也大幅增加了閱讀性與可靠性。對於程式的後續維護有很大的幫助。

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
       /*
         * 查詢條件：
         * 性別：男
         * 年齡：大於30
         * 部門別：行銷部
         * 排序：員工id 倒續
         * 每頁數量： 10 筆
         * 當前頁面：第 1 頁
         * */
        $employees = $employeeCollection
            -&gt;addFieldToSelect('*')
            -&gt;addFieldToSelect('gender', '男')
            -&gt;addFieldToSelect('age', ['gt' =&gt; 30])
            -&gt;addFieldToSelect('department', 'marketing')
            -&gt;setOrder('entity_id', 'desc')
            -&gt;setPageSize('10')
            -&gt;setCurPage(1)
            -&gt;getItems();
</code></pre>

<h2>Collection 取得資料：</h2>

一般我們大部分會使用 <code>getItem</code> 的方法取得資料，但是 他回傳的是 <code>Collecion</code> ，要怎麼樣法資料取出來？

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
       /* 取得資料*/
        /* @var $employees \Astralweb\ORM\Model\Employee[] */
        $employees = $employeeCollection
            -&gt;addFieldToSelect('*')
            -&gt;addFieldToSelect('gender', '男')
            -&gt;getItems();

        /* 使用 for 迴圈取得 每個 entity 的資料 */
        foreach ($employees as $employee) {

            print_r($employee-&gt;getData());

        }
</code></pre>

<br>
<code>getData</code> 的方法是將取得的資料轉換成 <code>Array</code> 後丟出來，有時候較難處理。另外也可以把 <code>$employee</code>  當作物件來操作，取資料的方法就會變成：

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
      /* 取得資料*/
        /* @var $employees \Astralweb\ORM\Model\Employee[] */
        $employees = $employeeCollection
            -&gt;addFieldToSelect('*')
            -&gt;addFieldToSelect('gender', '男')
            -&gt;getItems();

        /* 使用 for 迴圈取得 每個 entity 的資料 */
        foreach ($employees as $employee) {
            /*
             * 除了getEntityId 的方法之外
             * 其餘的方法，都是Magento自動從資料表中欄位生成
             * 轉換原則：
             * 1. get+欄位名稱
             * 2.字首改大寫
             * 3.底線移除
             * 4.底線移除後第一個字首也為大寫
             *
             * ex:
             * name =&gt; getName();
             * sec_department =&gt; getSecDepartment();
             * */
            $employee-&gt;getEntityId();
            $employee-&gt;getName();
            $employee-&gt;getAge();
            $employee-&gt;getDepartment();
            $employee-&gt;getGender();
        }
</code></pre>

看出來中間的差異了嗎？
<br><br>

<h2>參考資料</h2>

<ul>
<li><a href="https://github.com/AstralWebTW/ORM-module" title="ㄎ">範例程式碼 Github</a></li>
</ul>