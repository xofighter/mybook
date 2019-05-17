[TOC]

# 关于平湖人社APP后台开发总结
> 这是我第一次做联合开发,对于多部门多人员合作开发项目我这个才毕业没多久的新人菜鸟还是有很多挑战与困难的,不过只有在实际项目开发中才能学到东西.话不多说,我们直奔主题.(忽略我使用英文标点符号,开发不易啊…)

## APP后台接口开发我总结出以下几个部分:
- 需求分析与整理
- 分析业务需求涉及的模块功能与数据库关联表
- 接口开发文档大致编写并提交产品与APP开发人员
- 存储过程接口编写
- 存储过程接口测试
- APP端完成模块功能后联合测试调整
- 完成各项测试后上线正式版本
### 需求分析与整理
> 需求分析主要是我在现场与客户协商探讨分析大体上需要完成的功能和APP原型,然后整理后前期需求给产品经理,后产品过来与客户再进行商讨具体功能之类事宜.我主要是了解大体上要做的东西和业务系统之间的区别关联,方便日后去参考业务系统判断编写规则.这期间我就要准备相关的接口编写事宜了,大体的接口编写我其实是不了解的,之前都没接触过,之后一点一点的询问项目经理,好在项目经理跟我说之前其他项目组曾今做个接口开发的模板我可以去借鉴下.这就我开始接口开发的准备了.

### 分析业务需求涉及的模块功能与数据库关联表
在产品经理整理好需求文档和APP原型图发于我后开始分析自己要完成的任务有哪些 

![éæ±ææ¡£æªå¾](https://img-blog.csdn.net/20180426125337821?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzM1NzkwODEy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
这里我就取APP中最主要的功能模块–人员续保做实例. 
这个功能主要是用于人员续保操作,其中涉及的数据表结构多,判断规则多,完成难度大.当然也是我花费时间最多的功能了. 主要很多续保的规则我通过业务系统代码来看也是不够的,好在客户理解续保的麻烦跟我很细致的解说并在后续开发测试中不予余力的提供帮助,非常感谢他们.在完成续保需求分析后,我先不急着编写接口,主要是现在写也写不好,必须要先进行接口功能UML建模(其实是之前的接口编写吃过亏,没有细致分析判断规则导致代码十分臃肿,不易于阅读理解…)所以建模很重要!!!有助于理解复杂关系判断.

**部分判断流程图：**

```flow
st=>start: 开始续保
e=>end: 续保成功
op1=>operation: 校验人员单位信息/基数范围/险种类型...
cond1=>condition: 是否符合判断规则？
op2=>operation: 校验参保单位参保情况/工伤生育可多单位参保
cond2=>condition: 是否符合判断规则？
op3=>operation: 校验灵活人员单工伤/特殊企业不可参保
cond3=>condition: 是否符合判断规则？
op4=>operation: 插入数据库
cond4=>condition: 数据是否插入成功？
st->op1->cond1
cond1(yes)->op2
cond1(no)->op1
op2->cond2
cond2(yes)->op3
cond2(no)->op1
op3->cond3
cond3(yes)->op4
cond3(no)->op1
op4->cond4
cond4(yes)->e
cond4(no)->op1
```


### 接口开发文档大致编写并提交产品与APP开发人员
接口开发文档以前也是没有写过的,难以下笔啊.一样是询问项目经理完成的…

#### 1.主要是webservise 地址: http://XXXX/services/SbcxWebservice?wsdl
#### 2.接口函数统一为 sbp_sbcx_XXXX
#### 3.传入参数: 传入参数全为Stirng类型，格式为json字符串，基本格式为(传出类似不列出)：

| 字段名称 | 类型 | 可否为空 | 字段说明 |
|-----|-----|-----|-----|
|jhjd | string | 否 | 交换节点 |
|sbbh | string | 否 | 社保编码 |
|certno | string | 否 | 身份证号 |
|jylx | string | 否 | 交易类型 |
```json
//json格式内容：
{"jhjd":"330482","sbbh":"000001","certno":"330425199009098787","jylx":"1"}
```
#### 4.数据字典 :对部分字段进行补充示意
代码块语法遵循标准plsql代码，贴出部分代码吧(也就是存储过程编写):
```plsql
--1007人员续保申报
  Procedure sbp_sbcx_1007(as_inmsg in long, as_outmsg out clob) is

    al_inmsg      long;
    j_JsonmainIn  Json.Jsonstructobj; --主信息JSON
    Ret_ErrCode   varchar2(10); --错误代码
    Ret_Errmsg    varchar2(32670); --提示信息
    j_JsonmainOut Json.Jsonstructobj; --主信息JSON
    j_Json        Json.Jsonstructobj; --主信息JSON
    t_Array       Sbp_Public.t_Varchar2;
    iv_dwbm       varchar2(30);
    iv_sbbh       varchar2(30);
    iv_sfzh       varchar2(30);
    iv_xm         varchar2(30);
    iv_xb         varchar2(4);
    iv_csrq  varchar2(30); --出生日期        
    iv_hkxz  varchar2(30); --户口性质        
    iv_lxdh  varchar2(30); --联系电话        
    iv_lxdz  varchar2(50);-- 联系地址        
    iv_ygxz  varchar2(30); --用工性质        
    iv_jfjs  varchar2(30);--缴费基数         
    iv_xbyy  varchar2(30); --续保原因   
 Begin
 Ret_ErrCode := 'B00';
    Ret_Errmsg  := null;
    --判断传入参数
    app_sjjc.P_Dissemble('1007','1',as_inmsg,Ret_ErrCode,Ret_Errmsg);
    if Ret_ErrCode = 'ERROR' then
      sbp_err(Ret_ErrCode, Ret_Errmsg, as_outmsg);
      Return;
    end if;
    ---解包
    al_inmsg := as_inmsg;
    ---转换成json
    j_JsonmainIn := Json.String2json(al_inmsg, '"'); --转换成JSON


iv_dwbm := Json.Getattrvalue(j_JsonmainIn, 'dwbm'); --取得单位编码;
iv_sbbh := Json.Getattrvalue(j_JsonmainIn, 'sbbh'); --取得身份证号
iv_sfzh := Json.Getattrvalue(j_JsonmainIn, 'zjhm'); --取得身份证号
iv_xm   := Json.Getattrvalue(j_JsonmainIn, 'xm'); --取得姓名
iv_csrq := Json.Getattrvalue(j_JsonmainIn, 'csrq');
iv_hkxz := Json.Getattrvalue(j_JsonmainIn, 'hkxz');
iv_lxdh := Json.Getattrvalue(j_JsonmainIn, 'lxdh');
iv_lxdz := Json.Getattrvalue(j_JsonmainIn, 'lxdz');
iv_ygxz := Json.Getattrvalue(j_JsonmainIn, 'ygxz');
iv_jfjs := Json.Getattrvalue(j_JsonmainIn, 'jfjs');

begin  

  select * into rec_ac01 from ac01 where aae135 = iv_sfzh;

  select aaz001 into v_aaz001 from ab01 where aab001 = iv_dwbm;
exception
  when others then 
     Ret_ErrCode := 'ERROR';
     Ret_Errmsg  := '未找到人员信息或单位信息';
     sbp_err(Ret_ErrCode, Ret_Errmsg, as_outmsg);
     Return;
end;  

select aab019 into v_aab019 from ab01 where aab001 = iv_dwbm;
if v_aab019 = '99' then
     Ret_ErrCode := 'ERROR';
     Ret_Errmsg  := '行业统筹类型单位不允许手机参保,请去社保中心办理';
     sbp_err(Ret_ErrCode, Ret_Errmsg, as_outmsg);
     Return;
end if;


-- ac02 无信息
   --插入ac21
    select sq_aaz308.nextval into rec_ac21.aaz308 from dual;
    rec_ac21.aaa027 := rec_ac01.aab301;
    rec_ac21.aac001 := nvl(i_aac001,rec_ac01.aac001); 
    rec_ac21.aae140 := v_cbxz; 
    rec_ac21.aae030 := n_aae002;
    rec_ac21.aic001 := 0; 
    rec_ac21.eac070 := '0';
    rec_ac21.aac033 := to_char(sysdate,'yyyyMMdd');
    rec_ac21.aae206 := null;
    rec_ac21.aae011 := 'app';
    rec_ac21.aae036 := sysdate;
    select sb_prseno.nextval into rec_ac21.prseno from dual;
    select sq_aaz159.nextval into rec_ac21.aaz159 from dual;
    rec_ac21.aac050 := '01';
    rec_ac21.eaz132 := '1';
    rec_ac21.aac008 := '1';
    insert into ac21 values rec_ac21;

   commit;
Json.Newjsonobj(j_JsonmainOut, TRUE);
j_JsonmainOut := Json.Addattr(j_JsonmainOut, 'recode', Ret_ErrCode); --交易结果
j_JsonmainOut := Json.Addattr(j_JsonmainOut, 'remessage', Ret_Errmsg); --交易结果信息

Json.Closejsonobj(j_JsonmainOut);

--json转化成数据
as_outmsg := Json.Json2string(j_JsonmainOut);
Return;

  Exception
    When Others Then
      Ret_ErrCode := 'ERROR';
      Ret_Errmsg  := '交易异常出错:' || sqlerrm;
      sbp_err(Ret_ErrCode, Ret_Errmsg, as_outmsg);
      Return;
 End;
```

接口开发中主要的是对一些判断逻辑进行编写,当一切判断规则都符合后才进行表增删改查操作.

### 存储过程接口测试
写完接口肯定是先要自己这边测试下的啊,功能判断是否正确,这时候就瞎XX 输入可能的错误正确情况了… 存储过程的调试就一个一个试咯. 

![è°è¯å¥å£æªå¾](https://img-blog.csdn.net/20180426133305502?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzM1NzkwODEy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这里吐槽下续保的判断真是多啊,多到我都佩服制定这规则的人怎么想到这么细致的呢,什么脑子脑子脑…

### 接口开发文档完善细致编写并提交产品与APP开发人员
接口开发好了就要修改完善之前写的接口文档了,那个先前版本的文档是让APP端先进行大体开发写的(同步开发!),具体的参数现在要调整之类的.这期间也是有烦人事的,不过都是比对前后变化的问题. 然后就是发给产品和APP开发了,我这边稍稍喘口气,等APP端完成模型和功能再联调吧!

### APP端完成模块功能后联合测试调整

![ä¸ä¼ ä¸ªå¤æ­æªå¾](https://img-blog.csdn.net/20180426174051693?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzM1NzkwODEy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

联调是个尽出幺蛾子的过程,什么奇葩不可能不理解的错误都跳出来嘚瑟了.APP前端改,APP服务端改,我这边后台改,改,改…产品的消息不停啊.也是这个时候才能大规模发现问题不足.不过测试的妹子qq倒是加了不少=-=

### 完成各项测试后上线正式版本

![æµè¯é®é¢æ¶å½](https://img-blog.csdn.net/20180426173742572?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzM1NzkwODEy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


等各项测试完成且确认没有问题后,将测试库的接口程序移植到正式库,APP端也将程序部署到正式版本供用户使用,一个完整的APP开发即结束了.

最后感觉一个APP的开发流程跟我之前想象中还是有很多区别的.比如大部分几乎所有的判断逻辑都是我在接口里写的,还以想java一样呢,不过确实写在存储过程里感觉更快一点呢…不知道对数据库性能有什么影响(待研究!)

### 待提升的地方还有很多:
- 判断逻辑有待优化
- 查询语句有待优化
- http请求连接方面有待加深
- plsql语法有待学习
- 与客户,项目成员交流有待提升(毕竟沟通的好会让开发事半功倍!切身体会啊)

---------------------
