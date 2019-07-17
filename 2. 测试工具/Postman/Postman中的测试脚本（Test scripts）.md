**一、postman测试脚本**



测试脚本是在发送请求之后运行的，并且已经从服务器接收到响应。



**二、测试举例**



**1、设置环境变量**



pm.environment.set("variable_key", "variable_value");



**2、将嵌套对象设置为环境变量**

1. var array = [1, 2, 3, 4];
2. pm.environment.set("array", JSON.stringify(array, null, 2));
3. var obj = { a: [1, 2, 3, 4], b: { c: 'val' } };
4. pm.environment.set("obj", JSON.stringify(obj));

**3、获取环境变量**



pm.environment.get("variable_key");



**4、获取环境变量（其值是严格化对象）**

1. *// These statements should be wrapped in a try-catch block if the data is coming from an unknown source.*
2. var array = JSON.parse(pm.environment.get("array"));
3. var obj = JSON.parse(pm.environment.get("obj"));

**5、清除环境变量**



pm.environment.unset("variable_key");



**6、设置全局变量**



pm.globals.set("variable_key", "variable_value");



**7、获取全局变量**



pm.globals.get("variable_key");



**8、清除全局变量**



pm.globals.unset("variable_key");



**9、获取变量**



pm.variables.get("variable_key");



**10、检查响应体是否包含字符串**

1. pm.test("Body matches string", function () {
2. ​    pm.expect(pm.response.text()).to.include("string_you_want_to_search");
3. });

**11、检查响应体是否等于字符串**

1. pm.test("Body is correct", function () {
2. ​    pm.response.to.have.body("response_body_string");
3. });

**12、检查JSON值**

1. pm.test("Your test name", function () {
2. var jsonData = pm.response.json();
3. ​    pm.expect(jsonData.value).to.eql(100);
4. });

**13、内容类型存在**

1. pm.test("Content-Type is present", function () {
2. ​    pm.response.to.have.header("Content-Type");
3. });

**14、响应时间小于200毫秒**

1. pm.test("Response time is less than 200ms", function () {
2. ​    pm.expect(pm.response.responseTime).to.be.below(200);
3. });

**15、状态代码为200**

1. pm.test("Status code is 200", function () {
2. ​    pm.response.to.have.status(200);
3. });

**16、代码名包含字符串**

1. pm.test("Status code name has string", function () {
2. ​    pm.response.to.have.status("Created");
3. });

**17、成功后请求状态代码**

1. pm.test("Successful POST request", function () {
2. ​    pm.expect(pm.response.code).to.be.oneOf([201,202]);
3. });

**18、为JSON数据使用TinyValidator**

1. var schema = {
2. "items": {
3. "type": "boolean"
4.  }
5. };
6. var data1 = [true, false];
7. var data2 = [true, 123];
8. pm.test('Schema is valid', function() {
9.   pm.expect(tv4.validate(data1, schema)).to.be.true;
10.   pm.expect(tv4.validate(data2, schema)).to.be.true;
11. });

**19、解码BASE64编码数据**

1. var intermediate,
2. ​	base64Content, *// assume this has a base64 encoded value*
3. ​	rawContent = base64Content.slice('data:application/octet-stream;base64,'.length);
4. intermediate = CryptoJS.enc.Base64.parse(base64content); *// CryptoJS is an inbuilt object, documented here: https://www.npmjs.com/package/crypto-js*
5. pm.test('Contents are valid', function() {
6.   pm.expect(CryptoJS.enc.Utf8.stringify(intermediate)).to.be.true; *// a check for non-emptiness*
7. });

**20、发送异步请求**

1. pm.sendRequest("https://postman-echo.com/get", function (err, response) {
2. console.log(response.json());
3. });

**21、将XML体转换为JSON对象**



var jsonObject = xml2Json(responseBody);



这些基本上都是最新的脚本，要是需要老版本的编写样式，请参考postman官方文档：[点击打开链接](https://www.getpostman.com/docs/v6/postman/scripts/test_examples)