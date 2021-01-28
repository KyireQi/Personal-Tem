# 数据结构
---

## 二叉堆
二叉堆是一种支持插入、删除、查询最值的数据结构。其实是一颗满足堆性质的**完全二叉树**结构，堆性质又分为“**大根堆**”和“**小根堆**”。
下面是大根堆的板子：
插入一个节点
```cpp
//Insert
int heap[maxn],n;
void up(int p)
{
    while(p > 1){
        if(heap[p] > heap[p / 2]){
            swap(heap[p],heap[p / 2]);
            p /= 2;
        }
        else break;
    }
}

void Insert(int val)
{
    heap[++n] = val;
    up(n);
}
```

获得堆顶元素
```cpp
//Getop
int Gettop()
{
    return heap[1];
}
```

把堆顶从二叉堆中删除
```cpp
//Extract
void down(int p)
{
    int s = p * 2;
    while(s <= n){
        if(s < n && heap[s] < heap[s + 1]) s++;
        if(heap[s] > heap[p]){
            swap(heap[s],heap[p]);
            p = s;
            s = p * 2;
        }
        else break;
    }
}

void Extract()
{
    heap[1] = heap[n --];
    down(1);
}
```
删除`heap`数组中下标为`k`的数
```cpp
void Remove(int k)
{
    heap[k] = heap[n --];
    up(k);down(k); //因为这里可能执行向上调整也可能去向下调整，所以需要分别检查处理。
}
```