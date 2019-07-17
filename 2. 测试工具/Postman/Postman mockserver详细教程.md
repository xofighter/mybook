**1.发送一个request**



发送请求之后在History标签下保存了请求的数据，但是没有保存response 数据

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/3d30615d1fd64ebd8dd9fc75cb4c8be5/2639ed519ad843c7b6ef52d9d8776ee6.png)

**2.将request保存到一个Collection中**



点击Save 按钮，将请求数据保存到Collection中

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/fae9bd8809924ff68d6e4301779f8e3f/d136acfdeef544f0b8be51047154adcf.png)



弹出对话框，编辑请求名称和接口描述，选择一个Collection或者新建一个

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/bc13e48926cd4746bcba4bf86525706d/3ea6abddb2ef4ba79061c445b2e5c393.png)



**3.保存请求的响应作为一个example**



点击 Save Response 按钮，保存响应信息

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/f595df347a69416b8059ba4261104571/c33df0395b5e41cca92605489492483e.png)

输入mock 接口名称，点击Save Example 按钮，完成接口返回相信保存

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/107bca488b4b4ed596d7f430c27896b8/e13640a2290f417e9bc7bb0dea14c5bf.png)

**4.为Collection创建一个mock**



点击Collection右侧向右的">",再点击Mocks标签，点击Add a mock按钮

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/9a92358fe1bd4f7a9e7c1775ad210317/a491d349b5cd4463b358cb732b65f037.png)

弹出对话框，可以选择Environment，点击 Mock this collection 按钮

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/e421ed7e2d4f43d6a31628e7d64f6419/272bf8dcfca642f6825b58c9e4191314.png)

mock server新建完成，页面展示了当前mock server 的host地址

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/c726546d88b74f08bf89cdb1e7ab981b/185d43a60d1f4d10905b41beea1e019b.png)



**5.使用mock server 发送请求**



使用mock server 的host地址，请求我们刚跟mock的get接口，得到response

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/0236a6ee77a748a9961af3032d134b3c/3553907adc884cb099eb950821924e62.png)

**6.编辑mock接口的response数据**



去刚刚mock的Collection打开mock的接口，点击右上角“Examples”按钮，下拉展示了基于这个接口刚刚保存的examples，点击“mock test practice”（我刚刚保存命名的example name）

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/482fe3079c7f4c81b957a49e44805b0a/0315c0c00f9c44af970f2ce5f321a8f3.png)



我在response中新增了一个content字段，点击 “Save Examples”，保存修改后的数据

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/6649676fb1e9482889dd3b57843bd1f9/f2a1707f093f4287aa8c31f149d1a401.png)



再次访问刚刚的mock服务，可以看到response中展示了新增的字段content

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/bd213e1df29e4cb58cb1026ae7229300/23d24379e9f14f148f1fad755b3712e5.png)



**7.设置私有mock服务**



在Collection上添加mock服务，弹出的对话框上选中“Make mock server private”

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/efadf6e5d3824acfb0d0394f4ac00ab4/f4633b07d78342ac9a5edabea7fb7bbf.png)



点击“Get your Postman Pro API Key”，跳转到浏览器页面获取key，点击“Mock this collection”按钮完成mock 建立

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/fc005544589f47beaf2e756f90c77e21/c1c57f31563840f798a538b82f71eeb4.png)



直接请求刚刚的mock接口报错，提示header需要x-api-key

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/5cadd6ca11864d1abb24f26dcdb6c608/0985b3b395bf4cf9a27c8ffdae20a655.png)



将从浏览器的key值添加到header 部分的x-api-key，发送请求获得返回信息

![img](C:/Users/xu_bob/AppData/Local/YNote/data/bob_xu2014@163.com/ff641ba25b494edab95e4b39e44e0337/eb85da8680534f02902660ab5dc75e3b.png)