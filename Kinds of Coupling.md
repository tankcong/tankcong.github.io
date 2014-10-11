看到Code Complete里对常见Coupling种类有很好的划分：

1. 简单数据参数耦合
    两个模块之间通过参数来传递数据，并且所有的数据都是primitive data type
    ACCEPTABLE

2. 简单对象耦合
    一个模块实例化另一个对象
    ACCEPTABLE

3. 对象参数耦合
    Object1要求Object2传给它一个Object3。
    与Object1仅要求Object2传递给primitive type比，耦合关系更紧密，因为Object2需了解
    Object3

    项目经历：
    OA模块与IM模块之间的耦合，IM只提供了UserUtils.getEmployeeInfo(Contact)，未提供类似
    UserUtils.getEmplyeeInfo(int userID)这样的方法。实际上OA作为单独的子模块完全不需要
    了解IM中的Contact对象。

4. 语义上的耦合（尽量避免）
    一个模块不仅使用了另一个模块的语法元素(syntactic element)，还使用了其他模块内部工作细
    节的语义知识(semantic knoledge)。例子：
  a. Module1向Module2传递控制标志，用来告诉Module2该做什么。这要求Module1对Module2的工
  作细节有所了解（需了解Module2对控制标志的使用）。
    ACCEPTABLE 如果控制标志定义为特定的数据类型(Enum或者对象)。

  b. Module2在Module1修改了某个全局数据之后使用该全局数据。这要求Module2假设Module1对该
  数据所做的修改符合Module2的需要，并且Module1已经在恰当的时间被调用过。

  c. Module1的接口要求Module1.Intialize()子程序必须再它的Module1.Routine()之前得到调
  用。Module2知道Module1.Routine()无论如何都会调用Module1.Intialize()，所以Module2在
  实例化Module1之后只是调用了Module1.Routine()，而没有先调用Module1.Intialize()。
    
    这种情况其实Module1就不应该对外暴露Initialize()子程序

  d. Module1把Object传给Module2。由于Module1知道Module2只用了Object的7个方法中的3个，
  因此它只部分地初始化Object（只包含那3个方法所需的数据）

    项目经历：
    OA模块与IM模块耦合，由于IM只提供UserUtils.getEmployeeInfo(Contact)接口，查看源码后
    发现getEmployeeInfo只是用了Contact的ID字段，所以OA模块部分初始化Contact对象。当时可
    能不会有问题，但会对后期维护的工作带来隐患，维护人员并不清楚该Contact只有部分初始化。

  e. Module1把BaseObject传给Module2.由于Module2知道Module1实际上传给它的是
    DerivedObject，所以它把BaseObject转换成DerivedObject，并且调用了DerivedObject特
    有的Method。

    项目经历：
    在Fragment可能被多Activity复用的情况下，通过 instanceof getActivity() --> 具体子
    类型的方式处理不同逻辑。造成此现象的主要原因是使用该Fragment的Activity并未进行良好的抽
    象(BaseActivity)，或者有些场景下Fragment根本不该被复用。
