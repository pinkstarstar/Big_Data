## MapReduce基本原理

将一个复杂的问题（数据集）分成若干个简单的子问题（数据块）进行解决（Map函数）；然后对子问题的结果进行合并（Reduce函数），得到原有问题的解（结果）。

MapReduce模型适合于大文件的处理，对很多小文件的处理效率并不是很高，这一点与HDFS一样。

## MapReduce编程模型

MapReduce是一个分布式计算框架，是Hadoop的一个基础组件。MapReduce编程模型主要由两个抽象类构成，即Mapper类和Reduce类，Mapper用以对切分过的原始数据进行处理，Reduce则对Mapper的结果进行汇总，得到最后的输出结果。

在数据格式上，Mapper接受<key，value>格式的数据流，并产生一系列同样是<key，value>形式的输出，这些输出经过相应处理，形成<key，{value list}>的形式的中间结果；之后，由Mapper产生的中间结果再传给Reduce作为输入，把相同key值的{value list}做相应处理，最终生成<key，value>形式的结果数据，再写入HDFS中。

Mapper和Reduce在实际处理中，会根据输入的具体情况，一般会有多个Mapper实例和Reduce实例，并且运行在不同的结点上。其实能使用MapReduce编程模型处理的问题是有限制的。MapReduce适用于大问题分解而成的小问题疲敝智拣没有依赖关系的情形。

## MapReduce数据流

从MapReduce的编程模型中可以发现，数据以不同的形式在不同的结点之间流动，即经过本结点的分析处理，以另外一种形式进入下一个结点，从而得出最终结果。

Mapper处理的是<key，value>形式的数据，即不能直接处理文件流，它的数据源以及多个Mapper产生的数据如何分配给多个Reduce，这两个操作都是由Hadoop提供的基本API实现的。

分片、格式化数据（InputFormat）。InputFormat主要由两个任务，一个是对源文件进行分片，并确定Mapper的数量；另一个是对各分片进行格式化，处理成<key，value>形式的数据流并传给Mapper。

Map过程。Mapper接受<key，value>形式的数据，并处理成<key，value>形式的数据，具体的处理过程可由用户定义。

Combiner过程。每一个map()都可能会产生大量的本地输出，Combiner()的作用就是对map()端的输出先做一次合并，以减少在Map和Reduce结点之间的数据传输量，提高网络I/O性能，是MapReduce的一种优化手段之一。

Shuffle过程。此过程是指从Mapper产生的直接输出结果，经过一系列的处理，称为最终的Reduce直接输入数据的整个过程，这一过程也是MapReduce的核心过程。

Reduec过程。Reducer接收<key，{value list}>形式的数据流，形成<key，value>形式的数据输出，输出数据直接写入HDFS，具体的处理过程可由用户定义。

## MapReduce任务运行流程

MapReduce的任务流程是从客户端提交任务开始，直到任务运行结束的一系列流程。在Yarn中，资源管理有ResourceManage和NodeManager共同完成，其中，ResourceManage将某个NodeManager上资源分配给任务后，NodeManager需按照要求为任务提供相应的资源，并保证这些资源具有独占性，为任务运行提供基础的保证。