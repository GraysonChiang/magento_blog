---
ID: 267
post_title: >
  解決 Magento 2 後台無法顯示
  Datetime 欄位
author: Grayson
post_excerpt: ""
layout: post
permalink: 'http://blog.magento.im/2018/02/22/%e8%a7%a3%e6%b1%ba-magento-2-%e5%be%8c%e5%8f%b0%e7%84%a1%e6%b3%95%e9%a1%af%e7%a4%ba-datetime-%e6%ac%84%e4%bd%8d/'
published: true
post_date: 2018-02-22 19:38:57
---
Magento 2 中，後台可以透過 ui conponent 產生 layout 跟 grid，欄位也有多種選擇，像是 Text、Textarea、Select 等等，但是 Datetime 欄位顯示的時候僅有日期選擇器的部分，沒有時間，導致實務應用上的不便。這也是 Magent o 的 Issue 之一，今天我們就來看看怎麼解決吧！

<h3>適用版本</h3>

<ul>
<li>Magento 2.1、2.2</li>
</ul>

<br>

<h2>新增 Attribute</h2>

通常會寫在 <code>InstallData.php</code> 內，今天我們就不特別說明新增的部分 但是在新增的時候，需要 給予 <code>input_renderer</code> 選項，裡面填寫自定義的 <code>Class</code>。

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">        $eavSetup-&gt;addAttribute(
           \Magento\Catalog\Model\Product::ENTITY,
            'datetime_example',
            [
                'label' =&gt; 'datetime example',
                'type' =&gt; 'datetime',
                'input' =&gt; 'date',
                'input_renderer' =&gt; Datetime::class,
                'class' =&gt; 'validate-date',
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

<h2>自定義 <code>class</code></h2>

這邊的 <code>class</code> 個人是習慣放置於 <code>Ui\DataProvider\Product\Form\Modifier</code> 這個資料夾內，當然你也可以放置於自己喜歡的 <code>namespace</code>，而另外要注意的是，<code>FIELD_CODE</code> 常數內的 <code>code</code> 就是 <code>Attribute Code</code>，一定要跟剛剛設定的一樣。

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php"><br />&lt;?php

namespace Vendor\Extension\Ui\DataProvider\Product\Form\Modifier;

use Magento\Catalog\Ui\DataProvider\Product\Form\Modifier\AbstractModifier;
use Magento\Framework\Stdlib\ArrayManager;

/**
 * Class Datetime
 * @package Vendor\Extension\Ui\DataProvider\Product\Form\Modifier
 */
class Datetime extends AbstractModifier
{

    const FIELD_CODE = 'datetime_example';

    /**
     * @param ArrayManager $arrayManager
     */
    public function __construct(
        ArrayManager $arrayManager
    )
    {
        $this-&gt;arrayManager = $arrayManager;
    }

    /**
     * {@inheritdoc}
     */
    public function modifyMeta(array $meta)
    {
        $meta = $this-&gt;enableTime($meta);

        return $meta;
    }

    /**
     * {@inheritdoc}
     */
    public function modifyData(array $data)
    {
        return $data;
    }

    /**
     * @param array $meta
     * @return array
     */
    protected function enableTime(array $meta)
    {

        $elementPath = $this-&gt;arrayManager-&gt;findPath(self::FIELD_CODE, $meta, null, 'children');

        $containerPath = $this-&gt;arrayManager-&gt;findPath(static::CONTAINER_PREFIX . self::FIELD_CODE, $meta, null, 'children');

        if (!$elementPath) return $meta;

        $meta = $this-&gt;arrayManager-&gt;merge(
            $containerPath,
            $meta,
            [
                'children' =&gt; [
                    self::FIELD_CODE =&gt; [
                        'arguments' =&gt; [
                            'data' =&gt; [
                                'config' =&gt; [
                                    'default' =&gt; '',
                                    'options' =&gt; [
                                        'dateFormat' &gt; 'Y-m-d',
                                        'timeFormat' =&gt; 'HH:mm:ss',
                                        'showsTime' =&gt; true
                                    ]
                                ],
                            ],
                        ],
                    ]
                ]
            ]
        );

        return $meta;
    }
}

</code></pre>

<br>

<h2>使用 <code>modifiers</code> 重新定義欄位</h2>

有了欄位資料之後，需要使用 <code>modifiers</code> 來取代原本的設定，首先要建立 <code>etc/adminhtml/di.xml</code> 並且貼上以下程式，這邊除了要在 <code>class</code> 欄位指定剛剛新增的 <code>class</code> 之外，還必須在 <code>item</code> 的 <code>name</code> 裡面寫入 <code>Attribute Code</code> ，如此一來才能完成定義。

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-xml">&lt;?xml version="1.0"?&gt;
&lt;config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:ObjectManager/etc/config.xsd"&gt;
    &lt;virtualType name="Magento\Catalog\Ui\DataProvider\Product\Form\Modifier\Pool"&gt;
        &lt;arguments&gt;
            &lt;argument name="modifiers" xsi:type="array"&gt;
                &lt;item name="datetime_example" xsi:type="array"&gt;
                    &lt;item name="class" xsi:type="string"&gt;Vendor\Extension\Ui\DataProvider\Product\Form\Modifier\Datetime&lt;/item&gt;
                    &lt;item name="sortOrder" xsi:type="number"&gt;100&lt;/item&gt;
                &lt;/item&gt;
            &lt;/argument&gt;
        &lt;/arguments&gt;
    &lt;/virtualType&gt;
&lt;/config&gt;

</code></pre>

<br>

<h2>更改前的 Datatime picker</h2>

<img src="http://blog.magento.im/wp-content/uploads/2018/02/%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7-2018-02-22-19.33.56.png" alt="" />

<br>

<h2>更改後的 Datatime picker</h2>

<img src="http://blog.magento.im/wp-content/uploads/2018/02/%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7-2018-02-22-19.34.09.png" alt="" />

<br>

<h2>參考連結</h2>

<ul>
<li><a href="https://github.com/magento/magento2/issues/4053">Magento Github</a></li>
</ul>