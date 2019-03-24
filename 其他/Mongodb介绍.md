---
title: Mongodb介绍
date: 2017-01-13
categories: 
- 技术生活
- 前端技术
tags: 
- Javascript
---


<div id="MathJax_Message" style="display: none;"></div><h1 id="wiz_toc_0">Mongodb</h2>

<h2 id="wiz_toc_1">特点</h2>

<ul>
<li>使用BSON存储数据（二进制的JSON）</li>
<li>支持相对丰富的查询操作</li>
<li>支持索引</li>
<li>副本集</li>
<li>分片</li>
<li>无模式</li>
<li>部署简单方便（默认无身份认证）</li>
</ul>

<h2 id="wiz_toc_2">服务启动</h2>

<p>运行两种方式</p>

<h3 id="wiz_toc_3">守护进程：</h3>

<pre class="prettyprint prettyprinted"><code><span class="pln">mongod </span><span class="pun">[</span><span class="pln">opt</span><span class="pun">]</span><span class="pln"> </span><span class="pun">--</span><span class="pln">fork </span><span class="pun">--</span><span class="pln">logpath</span><span class="pun">=...</span></code></pre>

<h3 id="wiz_toc_4">前端使用：</h3>

<pre class="prettyprint prettyprinted"><code><span class="pln">mongod </span><span class="pun">[</span><span class="pln">opt</span><span class="pun">]</span></code></pre>

<h3 id="wiz_toc_5">命令参数：</h3>

<p>–dbpath ： 数据库目录（默认/data/db）</p>

<p>–port : 指定端口号（默认27017）</p>

<p>–fork : 以守护进程起来（必须制定log存储位置 ）</p>

<p>–logpath ： 指定log路径</p>

<p>–syslog ：使用默认系统log（我的在/var/log/mongodb/mongodb.log）</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">mongod </span><span class="pun">--</span><span class="pln">dbpath  </span><span class="pun">/</span><span class="pln">home</span><span class="pun">/</span><span class="pln">rocky</span><span class="pun">/</span><span class="pln">data</span><span class="pun">/</span><span class="pln">db  </span><span class="pun">--</span><span class="pln">port </span></code></pre>

<p><img src="http://img.hksite.cn/1483527212989" alt="your text" title=""></p>

<p>监视log的命令：用tail的-f参数可以持续监听目录</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">tail </span><span class="pun">-</span><span class="pln">f mongodb</span><span class="pun">.</span><span class="pln">log</span></code></pre>

<p><img src="http://img.hksite.cn/1483527357856" alt="your text" title=""></p>

<h2 id="wiz_toc_6">客户端</h2>

<pre class="prettyprint prettyprinted"><code class="language-mongo"><span class="pln">mongo IP</span><span class="pun">:</span><span class="pln">PORT</span></code></pre>

<p><img src="http://img.hksite.cn/1478075611496" alt="your text" title=""></p>

<h2 id="wiz_toc_7">基本命令</h2>

<blockquote>
  <p>由于是是nosql数据库，所以不需要特定的命令去创建数据库创建表，如果要使用一个数据库，但是它不存在的话，可以直接使用的</p>
</blockquote>

<p>更加详细的命令请参考官方手册：<a href="https://docs.mongodb.com/getting-started/shell">https://docs.mongodb.com/getting-started/shell</a></p>

<h3 id="wiz_toc_8">数据库</h3>

<ul>
<li><strong>show</strong> dbs   查看所有数据库</li>
<li><strong>show</strong> collections   查看数据库里的所有的集合</li>
<li><strong>use</strong> <code>db</code>  切换到某个数据库  <img src="http://img.hksite.cn/1483527714439" alt="your text" title=""></li>
</ul>

<h3 id="wiz_toc_9">查询</h3>

<ul>
<li><p><strong>db</strong>.<code>CollectionName</code>.<strong>find</strong> (<code>条件</code>)   (如果不加参数则为查询全部)</p>

<p><strong>完全匹配</strong>：{ \<field1>: \<value1>, \<field2>: \<value2>, … }</value2></field2></value1></field1></p>

<pre class="prettyprint prettyprinted"><code><span class="pln">db</span><span class="pun">.</span><span class="pln">restaurants</span><span class="pun">.</span><span class="pln">find</span><span class="pun">(</span><span class="pln"> </span><span class="pun">{</span><span class="pln"> </span><span class="str">"borough"</span><span class="pun">:</span><span class="pln"> </span><span class="str">"Manhattan"</span><span class="pln"> </span><span class="pun">}</span><span class="pln"> </span><span class="pun">)</span></code></pre>

<p><strong>条件格式</strong>：{ \<field1>: { \<operator1>: \<value1> } }</value1></operator1></field1></p>

<ul><li><p>大于/小于：{<code>$gt</code>/<code>$lt</code> : \<value1> }</value1></p>

<pre class="prettyprint prettyprinted"><code><span class="pln">db</span><span class="pun">.</span><span class="pln">restaurants</span><span class="pun">.</span><span class="pln">find</span><span class="pun">(</span><span class="pln"> </span><span class="pun">{</span><span class="pln"> </span><span class="str">"grades.score"</span><span class="pun">:</span><span class="pln"> </span><span class="pun">{</span><span class="pln"> $gt</span><span class="pun">:</span><span class="pln"> </span><span class="lit">30</span><span class="pln"> </span><span class="pun">}</span><span class="pln"> </span><span class="pun">}</span><span class="pln"> </span><span class="pun">)</span></code></pre></li>
<li><p>或{<code>$or</code>: [{ \<field1>: \<value1>},{\<field2>: \<value2>}] }</value2></field2></value1></field1></p>

<pre class="prettyprint prettyprinted"><code><span class="pln">db</span><span class="pun">.</span><span class="pln">restaurants</span><span class="pun">.</span><span class="pln">find</span><span class="pun">(</span><span class="pln">
</span><span class="pun">{</span><span class="pln"> $or</span><span class="pun">:</span><span class="pln"> </span><span class="pun">[</span><span class="pln"> </span><span class="pun">{</span><span class="pln"> </span><span class="str">"cuisine"</span><span class="pun">:</span><span class="pln"> </span><span class="str">"Italian"</span><span class="pln"> </span><span class="pun">},</span><span class="pln"> </span><span class="pun">{</span><span class="pln"> </span><span class="str">"address.zipcode"</span><span class="pun">:</span><span class="pln"> </span><span class="str">"10075"</span><span class="pln"> </span><span class="pun">}</span><span class="pln"> </span><span class="pun">]</span><span class="pln"> </span><span class="pun">}</span><span class="pln">
</span><span class="pun">)</span></code></pre></li>
<li><p>与：直接用逗号连接</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">db</span><span class="pun">.</span><span class="pln">restaurants</span><span class="pun">.</span><span class="pln">find</span><span class="pun">(</span><span class="pln"> </span><span class="pun">{</span><span class="pln"> </span><span class="str">"cuisine"</span><span class="pun">:</span><span class="pln"> </span><span class="str">"Italian"</span><span class="pun">,</span><span class="pln"> </span><span class="str">"address.zipcode"</span><span class="pun">:</span><span class="pln"> </span><span class="str">"10075"</span><span class="pln"> </span><span class="pun">}</span><span class="pln"> </span><span class="pun">)</span></code></pre></li></ul></li>
<li><p><strong>db</strong>.<code>xxx</code>.<strong>find</strong>().<strong>count</strong>()   可以统计查询出的数目</p></li>
</ul>

<h3 id="wiz_toc_10">插入数据</h3>

<ul>
<li><strong>db</strong>.<code>CollectionName</code>.<strong>insert</strong> ({"<code>key</code>":"<code>value</code>"……})</li>
</ul>

<p><img src="http://img.hksite.cn/1483528125879" alt="your text" title=""></p>

<p>因为mongodb是无模式的，所以它不要求数据库中的数据必须使用相同的格式，也就是说，你可以再插入以下数据：</p>

<p><img src="http://img.hksite.cn/1483528527859" alt="your text" title=""></p>

<p><strong>注意：</strong>这里的<code>_id</code>字段是所有的文档都有的字段，主要是通过它来查询不同的文档的。</p>

<p>当然你可以插入很复杂的数据，比如官方提供的例子：</p>

<pre class="prettyprint prettyprinted"><code class="language-javascript"><span class="pln">db</span><span class="pun">.</span><span class="pln">restaurants</span><span class="pun">.</span><span class="pln">insert</span><span class="pun">(</span><span class="pln">
   </span><span class="pun">{</span><span class="pln">

      </span><span class="str">"address"</span><span class="pln"> </span><span class="pun">:</span><span class="pln"> </span><span class="pun">{</span><span class="pln">
         </span><span class="str">"street"</span><span class="pln"> </span><span class="pun">:</span><span class="pln"> </span><span class="str">"2 Avenue"</span><span class="pun">,</span><span class="pln">
         </span><span class="str">"zipcode"</span><span class="pln"> </span><span class="pun">:</span><span class="pln"> </span><span class="str">"10075"</span><span class="pun">,</span><span class="pln">
         </span><span class="str">"building"</span><span class="pln"> </span><span class="pun">:</span><span class="pln"> </span><span class="str">"1480"</span><span class="pun">,</span><span class="pln">
         </span><span class="str">"coord"</span><span class="pln"> </span><span class="pun">:</span><span class="pln"> </span><span class="pun">[</span><span class="pln"> </span><span class="pun">-</span><span class="lit">73.9557413</span><span class="pun">,</span><span class="pln"> </span><span class="lit">40.7720266</span><span class="pln"> </span><span class="pun">]</span><span class="pln">
      </span><span class="pun">},</span><span class="pln">
      </span><span class="str">"borough"</span><span class="pln"> </span><span class="pun">:</span><span class="pln"> </span><span class="str">"Manhattan"</span><span class="pun">,</span><span class="pln">
      </span><span class="str">"cuisine"</span><span class="pln"> </span><span class="pun">:</span><span class="pln"> </span><span class="str">"Italian"</span><span class="pun">,</span><span class="pln">
      </span><span class="str">"grades"</span><span class="pln"> </span><span class="pun">:</span><span class="pln"> </span><span class="pun">[</span><span class="pln">
         </span><span class="pun">{</span><span class="pln">
            </span><span class="str">"date"</span><span class="pln"> </span><span class="pun">:</span><span class="pln"> </span><span class="typ">ISODate</span><span class="pun">(</span><span class="str">"2014-10-01T00:00:00Z"</span><span class="pun">),</span><span class="pln">
            </span><span class="str">"grade"</span><span class="pln"> </span><span class="pun">:</span><span class="pln"> </span><span class="str">"A"</span><span class="pun">,</span><span class="pln">
            </span><span class="str">"score"</span><span class="pln"> </span><span class="pun">:</span><span class="pln"> </span><span class="lit">11</span><span class="pln">
         </span><span class="pun">},</span><span class="pln">
         </span><span class="pun">{</span><span class="pln">
            </span><span class="str">"date"</span><span class="pln"> </span><span class="pun">:</span><span class="pln"> </span><span class="typ">ISODate</span><span class="pun">(</span><span class="str">"2014-01-16T00:00:00Z"</span><span class="pun">),</span><span class="pln">
            </span><span class="str">"grade"</span><span class="pln"> </span><span class="pun">:</span><span class="pln"> </span><span class="str">"B"</span><span class="pun">,</span><span class="pln">
            </span><span class="str">"score"</span><span class="pln"> </span><span class="pun">:</span><span class="pln"> </span><span class="lit">17</span><span class="pln">
         </span><span class="pun">}</span><span class="pln">
      </span><span class="pun">],</span><span class="pln">
      </span><span class="str">"name"</span><span class="pln"> </span><span class="pun">:</span><span class="pln"> </span><span class="str">"Vella"</span><span class="pun">,</span><span class="pln">
      </span><span class="str">"restaurant_id"</span><span class="pln"> </span><span class="pun">:</span><span class="pln"> </span><span class="str">"41704620"</span><span class="pln">
   </span><span class="pun">}</span><span class="pln">
</span><span class="pun">)</span></code></pre>

<h3 id="wiz_toc_11">删除</h3>

<p><strong>db</strong>.<code>xxx</code>.<strong>remove</strong>(<code>删除条件</code> , <code>参数</code>)</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">db</span><span class="pun">.</span><span class="pln">restaurants</span><span class="pun">.</span><span class="pln">remove</span><span class="pun">(</span><span class="pln"> </span><span class="pun">{</span><span class="pln"> </span><span class="str">"borough"</span><span class="pun">:</span><span class="pln"> </span><span class="str">"Manhattan"</span><span class="pln"> </span><span class="pun">}</span><span class="pln"> </span><span class="pun">)</span></code></pre>

<p>如果不加参数，默认删除的是所有匹配的文档，如果你只想删除一个文档，加入参数：<code>{ justOne: true }</code> </p>

<pre class="prettyprint prettyprinted"><code><span class="pln">db</span><span class="pun">.</span><span class="pln">restaurants</span><span class="pun">.</span><span class="pln">remove</span><span class="pun">(</span><span class="pln"> </span><span class="pun">{</span><span class="pln"> </span><span class="str">"borough"</span><span class="pun">:</span><span class="pln"> </span><span class="str">"Queens"</span><span class="pln"> </span><span class="pun">},</span><span class="pln"> </span><span class="pun">{</span><span class="pln"> justOne</span><span class="pun">:</span><span class="pln"> </span><span class="kwd">true</span><span class="pln"> </span><span class="pun">}</span><span class="pln"> </span><span class="pun">)</span></code></pre>

<p>删除所有的文档：</p>

<pre class="prettyprint prettyprinted"><code><span class="pln">db</span><span class="pun">.</span><span class="pln">restaurants</span><span class="pun">.</span><span class="pln">remove</span><span class="pun">(</span><span class="pln"> </span><span class="pun">{</span><span class="pln"> </span><span class="pun">}</span><span class="pln"> </span><span class="pun">)</span></code></pre>

<ul>
<li><strong>db</strong>.<code>xxx</code>.<strong>drop</strong>()</li>
</ul>

<p>当然，更加高效的删除整个文档的方式，是把集合删除，这时候要用<code>drop</code>命令，这时候，它会把包括索引在内的所有集合内容删除。</p>

<h3 id="wiz_toc_12">修改</h3>

<ul>
<li><p><strong>db</strong>.<code>xxx</code>.<strong>update</strong>(<code>条件</code>，<code>修改内容</code> ，<code>参数</code>)</p>

<p>update语句是更新，所以它是会新增或修改原有的值。</p></li>
</ul>

<p><code>条件</code>可以参考之前的查询，如果修改全部内容可以设为<code>{}</code></p>

<p>修改内容表达式的书写方式是：<code>$set</code> : { \<field1>: \<value1>, \<field2>: \<value2>, … }</value2></field2></value1></field1></p>

<pre class="prettyprint prettyprinted"><code><span class="pln">db</span><span class="pun">.</span><span class="kwd">class</span><span class="pun">.</span><span class="pln">update</span><span class="pun">(</span><span class="pln"> </span><span class="pun">{</span><span class="str">"name"</span><span class="pun">:</span><span class="str">"ZhangQiang"</span><span class="pun">},</span><span class="pln"> </span><span class="pun">{</span><span class="pln"> $set</span><span class="pun">:{</span><span class="str">"No"</span><span class="pun">:</span><span class="pln"> </span><span class="str">"123"</span><span class="pun">}</span><span class="pln"> </span><span class="pun">}</span><span class="pln"> </span><span class="pun">)</span></code></pre>

<p><img src="http://img.hksite.cn/1484209181920" alt="your text" title=""></p>

<p><strong>默认修改只修改单条数据</strong>，如果要修改多条数据，可以设置参数<code>{multi:true}</code></p>

<p>db.<code>xxx</code>.update(<code>条件</code>,修改内容,{multi:true})</p>

<ul>
<li><p><strong>db</strong>.<code>xxx</code>.<strong>save</strong>({"_id": ObjectId("xxxx"), \<field1>: \<value1>, \<field2>: \<value2>, … })</value2></field2></value1></field1></p>

<p>注意save语句和update语句完全不同，它只有<strong>一个参数</strong>，这个参数需要指定修改的ID号，后面接上修改后的值列表，这是彻底修改，不是更新，修改后的文档会完全和save的值列表一致。</p></li>
</ul>
    