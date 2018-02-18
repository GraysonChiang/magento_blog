---
ID: 242
post_title: Magento 2 System Config Data
author: Grayson
post_excerpt: ""
layout: post
permalink: >
  http://blog.magento.im/2018/02/19/magento-2-system-config-data/
published: true
post_date: 2018-02-19 00:40:34
---
Magento 內部有一張資料表是用來儲存系統參數，但常常不知道怎麼新增自定義的欄位進去。今天，我們就來介紹資料設定欄位的方法吧！

<h2>資料表 Database</h2>

<h3>Table Name： <code>core_config_data</code></h3>

<ul>
<li>這張就是我們今天要介紹的主要資料表，它負責了幾乎一切 Magento 系統變數的儲存，我們透過以下表格來看看他有哪些欄位吧！</li>
</ul>

<table>
<thead>
<tr>
  <th align="center">欄位名稱</th>
  <th align="center">型別</th>
  <th align="center">長度</th>
  <th align="left">說明</th>
</tr>
</thead>
<tbody>
<tr>
  <td align="center">config_id</td>
  <td align="center">int</td>
  <td align="center">10</td>
  <td align="left">Entity id</td>
</tr>
<tr>
  <td align="center">scope_id</td>
  <td align="center">varchar</td>
  <td align="center">8</td>
  <td align="left">n/a</td>
</tr>
<tr>
  <td align="center">scope_id</td>
  <td align="center">int</td>
  <td align="center">11</td>
  <td align="left">n/a</td>
</tr>
<tr>
  <td align="center">path</td>
  <td align="center">varchar</td>
  <td align="center">255</td>
  <td align="left">路徑由 <code>section/group/field</code> 所組成</td>
</tr>
<tr>
  <td align="center">value</td>
  <td align="center">text</td>
  <td align="center">n/a</td>
  <td align="left">儲存資料內容</td>
</tr>
</tbody>
</table>

<br>

<h2>欄位名稱定義</h2>

裡面所有的 config Data 都是透過 <code>section</code>、<code>group</code>、<code>field</code> 這三個值來定義資料，那這三個又是什麼呢？
我們參考下圖：

<h2>資料處理 Data Process</h2>

<h3>讀取 Read</h3>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php

        $obj = \Magento\Framework\App\ObjectManager::getInstance();

        /* @var $scopeConfig \Magento\Framework\App\Config\ScopeConfigInterface */
        $scopeConfig = $obj-&gt;create('Magento\Framework\App\Config\ScopeConfigInterface');

        $scopeConfig-&gt;getValue('section/group/field');

</code></pre>

<h3>寫入 Write</h3>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-php">&lt;?php

        $obj = \Magento\Framework\App\ObjectManager::getInstance();

        /* @var $scopeConfig \Magento\Framework\App\Config\ScopeConfigInterface */
        $scopeConfig = $obj-&gt;create('Magento\Framework\App\Config\ScopeConfigInterface');
       $scopeConfig-&gt;saveConfig('section/group/field',$value, 'default',0);

</code></pre>

<br>

<h2>自定義欄位 Custom Field</h2>

<h3>XML 檔</h3>

<ul>
<li>跟大多數的 Magento 定義檔一樣，需要透過 XML 來定義 System 內的資料欄位，假設我們今天要開設一個遠端系統的 extension ，欄位有 <code>username</code>、<code>password</code>、<code>ip address</code>、<code>port number</code> 四個。檔案設定如下：</p></li>
<li><p>檔名及存放路徑

<blockquote>
  <code>vendor/extension/etc/adminhtml/system.xml</code>
</blockquote></li>
<li>程式碼</p></li>
</ul>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-XML">&lt;?xml version="1.0"?&gt;
&lt;config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Config:etc/system_file.xsd"&gt;
    &lt;system&gt;
        &lt;tab id="example_core_tab" translate="label" sortOrder="888"&gt;
            &lt;label&gt;Remote System&lt;/label&gt;
        &lt;/tab&gt;
        &lt;section id="example_core_setting" translate="label" sortOrder="130" showInDefault="1" showInWebsite="1" showInStore="1"&gt;
            &lt;label&gt;Setting&lt;/label&gt;
            &lt;tab&gt;example_core_tab&lt;/tab&gt;
            &lt;resource&gt;Grayson_SysConfigExample::example_core_setting&lt;/resource&gt;
            &lt;group id="setting_ip" translate="label" type="text" sortOrder="10" showInDefault="1" showInWebsite="0" showInStore="0"&gt;
                &lt;label&gt;Setting&lt;/label&gt;
                &lt;field id="field_ip" translate="label" type="text" sortOrder="20" showInDefault="1" showInWebsite="1" showInStore="1"&gt;
                    &lt;label&gt;Ip Address&lt;/label&gt;
                &lt;/field&gt;
                &lt;field id="field_port" translate="label" type="text" sortOrder="20" showInDefault="1" showInWebsite="1" showInStore="1"&gt;
                    &lt;label&gt;Port&lt;/label&gt;
                &lt;/field&gt;
                &lt;field id="field_username" translate="label" type="text" sortOrder="20" showInDefault="1" showInWebsite="1" showInStore="1"&gt;
                    &lt;label&gt;Username&lt;/label&gt;
                &lt;/field&gt;
                &lt;field id="password" translate="label" type="obscure" sortOrder="25" showInDefault="1" showInWebsite="1" showInStore="1"&gt;
                    &lt;label&gt;Password&lt;/label&gt;
                    &lt;backend_model&gt;Magento\Config\Model\Config\Backend\Encrypted&lt;/backend_model&gt;
                    &lt;depends&gt;
                        &lt;field id="*/*/active"&gt;1&lt;/field&gt;
                    &lt;/depends&gt;
                &lt;/field&gt;
            &lt;/group&gt;
        &lt;/section&gt;
    &lt;/system&gt;
&lt;/config&gt;
</code></pre>

<h3>賦予預設值</h3>

<ul>
<li><p>通常除了帳號密碼之外，我們可能會給予一些變數預設的資料，像是 <code>Port Number</code>，或是 <code>IP Address</code> 等等，而我們透過 <code>config.xml</code> 設定檔可以完成這件事情，讓資料永遠都有預設值。底下範例給予預設 <code>IP</code> 為 <code>127.0.0.1</code>，預設 <code>port</code> 為 <code>22</code>，預設 <code>username</code> 為 <code>default_username</code>。</p></li>
<li><p>檔名及存放路徑

<blockquote>
  vendor/extension/etc/config.xml
</blockquote></li>
<li>程式碼：</p></li>
</ul>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-XML">&lt;?xml version="1.0"?&gt;
&lt;config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Store:etc/config.xsd"&gt;
    &lt;default&gt;
        &lt;example_core_setting&gt;
            &lt;setting_ip&gt;
                &lt;field_ip&gt;127.0.0.1&lt;/field_ip&gt;
                &lt;field_port&gt;22&lt;/field_port&gt;
                &lt;field_username&gt;default_username&lt;/field_username&gt;
                &lt;password backend_model="Magento\Config\Model\Config\Backend\Encrypted" /&gt;
            &lt;/setting_ip&gt;
        &lt;/example_core_setting&gt;
    &lt;/default&gt;
&lt;/config&gt;

</code></pre>

<h3>密碼欄位</h3>

<p>Magento 在 <code>core_config_data</code> 的 <code>value</code> 中，可以儲存加密過後的字串。Magento 也實作了加 ( 解 ) 密的方法，讓開發人員能夠使用，在需要用到時 <code>decode</code> 回原本的字串。

<code>！！注意！！</code>這邊的加密非 <code>MD5</code> 或是 <code>SHA256</code> 類的雜湊 ( Hash ) 演算法！

因為有 Magento 實作了 <code>Encrypted</code> 方法，所以我們在設定 <code>Password</code> 的時候，要賦予 <code>backend_model</code> 的參數，才能用 <code>getValue</code> 方法正確的取得解密過後的字串。

<ul>
<li>system.xml 程式碼</li>
</ul>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-XML">                &lt;field id="password" translate="label" type="obscure" sortOrder="25" showInDefault="1" showInWebsite="1" showInStore="1"&gt;
                    &lt;label&gt;Password&lt;/label&gt;
                    &lt;backend_model&gt;Magento\Config\Model\Config\Backend\Encrypted&lt;/backend_model&gt;
                    &lt;depends&gt;
                        &lt;field id="*/*/active"&gt;1&lt;/field&gt;
                    &lt;/depends&gt;
                &lt;/field&gt;
</code></pre>

<ul>
<li>config.xml 程式碼</li>
</ul>

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-XML">&lt;?xml version="1.0"?&gt;
&lt;config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Store:etc/config.xsd"&gt;
    &lt;default&gt;
        &lt;example_core_setting&gt;
            &lt;setting_ip&gt;
                &lt;field_ip&gt;127.0.0.1&lt;/field_ip&gt;
                &lt;field_port&gt;22&lt;/field_port&gt;
                &lt;field_username&gt;default_username&lt;/field_username&gt;
                &lt;password backend_model="Magento\Config\Model\Config\Backend\Encrypted" /&gt;
            &lt;/setting_ip&gt;
        &lt;/example_core_setting&gt;
    &lt;/default&gt;
&lt;/config&gt;

</code></pre>

<h3>清除 Cache</h3>

因為有動到 <code>XML</code> 設定檔，更改完設定後，記得要用指令清除 <code>Cache</code>。

<blockquote>
  $ bin/magento cache:flush
  
  $ bin/magento cache:clean
</blockquote>

<br>

<h2>參考範例：</h2>