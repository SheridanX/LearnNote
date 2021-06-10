# 0 线性表

## **0.1定义:**

​	线性表List):零个或多个数据元素的**有限**序列。

## **0.2.线性表是一个序列**

​    也就是说，线性表的元素之间是有序的.若元素存在多个，对于其中一个元素来说，它前面的元素叫前驱，后面的元素叫后继。第一个元素无前驱，最后一-个元素无后继，中间的元素只有一个前驱和一个后继。

## **0.3.线性表是有限的**

​    事实上，在计算机科学领域，我们只研究有限的序列，因为计算机的处理对象都是有限的.而对于那些无限的序列，只在数学中讨论.
线性表的元素个数n(n>=0)定义为线性表的长度，当n=0时，称为空表。

## **0.4.举例**

​	现实中的排队。

## 0.5线性表的顺序存储结构

**定义：**
	线性表的顺序存储结构，指的是用**一段地址连续的存储单元依次存储线性表的数据元素**。在C语言中，我们可以使用一维数组来实现顺序表的存储结构。即把表的第一个元素数据存放到数组下标为0的位中，接着把线性表相邻的元素存储到数组中的相邻位置。

**英文单词缩写解释：**

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

## 0.6操作函数

### 0.2.1 结构体

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



### 0.2.2 创建空的线性表函数

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



### 0.2.3 查找元素

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



### 0.2.4 插入元素(按位置插入)

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



### 0.2.5 删除元素

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



### 0.2.6 清空表

```C
int ClearList(sqList *sq)
{
    if(NULL == sqList)
    {
        printf("Clear list error!\n");
        return ERROR;
    }
    
    free(sqList);
    
    return OK;
}
```

### 0.2.7 缺点

最大的缺点就是插入和删除时需要移动大量元素 ,非常耗时。

# 1 单链表

## 1.1单链表的定义

​     单向链表是链表的一种，其特点是链表的链接方向是单向的，对链表的访问要通过顺序读取从头部开始。链表是使用指针进行构造的列表，并且是由一个个结点组装起来的，因此又称为结点列表。其中每个结点都有指针成员变量指向列表中的下一个结点，head指针指向第一个结点称为表头，而终止于最后一个指向nuLL的指针。



## 1.2结点的数据结构

```c
#define OK 0
#define ERR -1

typedef struct _LINK_NODE  
{  
    int data;  
    struct _LINK_NODE* pNext;  
}LINK_NODE;
```

 各个结点连接在一起构成一个单向链表（示意图）

![img](image/20160928205306919)



## 1.3 单向链表的优缺点

和普通的线性结构（如数组）相比，链表结构有以下特点：
    （1）单个结点创建非常灵活，普通的线性内存通常在创建的时候就需要设定数据的大小
    （2）结点的删除、插入非常方便，不需要像线性结构那样移动剩下的数据
    （3）结点的访问方便，可以通过循环或者递归的方法访问到任意数据，但是平均的访问效率低于线性表



## 1.4 单向链表的基本操作

### 1.4.1 建立一个新的链表

```c
LINK_NODE* create_node(int value)
{
    LINK_NODE *pLinkNode = NULL;
    pLinkNode = (LINK_NODE *)malloc(sizeof(LINK_NODE); //申请内存
    pLinkNode->date = value;
    pLinkNode->pNext = NULL;
    
    reuturn pLinkNode;
}
```



###  1.4.2增加一个结点（增加到末尾）

```c
int _add_node(LINK_NODE **pNode,  LINK_NODE *pDateNode)
{
    if(NULL == *pNode)
    {
        *pNode->pNext = pDateNode;   //pNode的指针被改了, 所以传入的时候需要双重指针
        reuturn OK;
    }
    
	return _add_node(&(*pNode)->pNext, pDateNode);  //递归调用寻找尾节点
}

int add_node(LINK_NODE **pNode, int value)
{
    if(NULL == *pNode)
    {
        return ERR;
    }
    
    LINK_NODE *pDateNode = NULL;
    
    pDateNode = create_node(value);  //创建新节点
    if(pDataNode == NULL) 
    {
        return ERR;
    }
    
    return _add_node(pNode, pDateNode); //将新的节点插入指定链表的尾部
}
```



### 1.4.3 删除一个结点

