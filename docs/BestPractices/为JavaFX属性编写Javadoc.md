# 为JavaFX属性编写Javadoc

## 背景

为 JavaFX API 编写文档与为 Java API 编写文档似乎没有太大区别，但我们大多数人都没有意识到有哪些工具可以简化我们的工作。

在编写JavaFX应用程序或设计 JavaFX 控件时，开发人员会添加各种JavaFX属性，这些属性通常由一个字段和三个方法组成，即 JavaFX 属性方法、setter 和 getter。这些方法通常是公开的，因此应该记录下来。当大多数方法都是隐含的时候，为所有这三种方法编写 Javadoc 是没有意义的。然而，应该有一些文档和方法来显示所有这些方法之间的联系。

JavaFX 团队非常周到，为 javadoc 命令引入了一个特殊的选项 “-javafx”。此选项引入了以下灵活性:

- 使用标准 doclet 的 JavaFX 扩展生成 HTML 文档。除了标准 Java doclet 生成的其他摘要部分之外，生成的文档还包括一个“属性摘要”部分。列出的属性链接到每个属性的 getter 和 setter 方法的部分。

- 如果没有为 getter 和 setter 方法显式编写的文档注释，则来自属性方法的文档注释将自动复制到为这些方法生成的文档中。

- 添加了一个新的 @defaultValue 标签，允许记录属性的默认值。

- 添加了一个新的 @treatAsPrivate 标签，增加了不发布公共方法文档的灵活性，这是实现细节的一部分。

```java
javadoc -javafx MyClass.java -d testdir
```

## 示例

通常，如果您引入 JavaFX 属性字段，您将添加其相应的属性方法以及 setter 和 getter。在这种情况下，建议将它们捆绑在一起，只记录字段。javadoc 命令上的 "-javafx" 选项将为其余方法生成适当的文档。

注:如果您想为方法添加显式信息，您可以为单个方法编制文档。

```java
/**
 * Specifies whether this {@code Node} and its child nodes should be rendered
 * as part of the scene graph. A node may be visible and yet not be shown
 * in the rendered scene if, for instance, it is off the screen or obscured
 * by another Node. Invisible nodes never receive mouse events or
 * keyboard focus and never maintain keyboard focus when they become
 * invisible.
 *
 * @defaultValue true
 */
private BooleanProperty visible = new SimpleBooleanProperty(this, "visible", true);

public final void setVisible(boolean value) {
    visibleProperty().set(value);
}

public final boolean isVisible() {
    return visible.get();
}

public final BooleanProperty visibleProperty() {
    return visible;
}
```

