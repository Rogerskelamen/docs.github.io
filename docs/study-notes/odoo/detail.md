# odoo 12学习细节

`<menuitem/>`可以等同于`<record model="ir.ui.view">`
**其中菜单项存储在ir.ui.menu模型中**

******

`<act_window/>`可以等同于`<record model="ir.actions.act_window"`
**其中动作实现存储在ir.actions.act_window模型中**

****

> 要想在菜单中点击后弹出对话框打开视图，而非跳转新页面

在动作中添加属性`target="new"`，其默认值为`current`。

> 要想在列表视图中煤业显示记录数

在动作中添加属性`limit="n"`，这里的n是你填的数字

> 窗口操作还可在列表和表单视图的上方的 Action 菜单按钮中使用，它在 Fitlers 按钮旁。要使用这个，我们需要在动作中添加以下两个属性：

`src_model`设置Action所作用的模型，例如`src_model="library.checkout"`
*==这个属性不同于`res_model`==*

`multi="true"`也启用列表视图中的Action，这样它可以作用于多个已选记录。否则仅在表单视图中可用，并且一次只能应用于一条记录。

****

Odoo 11有做修改：

string 属性不能作为继承的锚点，因为在应用继承前会对其进行翻译。这时应使用 name 属性来代替它。
*（什么是继承的锚点就不用说了吧，`postion`属性。。）*

****

在group布局内：

`<newline>`元素会强制在新的一行，下一个元素会渲染到组的第一列。附加的版块标题可通过组内`<separator>`元素添加，如果带有 string 属性也会显示标题标签。

****

## \_\_manifest\_\_.py中的其他描述符：

- version：默认为1.0，应遵守[版本号规则](http://semver.org/)。建议在模块版本号前加上 Odoo 版本，如12.0.1.0
- license：默认为LGPL-3
- website：了解模块更多信息的 URL，可以帮助人们查看更多文档或提供文件 bug 和建议的跟踪
- category：带有模块功能性分类字符串，缺省为Uncategorized。已有分类可通过安全组表单（位于Settings > Users & Companies > Groups）的 Application字段下拉列表查看（需开启调试模式）
- installable：默认为 True，但可以通过设置为 False 来禁用模块
- auto_install：若设置为 True，在其依赖已安装时会自动安装，<u>用于胶水模块，用于同一实例上两个模块安装后功能的连接。</u>

****

## notebook的一些细节：

notebook 元素，一个包含多个称为页面(page)的选项卡分区的容器。它们可以让不常用的内容在不使用时隐藏起来，或者用于按话题组织大量字段。

page支持以下属性：

- string：选项卡的标题（必填）
- attrs：不可见属性与表达式映射的字典
- accesskey：HTML访问密钥

-----

视图中的字段的其他属性：

- options是一个带有组件附加数据的JSON数据结构，值随各组件的不同支持而不同
- readonly=”True”让表单中该字段不可编辑
- password=”True”用于文本字段。显示为密码项，隐藏所输入文字
- filename用于二进制字段，它是用于存储上传文件名的模型字段的名称

---

## 字段的组件（widget）

*对于文本字段：*

- email用于让 email 文本成为可操作的”mail-to”地址
- url用于将文本格式化为可点击的URL
- html用于将文本渲染为HTML内容；在编辑模式下，它显示为一个WYSIWYG(所见即所得)编辑器，可在不使用 HTML 代码的情况下格式化内容。

*对于数字字段：*

- handle在列表视图中作为一个排序字段，显示一个句柄来让我们可以拖放进行自定义排序
- float_time将一个浮点型字段格式化为带有小时和分钟的值
- monetary将一个浮点型字段显示为货币金额。它与currency_id字段一起使用，还可以通过options=”{‘currency_field’: ‘currency_id’}”来使用另一个字段名
- progressbar将一个浮点值显示为进度条百分比，有助于将字段展示为完成率
- percentage和percentpie组件可用于浮点型字段

*对于关联字段和选项字段：*

- many2many_tags将值显示为按钮标签列表
- many2many_checkboxes将选项值显示为一个复选框列表
- selection对many-to-one字段使用选择字段组件
- radio以单选按钮显示选择字段选项
- priority将选项字段显示为一个可点击星形列表。选择项目通常是数值。
- state_selection将看板状态选择列表显示为信号灯。普通状态显示为灰色，完成显示为绿色，其它状态显示为红色。
- pdf_viewer是一个二进制字段(在 Odoo 12中引入)。

---

## 关联字段中的一些属性：

> 在关联字段中，我们可让用户操作做一些额外控制。默认用户从这些字段中创建新记录（也称作“快速创建”）并打开关联记录表单。可通过options字段属性来关闭：

`options="{'no_open': True, 'no_create': True}"`

---

## 按钮的那些个属性

- string是按钮文本标签或使用图标时的 HTML alt 文本
- type是执行操作的类型，有以下值：
  - object用于调用 Python 方法
  - action用于运行窗口操作
- name标识按所选类型要操作的具体的操作，要么是模型方法名，要么是要运行的窗口操作的数据库 ID。可使用`%(xmlid)d`方程式来将XML ID转换成加载视图时所需的数据库 ID。
- args在类型为 object 时用于向方法传递额外的参数，须是在形成方法调用参数的记录 ID 之后所添加的纯静态 JSON 参数。
- context在上下文中添加值，可在窗口操作或 Python 代码方法调用之后产生效果。
- confirm在运行相关操作之前显示确认消息框，显示的内容是属性中分配的文本。`special="cancel"`用于向导表单。
- icon是按钮所显示的图标。可用的按钮来自Font Awesome图标集，**<u>版本为4.7.0</u>**，应通过对应的 CSS 类来指定，如icon=”fa-question”。更多信息可访问[Font Awesome](https://fontawesome.com/v4.7.0/icons/)。

---

## 智能按钮的细节：

> 智能按钮显示为带有数据指示的矩形，在点击时可进入。

其位置：Odoo 中使用的 UI样式是在放置智能按钮的地方带有一个隐藏框，按钮框通常是`<sheet>`的第一个元素，在`<div class="oe_title">`元素前(以及头像)
**（所以其实是有一个预留位置）**

按钮的容器是一个带有oe_button_box类的 div 元素。

```xml
<div name="button_box" class="oe_button_box">
    <!-- Smart buttons will go here... -->
</div>
```

