---
ID: 238
post_title: 'Magento 2 事件處理 ( 2 ) &#8211; 自定義事件'
author: Grayson
post_excerpt: ""
layout: post
permalink: 'http://blog.magento.im/2018/02/18/magento-2-%e4%ba%8b%e4%bb%b6%e8%99%95%e7%90%86-2-%e8%87%aa%e5%ae%9a%e7%be%a9%e4%ba%8b%e4%bb%b6/'
published: true
post_date: 2018-02-18 19:47:47
---
Magento 內建有許多的事件 ( Event )，其實使用起來非常方便，上一章的篇幅，我們介紹了如何使用官方的事件，也介紹了如何使用參數。今天我們來看看自定義事件的用法吧！

<h2>在 Controller 內產生事件 ( Event )</h2>

這次示範，以我們常用的 <code>Controller</code> 為例，依照底下程式碼新增一個 <code>Controller</code>。

其中，我們可以看到在 <code>41</code> 行的部分，我們呼叫了一個自定義的事件，並且命名為 <code>sample_controller_before_execute</code>。目前到這裡為止，已經完成 <code>99%</code> 了。我們可以知道，只要有正確的繼承到 Magento 原生的 <code>Action Class</code> 皆可以直接使用 <code>_eventManager</code> 來管理事件。

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php

namespace Grayson\Post\Controller\Index;

use Magento\Framework\App\Action\Context;
use Magento\Framework\View\Result\PageFactory;
use Magento\Framework\App\Action\Action;

/**
 * Class Index
 * @package Grayson\Post\Controller\Index\Index
 */
class Index extends Action
{


    /**
     * Index resultPageFactory
     * @var PageFactory
     */
    protected $resultPageFactory;

    /**
     * Index constructor.
     * @param Context $context
     * @param PageFactory $resultPageFactory
     */
    public function __construct(Context $context, PageFactory $resultPageFactory)
    {
        $this-&gt;resultPageFactory = $resultPageFactory;
        return parent::__construct($context);
    }

    /**
     * Function execute
     * @return \Magento\Framework\View\Result\Page
     */
    public function execute()
    {

        $this-&gt;_eventManager-&gt;dispatch(
            'sample_controller_before_execute',
            [
                'controller' =&gt; $this
            ]);

        return $this-&gt;resultPageFactory-&gt;create();
    }
}
</code></pre>

<h3>定義<code>xml</code></h3>

在 <code>Controller</code> 內使用事件之後，其他的模組如果也想要使用的的話，就需要使用 <code>xml</code> 來註冊事件，其實就是跟上一篇幅的 <code>xml</code> 類似，但是 Event Name 的部分，需要更改成我們自己定義的 Event Neme，這樣即可完成。

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-xml">&lt;?xml version="1.0"?&gt;
&lt;config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Event/etc/events.xsd"&gt;
    &lt;event name="controller_action_catalog_product_save_entity_after"&gt;
        &lt;observer name="sample_controller_before_execute" instance="Vendor\Extension\Observer\SampleControllerBeforeExecute" /&gt;
    &lt;/event&gt;
&lt;/config&gt;

</code></pre>

其中裡面參數參數的部分，我們再說明一次：

<ul>
<li>name – 每一個事件必須要有獨立且唯一的命名</li>
<li>instance – 必須指定一個 <code>Class</code> 的 <code>namespace</code>，並且繼承 <code>magento</code> 的 <code>interface</code></li>
<li>disabled – 開啟或關閉此事件</li>
<li>shared – 此 <code>instance</code> 是否要與其他事件共用，預設為 <code>false</code></li>
</ul>