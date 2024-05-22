# [[代码重构]使用Java多态和泛型重构一个业务场景的实现](https://github.com/humyna/gitblog/issues/26)

# 背景
一个业务场景需要针对来自不同数据源的数据进行格式化处理，需要处理成html表格形式，并以String返给前端。
ps:每个数据源返回的内容不同，列数也不同。

# 技术实现
## 不推荐实现
针对每个数据源单独实现解析方法处理，类似下面这样
```
private String geneHtmlTableFromAList( List<AInfo> list){
      // 实现转换逻辑
        return "";
    }

    private String geneHtmlTableFromBList( List<BInfo> list){
      // 实现转换逻辑
        return "";
    }

    private String geneHtmlTableFromCList(  List<CInfo> list){
      // 实现转换逻辑
        return "";
    } 
```


## 推荐实现
利用Java的多态和泛型特性将上述方法整合成一个方法。首先，定义一个接口或者抽象类来规范列表中元素的行为，然后让AInfo、BInfo和CInfo实现这个接口或继承这个抽象类。接口中定义一个方法，用于返回生成HTML表格所需的信息。

### s1.定义接口
toHtmlTableRow()，用于返回对象的HTML表格行表示。
```
public interface TableItemInterface {
    String toHtmlTableRow();
}
```

### s2.实现接口
让AInfo、BInfo和CInfo实现这个接口，并实现toHtmlTableRow()方法。
```
@Data
public class AInfo implements TableItemInterface {
    // 产品名称
    private String productName; 
    // 在线额度
    private String onlineLimit; 
     // 募集渠道
    private String raisingChannel;
    @Override
    public String toHtmlTableRow() {
        // 实现转换逻辑
        return "<tr>" +
        "<td>" + productName + "</td>" +
        "<td>" + onlineLimit + "</td>" +
        "<td>" + raisingChannel +"</td>"  +
        "</tr>";
    }
}

@Data
public class BInfo implements TableItemInterface {
    @Override
    public String toHtmlTableRow() {
        // 实现转换逻辑
        return "";
    }
}

@Data
public class CInfo implements TableItemInterface {
    @Override
    public String toHtmlTableRow() {
        // 实现转换逻辑
        return "";
    }
}
```

### s3.整合方法
```
@Component
public class TableItemHandler {
    <T extends TableItemInterface> String geneHtmlTableFromList(List<T> list, String tableHeadRow) {
        StringBuilder tableHtml = new StringBuilder();
        tableHtml.append("<table>").append(tableHeadRow);
        for (int i = 0; i < list.size(); i++) {
            tableHtml.append(list.get(i).toHtmlTableRow());
        }
        tableHtml.append("</table>");
        return tableHtml.toString();
    }
}
```

说明
tableHeadRow为表头，定义如下，不同的数据源定义不同的表头即可。
![image](https://github.com/humyna/gitblog/assets/2505439/40c09076-db51-4f3f-9469-5875f2fae34a)
