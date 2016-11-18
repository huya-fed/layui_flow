流加载文档
=========

> 简介：该模块包含信息流加载和图片懒加载两大核心支持，无论是对服务端、还是前端体验，都有非常大的性能帮助。

参考 ： 

[http://www.layui.com/doc/modules/flow.html](http://www.layui.com/doc/modules/flow.html)


##先看个实例

	var flow = require('layui_flow');

	flow.load(options);
  
    //图片懒加载
    flow.lazyimg(options);


##信息流
信息流即异步逐页渲染列表元素，这是你页面已经存在的一段列表，你页面初始时只显示了6个

	<ul id="demo">
	  <li>1</li>
	  <li>2</li>
	  ……
	  <li>6</li>
	</ul>

你想通过加载更多来显示余下列表，那么你只需要执行方法：flow.load(options) 即可

	flow.load({
	    elem: '#demo' //指定列表容器
	    ,done: function(page, next){ //到达临界点（默认滚动触发），触发下一页
	      var lis = [];
	      //以Ajax请求为例，请求下一页数据（注意：page是从2开始返回）
	      $.getJSON('/api/list?page='+page, function(res){
	        //假设你的列表返回在data集合中
	        layui.each(res.data, function(index, item){
	          lis.push('<li>'+ item.title +'</li>');
	        }); 
	        //执行下一页渲染，如果不存在数据，则告诉flow已经没有更多
	        next(lis.join(''), res.data.length === 0);   
	      });
	    }
	  });

上述是一个比较简单的例子，以下是信息流完整的参数支撑（即options对象），它们将有助于你更灵活地应对各种场景

<table class="site-table">
        <thead>
          <tr>
            <th>参数</th>
            <th>类型</th>
            <th>描述</th>
          </tr> 
        </thead>
        <tbody>
          <tr>
            <td>elem</td>
            <td>string</td>
            <td>指定列表容器的选择器</td>
          </tr>
          <tr>
            <td>scrollElem</td>
            <td>string</td>
            <td>滚动条所在元素选择器，默认document。如果你不是通过窗口滚动来触发流加载，而是页面中的某一个容器的滚动条，那么通过该参数指定即可。</td>
          </tr>
          <tr>
            <td>isAuto</td>
            <td>boolean</td>
            <td>是否自动加载。默认true。如果设为false，点会在列表底部生成一个“加载更多”的button，则只能点击它才会加载下一页数据。</td>
          </tr>
          <tr>
            <td>isShowEnd</td>
            <td>boolean</td>
            <td>是否尾页显示“没有更多了”。默认true。</td>
          </tr>
          <tr>
            <td>isLazyimg</td>
            <td>boolean</td>
            <td>是否开启图片懒加载。默认false。如果设为true，则只会对在可视区域的图片进行按需加载。但与此同时，在拼接列表字符的时候，你不能给列表中的img元素赋值src，必须要用lay-src取代，如：
            <pre><ol class="layui-code-ol"><li>     </li><li>layui.each(res.data, function(index, item){</li><li>  lis.push('&lt;li&gt;&lt;img lay-src="'+ item.src +'"&gt;&lt;/li&gt;');</li><li>});         </li></ol></pre>
            </td>
          </tr>
          <tr>
            <td>mb</td>
            <td>number</td>
            <td>与底部的临界距离，默认50。即当滚动条与底部产生该距离时，触发加载。注意：只有在isAuto为true时有效。<br>额，等等。。mb=margin-bottom，可不是骂人的呀。</td>
          </tr>
          <tr>
            <td>done</td>
            <td>function</td>
            <td>到达临界点触发加载的回调。信息流最重要的一个存在，携带两个参数：page、next，分别代表：下一页页码、插入下一页列表函数。
            <br>请注意，page是从2开始返回，也就是说你的第一页数据并非done来触发加载。
            <br>next是一个函数，用于向列表容器插入拼接好的字符。你必须给next传递两个参数：列表html字符、列表数据长度
            <pre class="layui-code layui-box layui-code-view" lay-title="JavaScript片段"><ol class="layui-code-ol"><li>//之所以要传列表数，是因为我们要根据它来得知后续已经没有数据了。            </li><li>next('列表html字符', res.data.length); </li><li>            </li></ol></pre>
            </td>
          </tr>
        </tbody>
      </table>


##图片懒加载
应该说比当前市面上任何一个懒加载的实现都更为强劲和轻量，她用不足80行代码巧妙地提供了一个始终加载当前屏图片的高性能方案（无论上滑还是下滑）。对你的网站因为图片可能带来的压力，可做出很好的应对。

###语法：flow.lazyimg(options)

图片懒加载的使用极其简单，其参数（options对象）可支持的key如下表所示：

<table class="site-table">
        <thead>
          <tr>
            <th>参数</th>
            <th>类型</th>
            <th>描述</th>
          </tr> 
        </thead>
        <tbody>
          <tr>
            <td>elem</td>
            <td>string</td>
            <td>指定开启懒加载的img元素选择器，如 elem: '.demo img' 或 elem: 'img.load'</td>
          </tr>
          <tr>
            <td>scrollElem</td>
            <td>string</td>
            <td>滚动条所在元素选择器，默认document。如果你不是通过窗口滚动来触发流加载，而是页面中的某一个容器的滚动条，那么通过该参数指定即可。</td>
          </tr>
          <tr>
        </tr></tbody>
      </table>

##destroy销毁

提供了个销毁的方法，主要是解除之前的绑定

	flow.destroy()