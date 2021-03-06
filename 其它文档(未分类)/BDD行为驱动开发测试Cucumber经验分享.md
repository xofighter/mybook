> 作者：robinlovesnow 
> 来源：CSDN 
> 原文：https://blog.csdn.net/robinlovesnow/article/details/50568417 
> 版权声明：本文为博主原创文章，转载请附上博文链接！
> 请勿在未经本人同意下，以任何形式转载本文

从事多年自动化测试框架的开发，并协助大中型企业建立自动化测试开发部门，提供可行的整体测试框架和测试方案，根据这些年在国外的工作经验，大体上我们通常使用TDD或者BDD两种形式，对一些大型软件，并不局限于这两种方案的理论，不论采用什么样的测试理论，目的只有一个，也就是保证发布的产品的质量，降低软件在发布后进行的维护或修复的成本，这也是作为QA这部门的职能所在。



这篇文章和大家分享的主题是: 行为驱动开发测试中何时使用Cucumber，以及使用Cucumber的先决条件和优点缺点



# 1. 为什么使用Cucumber

     a.  抛弃传统的类似QC的测试用例工具，将测试用例描述和测试用例执行整合在一起，即自然语言描述出来的测试用例可以直接被执行，而不需要人工的将自然语言转化为可运行的测试用例
    
     b.  Gherkin语言规则编写的测试用例，对于编写测试用例的人员没有技术要求，即测试人员可能精通业务逻辑，但不精通程序开发，或产品经理可以直接编写和管理测试用例，通常产品经理并不懂得开发技术
    
     c.  测试用例 + 测试执行 + 测试报告将高度整合，并且通过可持续集成的方法，易于开发人员了解测试人员测试点，根据测试用例，开发人员可以直接回测，并不需要了解测试框架的设计



# 2. 什么情况可以使用Cucumber

   a.  在实际操作中，Cucumber只可以使用在单纯的功能测试中(非UI)，在通过UI进行的测试中，需要根据软件的复杂度来判断是否Cucumber,  当然Cucumber绝对不能使用在单元测试。当UI中有过多复杂的功能时，例如为了完成一项功能，通过UI可以有不同的实现方法的时候， Cucumber最大的缺陷就是很难管理你的step definition ， 当你的测试项目中，有100条以上的step definitions的时候，你的测试框架将很难被别人接受，因为学习这套step definitions是一个非常费时的工作，即使你提供了auto completion你的step。

   b. step definition应该越少越好，通过regex丰富你的step definitions

   c. 产品已经有丰富的单元测试



# 3. Cucumber应该用什么版本

    Cucumber支持很多种语言，推荐给大家使用Ruby 版本，首先他在github是非常活跃的，其次在最新的cucumber中，已经支持了cucumber-wire 协议，也就是说您的step definitions不仅可以通过ruby编写，您还可以使用.net 等语言编写(例如嫁接white框架) 



# 4. Cucumber扩展

 这里不是指扩展Cucumber的库，而是如何将cucumber打包成一个测试IDE，您可以使用eclipse，进行RCP开发一个专门适合您公司的测试IDE，当然您也可以使用intellij扩展。 

这里不再进行过多的介绍，当然打个小广告。。。 如果您的公司真的需要一套适合自己的自动化测试框架或者CI，可以邮件联系我 xuanzhaopeng@gmail.com 