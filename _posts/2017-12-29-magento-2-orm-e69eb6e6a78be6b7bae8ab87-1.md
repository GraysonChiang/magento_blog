---
ID: 127
post_title: Magento 2 ORM 架構淺談 (1)
author: Grayson
post_excerpt: ""
layout: post
permalink: 'http://magento.im/2017/12/29/magento-2-orm-%e6%9e%b6%e6%a7%8b%e6%b7%ba%e8%ab%87-1/'
published: true
post_date: 2017-12-29 16:51:06
---
Magento 是一個包裝非常完整的 Framework ，除了實踐了許多設計模式（ Design Pattern ）的精神，也還有一些其他的 Pattern 在裡面，像是 ORM 的架構就是其中的一種，今天就來談談 ORM 的相關操作。

<h2>執行環境：</h2>

<ul>
<li>Ubuntu Linux 16.04 LTS</li>
<li>PhpStorm 2017.3
<br></li>
</ul>

<h2>什麼是 ORM</h2>

ORM，全名是 Object-Relational Mapping ( 對象關係映射 )，是一種程式設計模式，用於實現物件導向語言裡不同類型系統的資料之間的轉換。它的作用是在關聯資料庫數據庫和實體（ Entity ）之間作一個封裝，這樣，我們在具體的操作 Object 的時候，就不需要再去和複雜的 SQL 語句打交道，只需簡單的操作對象的 Property和 Method。
<br>

<h2>什麼時候需要使用 ORM</h2>

在開發期間，常常會與資料庫做互動，無論是在 <code>Controller</code>、<code>View</code>、<code>Model</code>裡面，都會有可能使用到資料庫四大功能：新增 ( Insert)、刪除（ Delete ）、查詢（ Select )、修改（ Update ），而透過 ORM 幫我們封裝好的方法，就能夠快速的取得我們想要的資料，或是達到我們想要對資料庫做的操作。

<br>

<h2>建立 Model</h2>

想要使用方便的 ORM 架構，必須先從建立資料表（ Table ）及 Model 開始，建立資料表超過本章的教學範圍，故先從 Model 開始講起。在 Magento 2 架構中，一張資料表原則以對應到一個 Model 為主（當然你想要多個也可以），在建立 Model 的時候，需要產生 三個檔案，以我們的 Model 名稱 Employee 為例，三個 Class 分別如下：

<h4>Class <code>Vender\Module\Model\Employee</code></h4>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-PHP">&lt;?php
namespace Astralweb\ORM\Model;

use \Magento\Framework\Model\AbstractModel;
class Employee extends AbstractModel
{
    /**
     * Initialize resource model
     * @return void
     */
    public function _construct()
    {
        $this-&gt;_init('Astralweb\ORM\Model\ResourceModel\Employee');
    }
}
</code></pre>

<h4>Class <code>Vender\Module\Model\ResourceModel\Employee</code></h4>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
namespace Astralweb\ORM\Model\ResourceModel;

use \Magento\Framework\Model\ResourceModel\Db\AbstractDb;

class Employee extends AbstractDb
{
    /**
     * Initialize resource
     *
     * @return void
     */
    public function _construct()
    {
        $this-&gt;_init('employee_entity', 'entity_id');
    }
}
</code></pre>

<h4>Class <code>Vender\Module\Model\ResourceModel\Employee</code></h4>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php

namespace Astralweb\ORM\Model\ResourceModel\Employee;

use \Magento\Framework\Model\ResourceModel\Db\Collection\AbstractCollection;

class Collection extends AbstractCollection
{
    /**
     * Initialize resource collection
     *
     * @return void
     */
    public function _construct()
    {
        $this-&gt;_init('Astralweb\ORM\Model\Employee', 'Astralweb\ORM\Model\ResourceModel\Employee');
    }
}
</code></pre>

<br>

<h2>取得 ORM Collection：</h2>

Magnento 2 中若要取得 Collection 來操作 ORM 的話，有兩種方式，第一種是先宣告 Model Entity ，由 Entity 中使用 getCollection() 的方法取得，第二種是直接建立一個 Collection Class，我們來看以下兩種在宣告上的差異。

<ul>
<li>宣告 Model Entity 方式</li>
</ul>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
        /*
         * Object Manager 是為了示範上較為方便
         * 官方是較不建議這麼做的
         * 在實作過程中，應該使用 DI 的方式注入
         * */
        /* @var $employeeEntity \Astralweb\ORM\Model\Employee */
        $objectManager = ObjectManager::getInstance();
        $employeeEntity = $objectManager-&gt;get('Astralweb\ORM\Model\Employee');
        $employeeCollection = $employeeEntity-&gt;getCollection();
</code></pre>

<br>
* 宣告 Collection 方式

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php

    /* @var $employeeCollection \Astralweb\ORM\Model\ResourceModel\Employee\Collection */
        $objectManager = ObjectManager::getInstance();
        $employeeCollection = $objectManager-&gt;get('Astralweb\ORM\Model\ResourceModel\Employee\Collection');
</code></pre>

在取得 Collection 之後，就可以開始對 ORM 資料表進行操作了。