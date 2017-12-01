---
title: kettle-javacode
date: 2017-12-01 22:10:26
tags: kettle
---

最近因为团队需要，所以研究了下kettle的User Defined Java Class

为何要在kettle中使用"java code"?

更加灵活，kettle本身是个流程控制的工具，如果我们使用`java code`可以最大限度的挖掘kettle的潜力，我们可在`java code`中自由设置要
传入的参数要传出的格式，在中间使用不同的jar包完成相关信息的获取。

我们需要使用kettle来控制流程，我们还需要从ElasticSearcher中取出数据做进一步操作，而kettle并没有提供ES取数的相关对象,
这时`java code`就派上用场了，我们可以在java代码中实现对ES的查询，生成相应的存储媒介
(如:Excel,text等)，或者传出到下一个对象中。

##相关配置
在`java code`中引入三方jar,需要做相关的配置，这里可以参考以下博客进行配置
>https://anotherreeshu.wordpress.com/2015/02/07/using-external-jars-import-in-pentaho-data-integration/

主要分为五步：创建libext -> 放入jar -> 在Launcher.properties中添加`:../libext` -> 重启 -> 在代码中添加import com.*;
![](https://anotherreeshu.files.wordpress.com/2015/02/capture.png)

##Java Code代码编写
官方代码样例：

```
String firstnameField;
String lastnameField;
String nameField;
 
public boolean processRow(StepMetaInterface smi, StepDataInterface sdi) throws KettleException
{
    // Let's look up parameters only once for performance reason.
    //
    if (first) {
      firstnameField = getParameter("FIRSTNAME_FIELD");
      lastnameField = getParameter("LASTNAME_FIELD");
      nameField = getParameter("NAME_FIELD");
      first=false;
    }
 
    // First, get a row from the default input hop
    //
    Object[] r = getRow();
 
    // If the row object is null, we are done processing.
    //
    if (r == null) {
      setOutputDone();
      return false;
    }
 
    // It is always safest to call createOutputRow() to ensure that your output row's Object[] is large
    // enough to handle any new fields you are creating in this step.
    //
    Object[] outputRow = createOutputRow(r, data.outputRowMeta.size());
 
    String firstname = get(Fields.In, firstnameField).getString(r);
    String lastname = get(Fields.In, lastnameField).getString(r);
 
    // Set the value in the output field
    //
    String name = firstname+" "+lastname;
    get(Fields.Out, nameField).setValue(outputRow, name);
 
    // putRow will send the row on to the default output hop.
    //
    putRow(data.outputRowMeta, outputRow);
 
    return true;
}

```

关于processRow()的官方描述：
>The processRow() method is the heart of the step.  This method is called by the transformation in a tight loop and will continue until false is returned.

输入数据获取：
```
Object[] r = getRow();
String firstname = get(Fields.In, firstnameField).getString(r);
String lastname = get(Fields.In, lastnameField).getString(r);
```
数据输出：
```
Object[] outputRow = createOutputRow(r, data.outputRowMeta.size());
get(Fields.Out, nameField).setValue(outputRow, name);
putRow(data.outputRowMeta, outputRow);
```
数据输出你还要配置相应的输出字段

参考资料：

>官方wiki: 
https://wiki.pentaho.com/display/EAI/User+Defined+Java+Class
>相关接口文档:
>https://javadoc.pentaho.com/kettle/org/pentaho/di/trans/steps/userdefinedjavaclass/TransformClassBase.html
>相关博客：
>https://anotherreeshu.wordpress.com/2015/02/07/using-external-jars-import-in-pentaho-data-integration/
