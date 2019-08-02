**本文介绍**



如果您曾经用Python编写过代码，那么等待某些代码块执行的时间可能比您希望的要长。虽然有一些方法可以提高代码的效率，但它很可能仍然比C代码慢。这主要归结为一个事实：Python是一种动态编程语言，它将许多东西移动到C在编译期间负责的运行时。

不过，如果您像我一样喜欢用Python编写代码，并且仍然希望加快代码的速度，那么您可以考虑使用Cython。虽然Cython本身是一种独立的编程语言，但是很容易将其融入到您的工作流程中，例如Jupyter Notebook。在执行时，Cython将您的Python代码转换为C，通常会显著地加快速度。

##  

## **安装Cython**



为了能够使用Cython，您需要一个C编译器。因此，安装过程会根据您当前的操作系统而有所不同。对于Linux，通常存在GNUC编译器(gncc)。对于Mac OS，您可以下载Xcode来获得gncc。如果您应该使用Windows，安装过程会稍微复杂一些。更多信息请访问Cython’s GitHub。

一旦你有了C编译器，你需要在你的终端运行的是:

```shell
pip install Cython
```



## **如何使用Cython**



演示Cython功能的最简单方法是通过Jupyter Notebooks。要在我们的笔记本中使用Cython，我们将使用IPython magic命令。Magic命令以百分号开始，并提供一些额外的功能，这些功能可以增强工作流。通常，有两种类型的Magic命令:

1. 行magic由单个“%”表示，并且只在一行输入进行操作
2. 单元格magic由两个“%”表示，并在多行输入上操作。

让我们开始:

首先，为了能够使用Cython，我们必须运行:

```shell
%load_ext Cython
```

现在，每当我们想在代码单元中运行Cython时，我们必须首先将以下magic命令放入单元格：

```shell
%%cython
```

完成这些之后，就可以开始编写Cython代码了。



## **Cython跑得有多快?**



与普通Python代码相比，Cython的速度快多少实际上取决于代码本身。例如，如果您正在运行具有许多变量的计算开销较大的循环，Cython将大大优于常规Python代码。递归函数也会使Cython比Python快很多。

让我们用斐波那契数列来证明这一点。简单地说，这个算法通过把前两个数相加找到下一个数。下面是Python中可能出现的情况:

![img](https://mmbiz.qpic.cn/mmbiz_png/NFR0Xqia16fbxy51TjFib1FWOMUAJxUhtAPfYiaYpibt8xibaAAwOHKc2hwbkSr5UsXH5tJRZkOUiaumf5iaPdZmxxCpg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我们让Python工作:

![img](https://mmbiz.qpic.cn/mmbiz_png/NFR0Xqia16fbxy51TjFib1FWOMUAJxUhtAdxWlB4IAqdIexHsVEXcHvjATydkvTSOadSziaIXRibmfXbgUvE0B89pA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

如您所见，找到序列中的第39个数字花费了13.3秒。这里的wall time是指从函数调用开始到结束所花费的总时间。

让我们在Cython中定义相同的函数。

![img](https://mmbiz.qpic.cn/mmbiz_png/NFR0Xqia16fbxy51TjFib1FWOMUAJxUhtAwnPTeDia4sRzG6RsZcvVfticN57hCYcgHWrypIYCTYBoAwY1lCavF04A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这是怎么回事？正如你所看到的，我们正在使用一些单元魔法，使我们可以在这个单元中使用Cython。我将很快解释“-a”选项的作用。然后，我们基本上使用与上面相同的代码，只是现在我们能够使用静态类型声明并将n定义为integer类型。



正如您所看到的，通过在magic命令后面添加’ -a ‘，我们收到了一些注释，这些注释向我们展示了代码中有多少Python交互。这里的目标是去掉所有的黄线，让它们有一个白色的背景。在这种情况下，将不存在Python交互，所有代码都将在C中运行。您还可以单击每行旁边的“+”符号，查看Python代码的C转换。

这个代码快了多少？让我们看看：

![img](https://mmbiz.qpic.cn/mmbiz_png/NFR0Xqia16fbxy51TjFib1FWOMUAJxUhtAWAukclrNKwZKOhJdphMvibUvJs2Xiaz7pmtr7XMqvRic3eaV5hjwapGAg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

本例中，Cython的速度大约是Python的6.75倍。这清楚地展示了利用Cython节省时间的能力，与常规Python代码相比，Cython提供了最大的改进。



## **附加选项**



如果您已经了解C语言，Cython还允许访问C代码，而Cython的创建者还没有为这些代码添加现成的声明。例如，使用以下代码，可以为C函数生成Python包装器并将其添加到模块dict中。

![img](https://mmbiz.qpic.cn/mmbiz_png/NFR0Xqia16fbxy51TjFib1FWOMUAJxUhtATQtvqmiaAEQDqEAxnibyyVAWFPYH4frdklgYhUtxOMl5wAGic7FvybKmQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

Cython证明了许多额外的功能，比如并行性，这些功能在文档中都得到了很好的描述，您可以在这里找到这些功能。



## **结论**



如果您有时遇到不得不等待太长时间才能执行python代码的问题，那么cython提供了一种非常灵活的集成和高效的方法来加速代码的执行。最重要的是，如果您对C稍微熟悉一点，它提供了许多进一步优化代码的功能。如果您有任何建议或评论，请随时与我联系。