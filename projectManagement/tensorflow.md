# Tensorflow

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