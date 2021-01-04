## 0 线性表

### 0.0 线性表的定义

**1.定义:**

​	线性表List):零个或多个数据元素的**有限**序列。

**2.线性表是一个序列**
    也就是说，线性表的元素之间是有序的.若元素存在多个，对于其中一个元素来说，它前面的元素叫前驱，后面的元素叫后继。第一个元素无前驱，最后一-个元素无后继，中间的元素只有一个前驱和一个后继。
**3.线性表是有限的**

​    事实上，在计算机科学领域，我们只研究有限的序列，因为计算机的处理对象都是有限的.而对于那些无限的序列，只在数学中讨论.
线性表的元素个数n(n>=0)定义为线性表的长度，当n=0时，称为空表。

**4.举例**

​	现实中的排队。

### 0.1 线性表的顺序存储结构

**1.定义：**
	线性表的顺序存储结构，指的是用**一段地址连续的存储单元依次存储线性表的数据元素**。在C语言中，我们可以使用一维数组来实现顺序表的存储结构。即把表的第一个元素数据存放到数组下标为0的位中，接着把线性表相邻的元素存储到数组中的相邻位置。

**2.英文单词缩写解释：**

```c
//线性    List
//树形    Tree
//图形    Graph

//顺序    sa/seq
//链式    link

//插入    insert
//删除    delete
//创建    create
//清空    clear
//获取    get
//查找    search
//排序    sort
```

### 0.2 操作函数

#### 0.2.1 结构体

```c
#define MAX 20
#define ERROR -1
#define OK     0

typedef int data_t;
typedef struct
{
    data_t data[MXA];
    int length;	//表长
}sqList;
    
/*
  表长：当前顺序表内有效元素的个数; 
  表长于数组长度区别：
	1.表长是变量， 数组长度是常量；
	2.表长表示当前顺序表内有效元素个数，因此表长时刻小于等于数组长度；
*/
```



#### 0.2.2 创建空的线性表函数

```c
sqList *CreatEmptySqlist()
{
    sqList *sq;
    sq = (sqList*)malloc(sizeof(SqList));
    if(NULL == sq)
    {
        perror("cannot malloc");
        return null;
    }
    sq->length = 0; //将表长置为0, 堆区和栈区内存是脏的,需要初始化;
    
    return sq;
}
```



#### 0.2.3 查找元素

```c
int GetElement(sqList *sq, int pos, data_t *element)
{
	if(sqList == NULL || pos < 0 && pos >= sq->length)
	{
		return ERROR;
	}
	*element = sq->data[i];
    
    return OK;
}
```



#### 0.2.4 插入元素(按位置插入)

```C
int InsertList(sqList *sq, int pos, const data_t element)
{
    if(sqList == NULL)
    {
        return ERROR;
    }
    
    /*step1: 判满*/
    if(sq->length == MAX)
    {
        printf("list full\n");
        return ERROR;
    }
    
    /*Step2: 判别插入位置是否合法*/
	if(pos < 0 && pos > sq->length)
	{
        printf("Insert position error!");
		return ERROR;
	}
    
    int i;
    data_t tmpElement;
    for(i = sq->length - 1; i >= pos; i--)
    {
        sq->data[i+1] = sq->data[i];
    }
    data[pos] = element;
    sq->length += 1;
    
    return OK;
}
```



#### 0.2.5 删除元素

```C
int DeleteElement(sqList *sq, int i, data_t *element)
{  
    if(sqList == NULL)
    {
        return ERROR;
    }
    
    /*step1: 判空*/
    if(sq->length == 0)
    {
        printf("list empty\n");
        return ERROR;
    }
    
    /*Step2: 判别删除的位置是否合法*/
	if(pos < 0 && pos >= sq->length)
	{
        printf("Insert position error!");
		return ERROR;
	}
    
    int i;
    for(i = pos; i < sq->length - 1; i++)
    {
        qs->data[i] = qs->data[i+1];
    }
    sq->length -= 1;
    
    return OK;
}
```



#### 0.2.6 清空表

```

```

