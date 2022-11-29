- **背景**
- 结算项目组提出需要在自动导出中支持复杂表头的能力，目前的自动导出只支持简单的单列表头导出。不满足结算项目组要求。故需要在DFA-Boot中支持复杂表头的能力。
- **功能梳理**
- 我们将按照导出的产物（Excel和Pdf）的不同分别梳理当前DFA-Boot中的支持能力，并且从现有方法中提取出前端能够展示的数据结构，用于让前端传入用于绘制合并表头的数据。
- **Excel导出**
- 现有的DFA-Boot中对于Excel导出的功能使用的是poi，poi对合并单元格的支持方式如下：
- // 合并单元格，参数依次为起始行，结束行，起始列，结束列 （索引0开始）
  sheet.addMergedRegion(new CellRangeAddress(0, 0, 0, 6));//标题合并单元格操作，6为总列数
- 需要传入四个参数：起始行，结束行，起始列，结束列。
- **Pdf导出**
- 现有的DFA-Boot中对于Pdf导出的功能使用的是itext 5.x，itext对合并单元格的支持方式如下:
- PdfPCell titleOne=new PdfPCell(litleParagraph);
  titleOne.setColspan(8); 
  titleOne.setBorder (0);
  titleOne.setHorizontalAlignment (Element.ALIGN_CENTER);
  table.addCell(titleOne);
- **设计说明**
- 由于上面两种格式的计算方法有所不同。我们需要找出一个统一的数据结构用于表达合并单元格的需求，并且能够在两个工具中均能完整的表达。故我们设计的格式如下：
- ![image.png](../assets/image_1667979340571_0.png){:height 124, :width 778}
-
-
- 例如上面是我们需要绘制的一个复杂表头。我们可知最大的列是5，最大的行是3。此时我们设计的数据结构表达如下：
- ```json
  [
    [{"title":"A", "row": 3 },{"title":"B","col":2}{"title":"C","col": 2}],
    [{"title":"B1"},{"title":"B2"},{"title":"C1"},{"title":"C2"}],
    [{"title":"B11"},{"title":"B21"},{"title":"C11"},{"title":"C21"}]
  ]
  ```
-
- **存放位置**
- 此部分json的存放位置可以下面的位置：
- ```javascript
  "exportParams": {
        "fileType": "fileexcel",
        "exportFileName":"myexcel",
        "customHeader":[
            [{"title":"A", "row": 3 },{"title":"B","col":2}{"title":"C","col": 2}],
            [{"title":"B1"},{"title":"B2"},{"title":"C1"},{"title":"C2"}],
            [{"title":"B11"},{"title":"B21"},{"title":"C11"},{"title":"C21"}]
        ]
   }
  ```
- 如果没有传此参数，则依然走原有逻辑。
- 由上述的JSON信息，我们可以按照以下方法进行操作。
- **Excel支持多表头导出**
- 由以上的数据，我们可以得知A只有一个单元格，但是他有3列，B在第一行第二个开始，C在第一行的第四个开始，并且有2个单元格。故，可以得到A、B和C的合并命令是
- ```java
    new CellRangeAddress(0, 2, 0, 0));  
    new CellRangeAddress(0, 0, 1, 2));  
    new CellRangeAddress(0, 0, 3, 4));
  ```
- 以此类推，由于上述的表格后面都不需要合并，故无生成合并内容的必要。
- **PDF支持多表头导出**
- 由以上的数据，我们可以得知A只有一个单元格，但是他有3列，B在第一行第二个开始，C在第一行的第四个开始，并且有2个单元格。故，可以得到A、B和C的合并命令是：
- ```java
    - PdfPCell titleA=new PdfPCell(litleParagraph);  
    titleA.setRowspan(3)  
    titleA.setBorder (0);  
    titleA.setHorizontalAlignment (Element.ALIGN_CENTER);  
    table.addCell(titleA);  
  - PdfPCell titleB=new PdfPCell(litleParagraph);  
    titleB.setColspan(2);   
    titleB.setBorder (0);  
    titleB.setHorizontalAlignment (Element.ALIGN_CENTER);  
    table.addCell(titleB);  
  - PdfPCell titleC=new PdfPCell(litleParagraph);  
    titleC.setColspan(2);   
    titleC.setBorder (0);  
    titleC.setHorizontalAlignment (Element.ALIGN_CENTER);  
    table.addCell(titleC);
  ```