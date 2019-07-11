# [以太坊RPC机制与API实例](https://www.cnblogs.com/Evsward/p/eth-rpc.html)



> [上一篇文章](http://www.cnblogs.com/Evsward/p/ethereum.html)介绍了以太坊的基础知识，我们了解了web3.js的调用方式是通过以太坊RPC技术，本篇文章旨在研究如何开发、编译、运行与使用以太坊RPC接口。

> 关键字：以太坊，RPC，JSON-RPC，client，server，api，web3.js，api实例，Postman

## rpc简介

RPC(remote process call)，名曰远程过程调用。意思就是两台物理位置不同的服务器，其中一台服务器的应用想调用另一台服务器上某个应用的函数或者方法，由于不在同一个内存空间不能直接调用，因此需要通过网络来表达语义以及传入的参数。RPC是跨操作系统，跨编程语言的网络通信方式。

> RMI(remote method invocation)，远程方法调用，只能被Java调用，可返回Java对象和基本类型，可以说是Java版的RPC。它需要一个RMI Registry来注册服务地址。

> JMS(java message service)，java消息服务，网络传输的是Java对象，而RMI还是远程调用方法，网络传输的是请求参数和返回值。

> SOAP(simple object access protocol)，简单对象访问协议，是一种交换信息的服务，通过WSDL(Web Services Description Language)方法描述文件来指导我们去调用远程服务，获得自己想要的对象，传输格式为xml。

RPC规定在网络传输中参数和返回值均被[序列化为二进制数据](http://www.cnblogs.com/Evsward/p/io.html#%E4%B8%89%E5%AF%B9%E8%B1%A1%E6%B5%81-%E5%BA%8F%E5%88%97%E5%8C%96)，这个过程被称为序列化（Serialize）或编组（marshal）。通过寻址和传输将序列化的二进制发送给另一台服务器。另一台服务器收到二进制数据以后会反序列化，恢复为内存中的表达方式，然后找到对应方法调用将返回值仍旧以二进制形式返回给第一台服务器，然后再反序列化读取返回值。

### netty

Netty框架不局限于RPC，更多的是作为一种网络协议的实现框架。通过上面的rpc介绍，我们指定rpc需要：

- 通讯方式，TCP/IP socket, HTTP
- 高效的序列化框架,protobuf, thrift
- 寻址方式，registry，endpoint URI, UDDI，另外服务注册可以选择redis，zookeeper等技术。
- 会话和状态保持 session

netty提供了一种事件驱动的，责任链式的，流水线式的网络协议实现方式。Netty对NIO有很好的实现，解决了众多的RPC的后顾之忧并且开发效率也有了很大程度的提高，很多RPC框架是基于Netty来构建的。

> 希望未来可以有更多的使用到netty的具体工作场景。

### rpc框架

rpc框架简直不要太多。

- dubbo 阿里出品，基于Netty，java实现的分布式开源RPC框架，但于12年年底已停止维护升级
- motan 新浪出品，微博使用的rpc框架。
- rpcx
- gRPC Google出品，强大新潮，基于HTTP2，以及protobuf构建并且支持多语言。
- thrift
- protobuf 序列化框架

## 以太坊rpc客户端机制研究

### geth命令中rpc相关api

之前介绍过这些API都可以在geth console中调用，而在实际应用中，纯正完整的RPC的调用方式，

```
geth --rpc --rpcapi "db,eth,net,web3,personal"
```

这个命令可以启动http的rpc服务，当然他们都是geth命令下的，仍旧可以拼接成一个多功能的命令串，可以了解一下上一篇介绍的geth的使用情况。下面介绍一下api相关的选项参数：

```
API AND CONSOLE OPTIONS:
  --rpc                  启动HTTP-RPC服务（基于HTTP的）
  --rpcaddr value        HTTP-RPC服务器监听地址(default: "localhost")
  --rpcport value        HTTP-RPC服务器监听端口(default: 8545)
  --rpcapi value         指定需要调用的HTTP-RPC API接口，默认只有eth,net,web3
  --ws                   启动WS-RPC服务（基于WebService的）
  --wsaddr value         WS-RPC服务器监听地址(default: "localhost")
  --wsport value         WS-RPC服务器监听端口(default: 8546)
  --wsapi value          指定需要调用的WS-RPC API接口，默认只有eth,net,web3
  --wsorigins value      指定接收websocket请求的来源
  --ipcdisable           禁掉IPC-RPC服务
  --ipcpath              指定IPC socket/pipe文件目录（明确指定路径）
  --rpccorsdomain value  指定一个可以接收请求来源的以逗号间隔的域名列表（浏览器访问的话，要强制指定该选项）
  --jspath loadScript    JavaScript根目录用来加载脚本 (default: ".")
  --exec value           执行JavaScript声明
  --preload value        指定一个可以预加载到控制台的JavaScript文件，其中包含一个以逗号分隔的列表
```

我们在执行以上启动rpc命令时可以同时指定网络，指定节点，指定端口，指定可接收域名，甚至可以同时打开一个console，这也并不产生冲突。

```
geth --rpc --rpcaddr <ip> --rpcport <portnumber>
```

我们可以指定监听地址以及端口，如果不谢rpcaddr和rpcport的话，就是默认的http://localhost:8545。

```
geth --rpc --rpccorsdomain "http://localhost:3000"
```

如果你要使用浏览器来访问的话，就要强制指定rpccorsdomain选项，否则的话由于JavaScript调用的同源限制，请求会失败。

```
admin.startRPC(addr, port)
```

如果已进入geth console，也可以通过这条命令添加地址和端口。

### rpc客户端：Postman模拟HTTP请求api

Postman是一个可以用来测试各种http请求的客户端工具，它还有其他很多用途，但这里只用它来测试上面的HTTP-RPC服务。
![image](https://github.com/evsward/mainbase/blob/master/resource/image/restful/Postman.png?raw=true)

看图说话，我们指定了请求地址端口，指定了HTTP POST请求方式，设置好请求为原始Json文本，请求内容为：

```
{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":67}
```

是用来请求服务器当前web3客户端版本的，然后点击"Send"，得到请求结果为：

```
{
    "jsonrpc": "2.0",
    "id": 67,
    "result": "Geth/v0.0.1-stable-930fa051/linux-amd64/go1.9.2"
}
```

### rpc客户端的研究：Go源码调用rpc

这种rpc客户端可以有两种，一种是通过j调用web3.js来实现，另一种是在geth consol中通过manageAPI来实现，但是它们的内部运行机制是一样的，包括上面的Postman模拟浏览器发起HTTP请求也是一样，下面我们通过一个完整的客户端调用例子来研究整个以太坊源码中对于客户端这块是如何处理的。这里我们就以最常用的[api：eth_getBalance](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_getbalance)为例，它的参数要求为：

```
Parameters
- DATA, 20 Bytes - address to check for balance.
- QUANTITY|TAG - integer block number, or the string "latest", "earliest" or "pending", see the default block parameter
```

该api要求的参数：

- 第一个参数为需检查余额的地址
- 第二个参数为整数区块号，或者是字符串“latest","earliest"以及"pending"指代某个特殊的区块。

在go-ethereum项目中查找到使用位置ethclient/ethclient.go：

```
func (ec *Client) BalanceAt(ctx context.Context, account common.Address, blockNumber *big.Int) (*big.Int, error) {
    var result hexutil.Big
    err := ec.c.CallContext(ctx, &result, "eth_getBalance", account, toBlockNumArg(blockNumber))
    return (*big.Int)(&result), err
}
func (ec *Client) PendingBalanceAt(ctx context.Context, account common.Address) (*big.Int, error) {
    var result hexutil.Big
    err := ec.c.CallContext(ctx, &result, "eth_getBalance", account, "pending")
    return (*big.Int)(&result), err
}
```

结合上面的RPC API和下面的go源码的调用，可以看到在go语言中的调用方式：要使用客户端指针类型变量调用到上下文Call的方法，传入第一个参数为上下文实例，第二个参数为一个hexutil.Big类型的结果接收变量的指针，第三个参数为调用的rpc的api接口名称，第四个和第五个为该api的参数，如上所述。

- 跟踪到ec.c.CallContext，CallContext方法是ec.c对象的。

```
// Client defines typed wrappers for the Ethereum RPC API.
type Client struct {
    c *rpc.Client
}
```

可以看到ethclient/ethclient.go文件中将原rpc/client.go的Client结构体进行了一层包裹，这样就可以区分出来属于ethclient的方法和底层rpc/client的方法。下面贴出原始的rpc.client的结构体定义：

```
// Client represents a connection to an RPC server.
type Client struct {
    idCounter   uint32
    connectFunc func(ctx context.Context) (net.Conn, error)
    isHTTP      bool

    // writeConn is only safe to access outside dispatch, with the
    // write lock held. The write lock is taken by sending on
    // requestOp and released by sending on sendDone.
    writeConn net.Conn

    // for dispatch
    close       chan struct{}
    didQuit     chan struct{}                  // closed when client quits
    reconnected chan net.Conn                  // where write/reconnect sends the new connection
    readErr     chan error                     // errors from read
    readResp    chan []*jsonrpcMessage         // valid messages from read
    requestOp   chan *requestOp                // for registering response IDs
    sendDone    chan error                     // signals write completion, releases write lock
    respWait    map[string]*requestOp          // active requests
    subs        map[string]*ClientSubscription // active subscriptions
}
```

ethclient经过包裹以后，可以使用本地Client变量调用rpc.client的指针变量c，从而调用其CallContext方法：

```
func (c *Client) CallContext(ctx context.Context, result interface{}, method string, args ...interface{}) error {
    msg, err := c.newMessage(method, args...) // 看来CallContext还不是终点，TODO:进到newMessage方法内再看看。
    // 结果处理
    if err != nil {
        return err
    }
    // requestOp又一个结构体，封装响应参数的，包括原始请求消息，响应信息jsonrpcMessage，jsonrpcMessage也是一个结构体，封装了响应消息标准内容结构，包括版本，ID，方法，参数，错误，返回值，其中RawMessage在go源码位置json/stream.go又是一个自定义类型，属于go本身封装好的，类型是字节数组[]byte，也有自己的各种功能的方法。
    op := &requestOp{ids: []json.RawMessage{msg.ID}, resp: make(chan *jsonrpcMessage, 1)}

    // 通过rpc不同的渠道发送响应消息：这些渠道在上面命令部分已经介绍过，有HTTP，WebService等。
    if c.isHTTP {
        err = c.sendHTTP(ctx, op, msg)
    } else {
        err = c.send(ctx, op, msg)
    }
    if err != nil {
        return err
    }

    // TODO:对wait方法的研究
    // 对wait方法返回结果的处理
    switch resp, err := op.wait(ctx); {
    case err != nil:
        return err
    case resp.Error != nil:
        return resp.Error
    case len(resp.Result) == 0:
        return ErrNoResult
    default:
        return json.Unmarshal(resp.Result, &result)// 顺利将结果数据编出
    }
}
```

先看wait方法，它仍旧在rpc/client.go中：

```
func (op *requestOp) wait(ctx context.Context) (*jsonrpcMessage, error) {
    select {
    case <-ctx.Done():
        return nil, ctx.Err()
    case resp := <-op.resp:
        return resp, op.err
    }
}
```

select的使用请参考[这里](http://www.cnblogs.com/Evsward/p/go.html#select)。继续正题，进入ctx.Done()，Done属于Go源码context/context.go:

```
// See https://blog.golang.org/pipelines for more examples of how to use
// a Done channel for cancelation.
Done() <-chan struct{}
```

> 想知道Done()咋回事，请转到我写的另一篇博文[Go并发模式：管道与取消](http://www.cnblogs.com/Evsward/p/goPipeline.html)，那里仔细分析了这一部分内容。

> 从上面的源码分析我感觉go语言就是一个网状结构，从一个结构体跳进另一个结构体，它们之间谁也不属于谁，谁调用了谁就可以使用，没有显式继承extends和显式实现implements，go就是不断的封装结构体，然后增加该结构体的方法，有时候你甚至都忘记了自己程序的结构体和Go源码封装的结构体之间的界限。这就类似于面向对象分析的类，定义一个类，定义它的成员属性，写它的成员方法。

### web3与rpc的关系

这里再多啰嗦一句，重申一下web3和rpc的关系：

> To make your app work on Ethereum, you can use the web3 object provided by the web3.js library. Under the hood it communicates to a local node through RPC calls. web3.js works with any Ethereum node, which exposes an RPC layer.

翻译过来就是为了让你的api工作在以太坊，你可以使用由web3.js库提供的web3对象。底层通过RPC调用本地节点进行通信。web3.js可以与以太坊任何一个节点通信，这一层就是暴露出来的RPC层。

## 以太坊rpc服务端机制研究

以上介绍了各种客户端的调用方式，包括通过web3提供的接口，从页面js调用，或者从ethclient调用，或者直接通过页面发起Json请求（Postman），无论什么形式，最终都是通过JSON-RPC框架传输到以太坊服务端。那么下面我们开发一个自己的api，首先从服务端接口开发开始：

服务端源码都在ethapi包下，首先它有一个Backend结构体，这个结构体是一个接口，提供了一套基本API服务的描述。然后就通过函数GetAPIs返回一个数组，其中包括各种不同命名空间，版本，服务地址，以及公开私密权限的API对象。下面进到其中的一个API类PublicBlockChainAPI，它包含一个backend属性，代表了它具备后台所有API的能力。这个类有很多方法，其中就包括上面我们讲到的关于eth_getBalance接口的服务端实现方法。

```
// GetBalance returns the amount of wei for the given address in the state of the
// given block number. The rpc.LatestBlockNumber and rpc.PendingBlockNumber meta
// block numbers are also allowed.
func (s *PublicBlockChainAPI) GetBalance(ctx context.Context, address common.Address, blockNr rpc.BlockNumber) (*big.Int, error) {
    state, _, err := s.b.StateAndHeaderByNumber(ctx, blockNr)
    if state == nil || err != nil {
        return nil, err
    }
    b := state.GetBalance(address)
    return b, state.Error()
}
```

可以看到GetBalance方法是PublicBlockChainAPI的方法，并且方法名首字母大写，说明是外部可访问的。它通过Backend的方法s.b.StateAndHeaderByNumber获取余额，上面提到了Backend只是描述了API的接口，那么具体实现还得继续探索，

```
StateAndHeaderByNumber(ctx context.Context, blockNr rpc.BlockNumber) (*state.StateDB, *types.Header, error)
```

通过查看Backend中对于该方法的定义，得到该方法的返回值为StateDB的指针对象。而这个对象正是下面真正获取余额所需要的对象。

```
b := state.GetBalance(address)
```

然后我们进入到该对象中，找到GetBalance方法。

```
// Retrieve the balance from the given address or 0 if object not found
func (self *StateDB) GetBalance(addr common.Address) *big.Int {
    stateObject := self.getStateObject(addr)
    if stateObject != nil {
        return stateObject.Balance()
    }
    return common.Big0
}
```

首先通过StateDB的getStateObject方法获得一个stateObject结构体的对象，其中该对象的一个属性是Account类型的data成员变量，进到Account中可以发现它的结构为：

```
type Account struct {
    Nonce    uint64
    Balance  *big.Int
    Root     common.Hash // merkle root of the storage trie
    CodeHash []byte
}
```

一切了然，这里存了Balance信息，那么外部stateObject通过一个方法Balance()

```
func (self *stateObject) Balance() *big.Int {
    return self.data.Balance
}
```

这个方法可以取出上面Accout中的Balance信息。stateObject是数据存储的一致性对象。

### leveldb

以太坊geth是采用leveldb作为数据库，它是：

- 过程数据库，没有sql，只支持api调用
- Go自带实现
- 键值对存储方式
- 多层磁盘存储，后台有调理
- 记录可追踪可证明

go-ethereum引入了 github\.com/syndtr/goleveldb/leveldb/db.go ，查看此源文件，可以看到它定义了一个DB结构体，包含很多字段以及方法，此外该文件还包含一些函数。其中就有func openDB(s *session) (*DB, error)函数，该函数可以开启一个数据库会话并返回一个数据库实例。

上面的stateObject结构体的字段，它按照钱包address为key存储了账户对象，通过key可以取出账户对象，进而取出账户余额信息。

### 以太坊的存储结构

以太坊并不存在中心服务器，而是基于p2p协议连接起来的平等节点，在众多节点中存储了全部数据。当用户发起一笔交易，会通过广播通知每一个节点，矿工对此进行验证打包并进一步广播，在区块链确认以后，此操作即认为是不可更改的。

在每个节点上，数据是以区块链来存储的。区块链由一个个块串起来而组成。以太坊被描述为一个交易驱动的状态机。它在某个状态下接收一些输入，会确定的转移到另一个状态。

> 状态机，包含一组状态集（states）、一个起始状态（start state）、一组输入符号集（alphabet）、一个映射输入符号和当前状态到下一状态的转换函数（transition function）的计算模型。

在以太坊的一个状态下，每个账户都有确定的余额和状态信息，当他接收到新的交易以后就会进入一个新的状态，从创世块开始，不断的收到新的交易，由此能进入到一系列新的状态。

以太坊每隔一段时间就会把一批交易信息打包存储到一个块里，这个块中包含的信息有：

1. ParentHash:父块的哈希值
2. Number:块编号
3. Timestamp:块产生的时间戳
4. GasUsed:交易消耗的Gas
5. GasLimit:Gas限制
6. Difficulty:POW的难度值
7. Beneficiary：块打包手续费的受益人，也称矿工
8. Nonce： (可选参数) 整型数字，可以通过使用相同nonce值来复写你的pending状态的交易（注意与ethash挖矿随机数做区分）。

这些字段我们在上面的web3接口中都可以获取得到。

> nonce，整数类型，允许使用相同随机数覆盖自己发送的处于pending状态的交易。

为了防止交易的重播攻击，每笔交易必须有一个nonce随机数，针对每一个账户nonce都是从0开始，当nonce为0的交易处理完之后，才会处理nonce为1的交易，并依次加1的交易才会被处理。以下是nonce使用的几条规则：

- 当nonce太小，交易会被直接拒绝。
- 当nonce太大，交易会一直处于队列之中，这也就是导致我们上面描述的问题的原因；
- 当发送一个比较大的nonce值，然后补齐开始nonce到那个值之间的nonce，那么交易依旧可以被执行。
- 当交易处于queue中时停止geth客户端，那么交易queue中的交易会被清除掉。

## 以太坊RPC实例：开发自己的api

> 设定一个小需求：就是将余额数值乘以指定乘数，这个乘数是由另一个接口的参数来指定的。

在ethapi中加入

```
var rateFlag uint64 = 1
// Start forking command.
// Rate is the fork coin's exchange rate.
func (s *PublicBlockChainAPI) Forking(ctx context.Context, rate uint64) (uint64) {
    // attempt: store the rate info in context.
    // context.WithValue(ctx, "rate", rate)
    rateFlag = rate
    rate = rate + 1
    return rate
}
```

然后在ethclient中加入

```
// Forking tool's client for the Ethereum RPC API
func (ec *Client) ForkingAt(ctx context.Context, account common.Address, rate uint64)(uint64, error){
    var result hexutil.Uint64
    err := ec.c.CallContext(ctx, &result, "eth_forking", account, rate)
    return uint64(result), err
}
```

保存，make geth编译，然后在节点目录下启动

```
geth --testnet --rpc console --datadir node0
```

然后进入到Postman中测试，可以看到
![image](https://github.com/evsward/mainbase/blob/master/resource/image/restful/forking.png?raw=true)
乘数已经改为3（输出4是为了测试，实际上已在局部变量rateFlag保存了乘数3）
然后我们再发送请求余额测试，
![image](https://github.com/evsward/mainbase/blob/master/resource/image/restful/balance.png?raw=true)
可以看到返回值为一串16进制数，通过转换结果为:417093750000000000000，我们原始余额为:139031250000000000000，正好三倍。

### rpc客户端

我们上面已经在rpc服务端对api进行了增加，而客户端调用采用的是Postman发送Post请求。而rpc客户端在以太坊实际上有两种：一个是刚才我们实验的，在网页中调用JSON-RPC；另一种则是geth console的形式，而关于这种形式，我还没真正搞清楚它部署的流程，只是看到了在源代码根目录下build/_workspace会在每一次make geth被copy进去所有的源码作为编译后环境，而我修改了源码文件，_workspace下文件，均未生效，可能还存在一层运行环境，我并没有修改到。但这无所谓了，因为实际应用中，我们很少去该console的内容，直接修改web3.js引入到网页即可。下面介绍一下配合上面自己的api，如何修改web3.js文件：

上面讲过了web3.js的结构，是一个node.js的module结构，因此我们先决定将这个api放到eth对象下，检查eth对应的id为38，找到对象体，在methods中增加对应api调用操作，

```
var forking = new Method({
    name: 'forking',
    call: 'eth_forking',
    params: 1,
    inputFormatter: [null],
    outputFormatter: formatters.outputBigNumberFormatter
});
```

然后在对象体返回值部分将我们新构建的method添加进去，

```
return [
    forking,
    ...
```

改好以后，我们将该文件引用到页面中去，即可通过web3.eth.forking(3)进行调用了。

## 总结

本文介绍了rpc的概念，rpc的流行框架，以太坊使用的rpc框架为JSON-RPC。接着描述了如何启动JSON-RPC服务端，然后使用Postman来请求JSON-RPC服务端api。通过这一流程，我们仔细分析并跟踪了源码中的实现，抽丝剥茧，从最外层的JSON-RPC的调用规范到源码中外层封装的引用，到内部具体实现，期间对各种自定义结构体进行了跟踪研究，直到Go源码库中的结构体，研究了服务端从接收客户端请求到发送响应的过程。最后我们仔细研究了web3.js文件的结构并且做了一个小实验，从服务端到客户端模仿者增加了一个自定义的api。希望本文对您有所帮助。