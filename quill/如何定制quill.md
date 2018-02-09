---
layout: docs
title: How to Customize Quill
permalink: /guides/how-to-customize-quill/
redirect_from:
  - /guides/customizing-and-extending/
---

Quill在设计时就考虑到了定制化和扩展。这是通过实现一个小的编辑器核心来实现的，这个编辑器核心是由一个细粒度的，定义良好的[API](/docs/api/)集合组成的，编辑器的核心由[模块](/docs/modules)扩充，您也可以使用的相同的[APIs](/docs/api/)自己实现模块。

通常情况下，大多数定制在[配置](#configurations/)中处理，用户界面由[主题](#themes)和CSS配置，功能由[模块](#modules)配置，编辑器的内容通过[Parchment](#content-and-formatting)处理。


### 配置

Quill支持Code Over Configuration™，但是对于非常常见的需求，特别是在等效代码冗长或复杂的情况下，Quill提供[配置](/docs/configuration/)选项。这将是一个很好的首要考虑，以确定您是否需要实现任何自定义功能。

`模块`和`主题`是两个强大的选项。您可以通过简单地添加或删除单个[模块](/docs/modules/) 或使用不同的[主题](/docs/themes/)来彻底改变或扩展Quill的功能。


### 主题

Quill官方支持一个标准的工具栏主题[Snow](/docs/themes/#snow)和一个浮动的工具提示主题[Bubble](/docs/themes/#bubble)。由于Quill不像许多传统编辑器那样被限制在iframe中，因此只需使用现有的主题之一，就可以使用CSS在主题上进行许多可视的修改。

如果你想大幅改变UI交互，你可以省略主题配置选项，这将给你一个没有样式的Quill编辑器。您仍然需要包含一个最小的样式表，例如，确保空格在所有浏览器中呈现，并且有序列表被适当编号。

```html
<link rel="stylesheet" href="https://cdn.quilljs.com/{{site.version}}/quill.core.css">
```

在上面的基础样式表文件中通过编写CSS你可以实现下拉菜单和悬浮提示的样式。 [从Parchment克隆媒体](/guides/cloning-medium-with-parchment/#final-polish)提供了一个动手的例子。


### 模块

Quill采用由小型编辑核心组成的模块化架构设计，其中包含增强其功能的模块。这些功能中的一部分对于编辑来说非常重要，例如管理撤销和重做的[历史](/docs/modules/history/)模块。由于所有模块都使用暴露给开发人员的相同公共[API](/docs/api/)，所以在必要时甚至可以交换核心模块。

像Quill的核心本身一样，许多模块公开了额外的配置选项和API。在决定更换[模块](/docs/modules/)之前，请查看其文档。通常您所需的定制已经作为配置或API调用实现。

否则，如果您想大幅改变现有模块已经涵盖的功能，你可以简单地不包含它，或者当一个主题默认包含它的时候明确地排除它，并且使用默认模块使用的相同的[API](/docs/api/)来实现你对Quill外部的功能。

```js
var quill = new Quill('#editor', {
  modules: {
    toolbar: false    // Snow主题默认包含了toolbar
  },
  theme: 'snow'
});
```

需要包含一些模块（[Clipboard](/docs/modules/clipboard/)，[Keyboard](/docs/modules/keyboard/)和[History](/docs/modules/history/)）作为核心功能，具体取决于它们提供的API。例如，即使undo和redo是基本的，预期的编辑器功能，本地浏览器的行为处理也是不一致和不可预测的。History模块通过实现自己的撤销管理器并将undo（）和redo（）暴露为API来填补这个空白。

尽管如此，坚持Quill模块化设计，仍然可以通过实现自己的撤消管理器来取代历史模块，从而彻底改变撤消和重做的方式，或者任何其他的核心功能。只要你实现相同的API接口，Quill会很乐意使用你的替换模块。通过继承现有的模块，并覆盖你想要改变的方法，这很容易完成。看看[模块](/docs/modules/)文档中的覆盖核心[Clipboard](/docs/modules/clipboard/)模块的一个非常简单的例子。

最后，您可能需要添加现有模块未提供的功能。在这种情况下，最好把你所需要的功能组装成一个Quill模块，你可以看一下[构建一个定制模块](/guides/building-a-custom-module/)。当然，在应用程序代码中将这个逻辑从Quill中分离出来肯定是正确的。


### 内容和格式

Quill允许通过其文档模型[Parchment](https://github.com/quilljs/parchment/)修改和扩展它了解的内容和格式。内容和格式在Parchment中表示为Blots或Attributors，大致对应于DOM中的节点或属性。

#### Class vs Inline

在可能的情况下，Quill使用类而不是内联样式属性，但两者都可以实现供您选择。[Playground snippet](/playground/#class-vs-inline-style)是一个实现的例子。

```js
var ColorClass = Quill.import('attributors/class/color');
var SizeStyle = Quill.import('attributors/style/size');
Quill.register(ColorClass, true);
Quill.register(SizeStyle, true);

// 正常初始化
var quill = new Quill('#editor', {
  modules: {
    toolbar: true
  },
  theme: 'snow'
});
```

#### 定制 Attributors

除了选择特定的Attributor之外，您还可以自定义现有的Attributor。以下是字体白名单添加附加字体的示例。

```js
var FontAttributor = Quill.import('attributors/class/font');
FontAttributor.whitelist = [
  'sofia', 'slabo', 'roboto', 'inconsolata', 'ubuntu'
];
Quill.register(FontAttributor, true);
```

请注意，您仍然需要将这些类的样式添加到CSS文件中。

```html
<link href="https://fonts.googleapis.com/css?family=Roboto" rel="stylesheet">
<style>
.ql-font-roboto {
  font-family: 'Roboto', sans-serif;
}
</style>
```

#### 定制 Blots

由Blots代表的格式也可以定制。以下是如何更改用于表示粗体格式的DOM节点。

```js
var Bold = Quill.import('formats/bold');
Bold.tagName = 'B';   // Quill 默认使用 <strong> 标签
Quill.register(Bold, true);

// 正常初始化Quill
var quill = new Quill('#editor', {
  modules: {
    toolbar: true
  },
  theme: 'snow'
});
```

#### 扩展 Blots

您也可以扩展现有的格式。这是一个不允许格式化其内容的列表项的快速ES6实现。代码块正是以这种方式实现的。

```js
var ListItem = Quill.import('formats/list/item');

class PlainListItem extends ListItem {
  formatAt(index, length, name, value) {
    if (name === 'list') {
      // 容许改变或者移除列表格式
      super.formatAt(name, value);
    }
    // 否则的话忽略
  }
}

Quill.register(PlainListItem, true);

// 正常初始化
var quill = new Quill('#editor', {
  modules: {
    toolbar: true
  },
  theme: 'snow'
});
```

您可以通过调用`console.log(Quill.imports);`查看可用的Blots和Attributors列表；不支持直接修改这个对象。使用 [`Quill.register`](/docs/api/#register) 替换。

关于Parchment，Blots和Attributors的完整说明请查看Parchment的[README](https://github.com/quilljs/parchment/)。若想深入了解，请参照[Cloning Medium with Parchment](/guides/cloning-medium-with-parchment/)，这篇文章描述了如何将Quill从一个最初的文本编辑器设计为支持添加多种[媒体](https://medium.com/)内容的富文本编辑器的过程。大多数情况下，由于绝大多数已经在Quill中实现，所以不需要从头开始构建格式，但是了解Quill如何在更深层次上工作仍然很有用。
