---
ID: 143
post_title: Magento 的 Eav Entity
author: Grayson
post_excerpt: ""
layout: post
permalink: 'http://magento.im/2017/12/29/magento-%e7%9a%84-eav-entity/'
published: true
post_date: 2017-12-29 17:27:02
---
在 Magento 的世界裡面（就是 code 啦），有許多東西是用 Attribute 所控制，而屬性的用途極廣，可以自己建立自己的 Attribute 、Attribute Set 、Attribute Group 更可以自己建立屬於網站專屬的 Entity Type ，在講了這麼多名詞之後，我們今天就來看看這些東西之間的關係。

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-sequence">st=&gt;start: Start:&gt;http://www.google.com[blank]
e=&gt;end:&gt;http://www.google.com
op1=&gt;operation: My Operation
sub1=&gt;subroutine: My Subroutine
cond=&gt;condition: Yes
or No?:&gt;http://www.google.com
io=&gt;inputoutput: catch something...

st-&gt;op1-&gt;cond
cond(yes)-&gt;io-&gt;e
cond(no)-&gt;sub1(right)-&gt;op1
</code></pre>