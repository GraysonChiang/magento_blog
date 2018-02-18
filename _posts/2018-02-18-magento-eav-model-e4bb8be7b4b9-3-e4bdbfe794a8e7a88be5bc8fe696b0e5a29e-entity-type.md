---
ID: 234
post_title: 'Magento EAV Model 介紹 (3) &#8211; 使用程式新增 Entity Type'
author: Grayson
post_excerpt: ""
layout: post
permalink: 'http://blog.magento.im/2018/02/18/magento-eav-model-%e4%bb%8b%e7%b4%b9-3-%e4%bd%bf%e7%94%a8%e7%a8%8b%e5%bc%8f%e6%96%b0%e5%a2%9e-entity-type/'
published: true
post_date: 2018-02-18 19:40:03
---
之前的文章內，我們介紹了 Eav Model 的關係。今天我們要透過程式的方式來教大家如何新增 Attribute 及 Attribute Set，利用程式的方式新增，不僅可以確保在安裝模組的時候開發機、測試伺服器及正式伺服器內都擁有一樣的 Attribute，也可以更有效的管理 Attribute 唷！

<h2>新增資料表</h2>

要建立 Entity type，必須依照 Magento 的規則建立相對應的資料表，我們以建立 Post 的 Entity type 為例，共需要產生以下 6 張資料表

<ul>
<li><code>post_entity</code></li>
<li><code>post_entity_datetime</code></li>
<li><code>post_entity_decimal</code></li>
<li><code>post_entity_int</code></li>
<li><code>post_entity_text</code></li>
<li><code>post_entity_varchar</code></li>
</ul>

要建立資料表，我們必須先在 <code>module</code> 的 <code>Setup</code> 資料夾內建立 <code>InstallSchema.php</code>，裡面程式碼如下：

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php"><br />&lt;?php

namespace Grayson\Post\Setup;

use Magento\Framework\DB\Adapter\AdapterInterface;
use Magento\Framework\DB\Ddl\Table;
use Magento\Framework\Setup\InstallSchemaInterface;
use Magento\Framework\Setup\ModuleContextInterface;
use Magento\Framework\Setup\SchemaSetupInterface;

/**
 * Class InstallSchema
 * @package Grayson\Post\Setup
 */
class InstallSchema implements InstallSchemaInterface
{
    const POST_ENTITY = 'post_entity';

    /**
     * @param SchemaSetupInterface $setup
     * @param ModuleContextInterface $context
     * @throws \Zend_Db_Exception
     */
    public function install(SchemaSetupInterface $setup, ModuleContextInterface $context)
    {
        $installer = $setup;

        $installer-&gt;startSetup();

    }
}

</code></pre>

<h3><code>post_entity</code> 資料表</h3>

這張是實體 ( Entity ) 主表，所有的紀錄都會在這邊產生，程式如下：

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php"><br />    /**
     * @param SchemaSetupInterface $setup
     * @param string $entity
     * @return InstallSchema
     * @throws \Zend_Db_Exception
     */
    private function addEntityTable(SchemaSetupInterface $setup, string $entity): InstallSchemaInterface
    {
        $table = $setup-&gt;getConnection()
            -&gt;newTable($setup-&gt;getTable($entity))
            -&gt;addColumn(
                'entity_id',
                Table::TYPE_INTEGER,
                null,
                [
                    'identity' =&gt; true,
                    'unsigned' =&gt; true,
                    'nullable' =&gt; false,
                    'primary' =&gt; true
                ],
                'Entity ID'
            )-&gt;addColumn(
                'created_at',
                Table::TYPE_TIMESTAMP,
                null,
                [
                    'nullable' =&gt; false,
                    'default' =&gt; Table::TIMESTAMP_INIT
                ],
                'Creation Time'
            )-&gt;addColumn(
                'updated_at',
                Table::TYPE_TIMESTAMP,
                null,
                [
                    'nullable' =&gt; false,
                    'default' =&gt; Table::TIMESTAMP_INIT_UPDATE
                ],
                'Update Time'
            );

        $setup-&gt;getConnection()-&gt;createTable($table);

        return $this;

    }

</code></pre>

<br>

<h3><code>post_entity_datetime</code> 資料表</h3>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php"><br />    /**
     * @param SchemaSetupInterface $setup
     * @param string $entity
     * @return InstallSchema
     * @throws \Zend_Db_Exception
     */
    private function addDatetimeTable(SchemaSetupInterface $setup, string $entity): InstallSchemaInterface
    {
        $table = $setup-&gt;getConnection()
            -&gt;newTable($setup-&gt;getTable($entity . '_datetime')
            )-&gt;addColumn(
                'value_id',
                Table::TYPE_INTEGER,
                null,
                ['identity' =&gt; true, 'nullable' =&gt; false, 'primary' =&gt; true],
                'Value ID'
            )-&gt;addColumn(
                'attribute_id',
                Table::TYPE_SMALLINT,
                null,
                ['unsigned' =&gt; true, 'nullable' =&gt; false, 'default' =&gt; '0'],
                'Attribute Id'
            )-&gt;addColumn(
                'store_id',
                Table::TYPE_SMALLINT,
                null,
                ['unsigned' =&gt; true, 'nullable' =&gt; false, 'default' =&gt; '0'],
                'Store ID'
            )-&gt;addColumn(
                'entity_id',
                Table::TYPE_INTEGER,
                null,
                ['unsigned' =&gt; true, 'nullable' =&gt; false, 'default' =&gt; '0'],
                'Entity Id'
            )-&gt;addColumn(
                'value',
                Table::TYPE_DATETIME,
                null,
                [],
                'value'
            )-&gt;addIndex(
                $setup-&gt;getIdxName($entity . '_decimal',
                    ['entity_id', 'attribute_id', 'store_id'],
                    AdapterInterface::INDEX_TYPE_UNIQUE),
                ['entity_id', 'attribute_id', 'store_id'],
                ['type' =&gt; AdapterInterface::INDEX_TYPE_UNIQUE]
            )-&gt;addIndex(
                $setup-&gt;getIdxName($entity . '_datetime',
                    ['store_id']),
                ['store_id']
            )-&gt;addIndex(
                $setup-&gt;getIdxName($entity . '_datetime',
                    ['attribute_id']),
                ['attribute_id']
            )-&gt;addForeignKey(
                $setup-&gt;getFkName(
                    $entity . '_datetime',
                    'attribute_id',
                    'eav_attribute',
                    'attribute_id'
                ),
                'attribute_id',
                $setup-&gt;getTable('eav_attribute'),
                'attribute_id',
                Table::ACTION_CASCADE
            )-&gt;addForeignKey(
                $setup-&gt;getFkName(
                    $entity . '_datetime',
                    'entity_id',
                    $entity,
                    'entity_id'
                ),
                'entity_id',
                $setup-&gt;getTable($entity),
                'entity_id',
                Table::ACTION_CASCADE
            )-&gt;addForeignKey(
                $setup-&gt;getFkName(
                    $entity . '_datetime', 'store_id', 'store', 'store_id'
                ),
                'store_id',
                $setup-&gt;getTable('store'),
                'store_id',
                Table::ACTION_CASCADE
            );
        $setup-&gt;getConnection()-&gt;createTable($table);

        return $this;
    }

</code></pre>

<br>

<h3><code>post_entity_decimal</code> 資料表</h3>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">    /**
     * @param SchemaSetupInterface $setup
     * @param string $entity
     * @return InstallSchema
     * @throws \Zend_Db_Exception
     */
    private function addDecimalTable(SchemaSetupInterface $setup, string $entity): InstallSchemaInterface
    {
        $table = $setup-&gt;getConnection()
            -&gt;newTable($setup-&gt;getTable($entity . '_decimal'))
            -&gt;addColumn(
                'value_id',
                Table::TYPE_INTEGER,
                null,
                ['identity' =&gt; true, 'nullable' =&gt; false, 'primary' =&gt; true],
                'Value ID'
            )-&gt;addColumn(
                'attribute_id',
                Table::TYPE_SMALLINT,
                null,
                ['unsigned' =&gt; true, 'nullable' =&gt; false, 'default' =&gt; '0'],
                'Attribute Id'
            )-&gt;addColumn(
                'store_id',
                Table::TYPE_SMALLINT,
                null,
                ['unsigned' =&gt; true, 'nullable' =&gt; false, 'default' =&gt; '0'],
                'Store ID'
            )-&gt;addColumn(
                'entity_id',
                Table::TYPE_INTEGER,
                null,
                ['unsigned' =&gt; true, 'nullable' =&gt; false, 'default' =&gt; '0'],
                'Entity Id'
            )-&gt;addColumn(
                'value',
                Table::TYPE_DECIMAL,
                '12,4',
                [],
                'value'
            )-&gt;addIndex(
                $setup-&gt;getIdxName($entity . '_decimal',
                    ['entity_id', 'attribute_id', 'store_id'],
                    AdapterInterface::INDEX_TYPE_UNIQUE),
                ['entity_id', 'attribute_id', 'store_id'],
                ['type' =&gt; AdapterInterface::INDEX_TYPE_UNIQUE]
            )-&gt;addIndex(
                $setup-&gt;getIdxName($entity . '_decimal',
                    ['store_id']),
                ['store_id']
            )-&gt;addIndex(
                $setup-&gt;getIdxName($entity . '_decimal',
                    ['attribute_id']),
                ['attribute_id']
            )-&gt;addForeignKey(
                $setup-&gt;getFkName(
                    $entity . '_decimal',
                    'attribute_id',
                    'eav_attribute',
                    'attribute_id'
                ),
                'attribute_id',
                $setup-&gt;getTable('eav_attribute'),
                'attribute_id',
                Table::ACTION_CASCADE
            )-&gt;addForeignKey(
                $setup-&gt;getFkName(
                    $entity . '_decimal',
                    'entity_id',
                    $entity . '_entity',
                    'entity_id'
                ),
                'entity_id',
                $setup-&gt;getTable($entity),
                'entity_id',
                Table::ACTION_CASCADE
            )-&gt;addForeignKey(
                $setup-&gt;getFkName(
                    $entity . '_decimal', 'store_id', 'store', 'store_id'
                ),
                'store_id',
                $setup-&gt;getTable('store'),
                'store_id',
                Table::ACTION_CASCADE
            );

        $setup-&gt;getConnection()-&gt;createTable($table);

        return $this;

    }

</code></pre>

<br>

<h3><code>post_entity_int</code> 資料表</h3>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php

    /**
     * @param SchemaSetupInterface $setup
     * @param $entity
     * @return InstallSchema
     * @throws \Zend_Db_Exception
     */
    private function addIntTable(SchemaSetupInterface $setup, $entity): InstallSchemaInterface
    {
        $table = $setup-&gt;getConnection()
            -&gt;newTable($setup-&gt;getTable($entity . '_int'))
            -&gt;addColumn(
                'value_id',
                Table::TYPE_INTEGER,
                null,
                ['identity' =&gt; true, 'nullable' =&gt; false, 'primary' =&gt; true],
                'Value ID'
            )-&gt;addColumn(
                'attribute_id',
                Table::TYPE_SMALLINT,
                null,
                ['unsigned' =&gt; true, 'nullable' =&gt; false, 'default' =&gt; '0'],
                'Attribute Id'
            )-&gt;addColumn(
                'store_id',
                Table::TYPE_SMALLINT,
                null,
                ['unsigned' =&gt; true, 'nullable' =&gt; false, 'default' =&gt; '0'],
                'Store ID'
            )-&gt;addColumn(
                'entity_id',
                Table::TYPE_INTEGER,
                null,
                ['unsigned' =&gt; true, 'nullable' =&gt; false, 'default' =&gt; '0'],
                'Entity Id'
            )-&gt;addColumn(
                'value',
                Table::TYPE_INTEGER,
                null,
                [],
                'value'
            )-&gt;addIndex(
                $setup-&gt;getIdxName($entity . '_int',
                    ['entity_id', 'attribute_id', 'store_id'],
                    AdapterInterface::INDEX_TYPE_UNIQUE),
                ['entity_id', 'attribute_id', 'store_id'],
                ['type' =&gt; AdapterInterface::INDEX_TYPE_UNIQUE]
            )-&gt;addIndex(
                $setup-&gt;getIdxName($entity . '_int',
                    ['store_id']),
                ['store_id']
            )-&gt;addIndex(
                $setup-&gt;getIdxName($entity . '_int',
                    ['attribute_id']),
                ['attribute_id']
            )-&gt;addForeignKey(
                $setup-&gt;getFkName(
                    $entity . '_int',
                    'attribute_id',
                    'eav_attribute',
                    'attribute_id'
                ),
                'attribute_id',
                $setup-&gt;getTable('eav_attribute'),
                'attribute_id',
                Table::ACTION_CASCADE
            )-&gt;addForeignKey(
                $setup-&gt;getFkName(
                    $entity . '_int',
                    'entity_id',
                    $entity,
                    'entity_id'
                ),
                'entity_id',
                $setup-&gt;getTable($entity),
                'entity_id',
                Table::ACTION_CASCADE
            )-&gt;addForeignKey(
                $setup-&gt;getFkName(
                    $entity . '_int', 'store_id', 'store', 'store_id'
                ),
                'store_id',
                $setup-&gt;getTable('store'),
                'store_id',
                Table::ACTION_CASCADE
            );
        $setup-&gt;getConnection()-&gt;createTable($table);

        return $this;
    }

</code></pre>

<br>

<h3><code>post_entity_text</code> 資料表</h3>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php

    /**
     * @param SchemaSetupInterface $setup
     * @param string $entity
     * @return InstallSchema
     * @throws \Zend_Db_Exception
     */
    private function addTextTable(SchemaSetupInterface $setup, string $entity): InstallSchemaInterface
    {
        $table = $setup-&gt;getConnection()
            -&gt;newTable($setup-&gt;getTable($entity . '_text'))
            -&gt;addColumn(
                'value_id',
                Table::TYPE_INTEGER,
                null,
                ['identity' =&gt; true, 'nullable' =&gt; false, 'primary' =&gt; true],
                'Value ID'
            )-&gt;addColumn(
                'attribute_id',
                Table::TYPE_SMALLINT,
                null,
                ['unsigned' =&gt; true, 'nullable' =&gt; false, 'default' =&gt; '0'],
                'Attribute Id'
            )-&gt;addColumn(
                'store_id',
                Table::TYPE_SMALLINT,
                null,
                ['unsigned' =&gt; true, 'nullable' =&gt; false, 'default' =&gt; '0'],
                'Store ID'
            )-&gt;addColumn(
                'entity_id',
                Table::TYPE_INTEGER,
                null,
                ['unsigned' =&gt; true, 'nullable' =&gt; false, 'default' =&gt; '0'],
                'Entity Id'
            )-&gt;addColumn(
                'value',
                Table::TYPE_TEXT,
                255,
                [],
                'value'
            )-&gt;addIndex(
                $setup-&gt;getIdxName($entity . '_text',
                    ['entity_id', 'attribute_id', 'store_id'],
                    AdapterInterface::INDEX_TYPE_UNIQUE),
                ['entity_id', 'attribute_id', 'store_id'],
                ['type' =&gt; AdapterInterface::INDEX_TYPE_UNIQUE]
            )-&gt;addIndex(
                $setup-&gt;getIdxName($entity . '_text',
                    ['store_id']),
                ['store_id']
            )-&gt;addIndex(
                $setup-&gt;getIdxName($entity . '_text',
                    ['attribute_id']),
                ['attribute_id']
            )-&gt;addForeignKey(
                $setup-&gt;getFkName(
                    $entity . '_text',
                    'attribute_id',
                    'eav_attribute',
                    'attribute_id'
                ),
                'attribute_id',
                $setup-&gt;getTable('eav_attribute'),
                'attribute_id',
                Table::ACTION_CASCADE
            )-&gt;addForeignKey(
                $setup-&gt;getFkName(
                    $entity . '_text',
                    'entity_id',
                    $entity,
                    'entity_id'
                ),
                'entity_id',
                $setup-&gt;getTable($entity),
                'entity_id',
                Table::ACTION_CASCADE
            )-&gt;addForeignKey(
                $setup-&gt;getFkName(
                    $entity . '_text', 'store_id', 'store', 'store_id'
                ),
                'store_id',
                $setup-&gt;getTable('store'),
                'store_id',
                Table::ACTION_CASCADE
            );
        $setup-&gt;getConnection()-&gt;createTable($table);
        return $this;
    }

</code></pre>

<br>

<h3><code>post_entity_varchar</code> 資料表</h3>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php
    /**
     * @param SchemaSetupInterface $setup
     * @param $entity
     * @return InstallSchema
     * @throws \Zend_Db_Exception
     */
    private function addVarcharTable(SchemaSetupInterface $setup, $entity): InstallSchemaInterface
    {
        $table = $setup-&gt;getConnection()
            -&gt;newTable($setup-&gt;getTable($entity . '_varchar'))
            -&gt;addColumn(
                'value_id',
                Table::TYPE_INTEGER,
                null,
                [
                    'identity' =&gt; true,
                    'nullable' =&gt; false,
                    'primary' =&gt; true
                ],
                'Value ID'
            )-&gt;addColumn(
                'attribute_id',
                Table::TYPE_SMALLINT,
                null,
                [
                    'unsigned' =&gt; true,
                    'nullable' =&gt; false,
                    'default' =&gt; '0'
                ],
                'Attribute Id'
            )-&gt;addColumn(
                'store_id',
                Table::TYPE_SMALLINT,
                null,
                [
                    'unsigned' =&gt; true,
                    'nullable' =&gt; false,
                    'default' =&gt; '0'
                ],
                'Store ID'
            )-&gt;addColumn(
                'entity_id',
                Table::TYPE_INTEGER,
                null,
                [
                    'unsigned' =&gt; true,
                    'nullable' =&gt; false,
                    'default' =&gt; '0'
                ],
                'Entity Id'
            )-&gt;addColumn(
                'value',
                Table::TYPE_TEXT,
                256,
                [],
                'value'
            )-&gt;addIndex(
                $setup-&gt;getIdxName($entity . '_varchar',
                    ['entity_id', 'attribute_id', 'store_id'],
                    AdapterInterface::INDEX_TYPE_UNIQUE),
                ['entity_id', 'attribute_id', 'store_id'],
                ['type' =&gt; AdapterInterface::INDEX_TYPE_UNIQUE]
            )-&gt;addIndex(
                $setup-&gt;getIdxName($entity . '_varchar',
                    ['store_id']),
                ['store_id']
            )-&gt;addIndex(
                $setup-&gt;getIdxName($entity . '_varchar',
                    ['attribute_id']),
                ['attribute_id']
            )-&gt;addForeignKey(
                $setup-&gt;getFkName(
                    $entity . '_varchar',
                    'attribute_id',
                    'eav_attribute',
                    'attribute_id'
                ),
                'attribute_id',
                $setup-&gt;getTable('eav_attribute'),
                'attribute_id',
                Table::ACTION_CASCADE
            )-&gt;addForeignKey(
                $setup-&gt;getFkName(
                    $entity . '_varchar',
                    'entity_id',
                    $entity,
                    'entity_id'
                ),
                'entity_id',
                $setup-&gt;getTable($entity),
                'entity_id',
                Table::ACTION_CASCADE
            )-&gt;addForeignKey(
                $setup-&gt;getFkName(
                    $entity . '_varchar', 'store_id', 'store', 'store_id'
                ),
                'store_id',
                $setup-&gt;getTable('store'),
                'store_id',
                Table::ACTION_CASCADE
            );
        $setup-&gt;getConnection()-&gt;createTable($table);

        return $this;
    }


</code></pre>

<h3>InstallSchema</h3>

<h2>新增 Model、Collection、ResouceModel</h2>

MVC 是一種現代化的設計模式，Model 則是 MVC 中的 <code>M</code>，它負責資料庫的操作，而 Magento 的 ORM 已經幫我們封裝好 Model 的 Class ，我們使用時僅需要繼承正確的抽象類別 ( Abstract Class ) 即可。
而除了 Model 之外，也幫我們封裝了 Collection 及 ResourceModel 的兩種類別，讓我們在使用 ORM 的時候更加的方便。

底下我們就要在這模組中新增這幾隻程式，來完成我們 Post 的 Entity Type。

<h3>Model</h3>

請注意！這邊是非常大的地雷！需要特別注意！

一般的 Model 是繼承 <code>\Magento\Framework\Model\AbstractModel</code>，但是因為是需要操作 Entity Type 的關係，我們繼承的 Class 需更改為 <code>Magento\Catalog\Model\AbstractModel</code>，可以參考下面程式的第 <code>6</code> 行。

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php"><br />&lt;?php

namespace Grayson\Post\Model;

use Magento\Catalog\Model\AbstractModel;
use Magento\Framework\DataObject\IdentityInterface;

/**
 * Class Post
 * @package Grayson\Post\Model
 */
class Post extends AbstractModel implements IdentityInterface
{
    /**
     * Entity code.
     * Can be used as part of method name for entity processing
     */
    const ENTITY = 'grayson_post';

    const CACHE_TAG = 'grayson_post';

    const STORE_ID = 'store_id';

    /* @var string */
    protected $_eventPrefix = 'grayson_post';

    /* @var string */
    protected $_eventObject = 'post';

    /* @var string */
    protected $_cacheTag = self::CACHE_TAG;

    /**
     * @return void
     */
    protected function _construct()
    {
        $this-&gt;_init(ResourceModel\Post::class);
    }

    /**
     * @return array
     */
    public function getIdentities()
    {
        return [self::CACHE_TAG . '_' . $this-&gt;getId()];
    }
}

</code></pre>

<h3>ResourceModel</h3>

也不同於原本繼承 <code>Magento\Framework\Model\ResourceModel\Db\AbstractDb</code>，這邊要更換為 <code>Magento\Catalog\Model\ResourceModel\AbstractResource</code>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php

namespace Grayson\Post\Model\ResourceModel;

use Magento\Catalog\Model\ResourceModel\AbstractResource;

/**
 * Class Post
 * @package Grayson\Post\Model\ResourceModel
 */
class Post extends AbstractResource
{
    /**
     *
     * @return \Magento\Eav\Model\Entity\Type
     * @throws \Magento\Framework\Exception\LocalizedException
     */
    public function getEntityType()
    {
        if (empty($this-&gt;_type)) {

            $this-&gt;setType(\Grayson\Post\Model\Post::ENTITY);

        }

        return parent::getEntityType();
    }
}

</code></pre>

<h3>Collection</h3>

Collection 的部分也需要由原本繼承的 <code>Magento\Framework\Model\ResourceModel\Db\Collection\AbstractCollection</code> 改為 <code>Magento\Catalog\Model\ResourceModel\Collection\AbstractCollection</code> 才行

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php

namespace Grayson\Post\Model\ResourceModel\Post;

use Grayson\Post\Model\Post;
use Grayson\Post\Model\ResourceModel\Post as ResourceModelPost;
use Magento\Catalog\Model\ResourceModel\Collection\AbstractCollection;

/**
 * Class Collection
 * @package Grayson\Post\Model\ResourceModel\Post
 */
class Collection extends AbstractCollection
{

    protected function _construct()
    {
        $this-&gt;_init(Post::class, ResourceModelPost::class);
    }
}


</code></pre>

<h2>執行 Upgrade 指令</h2>

在我們都完成上面的程式檔案後，並且做最後的確認，是否有 Module 沒有複製到正確的資料。如果一切都沒有問題，直接執行

<blockquote>
  $  bin/magento setup:upgrade
</blockquote>

即可看到安裝完成的訊息，我們可以至 <code>eav_entity_type</code> 資料表內檢查，是否有多出 <code>grayson_post</code> 這個 Entity Type 呢？

如果沒有，請回頭至第一個步驟再檢查一次有沒有程式碼遺漏掉，還是沒有的話，可以參考我下面的 Github 連結，裡面有完整的模組範例，包含上述的程式碼。

參考連結：

<ul>
<li><a href="https://github.com/GraysonChiang/EavExample">Github</a></li>
</ul>