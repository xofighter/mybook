# Jmeter-BeanShell PostProcessor传递参数



BeanShell PostProcessor可以有两个变量接受参数

Parameters ：原样接收参数

bsh.args：为字符串数组，数组元素为参数按照空格split后的结果



![img](https://upload-images.jianshu.io/upload_images/8779131-6bcf92be7bc15198?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



![img](https://upload-images.jianshu.io/upload_images/8779131-73cf53bc678c62b0?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



--------------

String newtoken=bsh.args[0];
print(newtoken);
${__setProperty(newtoken,${token},)};

String newcompanyId=bsh.args[1];
print(newcompanyId);
${__setProperty(newcompanyId,${companyId},)};

String newuserId=bsh.args[2];
print(newuserId);
${__setProperty(newuserId,${userId},)};

String newuserName=bsh.args[3];
print(newuserName);
${__setProperty(newuserName,${userName},)};

String neworgCode=bsh.args[4];
print(neworgCode);
${__setProperty(neworgCode,${orgCode},)};

String newcostCenterName=bsh.args[5];
print(newcostCenterName);
${__setProperty(newcostCenterName,${costCenterName},)};

String newprofitCenterName=bsh.args[6];
print(newprofitCenterName);
${__setProperty(newprofitCenterName,${profitCenterName},)};

String newcostCenterID=bsh.args[7];
print(newcostCenterID);
${__setProperty(newcostCenterID,${costCenterID},)};

parameters 写法 参数之间必须 空格 ，

${token} ${companyId} ${userId} ${userName} ${orgCode} ${costCenterName} ${profitCenterName} ${costCenterID}

否则  bsh.args[0]里面没有内容会报错

2018-12-20 00:03:24,131 ERROR o.a.j.u.BeanShellInterpreter: Error invoking bsh method: eval Sourced file: inline evaluation of: ``String newtoken=bsh.args[0]; print(newtoken); ; String newcompanyId=bsh.args[1] . . . '' : Typed variable declaration

 

![img](https://img2018.cnblogs.com/blog/183523/201812/183523-20181219235410498-1442253912.png)