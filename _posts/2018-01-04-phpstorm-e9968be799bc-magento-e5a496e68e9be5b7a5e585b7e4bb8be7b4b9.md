---
ID: 168
post_title: >
  PhpStorm 開發 Magento
  外掛工具介紹
author: Grayson
post_excerpt: ""
layout: post
permalink: 'http://magento.im/2018/01/04/phpstorm-%e9%96%8b%e7%99%bc-magento-%e5%a4%96%e6%8e%9b%e5%b7%a5%e5%85%b7%e4%bb%8b%e7%b4%b9/'
published: true
post_date: 2018-01-04 12:25:43
---
說到地表最強的 PHP 開發 IDE ，絕對是屬於 Jetbrains 公司所開發的 PhpStorm ，並且其支援大量的外掛套件提供給我們使用，而我們今天分享的是 PhpStorm 上兩個好用的外掛小工具，可以大幅度提升 Magento 的開發效率唷

<h2>Magento 2  工具</h2>

顧名思義，這是專門為 Magento 2.0 以上設計的套件。Magento 在開發過程中會遇到不少的 xml 定義檔，而這個套件就是幫大家清楚的把 xml 定義檔設定好，如果有遇到錯誤的 Namespace ，會自動加註紅色毛毛蟲底線，這樣就能夠快速的知道 xml 的錯誤在哪邊。

<br>

<h2>Maginto  工具</h2>

Magento 是一個大量模組化的購物平台，所有的功能都可以做到模組化，而人工建立模組的步驟實在是非常繁雜，常常大小寫拼錯、檔案位置放錯，皆會在模組建立時產生錯誤訊息，而這個外掛可以幫我們在 30 秒內快速建立模組（ 你沒看錯！真的是 30 秒 ），還可以建立 Controller 、 Model 、Theme，是不是非常的強大，就讓我們繼續看下去。
<br>

<h2>執行環境：</h2>

<ul>
<li>PhpStorm 2017.2.3</li>
<li>Magento 2.1.9</li>
<li>MacOS Sirra 10.12.6</li>
</ul>

<br>

<h2>安裝步驟</h2>

<ol>
<li><p>載入 Magento 2 專案資料夾，開啟專案後畫面如下圖
<img src="http://magento.im/wp-content/uploads/2018/01/%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7-2018-01-04-20.22.01.png" alt="" /></p></li>
<li><p>開啟偏好設定選項 PhpStorm -> Preferences -> Plugins，或是 MacOS 可以按下快速鍵 Command + ， 進入設定畫面。
<img src="http://magento.im/wp-content/uploads/2018/01/%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7-2018-01-04-20.23.41.png" alt="" /></p></li>
<li><p>搜尋套件 ‘Magento2’ 、 ‘Maginto’  並將兩個套件安裝完後 PhpStorm 會要求你重新啟動，就請依照指示重新啟動。
<img src="http://magento.im/wp-content/uploads/2018/01/%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7-2018-01-04-20.23.41.png" alt="" /></p></li>
<li><p>安裝完套件後，記得回到  PhpStorm -> Preferences -> Other Settings ，我們會看到多出兩個選項，就是我們剛剛所安裝的外掛，請依照下圖初始化外掛套件。
<img src="http://magento.im/wp-content/uploads/2018/01/%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7-2018-01-04-20.24.02.png" alt="" />
<img src="http://magento.im/wp-content/uploads/2018/01/%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7-2018-01-04-20.23.57.png" alt="" /></p></li>
<li><p>安裝完套件之後，我們就可以開始體驗這個快速開發模組的外掛了</p></li>
</ol>