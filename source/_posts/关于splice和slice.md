---
title: 关于splice和slice
date: 2017-12-05 09:42:19
tags: JavaScript
---
**splice()**

splice() 方法向/从数组中添加/删除项目，然后返回被删除的项目。

splice() 方法可删除从 index 处开始的零个或多个元素，并且用参数列表中声明的一个或多个值来替换那些被删除的元素。

如果从 arrayObject 中删除了元素，则返回的是含有被删除的元素的数组。

<!-- more -->
<table>
  <tr>
    <th width=40%, bgcolor=skyblue>参数</th>
    <th width="50%", bgcolor=skyblue>描述</th>
  </tr>
  <tr>
    <td> index 必需。 </td>
    <td> 整数，规定添加/删除项目的位置，使用负数可从数组结尾处规定位置。</td>
  </tr>
  <tr>
    <td>howmany 必需。</td>
    <td>要删除的项目数量。如果设置为 0，则不会删除项目。</td>
  <tr>
    <td>item1, ..., itemX 可选。</td>
    <td> itemX 可选。向数组添加的新项目。</td>
  </tr>
</table>

**slice()**

slice() 方法可从已有的数组中返回选定的元素。

**语法**

**arrayObject.slice(start,end)**

**返回值**

返回一个新的数组，包含从 start 到 end （不包括该元素）的 arrayObject 中的元素。
请注意，该方法并不会修改数组，而是返回一个子数组。如果想删除数组中的一段元素，应该使用方法 Array.splice()。
<table>
  <tr> 
    <th width=40%, bgcolor=skyblue>参数</th>
    <th width="50%", bgcolor=skyblue>描述</th>
  </tr>
  <tr>
    <td>start 必需。</td>
    <td>规定从何处开始选取。如果是负数，那么它规定从数组尾部开始算起的位置。也就是说，-1 指最后一个元素，-2 指倒数第二个元素，以此类推。</td>
  </tr>
  <tr>
    <td>end 可选。</td>
    <td>规定从何处结束选取。该参数是数组片断结束处的数组下标。如果没有指定该参数，那么切分的数组包含从 start 到数组结束的所有元素。如果这个参数是负数，那么它规定的是从数组尾部开始算起的元素。</td>
</table>