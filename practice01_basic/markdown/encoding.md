[返回](README.md)
<div class="x-wiki-content x-main-content"><h3>字符编码（整理自廖雪峰博文）</h3>
<p>我们已经讲过了，字符串也是一种数据类型，但是，字符串比较特殊的是还有一个编码问题。</p>
<p>因为计算机只能处理数字，如果要处理文本，就必须先把文本转换为数字才能处理。最早的计算机在设计时采用8个比特（bit）作为一个字节（byte），所以，一个字节能表示的最大的整数就是255（二进制11111111=十进制255），如果要表示更大的整数，就必须用更多的字节。比如两个字节可以表示的最大整数是<code>65535</code>，4个字节可以表示的最大整数是<code>4294967295</code>。</p>
<p>由于计算机是美国人发明的，因此，最早只有127个字符被编码到计算机里，也就是大小写英文字母、数字和一些符号，这个编码表被称为<code>ASCII</code>编码，比如大写字母<code>A</code>的编码是<code>65</code>，小写字母<code>z</code>的编码是<code>122</code>。</p>
<p>但是要处理中文显然一个字节是不够的，至少需要两个字节，而且还不能和ASCII编码冲突，所以，中国制定了<code>GB2312</code>编码，用来把中文编进去。</p>
<p>你可以想得到的是，全世界有上百种语言，日本把日文编到<code>Shift_JIS</code>里，韩国把韩文编到<code>Euc-kr</code>里，各国有各国的标准，就会不可避免地出现冲突，结果就是，在多语言混合的文本中，显示出来会有乱码。</p>
<p><img src="https://static.liaoxuefeng.com/files/attachments/923930471927008/0" data-src="/files/attachments/923930471927008/0" alt="char-encoding-problem"></p>
<p>因此，Unicode应运而生。Unicode把所有语言都统一到一套编码里，这样就不会再有乱码问题了。</p>
<p>Unicode标准也在不断发展，但最常用的是用两个字节表示一个字符（如果要用到非常偏僻的字符，就需要4个字节）。现代操作系统和大多数编程语言都直接支持Unicode。</p>
<p>现在，捋一捋ASCII编码和Unicode编码的区别：ASCII编码是1个字节，而Unicode编码通常是2个字节。</p>
<p>字母<code>A</code>用ASCII编码是十进制的<code>65</code>，二进制的<code>01000001</code>；</p>
<p>字符<code>0</code>用ASCII编码是十进制的<code>48</code>，二进制的<code>00110000</code>，注意字符<code>'0'</code>和整数<code>0</code>是不同的；</p>
<p>汉字<code>中</code>已经超出了ASCII编码的范围，用Unicode编码是十进制的<code>20013</code>，二进制的<code>01001110 00101101</code>。</p>
<p>你可以猜测，如果把ASCII编码的<code>A</code>用Unicode编码，只需要在前面补0就可以，因此，<code>A</code>的Unicode编码是<code>00000000 01000001</code>。</p>
<p>新的问题又出现了：如果统一成Unicode编码，乱码问题从此消失了。但是，如果你写的文本基本上全部是英文的话，用Unicode编码比ASCII编码需要多一倍的存储空间，在存储和传输上就十分不划算。</p>
<p>所以，本着节约的精神，又出现了把Unicode编码转化为“可变长编码”的<code>UTF-8</code>编码。UTF-8编码把一个Unicode字符根据不同的数字大小编码成1-6个字节，常用的英文字母被编码成1个字节，汉字通常是3个字节，只有很生僻的字符才会被编码成4-6个字节。如果你要传输的文本包含大量英文字符，用UTF-8编码就能节省空间：</p>
<table class="uk-table">
<tbody><tr><th>字符</th><th>ASCII</th><th>Unicode</th><th>UTF-8</th></tr>
<tr><td>A</td><td>01000001</td><td>00000000 01000001</td><td>01000001</td></tr>
<tr><td>中</td><td>x</td><td>01001110 00101101</td><td>11100100 10111000 10101101</td></tr>
</tbody></table>
<p>从上面的表格还可以发现，UTF-8编码有一个额外的好处，就是ASCII编码实际上可以被看成是UTF-8编码的一部分，所以，大量只支持ASCII编码的历史遗留软件可以在UTF-8编码下继续工作。</p>
<p>搞清楚了ASCII、Unicode和UTF-8的关系，我们就可以总结一下现在计算机系统通用的字符编码工作方式：</p>
<p>在计算机内存中，统一使用Unicode编码，当需要保存到硬盘或者需要传输的时候，就转换为UTF-8编码。</p>
<p>用记事本编辑的时候，从文件读取的UTF-8字符被转换为Unicode字符到内存里，编辑完成后，保存的时候再把Unicode转换为UTF-8保存到文件：</p>
<p><img src="https://static.liaoxuefeng.com/files/attachments/923923787018816/0" data-src="/files/attachments/923923787018816/0" alt="rw-file-utf-8"></p>
<p>浏览网页的时候，服务器会把动态生成的Unicode内容转换为UTF-8再传输到浏览器：</p>
<p><img src="https://www.liaoxuefeng.com/files/attachments/923923759189600/0" data-src="/files/attachments/923923759189600/0" alt="web-utf-8"></p>
<p>所以你看到很多网页的源码上会有类似<code>&lt;meta charset="UTF-8" /&gt;</code>的信息，表示该网页正是用的UTF-8编码。</p>
<h3>Python的字符串</h3>
<p>搞清楚了令人头疼的字符编码问题后，我们再来研究Python的字符串。</p>
<p>在最新的Python 3版本中，字符串是以Unicode编码的，也就是说，Python的字符串支持多语言，例如：</p>
<pre><code class="python"><span class="prompt">&gt;&gt;&gt; </span>print(<span class="string">'包含中文的str'</span>)
包含中文的str
</code></pre>
<p>对于单个字符的编码，Python提供了<code>ord()</code>函数获取字符的整数表示，<code>chr()</code>函数把编码转换为对应的字符：</p>
<pre><code class="python"><span class="prompt">&gt;&gt;&gt; </span>ord(<span class="string">'A'</span>)
<span class="number">65</span>
<span class="prompt">&gt;&gt;&gt; </span>ord(<span class="string">'中'</span>)
<span class="number">20013</span>
<span class="prompt">&gt;&gt;&gt; </span>chr(<span class="number">66</span>)
<span class="string">'B'</span>
<span class="prompt">&gt;&gt;&gt; </span>chr(<span class="number">25991</span>)
<span class="string">'文'</span>
</code></pre>
<p>如果知道字符的整数编码，还可以用十六进制这么写<code>str</code>：</p>
<pre><code class="python"><span class="prompt">&gt;&gt;&gt; </span><span class="string">'\u4e2d\u6587'</span>
<span class="string">'中文'</span>
</code></pre>
<p>两种写法完全是等价的。</p>
<p>由于Python的字符串类型是<code>str</code>，在内存中以Unicode表示，一个字符对应若干个字节。如果要在网络上传输，或者保存到磁盘上，就需要把<code>str</code>变为以字节为单位的<code>bytes</code>。</p>
<p>Python对<code>bytes</code>类型的数据用带<code>b</code>前缀的单引号或双引号表示：</p>
<pre><code class="ini"><span class="setting">x = <span class="value">b'ABC'</span></span>
</code></pre>
<p>要注意区分<code>'ABC'</code>和<code>b'ABC'</code>，前者是<code>str</code>，后者虽然内容显示得和前者一样，但<code>bytes</code>的每个字符都只占用一个字节。</p>
<p>以Unicode表示的<code>str</code>通过<code>encode()</code>方法可以编码为指定的<code>bytes</code>，例如：</p>
<pre><code class="xml">&gt;&gt;&gt; 'ABC'.encode('ascii')
b'ABC'
&gt;&gt;&gt; '中文'.encode('utf-8')
b'\xe4\xb8\xad\xe6\x96\x87'
&gt;&gt;&gt; '中文'.encode('ascii')
Traceback (most recent call last):
  File "<span class="tag">&lt;<span class="title">stdin</span>&gt;</span>", line 1, in <span class="tag">&lt;<span class="title">module</span>&gt;</span>
UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)
</code></pre>
<p>纯英文的<code>str</code>可以用<code>ASCII</code>编码为<code>bytes</code>，内容是一样的，含有中文的<code>str</code>可以用<code>UTF-8</code>编码为<code>bytes</code>。含有中文的<code>str</code>无法用<code>ASCII</code>编码，因为中文编码的范围超过了<code>ASCII</code>编码的范围，Python会报错。</p>
<p>在<code>bytes</code>中，无法显示为ASCII字符的字节，用<code>\x##</code>显示。</p>
<p>反过来，如果我们从网络或磁盘上读取了字节流，那么读到的数据就是<code>bytes</code>。要把<code>bytes</code>变为<code>str</code>，就需要用<code>decode()</code>方法：</p>
<pre><code class="python"><span class="prompt">&gt;&gt;&gt; </span><span class="string">b'ABC'</span>.decode(<span class="string">'ascii'</span>)
<span class="string">'ABC'</span>
<span class="prompt">&gt;&gt;&gt; </span><span class="string">b'\xe4\xb8\xad\xe6\x96\x87'</span>.decode(<span class="string">'utf-8'</span>)
<span class="string">'中文'</span>
</code></pre>
<p>如果<code>bytes</code>中包含无法解码的字节，<code>decode()</code>方法会报错：</p>
<pre><code class="sql">&gt;&gt;&gt; b'\xe4\xb8\xad\xff'.decode('utf-8')
Traceback (most recent <span class="operator"><span class="keyword">call</span> <span class="keyword">last</span>):
  ...
UnicodeDecodeError: <span class="string">'utf-8'</span> codec can<span class="string">'t decode byte 0xff in position 3: invalid start byte
</span></span></code></pre>
<p>如果<code>bytes</code>中只有一小部分无效的字节，可以传入<code>errors='ignore'</code>忽略错误的字节：</p>
<pre><code class="python"><span class="prompt">&gt;&gt;&gt; </span><span class="string">b'\xe4\xb8\xad\xff'</span>.decode(<span class="string">'utf-8'</span>, errors=<span class="string">'ignore'</span>)
<span class="string">'中'</span>
</code></pre>
<p>要计算<code>str</code>包含多少个字符，可以用<code>len()</code>函数：</p>
<pre><code class="python"><span class="prompt">&gt;&gt;&gt; </span>len(<span class="string">'ABC'</span>)
<span class="number">3</span>
<span class="prompt">&gt;&gt;&gt; </span>len(<span class="string">'中文'</span>)
<span class="number">2</span>
</code></pre>
<p><code>len()</code>函数计算的是<code>str</code>的字符数，如果换成<code>bytes</code>，<code>len()</code>函数就计算字节数：</p>
<pre><code class="python"><span class="prompt">&gt;&gt;&gt; </span>len(<span class="string">b'ABC'</span>)
<span class="number">3</span>
<span class="prompt">&gt;&gt;&gt; </span>len(<span class="string">b'\xe4\xb8\xad\xe6\x96\x87'</span>)
<span class="number">6</span>
<span class="prompt">&gt;&gt;&gt; </span>len(<span class="string">'中文'</span>.encode(<span class="string">'utf-8'</span>))
<span class="number">6</span>
</code></pre>
<p>可见，1个中文字符经过UTF-8编码后通常会占用3个字节，而1个英文字符只占用1个字节。</p>
<p>在操作字符串时，我们经常遇到<code>str</code>和<code>bytes</code>的互相转换。为了避免乱码问题，应当始终坚持使用UTF-8编码对<code>str</code>和<code>bytes</code>进行转换。</p>
<p>由于Python源代码也是一个文本文件，所以，当你的源代码中包含中文的时候，在保存源代码时，就需要务必指定保存为UTF-8编码。当Python解释器读取源代码时，为了让它按UTF-8编码读取，我们通常在文件开头写上这两行：</p>
<pre><code class="ruby"><span class="comment">#!/usr/bin/env python3</span>
<span class="comment"># -*- coding: utf-8 -*-</span>
</code></pre>
<p>第一行注释是为了告诉Linux/OS X系统，这是一个Python可执行程序，Windows系统会忽略这个注释；</p>
<p>第二行注释是为了告诉Python解释器，按照UTF-8编码读取源代码，否则，你在源代码中写的中文输出可能会有乱码。</p>
<p>申明了UTF-8编码并不意味着你的<code>.py</code>文件就是UTF-8编码的，必须并且要确保文本编辑器正在使用UTF-8 without BOM编码：</p>
<p><img src="https://www.liaoxuefeng.com/files/attachments/1008802356788736" data-src="/files/attachments/1008802356788736" alt="set-encoding-in-notepad++"></p>
<p>如果<code>.py</code>文件本身使用UTF-8编码，并且也申明了<code># -*- coding: utf-8 -*-</code>，打开命令提示符测试就可以正常显示中文：</p>
<p><img src="https://www.liaoxuefeng.com/files/attachments/1008802515054144" data-src="/files/attachments/1008802515054144" alt="py-chinese-test-in-cmd"></p>
<h3>格式化</h3>
<p>最后一个常见的问题是如何输出格式化的字符串。我们经常会输出类似<code>'亲爱的xxx你好！你xx月的话费是xx，余额是xx'</code>之类的字符串，而xxx的内容都是根据变量变化的，所以，需要一种简便的格式化字符串的方式。</p>
<p><img src="https://www.liaoxuefeng.com/files/attachments/928817906446432/0" data-src="/files/attachments/928817906446432/0" alt="py-str-format"></p>
<p>在Python中，采用的格式化方式和C语言是一致的，用<code>%</code>实现，举例如下：</p>
<pre><code class="python"><span class="prompt">&gt;&gt;&gt; </span><span class="string">'Hello, %s'</span> % <span class="string">'world'</span>
<span class="string">'Hello, world'</span>
<span class="prompt">&gt;&gt;&gt; </span><span class="string">'Hi, %s, you have $%d.'</span> % (<span class="string">'Michael'</span>, <span class="number">1000000</span>)
<span class="string">'Hi, Michael, you have $1000000.'</span>
</code></pre>
<p>你可能猜到了，<code>%</code>运算符就是用来格式化字符串的。在字符串内部，<code>%s</code>表示用字符串替换，<code>%d</code>表示用整数替换，有几个<code>%?</code>占位符，后面就跟几个变量或者值，顺序要对应好。如果只有一个<code>%?</code>，括号可以省略。</p>
<p>常见的占位符有：</p>
<table class="uk-table">
<thead>
<tr><th>占位符</th><th>替换内容</th></tr>
</thead>
<tbody>
<tr><td>%d</td><td>整数</td></tr>
<tr><td>%f</td><td>浮点数</td></tr>
<tr><td>%s</td><td>字符串</td></tr>
<tr><td>%x</td><td>十六进制整数</td></tr>
</tbody>
</table>
<p>其中，格式化整数和浮点数还可以指定是否补0和整数与小数的位数：</p>
<form class="uk-form uk-form-stack uk-margin-top uk-margin-bottom" action="#0"><pre id="pre-online-run-code-1" style="font-size: 14px; margin-bottom: 0px; border-bottom: none; padding: 6px; border-bottom-left-radius: 0px; border-bottom-right-radius: 0px;"># -*- coding: utf-8 -*-
</pre><textarea id="textarea-online-run-code-1" onkeyup="_mdAdjustTextareaHeight(this)" class="uk-width-1-1 x-codearea" rows="10" style="overflow: scroll; border-top-left-radius: 0; border-top-right-radius: 0;"></textarea><button type="button" onclick="execute_python('online-run-code-1', this)" class="uk-button uk-button-primary" style="margin-top:15px;"><i class="uk-icon-play"></i> Run</button></form>
<p>如果你不太确定应该用什么，<code>%s</code>永远起作用，它会把任何数据类型转换为字符串：</p>
<pre><code class="python"><span class="prompt">&gt;&gt;&gt; </span><span class="string">'Age: %s. Gender: %s'</span> % (<span class="number">25</span>, <span class="built_in">True</span>)
<span class="string">'Age: 25. Gender: True'</span>
</code></pre>
<p>有些时候，字符串里面的<code>%</code>是一个普通字符怎么办？这个时候就需要转义，用<code>%%</code>来表示一个<code>%</code>：</p>
<pre><code class="python"><span class="prompt">&gt;&gt;&gt; </span><span class="string">'growth rate: %d %%'</span> % <span class="number">7</span>
<span class="string">'growth rate: 7 %'</span>
</code></pre>
<h3>format()</h3>
<p>另一种格式化字符串的方法是使用字符串的<code>format()</code>方法，它会用传入的参数依次替换字符串内的占位符<code>{0}</code>、<code>{1}</code>……，不过这种方式写起来比%要麻烦得多：</p>
<pre><code class="python"><span class="prompt">&gt;&gt;&gt; </span><span class="string">'Hello, {0}, 成绩提升了 {1:.1f}%'</span>.format(<span class="string">'小明'</span>, <span class="number">17.125</span>)
<span class="string">'Hello, 小明, 成绩提升了 17.1%'</span>
</code></pre>
<h3>练习</h3>
<p>小明的成绩从去年的72分提升到了今年的85分，请计算小明成绩提升的百分点，并用字符串格式化显示出<code>'xx.x%'</code>，只保留小数点后1位：</p>
<form class="uk-form uk-form-stack uk-margin-top uk-margin-bottom" action="#0"><pre id="pre-online-run-code-2" style="font-size: 14px; margin-bottom: 0px; border-bottom: none; padding: 6px; border-bottom-left-radius: 0px; border-bottom-right-radius: 0px;"># -*- coding: utf-8 -*-

s1 = 72
s2 = 85
</pre><textarea id="textarea-online-run-code-2" onkeyup="_mdAdjustTextareaHeight(this)" class="uk-width-1-1 x-codearea" rows="10" style="overflow: scroll; border-top-left-radius: 0; border-top-right-radius: 0;"></textarea><button type="button" onclick="execute_python('online-run-code-2', this)" class="uk-button uk-button-primary" style="margin-top:15px;"><i class="uk-icon-play"></i> Run</button></form>
<h3>小结</h3>
<p>Python 3的字符串使用Unicode，直接支持多语言。</p>
<p>当<code>str</code>和<code>bytes</code>互相转换时，需要指定编码。最常用的编码是<code>UTF-8</code>。Python当然也支持其他编码方式，比如把Unicode编码成<code>GB2312</code>：</p>
<pre><code class="python"><span class="prompt">&gt;&gt;&gt; </span><span class="string">'中文'</span>.encode(<span class="string">'gb2312'</span>)
<span class="string">b'\xd6\xd0\xce\xc4'</span>
</code></pre>
<p>但这种方式纯属自找麻烦，如果没有特殊业务要求，请牢记仅使用<code>UTF-8</code>编码。</p>
<p>格式化字符串的时候，可以用Python的交互式环境测试，方便快捷。</p>
</div>

[返回](README.md)