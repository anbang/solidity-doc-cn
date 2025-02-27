.. index: variable cleanup

*********************
清理变量
*********************

当一个值短于256位时，在某些情况下，剩余位必须被清理。
Solidity 编译器在设计时，会在操作数据之前清理这些剩余位，以避免剩余位中潜在垃圾数据在操作产生任何不利影响。


例如，在将一个值写入存储器之前，需要清除剩余的位，因为存储器的内容可以用于计算哈希值或作为消息调用的数据发送。
同样，在将一个值存储到存储器中之前，也需要清除剩余的位，因为否则可以观察到垃圾数据。

另一方面，如果紧接着的操作不受影响，我们就不清理位。 例如，由于任何非零值都会被 ``JUMPI`` 指令认为是 ``true`` ，所以在布尔值被用作条件判断之前，不需要清理它们。
``JUMPI``。


除了上述设计原则外，Solidity 编译器也会在将输入数据（input data）加载到堆栈时，会对其进行清理。

不同的类型有不同的清理无效值的规则：

+---------------+---------------+-------------------+
|Type           |Valid Values   |Invalid Values Mean|
+===============+===============+===================+
|enum of n      |0 until n - 1  |exception          |
|members        |               |                   |
+---------------+---------------+-------------------+
|bool           |0 or 1         |1                  |
+---------------+---------------+-------------------+
|signed integers|sign-extended  |currently silently |
|               |word           |wraps; in the      |
|               |               |future exceptions  |
|               |               |will be thrown     |
|               |               |                   |
|               |               |                   |
+---------------+---------------+-------------------+
|unsigned       |higher bits    |currently silently |
|integers       |zeroed         |wraps; in the      |
|               |               |future exceptions  |
|               |               |will be thrown     |
+---------------+---------------+-------------------+
