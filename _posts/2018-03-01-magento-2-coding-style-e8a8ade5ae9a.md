---
ID: 277
post_title: Magento 2 Coding Style 設定
author: Grayson
post_excerpt: ""
layout: post
permalink: 'http://blog.magento.im/2018/03/01/magento-2-coding-style-%e8%a8%ad%e5%ae%9a/'
published: true
post_date: 2018-03-01 20:42:52
---
Magento 是使用 <code>PHP</code> 所開發而成，而現代化的 <code>PHP</code> 程式不但需要使用 <code>Composer</code> 套件管理，還需要遵循 <code>PSR (PHP Standards Recommendations)</code> 開發標準，保證程式碼的一致性及可讀性。而 Magento 開發遵循 <code>PSR-1</code> 和 <code>PSR-2</code> 編碼標準，並且也定義好了 <code>xml</code> 檔案。我們今天就來看看在 PHPStorm 裡面要怎麼設定吧。

<h3>開發環境</h3>

<ul>
<li>Magento 2.1、2.2</li>
<li>PHPStorm 2017.3</li>
</ul>

<br>

<h2>何謂 PSR</h2>

<code>PSR</code> 是現代化 <code>PHP</code> 開發人員的共同標準，是由 <code>PHP-FIG</code> 組織 所發佈的，每一個建議都是為了解決一個多數PHP框架都會遇到的特定問題，以下為目前常見的規範列表，若有興趣可以至官網 ( <a href="https://www.php-fig.org/psr/">傳送門</a> )了解

<table>
<thead>
<tr>
  <th>代號</th>
  <th>用途</th>
  <th>備註</th>
</tr>
</thead>
<tbody>
<tr>
  <td>PSR-0</td>
  <td>自動載入 Autoloading Standard</td>
  <td>已棄用</td>
</tr>
<tr>
  <td>PSR-1</td>
  <td>基本代碼規範 Basic Coding Standard</td>
  <td></td>
</tr>
<tr>
  <td>PSR-2</td>
  <td>代碼風格規範 Coding Style Guide</td>
  <td></td>
</tr>
<tr>
  <td>PSR-3</td>
  <td>日誌接口規範 Logger Interface)</td>
  <td></td>
</tr>
<tr>
  <td>PSR-4</td>
  <td>自動加載新規範 Improved Autoloading</td>
  <td></td>
</tr>
</tbody>
</table>

<br>

<h2>PHPStorm 設定</h2>

<code>PHP</code> 有好用的工具可以來隨時監看 <code>Code Style</code> 是否完整，這套工具可以幫我們做到，要在 <code>PHPStorm</code> 裡面設定 <code>Code Sniffer</code>，必須要做以下兩件事情：

<h3>設定 code sniffer 執行檔</h3>

<ul>
<li>打開設定路徑：

<blockquote>
  Languages &amp; Frameworks / PHP / Code Sniffer
</blockquote></li>
<li>畫面如下：
<img src="http://blog.magento.im/wp-content/uploads/2018/03/code-sniffer-install-1024x632.png" alt="" /></p></li>
<li><p>指定 <code>Code Sniffer</code> 路徑

<blockquote>
  vendor/squizlabs/php_codesniffer/bin/phpcs
</blockquote></li>
<li>設定畫面如下：
<img src="http://blog.magento.im/wp-content/uploads/2018/03/code-sniffer-install-path-1024x242.png" alt="" /></p></li>
</ul>

<h3>指定 coding style</h3>

<ul>
<li><p>打開設定路徑：

<blockquote>
  Editor / Inspections / PHP / PHP Code Sniffer validation
</blockquote></li>
<li>勾選後，指定 <code>coding standard</code> 為 <code>custom</code>，並且定義路徑，

<blockquote>
  dev/tests/static/framework/Magento/ruleset.xml
</blockquote></li>
<li>設定畫面如下：
<img src="http://blog.magento.im/wp-content/uploads/2018/03/code-sniffer-standard-1024x632.png" alt="" /></p></li>
</ul>

<p><br>

<h2>設定完成</h2>

當上面步驟做好後，就已經設定完畢了，我們來看看到底有什麼變化吧！

<h4>原本程式碼：</h4>

<img src="http://blog.magento.im/wp-content/uploads/2018/03/%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7-2018-03-01-%E4%B8%8B%E5%8D%888.33.59.png" alt="" />

我們可以看到會有黃色的底線出現，代表 Code Sniffer 有正常運作，並且幫我們把錯誤的格式標示出來，依照上圖，我們可以修改的部分為
* 第 3 行：<code>namespace</code> 與後面的 <code>class</code> 中間要空一行
* 第 4 行： Class Name 後面多一格空格
* 第 10 行： <code>if</code> 內程式碼，中間不得空行
* 第 19 行： 結束符號 <code>}</code> 後面允許 <code>1</code> 行空行，且距離最後一個 <code>functino</code> 結束 <code>}</code> 後，不得有空行

當然其他還有很多細部的規則，在 <code>PHPStorm</code> 裡面都會出現相對應的提示，所以不用怕會出錯。

<h4>修改後程式碼：</h4>

<img src="http://blog.magento.im/wp-content/uploads/2018/03/%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7-2018-03-01-%E4%B8%8B%E5%8D%888.53.45-1024x798.png" alt="" />