# 使用Airtest批量执行案例脚本并聚合报告的方法（踩坑，详细教程）

置顶    

个人分类： [airtest](https://blog.csdn.net/flower_drop/article/category/9022115)



### 根据网络上各位大神总结的方法自己修改bug后给出的 教程  **批量执行Airtest脚本**

**1.项目结构如图：**

![img](https://img-blog.csdnimg.cn/20190611112222223.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Zsb3dlcl9kcm9w,size_16,color_FFFFFF,t_70)

**2.执行结果图：**

![img](https://img-blog.csdnimg.cn/20190611112323409.png)

**3.点击跳转相信报告**

![img](https://img-blog.csdnimg.cn/20190611113217337.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Zsb3dlcl9kcm9w,size_16,color_FFFFFF,t_70)

**4.summary_template.heml 源代码**

```html
<!DOCTYPE html>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<html>
<head>
    <title>测试结果汇总</title>
    <style>
        .fail {
            color: red;
			width: 7emem;
			text-align: center;
        }
        .success {
            color: green;
			width: 7emem;
			text-align: center;
        }
		.details-col-elapsed {
			width: 7em;
			text-align: center;
		}
		.details-col-msg {
			width: 7em;
			text-align: center;
			background-color:#ccc;
		}
    </style>
</head>
<body>
<div>
<div><h2>Test Statistics</h2></div>
    <table width="800" border="thin" cellspacing="0" cellpadding="0">
        <tr  width="600">
            <th width="300" class='details-col-msg'>案例名称</th>
            <th class='details-col-msg'>执行结果</th>
        </tr>
        {% for r in results %}
        <tr>
            <td class='details-col-elapsed'><a href="log/{{r.name}}/log.html" target="view_window">{{r.name}}</a></td>
            <td class="{{'success' if r.result else 'fail'}}">{{"成功" if r.result else "失败"}}</td>
        </tr>
        {% endfor %}
    </table>
</div>
</body>
</html>
```

**4.启动器源代码**

```python
from airtest.cli.runner import AirtestCase, run_script
from argparse import *
import airtest.report.report as report
import jinja2
import shutil
import os
import io
 
 
class CustomAirtestCase(AirtestCase):
    def setUp(self):
        print("custom setup")
        # add var/function/class/.. to globals
        # self.scope["hunter"] = "i am hunter"
        # self.scope["add"] = lambda x: x+1
 
        # exec setup script
        # self.exec_other_script("setup.owl")
        super(CustomAirtestCase, self).setUp()
 
    def tearDown(self):
        print("custom tearDown")
        # exec teardown script
        # self.exec_other_script("teardown.owl")
        super(CustomAirtestCase, self).setUp()
 
    def run_air(self, root_dir='F:\\airtest-runner\\suite', device=['Windows:///526006']):
        # 聚合结果
        results = []
        # 获取所有用例集
        root_log = root_dir + '\\' + 'log'
        if os.path.isdir(root_log):
            shutil.rmtree(root_log)
        else:
            os.makedirs(root_log)
            print(str(root_log) + 'is created')
 
        for f in os.listdir(root_dir):
            if f.endswith(".air"):
                # f为.air案例名称：手机银行.airK
                airName = f
                script = os.path.join(root_dir, f)
                # airName_path为.air的全路径：D:\tools\airtestCase\案例集\log\手机银行
                print(script)
                # 日志存放路径和名称：D:\tools\airtestCase\案例集\log\手机银行1
                log = os.path.join(root_dir, 'log' + '\\' + airName.replace('.air', ''))
                log_1 = script + '\\' + 'log'
                print(log)
                if os.path.isdir(log):
                    shutil.rmtree(log)
                else:
                    os.makedirs(log)
                    print(str(log) + 'is created')
                output_file = log + '\\' + 'log.html'
                args = Namespace(device=device, log=log_1, recording=None, script=script)
                try:
                    run_script(args, AirtestCase)
                except:
                    pass
                finally:
                    # rpt = report.LogToHtml(script, log)
                    rpt = report.LogToHtml(script, log_1, script_name=f.replace(".air", ".py"))
                    rpt.report("log_template.html", output_file=output_file)
 
                    result = {}
                    result["name"] = airName.replace('.air', '')
                    result["result"] = rpt.test_result
                    results.append(result)
        # 生成聚合报告
        env = jinja2.Environment(
            loader=jinja2.FileSystemLoader(root_dir),
            extensions=(),
            autoescape=True
        )
        template = env.get_template("summary_template.html", root_dir)
        html = template.render({"results": results})
        output_file = os.path.join(root_dir, "summary.html")
        with io.open(output_file, 'w', encoding="utf-8") as f:
            f.write(html)
        print(output_file)
 
 
if __name__ == '__main__':
    test = CustomAirtestCase()
    device = ['Windows:///526006']
    test.run_air('F:\\airtest-runner\\suite', device)
```

 