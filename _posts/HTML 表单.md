---
layout:     post
title:      表单
subtitle:   HTML 表单控件
date:       2020-05-29
author:     Lee
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - HTML
---

## 表单
  - HTML 表单用于搜集不同类型的用户输入

## form 标签
  - 作用：定义 HTML 表单
  - action 属性
    - 规定向何处提交表单的地址（URL）（提交页面）
  - method 属性
    - 规定在提交表单时所用的 http 方法（默认：GET）
    - 可选值
      - post
      - get

## input 标签
  - 定义：用于搜集用户信息。根据不同的 type 属性值，输入字段拥有很多种形式。输入字段可以是文本字段、复选框、掩码后的文本控件、单选按钮、按钮等...
  - name 属性
    - 规定 input 元素的名称
    - 用于对提交到服务器后的表单数据进行标识，或者在客户端通过 JavaScript 引用表单数据

#### 文本框
  - type：text
  - 作用：定义单行的输入字段，用户可在其中输入文本，默认宽度为20个字符
  - 示例：`<input type='text'/>`

#### 密码框
  - type：password
  - 作用：定义密码字段，该字段中的字符会被掩码
  - 示例：`<input type='password'/>`

#### 单选按钮
  - type：radio
  - 作用：定义单选按钮
  - name：单选按钮需要通过 name 属性进行分组，name 属性值相同属于一组。
  - value：由于该字段不需要用户填写内容，所以必须为每一个单选按钮指定一个 value 属性。当某个单选按钮被选中时，其 value 属性的值将会作为单选按钮字段的值
  - checked：设置某个单选按钮为默认选中状态
  - 示例：
    ```HTML
      <input type='radio' name='gender' value='male' checked/> 男
      <input type='radio' name='gender' value='female'/> 女
    ```

#### 复选框
  - type：checkbox
  - 作用：定义复选框
  - name：通过 name 属性进行分组，name 属性值相同属于一组
  - 示例：
    ```HTML
      <input type='checkbox' name='fruits' value='banana' checked/> 香蕉
      <input type='checkbox' name='fruits' value='apple' checked/> 苹果
      <input type='checkbox' name='fruits' value='orange'/> 橙子
    ```

#### 提交按钮
  - type：submit
  - 作用：定义提交按钮，提交按钮会把表单数据发送到服务器
  - value：可以使用 value 属性来指定提交按钮上显示的文字
  - 示例：`<input type='submit' value='提交按钮'/>`

#### 重置按钮
  - type：reset
  - 作用：定义重置按钮，重置按钮会清除表单中的所有数据
  - value：可以使用 value 属性来指定重置按钮上显示的文字
  - 示例：`<input type='reset' value='重置按钮'/>`

#### 单击按钮
  - type：button
  - 作用：定义可点击按钮（单击按钮），多数情况下用于 JavaScript 启动脚本
  - value：可以使用 value 属性来指定单击按钮上显示的文字，单击按钮默认不显示文字
  - 示例：`<input type='button' value='单击按钮'/>`

## select 标签
  - 定义：select 元素可创建单选或多选菜单。select 元素中的 option 元素用于定义列表中的可用选项
  - name：规定下拉列表的名称
  - multiple：
    - 规定可同时选择多个选项
    - 在不同操作系统中，选择多个选项的差异：
      - 对于 Windows：按住 Ctrl 按钮来选择多个选项
      - 对于 Mac：按住 command 按钮来选择多个选项
    - 由于上述差异的存在，同时由于需要告知用户可以使用多项选择，对用户更友好的方式是使用复选框

## option 标签
  - 定义：定义下拉列表中的一个选项（一个条目）。浏览器将 option 标签中的内容作为 select 标签的菜单或是滚动列表中的一个元素显示
  - value：定义送往服务器的选项值
  - selected：规定选项（在首次显示在列表中时）表现为选中状态
  - 示例：
    ```HTML
      <select name='fruits'>
        <option value=''>请选择</option>
        <option value='apple'>苹果</option>
        <option value='orange'>橙子</option>
        <option value='banana'>香蕉</option>
      </select>
    ```

## button 标签
  - 定义：定义一个按钮
  - 在 button 元素内部可以放置内容，比如文本或图像。这是该元素与使用 input 创建的按钮之间的不同之处
  - **重要事项：**如果在 HTML 表单中使用 button 元素，不同的浏览器会提交不同的值。IE 将提交 \<button> 与 \</button> 之间的文本，而其他浏览器将提交 value 属性的内容。请在 HTML 表单中使用 input 元素来创建按钮。
  - 请始终为按钮指定 type 属性，IE 的默认类型是 button ，而其他浏览器中（包括 W3C 规范）的默认值是“submit”。
