# Styleable属性

一个 JavaFX 属性可以通过 css 使用 StyleableProperty 来设置样式。当控件需要通过 css 设置属性时，这很有用。

为了在控件上使用 StyleableProperty，需要使用 StyleableProperty 创建一个新的 CssMetaData。为控件创建的CssMetaData 需要添加到从控件的祖先获得的 List\<CssMetaData>中。然后从 getControlCssMetaData() 返回这个新列表。

按照惯例，具有 CssMetaData 的控件类将实现一个静态方法 getClassCssMetaData()，并且通常让 getControlCssMetaData() 简单地返回 getClassCssMetaData()。getClassCssMetaData() 的目的是允许子类轻松地包含某些祖先的 CssMetaData。

```java
// StyleableProperty
private final StyleableProperty<Color> color =
    new SimpleStyleableObjectProperty<>(COLOR, this, "color");

// Typical JavaFX property implementation
public Color getColor() {
    return this.color.getValue();
}
public void setColor(final Color color) {
    this.color.setValue(color);
}
public ObjectProperty<Color> colorProperty() {
    return (ObjectProperty<Color>) this.color;
}

// CssMetaData
private static final CssMetaData<MY_CTRL, Paint> COLOR =
    new CssMetaData<MY_CTRL, Paint>("-color", PaintConverter.getInstance(), Color.RED) {

    @Override
    public boolean isSettable(MY_CTRL node) {
        return node.color == null || !node.color.isBound();
    }

    @Override
    public StyleableProperty<Paint> getStyleableProperty(MY_CTRL node) {
        return node.color;
    }
};

private static final List<CssMetaData<? extends Styleable, ?>> STYLEABLES;
static {
    // Fetch CssMetaData from its ancestors
    final List<CssMetaData<? extends Styleable, ?>> styleables =
        new ArrayList<>(Control.getClassCssMetaData());
    // Add new CssMetaData
    styleables.add(COLOR);
    STYLEABLES = Collections.unmodifiableList(styleables);
}

// Return all CssMetadata information
public static List<CssMetaData<? extends Styleable, ?>> getClassCssMetaData() {
    return STYLEABLES;
}

@Override
public List<CssMetaData<? extends Styleable, ?>> getControlCssMetaData() {
    return getClassCssMetaData();
}
```

创建 StyleableProperty 和 CssMetaData 需要大量的样板代码，这可以通过使用 StyleablePropertyFactory 来减少。[StyleablePropertyFactory](https://docs.oracle.com/javase/8/javafx/api/javafx/css/StyleablePropertyFactory.html) 包含用相应的 CssMetaData 创建 StyleableProperty 的方法。

```java
// StyleableProperty
private final StyleableProperty<Color> color =
    new SimpleStyleableObjectProperty<>(COLOR, this, "color");

// Typical JavaFX property implementation
public Color getColor() {
    return this.color.getValue();
}
public void setColor(final Color color) {
    this.color.setValue(color);
}
public ObjectProperty<Color> colorProperty() {
    return (ObjectProperty<Color>) this.color;
}

// StyleablePropertyFactory
private static final StyleablePropertyFactory<MY_CTRL> FACTORY =
    new StyleablePropertyFactory<>(Control.getClassCssMetaData());

// CssMetaData from StyleablePropertyFactory
private static final CssMetaData<MY_CTRL, Color> COLOR =
    FACTORY.createColorCssMetaData("-color", s -> s.color, Color.RED, false); 

// Return all CssMetadata information from StyleablePropertyFactory
public static List<CssMetaData<? extends Styleable, ?>> getClassCssMetaData() {
    return FACTORY.getCssMetaData();
}

@Override public List<CssMetaData<? extends Styleable, ?>> getControlCssMetaData() {
    return getClassCssMetaData();
}
```

