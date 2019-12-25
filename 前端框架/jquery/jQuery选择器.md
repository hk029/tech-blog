---
date:  2016-12-01
categories: 
- 前端框架
- jquery
tags:
- jquery
title: jQuery选择器
---

<h2>$符号</h2>

<code>$</code>是著名的jQuery符号。实际上，jQuery把所有功能全部封装在一个全局变量jQuery中，而<code>$</code>也是一个合法的变量名，它是变量jQuery的别名：

<h2>选择器</h2>

选择器通用形式是：<strong>$('')</strong>

<h3>jquery 和 DOM对象转换</h3>

<pre><code class="javascript">var div = $('#abc'); // jQuery对象var divDom = div.get(0); // 假设存在div，获取第1个DOM元素var another = $(divDom); // 重新把DOM包装为jQuery对象
</code></pre>

<h2>jquery对象</h2>

选择器选择出来的是jquery对象，可以直接访问它的属性

<pre><code class="javascript">var ps = $('p[name=abc]');
ps.name
ps.class
ps.innerHTML
……
</code></pre>

<h2>基本选择器</h2>

<h3>按tag查询 : $('tag')</h3>

<pre><code class="javascript">var ps = $('p'); // 返回所有&lt;p&gt;节点
ps.length; // 数一数页面有多少个&lt;p&gt;节点
</code></pre>

<h3>按id查询 : $('#id')</h3>

根据id查询，直接在前面加上一个#就好了

<pre><code class="javascript">var div = $('#abc');
</code></pre>

如果不存在，返回<code>[]</code>，不用判断<code>undefined</code>或<code>null</code>

<h3>按class查询: $('.class')</h3>

多个class可以直接加，不用加空格

<pre><code class="javascript">var a = $('.red.green'); // 注意没有空格！
</code></pre>

<h3>按属性查询：$('[attr = xxx]')</h3>

<pre><code class="javascript">var email = $('[name=email]'); // 找出&lt;??? name="email"&gt;
</code></pre>

如果含有特殊字符则要用<code>“”</code>或<code>''</code>括起来

<h4>按前缀查 ： ^=</h4>

<pre><code class="javascript">var icons = $('[name^=icon]'); // 找出所有name属性值以icon开头的DOM
</code></pre>

<h4>按后缀查：$=</h4>

<pre><code class="javascript">var names = $('[name$=with]'); // 找出所有name属性值以with结尾的DOM
</code></pre>

<h3>组合查询</h3>

组合查找就是把上述简单选择器组合起来使用。如果我们查找<code>$('[name=email]')</code>，很可能把表单外的<code>&lt;div name="email"&gt;</code>也找出来，但我们只希望查找<code>&lt;input&gt;</code>，就可以这么写：

<pre><code class="javascript">var emailInput = $('input[name=email]'); // 不会找出&lt;div name="email"&gt;
</code></pre>

同样的，根据tag和class来组合查找也很常见：

<pre><code class="javascript">var tr = $('tr.red'); // 找出&lt;tr class="red ..."&gt;...&lt;/tr&gt;
</code></pre>

<h2>多项选择器：，</h2>

多项选择器就是把多个选择器用,组合起来一块选：

<pre><code class="javascript">$('p,div'); // 把&lt;p&gt;和&lt;div&gt;都选出来
$('p.red,p.green'); // 把&lt;p class="red"&gt;和&lt;p class="green"&gt;都选出来
</code></pre>

<h2>层级选择器： 空格</h2>

<pre><code class="javascript">$('ul.lang li');
</code></pre>

这种层级选择器相比单个的选择器好处在于，它缩小了选择范围，因为首先要定位父节点，才能选择相应的子节点，这样避免了页面其他不相关的元素。

<h2>子选择器：></h2>

子选择器<code>$('parent&gt;child')</code>类似层级选择器，但是限定了层级关系必须是父子关系，就是<code>&lt;child&gt;</code>节点必须是<code>&lt;parent&gt;</code>节点的直属子节点

<h2>过滤器</h2>

过滤器一般不单独使用，它通常附加在选择器上，帮助我们更精确地定位元素。观察过滤器的效果：

<pre><code class="javascript">$('ul.lang li'); // 选出JavaScript、Python和Lua 3个节点

$('ul.lang li:first-child'); // 仅选出JavaScript$('ul.lang li:last-child'); // 仅选出Lua$('ul.lang li:nth-child(2)'); // 选出第N个元素，N从1开始
$('ul.lang li:nth-child(even)'); // 选出序号为偶数的元素
$('ul.lang li:nth-child(odd)'); // 选出序号为奇数的元素
</code></pre>

<h2>表单相关选择器</h2>

针对表单元素，jQuery还有一组特殊的选择器：

<ul>
<li><code>:input</code>：可以选择<code>&lt;input&gt;，&lt;textarea&gt;，&lt;select&gt;</code>和<code>&lt;button&gt;</code>；</li>
<li><code>:file</code>：可以选择<code>&lt;input type="file"&gt;</code>，和<code>input[type=file]</code>一样；</li>
<li><code>:checkbox</code>：可以选择复选框，和<code>input[type=checkbox]</code>一样；</li>
<li><code>:radio</code>：可以选择单选框，和<code>input[type=radio]</code>一样；</li>
<li><code>:focus</code>：可以选择当前输入焦点的元素，例如把光标放到一个<code>&lt;input&gt;</code>上，用<code>$('input:focus')</code>就可以选出；</li>
<li><code>:checked</code>：选择当前勾上的单选框和复选框，用这个选择器可以立刻获得用户选择的项目，如<code>$('input[type=radio]:checked')</code>；</li>
<li><code>:enabled</code>：可以选择可以正常输入的<code>&lt;input&gt;、&lt;select&gt;</code>等，也就是没有灰掉的输入；</li>
<li><code>:disabled</code>：和<code>:enabled</code>正好相反，选择那些不能输入的。</li>
</ul>

<h2>查找和过滤</h2>

<h3>find</h3>

最常见的查找是在某个节点的所有子节点中查找，使用find()方法，它本身又接收一个任意的选择器。

<pre><code class="javascript">var ul = $('ul.lang'); // 获得&lt;ul&gt;var dy = ul.find('.dy'); // 获得JavaScript, Python, Schemevar swf = ul.find('#swift'); // 获得Swift 
var hsk = ul.find('[name=haskell]'); // 获得Haskell
</code></pre>

对于位于同一层级的节点，还可以通过<code>next()</code>和<code>prev()</code>方法，

<h3>filter</h3>

filter()方法可以过滤掉不符合选择器条件的节点：

<pre><code class="javascript">var langs = $('ul.lang li'); // 拿到JavaScript, Python, Swift, Scheme和Haskell
var a = langs.filter('.dy'); // 拿到JavaScript, Python, Scheme
</code></pre>

或者传入一个函数，要特别注意函数内部的this被绑定为DOM对象，不是jQuery对象：

<pre><code class="javascript">var langs = $('ul.lang li'); // 拿到JavaScript, Python, Swift, Scheme和Haskell

langs.filter(function () {return this.innerHTML.indexOf('S') === 0; // 返回S开头的节点
}); // 拿到Swift, Scheme
</code></pre>

<h3>map</h3>

map()方法把一个jQuery对象包含的若干DOM节点转化为其他对象：

<pre><code class="javascript">var langs = $('ul.lang li'); // 拿到JavaScript, Python, Swift, Scheme和Haskell
var arr = langs.map(function () {return this.innerHTML;
}).get(); // 用get()拿到包含string的Array：['JavaScript', 'Python', 'Swift', 'Scheme', 'Haskell']
</code></pre>

此外，一个jQuery对象如果包含了不止一个DOM节点，first()、last()和slice()方法可以返回一个新的jQuery对象，把不需要的DOM节点去掉：

<pre><code class="javascript">var langs = $('ul.lang li'); // 拿到JavaScript, Python, Swift, Scheme和Haskell
var javascript = langs.first(); // JavaScript，相当于$('ul.lang li:first-child')
var haskell = langs.last(); // Haskell, 相当于$('ul.lang li:last-child')
var sub = langs.slice(2, 4); // Swift, Scheme, 参数和数组的slice()方法一致
</code></pre>

<h2>练习</h2>

<pre><code class="language-html">&lt;form id="test-form" action="#0" onsubmit="return false;"&gt;
    &lt;p&gt;&lt;label&gt;Name: &lt;input name="name"&gt;&lt;/label&gt;&lt;/p&gt;
    &lt;p&gt;&lt;label&gt;Email: &lt;input name="email"&gt;&lt;/label&gt;&lt;/p&gt;
    &lt;p&gt;&lt;label&gt;Password: &lt;input name="password" type="password"&gt;&lt;/label&gt;&lt;/p&gt;
    &lt;p&gt;Gender: &lt;label&gt;&lt;input name="gender" type="radio" value="m" checked&gt; Male&lt;/label&gt; &lt;label&gt;&lt;input name="gender" type="radio" value="f"&gt; Female&lt;/label&gt;&lt;/p&gt;
    &lt;p&gt;&lt;label&gt;City: &lt;select name="city"&gt;
        &lt;option value="BJ" selected&gt;Beijing&lt;/option&gt;
        &lt;option value="SH"&gt;Shanghai&lt;/option&gt;
        &lt;option value="CD"&gt;Chengdu&lt;/option&gt;
        &lt;option value="XM"&gt;Xiamen&lt;/option&gt;
    &lt;/select&gt;&lt;/label&gt;&lt;/p&gt;
    &lt;p&gt;&lt;button type="submit"&gt;Submit&lt;/button&gt;&lt;/p&gt;
&lt;/form&gt;
</code></pre>

输入值后，用jQuery获取表单的javascriptON字符串，key和value分别对应每个输入的name和相应的value，例如：<code>{"name":"Michael","email":...}</code>

<pre><code class="javascript">function getjavascripton(){
    var javascripton = {};
    var element = $('#test-form :input');   //:input可以选&lt;input&gt;，&lt;textarea&gt;，&lt;select&gt;和&lt;button&gt;；

    //element.filter('input :unchecked');
    for(var i = 0;i &lt; element.length;i++)
    {
        var tmp = element[i];
        //if(tmp.type !== 'submit') //gender永远选的是f,因为这里是2个input
        if(tmp.type !== 'submit' &amp;&amp; (tmp.type !== "radio" || tmp.checked))
            javascripton[tmp.name] = tmp.value;

    }

    javascripton = javascriptON.stringify(javascripton);   //变成字符串
    alert(javascripton);
    return false;
}
</code></pre>

更好的实现：

<pre><code class="language-javascript">    function getjavascripton(){
    var javascripton = {};
    $('#test-form :input[type!=submit]').map(function(){  //直接过滤了submit
        if(this.type !== "radio" || this.checked){   //过滤了那个没有被选中的radio
            javascripton[this.name] = this.value;
        }
    });
    javascripton = javascriptON.stringify(javascripton);
    alert(javascripton);
    return false;
}
</code></pre>