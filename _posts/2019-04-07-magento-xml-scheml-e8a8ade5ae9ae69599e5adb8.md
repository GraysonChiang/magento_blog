---
ID: 315
post_title: Magento XML Scheml 設定教學
author: Grayson
post_excerpt: ""
layout: post
permalink: 'http://blog.magento.im/2019/04/07/magento-xml-scheml-%e8%a8%ad%e5%ae%9a%e6%95%99%e5%ad%b8/'
published: true
post_date: 2019-04-07 19:44:06
---
<blockquote>
  大家在開發 Magento 的過程中，一定會遇到 Magento 內許多的 XML 檔，但是常常，不知道該如何定義，也不知道還有哪些參數可以使用，有時候一個參數要找半天，真的蠻浪費時間。不過好在 Magento 有工具能夠快速產生這些資料，讓我們能夠輕鬆的對應到 xsd 定義檔，一起來看看怎麼做到的吧！
</blockquote>

<br><br>

<h4>示範環境</h4>

<ul>
<li>Magento 2.3.1</li>
<li>PhpStorm 2019.1</li>
</ul>

<h3>什麼是 xsd ？</h3>

<ul>
<li><code>xsd</code>檔在 xml 中是一個定義 xml schema 的 xml 檔，在 Magento 內，常常會看到下面這種格式，其中 <code>xsd</code> 就是定義在 <code>noNamespaceSchemaLocation</code> 的 attribute 裡面，而定義描述檔的副檔名正是以 <code>.xsd</code> 結尾</li>
</ul>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-xml">&lt;schema xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="urn:magento:framework:Setup/Declaration/Schema/etc/schema.xsd"&gt;
&lt;/schema&gt;
</code></pre>

<br><br>

<h3>產生 urn 位置</h3>

<ul>
<li>Magento 已經內建指令，讓我們能夠快速產生所有定義檔的 path，在 Magento 根目錄內，鍵入以下指令</li>
</ul>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-sh"> bin/magento dev:urn-catalog:generate &lt;PATH&gt;
</code></pre>

<br><br>

<h3>定義檔樣式</h3>

<ul>
<li>產生後的定義檔，格式大致如下，可以看到，每一條定義檔 <code>urn:magento:framework:ObjectManager/etc/config.xsd</code> 都有相對應的路徑位置，並且在 <code>ProjectResources</code> 的 <code>component</code> 底下</li>
</ul>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-xml">&lt;?xml version="1.0"?&gt;
&lt;project version="4"&gt;
  &lt;component version="2" name="ProjectRootManager"/&gt;
  &lt;component name="ProjectResources"&gt;
    &lt;resource url="urn:magento:framework:ObjectManager/etc/config.xsd" location="/var/www/html/Grayson/vendor/magento/framework/ObjectManager/etc/config.xsd"/&gt;
    &lt;resource url="urn:magento:framework:Module/etc/module.xsd" location="/var/www/html/Grayson/vendor/magento/framework/Module/etc/module.xsd"/&gt;
    ...以下略...
  &lt;/component&gt;
&lt;/project&gt;
</code></pre>

<br><br>

<h3>匯入至 PhpStorm</h3>

<ul>
<li>筆者以 PhpStorm 為範例，畢竟這是地表上最強的 IDE (誤</li>
<li>步驟如下：

<ol>
<li>開啟 <code>.idea/misc.xml</code>( 如果沒有這個檔案，就自己建立出來)</li>
<li>在剛剛匯出檔案內找到 <code>&lt;component name="ProjectResources"&gt;</code> 的欄位，複製整個欄位至 <code>.idea/misc.xml</code> 的 <code>component</code> 內，就大公告成啦。</li>
</ol></li>
<li>注意事項：複製過去的時候，也許路徑還是會有錯，可以把 <code>location</code> 內的路徑改掉</p></li>
<li><p>原本：

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-xml">location="/var/www/html/Grayson/vendor/magento/framework/ObjectManager/etc/config.xsd"/&gt;
</code></pre></li>
<li>改為：

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-xml">location="$PROJECT_DIR$/vendor/magento/framework/ObjectManager/etc/config.xsd"/&gt;
</code></pre>

<p><br><br></p></li>
</ul>

<h3>匯入完成後</h3>

<ul>
<li><p>匯入前/後如下圖，所有原先警告找不到路徑的紅色都消失了，也能夠正確的追蹤到相對應的 <code>xsd</code> 定義檔了，這對開發上來說，真的是非常方便</p></li>
<li><p>匯入前：
<img src="http://blog.magento.im/wp-content/uploads/2019/04/before-1024x602.png" alt="" /></p></li>
<li><p>匯入後：
<img src="http://blog.magento.im/wp-content/uploads/2019/04/after-1024x575.png" alt="" /></p></li>
</ul>