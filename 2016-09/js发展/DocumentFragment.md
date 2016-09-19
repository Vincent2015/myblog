将HTML字符串（假设字符串变量名为html）转换成节点呢？如下操作即可：

	// 创建div节点
	var div = document.createElement("div");
	// 装载html字符串
	div.innerHTML = html;
	// 此时div.childNodes就是我们需要的节点了！
	return div.childNodes;

下面要做的就是将这些节点append进去，下面是我们自然理解的实现，遍历:
	
	var nodes = div.childNodes;
	for (var i=0, length=nodes.length; i<length; i+=1) {
   // 容器container加载克隆的节点 - 克隆的作用是保证nodes的完整
   container.appendChild(nodes[i].cloneNode(true)); 


对于DOM节点插入，大家应该都熟知“文档片段优化法”。具体来讲，就是使用document.createDocumentFragment()创建一个文档片段，然后，把节点一个一个append到这个片段中，回到页面上的时候，直接append这个文档片段就可以了-只有一次。

代码解释就是：

var nodes = div.childNodes
    // 我就是那把快刀，
    , fragment = document.createDocumentFragment();

for (var i=0, length=nodes.length; i<length; i+=1) {
   // 文档片段加载克隆的节点
   fragment.appendChild(nodes[i].cloneNode(true)); 
}
// 一刀来个痛快
container.appendChild(fragment);
于是，我们在HTML元素原型上扩展，可以让高端点的浏览器(IE9+, …)都有了appendHTML方法。

HTMLElement.prototype.appendHTML = function(html) {
    var divTemp = document.createElement("div"), nodes = null
        // 文档片段，一次性append，提高性能
        , fragment = document.createDocumentFragment();
    divTemp.innerHTML = html;
    nodes = divTemp.childNodes;
    for (var i=0, length=nodes.length; i<length; i+=1) {
       fragment.appendChild(nodes[i].cloneNode(true));
    }
    this.appendChild(fragment);
    // 据说下面这样子世界会更清净
    nodes = null;
    fragment = null;
};