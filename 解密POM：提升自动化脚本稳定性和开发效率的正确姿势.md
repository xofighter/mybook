![img](https://mmbiz.qpic.cn/mmbiz_png/CMOUUEcN8Fer3BicrXSYcXAAlMHibqCcLWCYzY8nlTpo7P7wDS8XMD0LfVwMJ7ZN80Dg8esSkjh6VoEwpC5LQ01w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

长期以来，WEB或者UI自动化测试因为高维护成本、运行速度慢、假失败(False Failure)等问题饱受诟病。本文的目的，是通过介绍Page Object以及其它WEB自动化测试中有关封装的设计模式，分享如何进行有效的设计，提高测试项目的可维护性，可重用性，降低WEB自动化测试项目的总成本，提高工作效率。当然，任何一个类型的自动化都不是免费的，都是需要持续不断的成本投入，包括团队的学习成本。



# 一、	**页面对象模式**(Page Object Model, POM)

## 1.	QTP时代的探索

　　QTP是Quicktest Professional的简称，是Mercury公司推出的一种自动测试工具，并随着惠普公司的收购，并入惠普ALM产品线，称为UFT(目前又转入Micro Focus公司)。QTP是曾经的商用自动化测试工具的霸主，集结了当时的许多自动化测试的最佳实践，如对象库(Object Repository)、参数化方法(Parameterization and Action)、业务流程测试(Business Process Testing)等等。甚至笔者个人认为，本文所介绍的Page Object设计模式，并不是Selenium所独创的，在QTP时代就已经出现，如下例：

Browser("browserName").Page("pageTitle").Image("imgID").Click

通过浏览器->页面->页面元素->方法(或属性）的方式完成了一个页面中元素的定位以及操作。从这种类型的代码中可以看出，QTP中面向对象的思想其实已经展露无遗，只是QTP的高昂许可证费用，以及对于VBS的依赖等原因，约束了其在WEB2.0时代的发展。



## 2.	Selenium与POM

　　伴随着WEB2.0时代到来，2004年来自ThoughtWorks的Jason Huggins发布了Selenium这一划时代的开源WEB自动化测试工具，并在2007年Simon Stewart将他的Webdriver项目与Selenium项目进行合并，形成了也就是目前大家所熟知的Selenium 2.0。Selenium开发团队的另一大贡献，是伴随着Selenium这一工具推出的同时，大力推广POM这一设计模式。Selenium官网是这样介绍的POM的：

A page object is an object-oriented class that serves as an interface to a page of your AUT. The tests then use the methods of this page object class whenever they need to interact with that page of the UI. 

　　简单理解来说，就是将每一个页面封装成一个PO类，将页面上的操作封装成为这个类的方法。测试用例通过这个类的实例和方法的调用完成和页面的交互，达到自动化测试的目的。同时，也通过这种方式，实现了页面变更的有效隔离，避免了页面元素变化在用例中的扩散。



## 3.	三段式页面封装法

POM 作为一种设计模式，Selenium官方也提供了类似PageFactory以及基于注释的@Findby的实现方式。笔者结合工作实践，介绍一种三段式的页面封装法来实现POM，将页面封装过程划分为元素定位、元素获取、功能操作这三部分。以TestLink的登陆页面为例，如图1所示，页面的中完成登录的主要控件，是一个用户名输入框和一个密码输入框，以及一个登录按钮。

![img](https://mmbiz.qpic.cn/mmbiz_png/CMOUUEcN8Fer3BicrXSYcXAAlMHibqCcLWGTZV6hIO1niaLmg9bwGypsNsticSFd20tichKuwODhichl7MdwUH0r36uQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图1 TestLink登录

这个登陆页面可以用如下代码进行封装，
```
public class LoginSeMet extends SeMet  {  //继承自SeMet基类

@Override

public void prepare() {

waitfor(1000);	    //简单地等候一下

}

public LoginSeMet(){

		super("name="+formName); //页面定位

		prepare();

	}

//第一段，元素定位串。以下四个为页面元素定位信息。这里为了简单起见采用直接写在代码中。在工程实践中，也存在着将定位串数据进行集中处理的其它方式，这里就不赘述。

	private static String formName="login_form";

	private static String loginLocator="//input[@id='login']";

	private static String pwdLocator="//input[@name='tl_password']";

	private static String submitLocator="//input[@name='login_submit']";

//第二段，元素获取。该部分依据前述给定的元素定位串，通过调用Webdriver的API，进行实际的元素查找与实例的返回。请读者注意这里没有使用变量而是使用方法来定义元素，提供的是一个动态获取的元素实例，从而规避页面发生了重载或者刷新等问题导致的元素找不到等问题。

	public WebElement getLoginInputField(){

		return getElementUsingXpath(loginLocator);

	}

		public WebElement getPWDInputField(){

		return getElementUsingXpath(pwdLocator);

	}

	public WebElementgetLoginButton(){

		return getElementUsingXpath(submitLocator);

	}

	//第三段，业务操作。该部分依据测试的实际需要，针对该页面上常用的一些业务操作进行封装，提供给用例编写人员进行调用。该部分的目的，是让用例编写人员能将注意力集中在用例设计与实现上，而不是更底层的webdriver技术细节。

	public LoginSeMet setLoginName(String loginName){

		WebElement element=getLoginInputField(); //获取输入框元素实例

		element.clear();  //清空输入框

		element.sendKeys(loginName);  //输入用户名

		return this;

	}

	public LoginSeMet setPassword(String password){

		WebElement element=getPWDInputField();

		element.clear();

		element.sendKeys(password);

		return this;

	}

	public void submit(){

		WebElement element=getLoginButton();

		element.click();

	}

	public void login(String loginName,Stringpassword){

		setLoginName(loginName).setPassword(password).submit();

		waitForContent();

	}

}
```
因此，在测试用例中，可以直接通过类似下述的代码，完成登录操作。

​				LoginSeMetloginPage= new LoginSeMet();

​				loginPage.login("IamTester","MyPassord");

至此，基础的POM就介绍完毕了。



**二、	Composite Page Object**（组合PO）

　　Peter Tahchiev在《Junit in Action》中所提出了"组合优先于继承"的最佳实践[1]。在进行页面自动化封装时，可以考虑将此类页面按照一定的方式进行合理拆分，分别进行封装，再聚合成一个完整的页面类。这种方式，比通过类继承的方式进行页面的组合提供了更多的灵活性，也更为简洁，有利于后期的复用与维护，更符合单一职责原则(Single Responsibility Principle, SRP)。在本小节将介绍2种常见的聚合模式的应用场景。



**1.	控件封装**

在一个软件的开发架构选型过程中，有一个决策几乎是必须要做的，也就是选用何种前端框架。诚然，前端开发早已摆脱了水泥+砖头的初级阶段，通过如JQuery、Extjs、AngularJs等各种开源或者商用的框架，甚至公司自身开发的框架，在解放开发人员生产力的同时，也保障了前端设计的模块化、前后端接口以及用户体验的统一。这些百花齐放的前端框架，一方面给web 自动化测试带来了一系列的挑战。另一方面，也提供了一种参考思路，既然开发工程师可以通过前端框架和附带的控件库进行快速开发，测试工程师在编写web自动化测试用例时，也应该有一套设计良好的前端控件库供其调用，而不是直接用webdriver等工具与SUT(System Under Test)进行交互。   

![img](https://mmbiz.qpic.cn/mmbiz_png/CMOUUEcN8Fer3BicrXSYcXAAlMHibqCcLWibWBpIRQj4xkqZNbLeWNdUiaicb88RKDO9pEyxtTcgAceSgYvm9qI2lZg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图 2 树

![img](https://mmbiz.qpic.cn/mmbiz_png/CMOUUEcN8Fer3BicrXSYcXAAlMHibqCcLW6BzPmUcv5ibSF0GiaZFlRNPUavTqeMuvxAKhib9V51Gic8ZY2P1umgJic0A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图3 左右选择框

![img](https://mmbiz.qpic.cn/mmbiz_png/CMOUUEcN8Fer3BicrXSYcXAAlMHibqCcLWXziaIGib6Vice8U9u5NgSzab8pzbeOPK1xniaE7QeyfypicYhNxibNv408Ow/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图4 表



　　因此，WEB自动化测试的一项重要的基础性工作，就是通过分析SUT所使用的前端控件库，合理地实现封装，供外部使用者调用。通过简单分析，可以发现一个WEB页面总是由若干个页面控件所组成的，那么应用POM模式，这个页面的PO类，可以是组成这个页面的若干个控件所对应的控件类的实例聚合而成的。将如图2~图4所示的一些TestLink典型的控件封装成控件类，如TreeSeMet（树）、MultiSelectSeMet(左右选择框)和TableSeMet（表格）等，然后在各个页面的PO类中使用上述控件类的实例进行组合，就完成了该页面类的基础封装工作。各种页面上完成的业务操作，也可以转化为对一个个通用控件的操作组合而成。在Webdriver中，通过WebElement这个接口定义对于HTML元素和组件的一些典型操作和获取一些属性的方法。这些功能，对于一些单元素组成的较为简单的组件，如：输入框、按钮等等，基本可以满足使用。但是对于一些多元素组成的组件，或者由第三方UI框架所提供的组件，则有些心有余而力不足，因此需要额外的一些扩展。在Webdriver中，目前只针对标准的HTML下拉菜单提供了ui.suppport.Select这个类来实现一些功能。



**2.	导航类案例** 

　　在web应用中，经常需要在不同的系统或者模块间进行频繁的切换。为了提高软件的易用性，导航栏是经常提供的一种产品功能。在图5所示的页面中，可以发现测试项目管理以及测试规约的页面虽然有很大不同，但是在这两个页面顶部的导航栏是完全相同的。因此，按照Composite Page Object的模式，完全可以抽取出来作为一个独立的导航类，供外部调用者使用，从而避免了在不同页面中反复实现相同功能的问题。

 

![img](https://mmbiz.qpic.cn/mmbiz_png/CMOUUEcN8Fer3BicrXSYcXAAlMHibqCcLWuViaCHrklwFy4blvOZ6H9wAMjR7Me9gUJjqplIHhRDicl4D6BjRXqotw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

 图5 导航栏-测试项目管理页面、规约页面



　　这样做的另外一个好处是，如图6所示，在TestLink1.9.14中，导航栏的设计就发生了重大变化，从文字链接形式变成了图片链接的形式。

 ![img](https://mmbiz.qpic.cn/mmbiz_png/CMOUUEcN8Fer3BicrXSYcXAAlMHibqCcLWu9p43csb016Awib4AVVKdbbPrNbyz4Vf8OCibVial0XbDibricWf3d6Nv0g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

图6  TestLink1.9.14中的导航栏



　　在这种情况下，由于已经将导航栏单独抽取出来作为一个类，只要对导航类的实现进行修改，不改变对外服务的公共方法，那么所有外部调用该类的代码均无需修改，很好地隔离了页面变化对于测试框架和测试用例的带来的影响。



**三、	Extended Page Object**（扩展PO）

当然，在面向对象的系统设计中，通过类的继承来实现新类对于既有类的属性和行为的吸收，并能扩展新的功能，是一种普遍采用的方式。在POM中，也可以使用继承的方式，形成PO类之间的层次关系，更合理地实现对于页面封装代码的复用和可维护性。

以TestLink用户管理页面为案例，如图7所示，在比较了TestLink的创建用户(New User)和编辑用户（Edit User）的页面之后，可以发现两者在页面结构上完全一致。因此在进行页面封装的时候，自然而然就可以想到使用Extended Page Object模式，通过一个共同的父类UserCRUSeMet来实现对于创建用户和编辑用户的共同操作，也就是按照“三段式页面封装法”在UserCRUSeMet实现对于各元素定位信息、元素获取方法以及页面操作方法的封装，然后在各自的子类中通过继承该父类来拥有这些操作的功能。

public class CreateUserSeMet extends UserCRUSeMet {

}

public class EditUserSeMet extends UserCRUSeMet {

}

![img](https://mmbiz.qpic.cn/mmbiz_png/CMOUUEcN8Fer3BicrXSYcXAAlMHibqCcLWIvKcrx6dAUiaHcSYvGXxXAYv6xGibMibnTvibbXLic29atdDWIwYDfdeFZw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  

​					图7  创建用户、编辑用户



可能有读者要问，为什么不直接用UserCRUSeMet而是要定义一个空白的EditUserSeMet呢？针对目前的这个页面，其实这种处理也是可行的。不过如果后期编辑用户这个页面如果新增了元素，并且与创建用户不同，那么这时候就必须采用EditUserSeMet这个类了。考虑后续的这种可能性，建议读者使用现在的这种方式先打好预防针，从而规避后续变化所带来的风险。



**总结**

　　在本文中，笔者介绍了POM及其两种延伸模式。一个设计良好的WEB自动化测试框架，其应用POM模式应具有如下特征：

1. 可组合(composeable) 一个PO类易于和其它PO类一起使用，或者嵌套在另一个PO类内部，实现复杂页面和业务操作的合理拆分与组合。
2. 可重用(Reusable) 每个PO类应有独立的功能，它可以被使用在多个场景。
3. 可维护(Maintainable) 每个PO类仅仅包含自身的逻辑，更容易被理解和维护。
4. 分层的(Layered)  实现上述目标的框架必然是分层的。

最后，笔者需要强调的是，没有一种模式是可以包打天下，需要针对各单位自身系统和业务的特性，有针对性地进行测试框架的开发，从而更有效地支撑WEB自动化测试项目的实施。



**参考文献**

1 Junit实战: 第2版/(美) 塔凯文(Tahehiev,P.)等著;王奎译。-- 北京: 人民邮电出版社，2012.4 ISBN 978-115-27475-5  p73