       在上面一篇文章，我们定义了几个动作关键字，并把它们放入到DataEngine的Excel表格里。现在我们想在Selenium自动化测试脚本中去识别Excel中关键字，那么我们就需要利用Java去读取excel的数据，所以，这篇，我们主要来讲如何使用开源jar包工具—Apache POI来读取Excel文件数据。

 




搭建Data Engine—利用Apache POI

       我们需要一种方式去打开Excel和读取里面的数据，并且拿到Selenium自动化测试脚本中。为了实现这个场景，我这里使用Apache POI 类库，Apache POI是一个允许你使用Java去读取和创建和编辑微软的Excel文件格式的一种强大的工具包。我们下面要用到相关类或者方法去读取Excel数据，这些类或者方法是放在‘org.apache.poi.hssf.usermodel’包下。

 




从Excel中读取数据

 

1）下载Apache POIjar包文件，并添加到项目的lib类库.点击这里下载。

2）下载后解压，我们把poi-3.17.jar等添加到项目类库

 这些包需要添加到项目的librarys中去。



3）新建一个包，名称为utility。

4）在utility包下，新建一个类文件，名称为ExcelUtils.java

5）在ExcelUtils.java，输入以下内容

ExcelUtils.java

```
package utility;
 
import org.apache.poi.xssf.usermodel.XSSFCell;
import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
 
import java.io.FileInputStream;
import java.io.IOException;
 
public class ExcelUtils {
 
    private static XSSFSheet ExcelWSheet;
    private static XSSFWorkbook ExcelWBook;
    private static XSSFCell Cell;
 
    // 设置Excel文件路径，方便读取到文件
    public static void setExcelFile(String Path, String SheetName) throws IOException {
 
        FileInputStream ExcelFile = new FileInputStream(Path);
        ExcelWBook = new XSSFWorkbook(ExcelFile);
        ExcelWSheet = ExcelWBook.getSheet(SheetName);
    }
 
    // 读取Excel文件单元格数据
    public static String getCellData(int RowNum, int ColNum) throws Exception{
        Cell = ExcelWSheet.getRow(RowNum).getCell(ColNum);
        String CellData = Cell.getStringCellValue();
        return CellData;
    }
 
}
```
       下面，我们来修改DriverScript.java文件里面的内容，利用上面写的ExcelUtils类文件，我们在一个一个来读取进行测试。

```
package executionEngine;
import config.ActionsKeywords;
import utility.ExcelUtils;
 
 
/**
 * create by Anthony on 2018/1/30
 */
public class DriverScript {
 
    public static void main(String[] args) throws Exception {
        String excel_path = "C:\\Users\\ydbj0140\\IdeaProjects\\SeleniumKeywordFramework\\src\\dataEngine\\dataEngine.xlsx";
        // 加载读取excel文件
        ExcelUtils.setExcelFile(excel_path, "Test Steps");
 
        for (int iRow=1;iRow<=9;iRow++){
  
            String sActionKeyword = ExcelUtils.getCellData(iRow, 3);
 
            // 和excel文件中关键字进行对比
            if(sActionKeyword.equals("openBrowser")){
                // 如果Excel文件中存在openBrowser的关键字就会调用openBrowser()方法，进行相关操作；下面其他关键字同理。
                ActionsKeywords.openBrowser();
            } else if(sActionKeyword.equals("openUrl")){
                ActionsKeywords.openUrl();
            } else if(sActionKeyword.equals("click_Login_link")){
                ActionsKeywords.click_Login_link();
            } else if(sActionKeyword.equals("input_Username")){
                ActionsKeywords.input_Username();
            } else if(sActionKeyword.equals("input_Password")){
                ActionsKeywords.input_Password();
            } else if(sActionKeyword.equals("click_Submit")){
                ActionsKeywords.click_Submit();
            } else if(sActionKeyword.equals("closeBrowser")){
                ActionsKeywords.closeBrowser();
            }
 
        }
 
    }
}
```
       测试结果：通过，可以看到登录百度的效果（这里登录后提示输入验证码不是本系列解决的重点，这个问题不用管）。

项目代码结构如下图
![img](https://img-blog.csdn.net/20180201175733919?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

       到这里，我们已经实现了一个关键字驱动框架的骨架部分，关键字驱动框架的重点思路就是如何从Excel文件中读取和判断关键字，所以，所有的测试用例都可以写入到Excel文件。但是目前这个框架就是一个草稿，接下来我们要去给这个框架增加一点东西，变得丰满一点。