# 默认编辑器效果
创建一个编辑器的示例代码如下：
```
    <div id="editor">
    </div>

    <script type="text/javascript" src="/wangEditor-3.0.7.min.js"></script>
    <script type="text/javascript">
        var E = window.wangEditor
        var editor = new E('#editor')
        editor.create()
    </script>
```
注意，在这种方式下wangEditor会自动创建菜单和编辑区，并设置其css属性，那么表现的结果就是：编辑区高度固定为300px，无法拖拽放大，也无法自动扩展高度以适应编辑器内容。
![高度固定](./height%20auto.gif)

通过查看页面源码，我们发现编辑区的style属性如下：
```
border:1px solid #ccc; border-top:none; height:300px; z-index:10000;
```
而通过查看wangEditor的源码，我们发现有如下设置：
```
$textContainerElem.css('border', '1px solid #ccc').css('border-top', 'none').css('height', '300px');
```

# wangEditor初始化源码分析
wangEditor构造的时候是可以传入两个参数的：
```
// 构造函数
function Editor(toolbarSelector, textSelector) {
    this.toolbarSelector = toolbarSelector;
    this.textSelector = textSelector;
}
```
根据变量名可以知道，两个参数分别对应于菜单区和编辑区。而wangEditor具体的dom初始化代码如下：
```
    _initDom: function _initDom() {
        if (textSelector == null) {
            // 只传入一个参数，即是容器的选择器或元素，toolbar 和 text 的元素自行创建
            $toolbarElem = $('<div></div>');
            $textContainerElem = $('<div></div>');

            // 添加到 DOM 结构中
            $toolbarSelector.append($toolbarElem).append($textContainerElem);

            // 自行创建的，需要配置默认的样式
            $toolbarElem.css('background-color', '#f1f1f1').css('border', '1px solid #ccc');
            $textContainerElem.css('border', '1px solid #ccc').css('border-top', 'none').css('height', '300px');
        } else {
            // toolbar 和 text 的选择器都有值，记录属性
            $toolbarElem = $toolbarSelector;
            $textContainerElem = $(textSelector);
            // 将编辑器区域原有的内容，暂存起来
            $children = $textContainerElem.children();
        }
```
从编辑器源码中可以看到，如果只传一个参数，则wangEditor会自动创建toolBar和textContainer并设置默认style属性后添加到绑定的元素下；
而如果菜单区和编辑区两个参数都传入的情况下，则只是记录一下，并不修改其style属性。

# 自定义菜单和编辑区
因此，为了保持和默认编辑器样式一致(就是偷懒～)又能够保证编辑区的高度自适应，那么我们可以自定义菜单区和编辑区并在wangEditor构造的时候传入这两个selector：
```
    <div id="editor">
        <div style="background-color:#f1f1f1; border:1px solid #ccc;" class="editor-toolbar"></div>
        <div style="border:1px solid #ccc; border-top:none; height:auto;" class="editor-content"></div>
    </div>

    <script type="text/javascript" src="/wangEditor-3.0.7.min.js"></script>
    <script type="text/javascript">
        var E = window.wangEditor
        var editor = new E('#editor .editor-toolbar', '#editor .editor-content');
        editor.create()
    </script>
```
注意，我们这里只是把编辑区的style属性的height值由默认值300px改成了auto，效果如下：
![高度自适应](./height%20auto.gif)

当然实际场景中，为了显示美观我们需要给一个最小高度: min-height: 300px;
