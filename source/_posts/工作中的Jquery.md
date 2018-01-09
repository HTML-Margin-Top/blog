---
title: 工作中的Jquery
date: 2018-01-08 17:32:49
tags:
---
”是啊是啊，
那么在秋天，
落叶铺满了大地，
走在上面像松软的地毯，
山林却还是绿色的，
鹿群在山下的草原上纵情跳跃，
而你抬头，
金色的阳光便铺了你一脸，
蓝得像透明玉石的天空上，有
鹤与雁翅膀的影子……”
“你……”
“还有冬天来了时，
白雪覆盖了山林，
山野一片清幽，
晶莹的冰挂结在树枝上，
每一棵树都像是玉雕成的，
松鼠在大树的洞里，
听着风的呼啸与雪落的声音，
做一个关于来年的梦……”
<!-- more -->

##  hasClass() 方法
hasClass() 方法检查被选元素是否包含指定的 class。包含返回true，不包含false
用法：$(selector).hasClass(class)
实例：
```bash
$(this).hasClass("A")
```
* * *
##  each() 方法
-   $(selector).each()
    主要用于DOM遍历，each() 方法规定为每个匹配元素规定运行的函数。
    用法：$(selector).each(function(index,element))
       function(index,element) 必须要带的，
       index - 选择器的 index 位置，
       element - 当前的元素（也可使用 "this" 选择器）
    实例：
    遍历每个被选中的单选框
    ```bash
     $('input[type="radio"]:checked').each(function () {})
     ```
    跳出循环:
    return false跳出整个循环（break）
    return true跳至下个循环（continue）
-   $.each
    对参数一提供的对象的中所有的子元素逐一进行方法调用
    用法：$.each(data, function (index, value)）
    遍历处理data，可以是数组、DOM、json等
    如果是数组，function (index, value)中index是当前元素的位置，value是值。
    如果是对象，function (key, value)中key是当前元素的键，value是值。
    实例：
    遍历json数据
    ```bash
     $.each(answerQueue, function (key, value) {alert（key+value）})
    ```
* * *

##  prop()
用于设置或返回当前jQuery对象所匹配的元素的属性值。
-   prop( propertyName [, value ] )
    value设置则为其propertyName属性设置value，value未设置返回当前propertyName的值
    返回标签本身
    实例:
    ```bash
    answer["ConclusionID"] = $(this).prop("name");
    patientIDList.push($(this).prop("id"));
    ```

-   jQueryObject.prop( object )
    以对象形式同时设置任意多个属性的值。对象object的每个属性对应propertyName，属性的值对应value。
    返回获取数据
    实例：
    ```bash
    $("[name = checkItem]:checkbox").prop("checked", false);
    ```
    一般的，属性值是true和false的用prop（）方法设置获取
* * *

##    attr()
  attr() 方法设置或返回被选元素的属性值。
  - $(selector).attr(attribute)   返回被选元素的属性值。
  - $(selector).attr(attribute,value) 设置被选元素的属性和值。
  - $(selector).attr({attribute:value, attribute:value ...})  为被选元素设置一个以上的属性和值。
* * *

##  prop和attr的区别
1.  对于HTML元素本身就带有的固有属性，在处理时，使用prop方法。
2.  对于HTML元素我们自己自定义的DOM属性，在处理时，使用attr方法。
3.  是有true,false两个属性使用prop()