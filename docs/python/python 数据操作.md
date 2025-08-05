# Pandas

## 一、Numpy

1、如何创建一个 numpy 的 ndarray 对象，一维、二维、三维的？

```python
# 一维

# 二维

# 三维

```

2、ndarray 对象有哪些属性、分别是什么性质？

```python

```

3、怎么创建一个全 0 ndarray、全 1 ndarray、在范围内随机的数字，并指定这些 ndarray 的形状？

```python

```

4、np 库有哪些计算的函数？

```python

```

5、生成一个形状为 4 * 5 的 ndarray 对象，值随机在 [0,100] 之间，计算：

1. 整个 ndarray 的和
2. 最大值与最小值
3. 75% 分位的值

```python

```



## 二、Series

1、如何创建一个 series 对象？怎么指定显式索引，怎么指定 series 的名字？

```

```

2、series 对象有哪些属性？

```python

```

3、如何通过 loc、iloc 取值？如何一次取多个？

```python

```

4、如何通过 at、iat 取值？

```python

```

5、series 对象有哪些成员方法？

```python

```

6、计算 sales 这个销售记录中：

1. 哪个月比前面一月增长的最多？增加了多少？
2. 哪个月买的最少？哪个月买的最多？分别是多少？
3. 月环比增长率（环比 = 当前这个月的销量 / 上个月的销量）

```python
sales = pd.Series([120, 135, 145, 160, 155, 170, 180, 175, 190, 200, 210, 220],
                  index=pd.date_range('2024-01-01', periods=12, freq='MS'))


```



## 三、DataFrame

