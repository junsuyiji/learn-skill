# 数据结构

## 线性表

### 顺序表

**属性**

应该有一个最大值，毕竟这个顺序表用数组来存储，然后还应该有一个当前的长度，然后在开辟一个数组来存储数据就ok

```C
#define MAXLENGTH 10

struct seqList {
	int length;
    int *data;
};
```



 既然有了属性，接下来就应该有对应的方法了

**首先进行初始化**

```c
//C语言中没有引用，传递地址仅仅能够使用指针
void init(struct seqList *L) {
	L->data = (int *) malloc(MAXLENGTH*sizeof(int));
    
    if (!L->data) {
    	printf("分配存储空间失败");
    }
    
    L->length = 0;
}
```



**判断顺序表是否为空**

```c
int is_empty(struct seqList L) {
	if (L.length == 0) {
    	return 1;
    }
    return 0;
}

//同理是否为满如下
int is_full(struct seqList L) {
	if (L.length == MAXLENGTH) {
    	return 1;
    }
    return 0;
}
```



**插入一个元素**

```c
void insert(struct seqList *L, int index, int value) {
 //判定插入的位置是否合法，判断空间有没有剩余,index 从0开始
 if (index < 0 | index > L->length | L->length > (MAXLENGTH - 1)){
 	printf("插入错误\n");
    return ;
 }
 
 for (int i = L->length; i > index; i--) {
 	L->data[i] = L->data[i-1];
 }
 
 L->data[index] = value;
 L->length++;
  
}
```



**删除一个元素**

```c
void delete(struct seqList *L, int index) {
if (index < 0 | index > L->length - 1 | L->length < 0 )
{
    printf("删除错误\n");
    return ;
}

int temp = L->data[index];
for (int i = index; i < L->length - 1; i++) {
	L->data[i] = L->data[i+1]
}

L->length--;
}
```





**显示当前线性表的元素**

```c
void show(struct seqList L) {
	for (int i = 0; i < L.length; i++)
        printf("%d  ", L.data[i]);
    printf("\n");
}
```









# stm32

## GPIO

**简介**





























































