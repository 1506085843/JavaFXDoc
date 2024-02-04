# 忽略Null选择绑定表达式的警告

当使用 JavaFX Bindings 类时，您可能会在控制台日志中获得评估警告。当对可能为空的对象使用绑定表达式时，就会出现这种情况。例如，一个 TableView 选择可能会驱动按钮的启用状态。当 TableView 第一次显示时，没有选择。这将在WARNING 级别的日志中显示一个错误。

```java
Dec 31, 2016 9:11:14 AM com.sun.javafx.binding.SelectBinding$SelectBindingHelper getObservableValue
WARNING: Exception while evaluating select-binding [taxable]
```

“应税的”是 POJO 上的布尔属性。导致此消息的表达式如下所示。

```java
    btnCalcTax.disableProperty().bind(
        tblItems.getSelectionModel().selectedItemProperty().isNull().or(
                Bindings.select(
                    tblItems.getSelectionModel().selectedItemProperty(),
                    "taxable"
                ).isEqualTo(false)
        )
    );
```

如果没有表选择，或者如果有表选择，则所选项目的应税字段设置为 false，则前面的语句将禁用 Calc Tax 按钮。也就是说，只有在选择应税项目时，才应该启用 Calc Tax 按钮。

JavaFX 绑定使用 Java 日志记录，因此将冗长度提高到 SEVERE 将忽略 WARNING 级别消息。相反，如果您希望降低详细信息以查看支持 WARNING 的堆栈跟踪，请将详细信息降低到 FINE。这条语句被添加到日志中。属性文件。可以通过指定 -Djava.util.logging.config 来指示程序使用该文件。文件中的命令。

```java
javafx.beans.level=SEVERE
```

对于不需要单独文件或命令修改的快速检查，您可以将其添加到程序中。由于对 Sun 类的依赖，您应该在提交之前删除它。

```java
Logging.getLogger().setLevel(PlatformLogger.Level.FINE )
```

JavaFX WARNING 对于普通用例来说可能太强了。有一个JDK-8162600 的票证可以在以后的JavaFX版本中解决这个问题。