# Tensorflow

![Tensorflow.jpg](https://upload-images.jianshu.io/upload_images/6278050-cf5275698e854a57.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

机器学习的程序可以宏观上分为数据和模型两部分，在Tensorflow当中，这种思想尤为明确：尽管模型和数据联系密切，但是基于Tensorflow架构的机器学习程序，模型是由Tensor组成的图，犹如一个大楼的水电气管道；而数据会以Session为接口从模型输入输出，即犹如水、电、气进入管道，进行处理和运算。

# 1. Tensorflow基本模型
习惯上，先搭建Tensorflow的模型，以RNN为例，模型结构如下：
![Tensorflow basic model](https://upload-images.jianshu.io/upload_images/6278050-49fecb0492cd3d30.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/480)

在\_\_init\_\_()部分，通过参数config将相关模型参数导入，程序如下：
```python
import tensorflow as tf
class RNNmodel(object):
  def __init__(self, config):
    '''
    config: basic configaration parameters
    return: none
    '''
    self.Config = config
    with tf.name_scope('Input'):
      self.batchX_placeholder = tf.placeholder(tf.float32, [self.Config.input_size, self.Config.look_back], name='Input_Placeholder')
      self.batchY_placeholder = tf.placeholder(tf.float32, [self.Config.output_size, self.Config.look_back], name='Output_Placeholder')
    with tf.name_scope('RNNVariable'):
    '  ''Related RNN variables declaration'''
    with tf.name_scope('DenseVariable'):
      self.W2 = tf.Variable(np.random.rand(self.Config.output_size,self.Config.state_size), dtype=tf.float32, name='Wd')
      self.b2 = tf.Variable(np.zeros((self.Config.output_size, 1)), dtype=tf.float32, name='bd')
    self.states_series = []
    # prediction series placeholder
    self._predictions_series = None
    # total_loss placeholder
    self._total_loss = None
    # train step placeholder
    self._train_step = None
    # saver for the model
    self.saver = tf.train.Saver()

  def forwardProp(self):
    '''Forward propagation'''

  @property
  def predictions_series(self):
    if self._predictions_series is None:

    return self._predictions_series

  @property
  def total_loss(self):
    if self._total_loss is None:
    
    return self._total_loss

  @property
  def train_step(self):
    if self._train_step is None:
   
    return self._train_step

```
# 2. 训练及预测模型
对Tensorflow模型的训练和预测，需要结合数据展开，该部分包含三个步骤：

* Tensorflow基本Tensor模型的实例化
* 数据预处理（数据划分为训练、交叉检验、以及预测部分）
* 通过Session将数据输入到模型进行训练、交叉检验以及预测
在时间序列模型当中，我习惯将该部分写成一个class，\_\_init\_\_部分实现对基本模型的实例化、数据预处理，另外包含train_validate()以及test()，两个成员函数分别实现__单次__数据训练、交叉检验以及测试（预测）。

# 3. 其他
这里记录一些在书写代码时候的习惯，

+ Tensorflow的结构注明节点和节点域名称，便于理解和可视化结构
+ 对于程序可能出现的bug，给定对应的try: except
+ 不同的参数和函数声明时进行相关注释
+ 避免内存溢出，可以考虑手动释放垃圾内存
+ class内部成员函数中的局部变量和self成员变量：减少不必要的self成员变量，节约内存消耗

# 4. Tensorflow 内存溢出问题相关解决方案


本文总结了Tensorflow内存溢出的各种原因，以及在此排查过程中使用的方法。
本文在实现Tensorflow模型的保存(save)和重新调用(restore)过程中，程序总是发生内存溢出而中止的问题，所以对其进行故障排查。
## 4.1 排查工具及步骤
### 4.1.1 Tensorboard
Tensorboard是Tensorflow提供的向用户展示模型结构以及运行结果等的可视化工具。当Tensorflow相关程序发生内存溢出，并且确认程序所使用的数据量并不大时，首先想到使用Tensorboard对模型结构进行可视化，在此之前，需要对模型中的各个变量(variable, placeholder)、节点(nodes)、以及节点域进行命名，从而方便可视化后对模型各个部分的辨别和故障排查。例如：

```python
import tensorflow as tf
with tf.name_scope('Input'):
    batchX_placeholder = tf.placeholder(tf.float32, [input_size, look_back], name='Input_Placeholder')
    batchY_placeholder = tf.placeholder(tf.float32, [output_size, look_back], name='Output_Placeholder')
```
这段代码对应在Tensorboard中如下图所示：

![input nodes.png](https://upload-images.jianshu.io/upload_images/6278050-043fc1da3992ec2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/480)

类似地，搭建出整个模型的结构图之后，通过如下代码将图保存在文件当中，之后就可以通过tensorboard --logdir=filedir --host=127.0.0.1运行Tensorboard，并使用浏览器打开。
```
train_writer = tf.summary.FileWriter('graph', sess.graph)  # save the graph
```
完成以上步骤后，进行核对，发现graph的结构会随着程序运行逐渐变大，原因在于在生成新的结构时，没有将之前的结构删去，于是，通过如下代码，每次生成新的图时，将之前的结构重置（删去）：
```
tf.reset_default_graph()  # reset the previous graph and nodes
```
结果：Graph不再随着新的模型声明而积累旧的结构，内存溢出问题得到缓解，但并没有完全解决。
### 4.1.2 内存监视

在查找了程序书写结构上的问题之后，没有什么头绪，于是尝试监视内存查看程序运行问题。
```python
import psutil
def memory_monitor():
# https://github.com/giampaolo/psutil
# 1. virtual_memory():
#   total: total physical memory.
#   available: the memory that can be given instantly to processes without the system going into swap.
#              This is calculated by summing different memory values depending on the platform and it
#               is supposed to be used to monitor actual memory usage in a cross platform fashion.
# 2. swap_memory():
#   total: total swap memory in bytes
#   used: used swap memory in bytes
    print psutil.virtual_memory()
    print psutil.swap_memory()
```

将该函数插入到想要查看当前内存使用情况的位置，但是没有任何发现。

### 4.1.3 给Tensorflow结构分配给不同的GPU
因为之前Tensorflow的结构是使用CPU进行运行，猜想如果将Tensorflow运算分配给不同的GPU，是否可以解决内存溢出的问题。

分配GPU和CPU的原则是，并行结构和运算分配给不同的GPU，主线运算分配给CPU。
![tensorboard-gpu.png](https://upload-images.jianshu.io/upload_images/6278050-49453be336987a09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/340)

如图所示，可以通过打开Tensorboard中的Device开关，可以在Graph中不同结构标识不同的device。

同样的，内存溢出的问题并没有得到解决。

### 4.1.4 查看不同变量类型的增长情况以及调用结构

为了进一步查找导致内存溢出的增长源，采用python的gc模块对程序运行过程中的不同变量类型的增长情况以及调用结构进行分析。代码如下：
```
import objgraph
objgraph.show_growth()  # show the growth of the objects
objgraph.show_refs(variableName, filename='graph.png')  # show the reference structure of the variable
```
通过将show_growth()放置在程序单次循环的末尾，以及查看每次循环所带来的变量类型的增长情况进行分析，发现在Tensorflow模型的训练以及预测过程中，Tensor类型的变量数在不断增加，这就是导致内存溢出的关键。

其间，使用了gc.collect()对垃圾进行手动回收，但是没有解决问题。

虽然程序已经包含了之前的tf.reset_default_graph，但是变量数却在增加，这点非常奇怪。为了进一步排除问题的可能性，重新声明一个新的测试类，只包含了模型的声明，测试类的结构和调用方式仿照存在内存溢出问题的类，如下所示：
```python
class testClass(object):
    def __init__(self):
        pass
    def test1(self, CF):
        print '------------------------'
        objgraph.show_growth()
        tf.reset_default_graph()
        '''Establish the model'''
        sess = tf.InteractiveSession()
        sess.run(tf.global_variables_initializer())
        sess.close()
        objgraph.show_growth()

    def test2(self, CF):
        print '------------------------'
        objgraph.show_growth()
        tf.reset_default_graph()
        '''Establish the model'''
        sess = tf.InteractiveSession()
        sess.run(tf.global_variables_initializer())
        sess.close()
        objgraph.show_growth()
```
经过测试发现，sess虽然是局部变量，但是如果不对sess进行close操作，那么之前所有的graph, nodes, variables等等Tensor将会全部被保留下来，（尽管已经完成了reset_default_graph），找到了根源之后，添加close操作，再通过show_growth可以观察到，在重复调用test1和test2时，内存不会产生之前无用的消耗和溢出。

至此，完成了对tensorflow内存溢出问题的排查和调试。

### 4.2 总结：

出现内存溢出问题，可能的原因有很多种，对于机器学习程序，从数据和模型两方面进行入手，首先确认数据的处理、调用等是否存在内存溢出的问题，这部分可以通过objgraph模块内的相关函数对其进行调试和排查；其次需要对Tensorflow的模型进行核查，是否存在旧的节点和图未删去、Session使用后未关闭等问题。
