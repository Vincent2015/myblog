# css动态“加载”之歪门邪道
	在前端项目开发中、团队协作时，免不了出现css命令冲突，导致样式被冲掉。或者因为设计原因，对同一元素出现截然不同的样式。往往要使用JS去做大量的修正。
<code>
<head>
<link rel="stylesheet" type="text/css" href="public.css" id="public" name="public"/>

<link rel="stylesheet" type="text/css" href="mouldeA.css" id="mouldeA" name="mouldeA"/>

<link rel="stylesheet" type="text/css" href="mouldeB.css" id="mouldeB" name="mouldeB"/>

<link rel="stylesheet" type="text/css" href="mouldeC.css" id="mouldeC" name="mouldeC"/>
<!--

    更多css link ......  
 -->
</head>
</code>
	通过link标签的disabled切换，实现加载DOM需要的css样式表
<code>
<script type="text/javascript">
  if (a){
  	document.getElementById("mouldeA").disabled = false;
  	document.getElementById("mouldeB").disabled = true;
  	document.getElementById("mouldeC").disabled = true;
  }else if (b){
  	document.getElementById("mouldeA").disabled = true;
  	document.getElementById("mouldeB").disabled = false;
  	document.getElementById("mouldeC").disabled = true;
  }else if(c){
  	document.getElementById("mouldeA").disabled = true;
  	document.getElementById("mouldeB").disabled = true;
  	document.getElementById("mouldeC").disabled = false;
  }else if(....){

  	//.........
  }
</script>
  </code>
# 背后的原理：
  CSSStyleSheet类型表示的是样式表，包括通过<link>元素包含的样式表和在<style>元素中定义的样式表。有读者可能记得，这两个元素本身分别是由HTMLLinkElement和HTMLStyleElement类型表示的。但是，CSSStyleSheet类型相对更加通用一些，它只表示样式表，而不管这些样式表在HTML中是如何定义的。此外，上述两个针对元素的类型允许修改HTML特性，但CSSStyleSheet对象则是一套只读的接口。使用下面的代码可以确定浏览器是否支持DOM2级样式表。

  CSSStyleSheet继承自StyleSheet，后者可以作为一个基础接口来定义非CSS样式表。从StyleSheet接口继承而来的属性如下。

* disabled：表示样式表是否被禁用的布尔值。这个属性是可读/写的，将这个值设置为true可以禁用样式表。
href：如果样式表是通过<link>包含的，则是样式表的URL；否则，是null。
* media：当前样式表支持的所有媒体类型的集合。与所有DOM集合一样，这个集合也有一个length属性和一个item()方法。也可以使用方括号语法取得结合中特定的项。如果集合是空列表项，表示样式表适用于所有媒体。在IE中，media是一个反映<link>和<style>元素media特性的字符串。
* ownerNode：指向拥有当前样式表的节点的指针，样式表可能是在HTML中通过<link>或<style>引入的。如果当前样式表是其它样式表通过@import导入的，则这个属性值为null。IE不支持这个属性。


parentStyleSheet：在当前样式表是通过@import导入的情况下，这个属性是一个指向导入它的样式表的指针。
* title：ownerNode中的title属性的值。
* type：表示样式表类型的字符串。对CSS样式表而言，这个字符串是“type/css”。
除了disabled属性之外，其它属性都是只读的。在支持以上所有这些属性的基础上，CSSStyleSheet类型还支持下列属性和方法：
* cssRules：样式表中包含的样式规则的集合。IE不支持这个属性，但有一个类似的rules属性。
* ownerRule：如果样式表是通过@import导入的，这个属性就是一个指针，指向表示导入的规则；否则，值为null。IE不支持这个属性。
* deleteRule(index)：删除cssRules集合中指定的位置的规则。IE不支持这个方法，但支持一个类似的removeRule()方法。
* insertRule(rule,index)：向cssRules集合中指定的位置插入rule字符串。IE不支持这个方法，但支持一个类似的addRule()方法
	
	
	


