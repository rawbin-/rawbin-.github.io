---
layout: post
title: "Chrome数组排序那些事"
sticky: 98
cagegories: [Web开发,前端开发,移动端]
tags: [异步,JavaScript,NodeJS,浏览器]
---

# 前言

[v8源码](https://github.com/v8/v8) 可以在[github](https://github.com/v8/v8)上面直接下，[早期的ArraySort](https://github.com/v8/v8/blob/98d735069d0937f367852ed968a33210ceb527c2/src/js/array.js#L709) 是用JS来实现的，整合了插入排序和快速排序两种算法，在10以下就用插入，以上就用快排。后来更换了算法和实现方式，到目前为止(20211125) [最新的数组排序还是](https://github.com/v8/v8/blob/main/third_party/v8/builtins/array-sort.tq) 使用的TimSort这个算法，看起来是从[Python](https://github.com/python/cpython/blob/main/Objects/listobject.c)那学来的。 

详情可以参考[这里](https://juejin.cn/post/6844903953964990471)，也有一些过程内容可以[译文](https://juejin.cn/post/6844903765707866119) 或者 [原版V8开发博客](https://v8.dev/blog/array-sort)

解决的问题就一个，[排序稳定性问题](https://bugs.chromium.org/p/v8/issues/detail?id=90)，从这个文章中可以看出从这个问题提出（2008-09-25）到这个问题上从代码上解决（2018-09-04），一晃就10年过去了，我们也可以看出，作为Google的技术人员，不是什么问题都要解决的，同时也不是什么问题都要立马解决。【此时无声，感受一下，十年呐】

同时，反过来，如果是一个好问题，即使过了十年，最终也是会被解决的。同时也对我们提出了一些新的可能，技术和产品之间的关系，即使这样的问题不被解决，也不妨碍Chrome是一个好产品，所以对产品问题而言，正确客观分级尤为重要。我们经常犯的一个错误就是为了某个或者某些用户去搞一些劳民伤财的事情，甚至还顶着一个技术突破的光环，对于企业来说，是不是在浪费成本？对于整个社会来说，是不是徒增碳排放？有个成语叫【过尤不及】，简单的说就是有些事情它是正确的，同时它也是错误的，关键要看他们在什么场景什么语境下，有没有明显错过那个平衡点。

我们先梳理下基本的常见的排序相关的算法，再回过头来看看这里面的算法是怎么回事，看看这个过程中会不会有什么新的发现。

# 排序算法分类集锦

## 常见排序算法及其实现

尽可能描述清楚算法的思想和实现过程思路，代码只是一个参考，可能有多种不同的写法。

实现的思路基本都会有循环和递归的方式，有的描述用递归更简单，有的用循环和递归都可以，具体看算法的思路。以冒泡为例写的详细些，其他的思路拓展类似同理。

所有的实现代码可 [在这里](https://github.com/rawbin-/algorithms-sort-js) 找得到。

### 1、冒泡排序

#### 算法思想

从第一个元素开始，依次跟下一个元素做对比，如果比下一个元素大，就交换位置，如果比下一个元素小，就保持不变；移动到下一个元素，重复上面的操作。这样每一趟（从头到尾）都可以获得一个有序的结果（最小的放在了最前面），最多进行`n`趟即可完成所有元素排序。

#### 实现思路

循环的思路: 可以用双重循环，外层循环处理所有元素，内层循环处理当前元素之后的元素，这样每一趟处理完就可以得到一个最大或者最小值，可以选择小的放前面或者大的放后面的方式。

递归的思路：跟循环类似，先一趟得到一个有序的元素，然后递归处理剩下的元素

算法优化：冒泡算法没法检测出已经有序的序列，所以可以通过整个过程一趟中是否发生交换来判断，如果没发生交换，说明本身是有序的，可以直接结束。

##### 实现一 小的往前放

```javascript
export function bubbleSortA(dataList){
    const n = dataList.length
    for(let i = 1; i <= n; i++){
        for(let j = n - 1; j >= i; j--){
            if(dataList[j] < dataList[j - 1]){
                [dataList[j],dataList[j - 1]] = [dataList[j - 1],dataList[j]]
            }
        }
    }
    return dataList
}
```



##### 实现二 小的往前放递归

```javascript
export function bubbleSortARecursive(dataList,startIndex = 0){
    const n = dataList.length
    if(startIndex === n - 1){
        return
    }
    for(let i = startIndex; i < n - 1; i++){
        if(dataList[i] > dataList[i+1]){
            [dataList[i],dataList[i + 1]] = [dataList[i + 1],dataList[i]]
        }
    }
    bubbleSortARecursive(dataList,startIndex + 1)
    return dataList
}
```

##### 实现三 大的往后放

```javascript
export function bubbleSortB(dataList){
    const n = dataList.length
    for(let i = 1; i < n; i++){
        for(let j = 0; j < n - i; j++){  // 第 i+1 趟让下标 n-i 的元素有序
            if(dataList[j] > dataList[j + 1]){
                [dataList[j],dataList[j + 1]] = [dataList[j + 1],dataList[j]]
            }
        }
    }
    return dataList
}
```

#####实现四 大的往后放递归

```javascript
export function bubbleSortBRecursive(dataList, endIndex = dataList && dataList.length){
    if(endIndex === 0){
        return
    }
    for(let i = 0; i < endIndex; i++){
        if(dataList[i] > dataList[i+1]){
            [dataList[i],dataList[i + 1]] = [dataList[i + 1],dataList[i]]
        }
    }
    bubbleSortBRecursive(dataList, endIndex - 1)
    return dataList
}
```



#####实现五 小的往前放特殊优化版

```javascript
export function bubbleSortAOpt(dataList){
    const n = dataList.length
    let isOrdered = true
    for(let i = 1; i <= n; i++){
        for(let j = n - 1; j >= i; j--){  // 第 i+1 趟让下标 i 的元素有序
            if(dataList[j] < dataList[j - 1]){
                [dataList[j],dataList[j - 1]] = [dataList[j - 1],dataList[j]]
                isOrdered = false // 第一轮发生过交换，就不是完全有序的
            }
        }
        if(isOrdered){
            break
        }
    }
    return dataList
}
```



### 2、选择排序

#### 算法思想

从第一个元素开始，遍历所有的元素，选出一个最小的元素跟第一个元素交换位置；然后从第二个元素开始遍历之后的元素，选出一个最小的放入第二个元素的位置，以此类推，直到排序完成。

#### 实现思路

这里涉及到一个临时的辅助来记录当前最小的元素，可以记录元素位置，也可以记录索引值，这里选择记录索引值。

同样可以有循环和递归的思路，这里就用循环的思路来实现。

```javascript
export function selectSort(dataList){
    const n = dataList.length
    for(let i = 0; i < n; i++){
        let minElIndex = i;
        for(let j = i + 1; j < n; j++){
            if(dataList[minElIndex] > dataList[j]){
                minElIndex = j; // 这个地方要分号
            }
        }
        [dataList[i],dataList[minElIndex]] = [dataList[minElIndex],dataList[i]]

    }
    return  dataList
}

```



### 3、插入排序

#### 算法思想

把待排序序列分成两部分，一部分为有序部分，一部分为待排序部分，每次从待排序部分中选择第一个元素插入到有序部分的合适位置（并做必要的位置后移），直到所有的元素都被插入放置过。

#### 实现思路

可以先假设第一个元素是已经排序的，从第二个元素是待排序的，然后从第二个元素跟已经排序的元素做对比，并插入到合适的位置。

插入有不同的方法，找到合适的位置也可以有不同的方法。

##### 实现一 使用findIndex找位置并挪动

```javascript
/**
 * 元素挪动的方式实现插入
 * @param dataList
 * @returns {*}
 */
export function insertSort(dataList){
    const n = dataList.length
    //初始0-i 的元素是有序的，i-n的元素是待排序的
    for(let i = 1; i < n; i++){
        // 只有当前元素比最大的有序序列小才需要插入，否则原地不动就行
        if(dataList[i] < dataList[i-1]){  // 这里要用< 才能使算法稳定，否则后面的等值元素就可能出现在前面
            // 先找到第一个比当前元素大的元素，然后插入到他的前面(在这之前需要先把空位置挪出来)
            // 这个也是基于外层的判断的，否则可能找的位置不对,比如当前元素后面有比他小的的时候
            let targetIndex = dataList.findIndex(item => item > dataList[i]) // 这里也一样，要 > 才能使算法稳定，否则后面的等值元素可能出现在前面
            let tmpValue = dataList[i]
            for(let j = i; j > targetIndex; j--){
                dataList[j] = dataList[j-1]
            }
            dataList[targetIndex] = tmpValue
        }
    }
    return dataList
}
```



##### 实现二 使用splice插入

```javascript
/**
 * 利用数组api splice实现插入
 * @param dataList
 * @returns {*}
 */
export function insertSortEx(dataList){
    const n = dataList.length
    //初始0-i 的元素是有序的，i-n的元素是待排序的
    for(let i = 1; i < n; i++){
        // 只有当前元素比最大的有序序列小才需要插入，否则原地不动就行
        if(dataList[i] < dataList[i-1]){  // 这里要用< 才能使算法稳定，否则后面的等值元素就可能出现在前面
            // 先找到第一个比当前元素大的元素，然后插入到他的前面(在这之前需要先把空位置挪出来)
            // 这个也是基于外层的判断的，否则可能找的位置不对,比如当前元素后面有比他小的的时候
            let targetIndex = dataList.findIndex(item => item > dataList[i]) // 这里也一样，要 > 才能使算法稳定，否则后面的等值元素可能出现在前面
            let tmpValue = dataList[i]
            dataList.splice(i,1) // 删掉当前元素
            dataList.splice(targetIndex,0,tmpValue) // 插入当前元素
        }
    }
    return dataList
}
```



##### 实现三 使用循环找位置

```javascript
export function insertSortExA(dataList){
    const n = dataList.length
    //初始0-i 的元素是有序的，i-n的元素是待排序的
    for(let i = 1; i < n; i++){
        // 只有当前元素比最大的有序序列小才需要插入，否则原地不动就行
        if(dataList[i] < dataList[i-1]){  // 这里要用< 才能使算法稳定，否则后面的等值元素就可能出现在前面
            // 先找到第一个比当前元素大的元素，然后插入到他的前面(在这之前需要先把空位置挪出来)
            // 这个也是基于外层的判断的，否则可能找的位置不对,比如当前元素后面有比他小的的时候
            let targetIndex = 0
            for(let j = 0 ; j < i; j++){
                if(dataList[j] > dataList[i]){ // 这里也一样，要 > 才能使算法稳定，否则后面的等值元素可能出现在前面
                    targetIndex = j
                    break
                }
            }
            let tmpValue = dataList[i]
            dataList.splice(i,1) // 删掉当前元素
            dataList.splice(targetIndex,0,tmpValue) // 插入当前元素
        }
    }
    return dataList
}
```



##### 实现四 使用二分找位置

```javascript
/**
 * 利用数组api splice实现插入，二分做查找
 * @param dataList
 * @returns {*}
 */
export function insertSortExB(dataList){
    const n = dataList.length
    //初始0-i 的元素是有序的，i-n的元素是待排序的
    for(let i = 1; i < n; i++){
        // 只有当前元素比最大的有序序列小才需要插入，否则原地不动就行
        if(dataList[i] < dataList[i-1]){  // 这里要用< 才能使算法稳定，否则后面的等值元素就可能出现在前面
            // 先找到第一个比当前元素大的元素，然后插入到他的前面(在这之前需要先把空位置挪出来)
            // 这个也是基于外层的判断的，否则可能找的位置不对,比如当前元素后面有比他小的的时候
            let tmpValue = dataList[i]
            let minIndex = 0, maxIndex = i - 1
            while (minIndex <= maxIndex){
                let middleIndex = (minIndex + maxIndex) >> 1; // 这里通过向右移动一位实现折半（除以二）的效果，比做除法要高效，同时还不用取整，移位不会产生小数
                if(dataList[middleIndex] > tmpValue){ // 中位大就在低区找 // 这里会造成算法不稳定
                    maxIndex = middleIndex - 1
                }else{
                    minIndex = middleIndex + 1
                }
            }

            dataList.splice(i,1) // 删掉当前元素
            dataList.splice(minIndex,0,tmpValue) // 插入当前元素
        }
    }
    return dataList
}
```



### 4、快速排序

#### 算法思想

先从待排序元素中原则一个作为比较基准，然后将剩下元素与这个基准元素做比较，同时将小于基准元素的放在基准元素左边，不小于基准元素的放在基准元素的右边，这样就可以得到左右两个序列，同理对左边和右边序列进行快速排序，直到序列元素个数为1。

#### 实现思路

基于上面的思想，可以有不同的思路来解决：

直接的方式，把基准元素选出来并存起来（第一个），同时用一个指针记录基准元素预期位置，从头到尾走一趟，找到小于基准元素的元素，挪动基准元素预期位置到下一个，并把小于基准元素的元素与基准元素预期位置的元素交换，处理完成后，把基准元素位置的当前元素放到基准元素的位置（第一个），再把基准元素放到预期的位置。这样就走完一趟实现左右分区，再分别对左右分区使用相同的方法。

在选定基准元素之后，定义两个指示器分别指向待排序元素的头部和尾部，分别从前到后和从后往前检查，后面的指针找比基准元素小的，前面的指针找比基准元素大的，找到了就将当前指示器处的两个元素交换，这样就好像这个基准元素已经在中间了一样，最后当前后指针相同的时候，结束本轮，得到左右两个分区，分别对左右分区使用同样的方法。

##### 实现一 使用单指针方式

```javascript
export function quickSortA(dataList,start,end){
    if(start < end){
        let pivot = dataList[start] // 第一个元素为基准
        let pivotIndex = start // 预期基准的位置
        for(let j = start + 1; j <= end; j++){
            if(pivot > dataList[j]){
                let tmp = ++pivotIndex; // 每次交换的是基准预期位置的元素，有交换的话（会来一个小的），预期位置就是下一个位置
                [dataList[j],dataList[tmp]] = [dataList[tmp],dataList[j]]
            }
        }

        // 将基准预期位置的元素和基准元素交换
        [dataList[start],dataList[pivotIndex]] = [dataList[pivotIndex],dataList[start]]

        quickSortA(dataList,start,pivotIndex -  1)
        quickSortA(dataList,pivotIndex + 1, end)
    }
    return dataList
}
```



##### 实现二 使用双指针方式

```javascript
export function quickSortB(dataList,start,end){
    if(start < end){
        let low = start, high = end;
        let pivot = dataList[start]
        while (low < high){
            while (low < high && pivot >= dataList[low]){
                low++
            }
            while (low < high && pivot < dataList[high]){
                high--
            }
            [dataList[low],dataList[high]] = [dataList[high],dataList[low]]
        }
        // 这里low === high了
        [dataList[start],dataList[high-1]] = [dataList[high-1],dataList[start]]

        quickSortB(dataList,start, low - 1)
        quickSortB(dataList,low + 1, end)
    }

    return dataList
}
```



### 5、归并排序

#### 算法思想

归并排序采用了先拆后合的思路，先把待排序序列分成两半，再用同样的方式将左右两半继续拆分，直到拆解为单元素为止，这个时候拿到的就是单元素，然后依次合并两个单元素得到一个有序的序列，然后依次合并有序的序列得到更大的有序序列，直到得到整个有序序列。这是最简单的二路归并，当然按分层的份数可以有多路归并，这里先探讨二路归并。

#### 实现思路

这也是典型的递归思路，先用递归做分解左右两半，然后分别对左右两半做分解，最后合并分解完的结果，过程为合并相邻两数，合并相邻俩序列。合并方式为都有元素的情况下，各取第一个相互比较，谁小谁先放，以此类推；最后把剩余元素放上。这里涉及到临时存储，有必要的话释放掉，函数中会自动释放可以不手动。

```javascript
export function mergeSort(dataList,start,end){
    if(start < end){
        let middleIndex = (start + end) >> 1
        mergeSort(dataList,start,middleIndex)
        mergeSort(dataList,middleIndex + 1,end)

        // 以下是合并的过程
        let tempList = [] // 存放临时已排序的序列
        // 合并左右两部分 第一部分是start到middleIndex, 第二部分是middleIndex + 1 到end
        let leftIndex = start, rightIndex = middleIndex + 1
        // 先合并都有元素可对比的情况
        while (leftIndex <= middleIndex && rightIndex <= end){
            if(dataList[leftIndex] <= dataList[rightIndex]){
                tempList.push(dataList[leftIndex++]) // 先取值再自增
            }else{
                tempList.push(dataList[rightIndex++]) // 先取值再自增
            }
        }

        // 再合并只有左或者只有右存在的元素
        // 合并左序列剩余元素
        while (leftIndex <= middleIndex){
            tempList.push(dataList[leftIndex++])
        }
        // 合并右序列剩余元素
        while (rightIndex <= end){
            tempList.push(dataList[rightIndex++])
        }

        // 再把临时序列已排序的数据写回到dataList中
        for(let i = 0, len = tempList.length; i < len; i++){
            dataList[start + i] = tempList[i]
        }
    }

    return dataList
}

```



### 6、堆排序

在堆排序中，需要有一些先决的条件和概念，我们先来简单梳理下，就不放大了，放大的内容放到后面专门讲对应的数据结构的时候来说。

#### 相关的概念基础

- 树：是一系列点（被称为节点）和线（被称为边）的集合。这个集合还需要满足一些约束条件：

  - 树是表达一个层次结构，类似于现实世界中的树，抽象的树结构一般根节点在上面，叶子节点在下面（倒着的树）
  - 树中有一个特殊的节点，被称为根，根节点与其他节点的关系可以是父子关系或者祖先和子孙关系
  - 除根之外的每个节点都是由一条边，连接到它的父节点（这个点称为它的父节点的子节点），也就是每个节点都可以通过递归找父节点的方式找到树根。

  树的相关概念

  - 路径：是树节点之间的连续连线
  - 子树：树中的某个节点及其所有的子节点合称该树的子树
  - 叶子节点：树中没有子节点的节点是叶子节点
  - 节点的度：节点含有的子节点的个数
  - 树的度：树中度最大的节点的度
  - 节点的高度：从当前节点到叶子节点的最长路径
  - 树的高度：根节点的高度
  - 节点的深度：从根节点到当前节点的路径的长度
  - 树的深度：最深节点的深度

- 有序树：树的节点逻辑上指定了从左到右的顺序，并且节点是有序的

- 无序树：树中节点没有逻辑上的顺序关系，也称为自由树

- 标号树：树中每个节点都有与之关联的标号或值的树，标号可能是一个简单符号，也可以是一个文档内容，可以认为标号是节点相关的信息，可以改变节点的标号，不能改变节点的名称（名称一般是唯一的，但标号不一定）

- 表达式树：这是我们在谈编译语法时常用的，用来表达操作符和操作数之间关系的树

- 二叉树：是节点最多有两个子节点的树

  - 类似于有作用两个位置分别为左子树和右子树，左右子树都可能为空，所以单节点的树也是特殊的二叉树（左右节点全为空）
  - 同一个父节点的左右两个节点成为左子节点和又子节点，如果包含了节点的子孙节点那么成为左子树和右子树

- 二叉排序树：要么是空树，要么满足如下特性的树：

  - 若存在左子节点，那么左子节点的值均小于根节点的值
  - 若存在右子节点，则右子树上所有的节点均不小于根节点的值
  - 左右子树分别是二叉排序树

- 平衡二叉树：要么是空树，要么满足如下特性的树：

  - 左右两个子树的高度差绝对值不超过1
  - 左右两个子树都是平衡二叉树

- 满二叉树：除最后一层（叶子节点）无任何子节点外，其他每一层上的所有节点都有两个子节点的二叉树

- 完全二叉树：除最后一层外，其他每一层的都有两个子节点，且最后一层的所有节点都连续集中在最左边

- 二叉查找树：是一种带标号的二叉树，满足所有节点的左子树的标号都小于跟节点的标号，根节点的标号都小于右子树的标号。

- 霍夫曼树/哈夫曼树：带权路径最短的二叉树，也称为最优二叉树

- 偏序树（POT Partially Ordered Tree）：是一种特殊的带标号二叉树，特殊在有如下的约束条件：

  - 树中节点有标号，同时标号具有优先级关系，优先级可以体现在元素的值上，也可以体现在某个组成部分的值上
  - 存储在节点中的元素的优先级，不小于存储在其子节点中元素的优先级，换句话说，任何子树根节点处的元素是该子树中最大（优先级）的元素，这是偏序树的特性

  偏序树是实现优先级队列的一种有效的方式，最优先的元素一定是在根节点上。偏序树的固有属性是保持优先级顺序，如果对偏序树进行插入元素和删除元素操作，那么偏序树会通过调整重新保持偏序特性，这为优先级队列提供了很大的方便。偏序树的操作过程：

  - 删除根节点（优先级最高的），先找到根节点，然后用最右的节点替代它，这样就破坏了偏序特性，然后为了还原偏序特性，新的根节点元素会不断【往下沉】，直到找到它合适的位置，使得它满足偏序特性（也就是优先级小于他的父节点，但不小于它的子节点）
  - 插入新节点，要在底层尽可能左边的位置增加一个新的叶子结点，如果底层没有空位置，就要新增加一个层级，并将新的节点放在新一层级的左端，这样也会破坏偏序特性，为了还原偏序特性，新的叶子节点需要不断【往上冒】，直到找到合适的位置，使得它满足偏序特性

- 平衡偏序树：若干偏序树除最下层之外的所有层级的节点都存在，而且最下层的叶子节点尽可能集中在左侧，那么这个偏序树是平衡的，称作平衡偏序树。（当然这是大佬们的定义，就我个人而言这里面其实更合适的是完全偏序树，因为[平衡]不一定[完全]，[完全]一定[平衡]，虽然这里叫[平衡]，但概念里面却是[完全]的意思，理解就行）

#### 相关概念的理解

这里没有一个固定的标准，有一些内容可以帮助理解，往后退一步看看：

- 首先是人类通过定义语言，同时通过命名，来实现沟通和交流，同时对世界形成一个相对共有的定义，比如先定义了人，然后再定义了什么人，概念的定义是为了快速达成共识
- 由于在语言中使用的灵活性，同样的概念在不同的地方都可以套用，可能不同语境下有相近的含义，但不一定是一致的
- 人类无法准确定义一个概念（因为每个概念都是套在其他概念上的，比如什么是人？可以一直问下去直到答不下去），但可以通过不一定准确的概念，让大家意会理解并达成共识，实现沟通和交流的目的。比如暗示，或者一个眼神就可以秒懂，这是人类的一大独有特质。
- 综上，有没有一个准确清晰的概念没有那么重要，当然有了更好，没有也行，关键是能准确理解，实现沟通交流就行
- 基于上面这些特性，我们试着来理解下相关的概念，但不一定能定义它们

##### 对堆栈的理解

###### 在程序运行场景下

【堆栈】是一种对程序运行时关键数据结构的统称，就像【吃喝拉撒】一样。但【堆】和【栈】是两种不同的概念，就像【吃】和【喝】一样。

【堆】和【栈】都是一种逻辑上的抽象结构，都具有各自的特性，实现方式可能有不同。比如【栈（内存）】有先进后出的特性，很适合层级现场状态保存，常用的实现方式是连续的内存空间，按约束的方式访问，当然也可以用非连续的内存空间，为什么不能呢？【堆（内存）】有动态扩展性高的特性，不管是连续还是不连续的都是从空余内存中动态申请的。



###### 在数据结构场景下

【栈】是一种逻辑抽象数据结构，具有先进后出的特性，有一些特定的入栈、出栈、清空栈等操作。就像ATM有存款、取款等操作一样。

【堆】是一种逻辑抽象数据结构，不同的约束下有不同的特性，比如最大堆，最小堆，还有共同的特性那就是动态和平衡，就像我们土堆、谷堆等，往上堆东西时会动态流动直到平衡，从上面挖东西后要保持堆型还要把堆堆起来一样。这种动态平衡可以被有效的利用起来，比如堆排序就是利用堆的特性来进行排序。到这里我们可以简单下个定义，堆就是平衡偏序树，可以用数组来存储和表示，表示堆和平衡偏序树的数组元素之间要满足偏序关系。

数据在内存和硬盘中存储的结构可能不一样，比如咱们最常见的一个组织架构是一个树的结构，但存储的时候可能还是顺序的结构，只是在内存中在逻辑上通过了parentId,children等字段来吧顺序的结构的层级关系联系起来形成了一个逻辑上的树形结构。类似的在咱们这个场景下，可能有二叉树的数据可能是一个数组来表示的。

#### 算法思想

堆排序就是利用偏序树的这种动态平衡的特性，根据需求设计最小堆和最大堆，先用所有元素建立堆，然后每次从堆顶（也就是偏序树根）取一个元素，依次排列，得到的结果就是有序的。

#### 实现思路

这个思路相对简单和大块，先实现一个偏序树及其自平衡的特性，然后每次从堆顶（树根）处取元素，然后将元素依次存下来，得到的结果就是排序的结果。

这里面有的隐含逻辑，完全二叉树可以用数组来存储，因为它是完全的，数据从前到后就可以对号入座，一层一层的装满，剩下的就是下一层的，最后一层从左往右装，可能装不满，但左边一定是连续的满的。这是完全二叉树的特性，也是平衡偏序树的特性（平衡偏序树是特殊的完全二叉树）。

对于平衡偏序树或者完全偏序树的数组结构（堆）中，有如下的关系(i 为数组下标)：

- 下标`i` 的元素的左子节点为下标`2i+1` 的元素，右子节点为下标`2i+2`的元素
- 最后一个带叶子的子节点索引是 `n >> 1 - 1` 数组元素个数的一半-1(减一的原因是索引从零开始)
- 那么根据偏序特性，对于大顶堆有 `dataList[i] >= dataList[2*i + 1] && dataList[i] >= dataList[2*i + 2]`
- 那么根据偏序特性，对于小顶堆有 `dataList[i] <= dataList[2*i + 1] && dataList[i] <= dataList[2*i + 2]`
- 由此对于堆的操作其实就是对数组特定关系的元素进行操作



##### 实现一 直接对整个数组建堆

这样的话会改变传入参数数组的引用，比如这里带来的公共测试用例的时候，后面写的用例就无法通过，需要特殊处理，因为input 被堆排序过程改写了，同时会创建额外的空间

```javascript
function heapifyNode(dataList,index = 0, rootMax = false){
    let leftIndex = 2 * index + 1,rightIndex = 2 * index + 2
    let maxLength = dataList.length
    let targetIndex = index
    if(rootMax){
        if(leftIndex < maxLength && dataList[targetIndex] < dataList[leftIndex]){
            targetIndex = leftIndex
        }

        if(rightIndex < maxLength && dataList[targetIndex] < dataList[rightIndex]){
            targetIndex = rightIndex
        }
    }else{
        if(leftIndex < maxLength && dataList[targetIndex] > dataList[leftIndex]){
            targetIndex = leftIndex
        }

        if(rightIndex < maxLength && dataList[targetIndex] > dataList[rightIndex]){
            targetIndex = rightIndex
        }
    }

    if(targetIndex !== index){
        [dataList[index],dataList[targetIndex]] = [dataList[targetIndex],dataList[index]]
        heapifyNode(dataList,targetIndex,rootMax)
    }
}

function heapify(dataList,rootMax = false){
    // 先把堆建立起来
    const count = dataList.length
    const maxParentNodeIndex = (count >> 1) - 1 // 对半减一，减的一为索引从零开始
    for(let i = maxParentNodeIndex ; i >= 0; i--){
        heapifyNode(dataList,i,rootMax)
    }

    return dataList
}

/**
 *
 * @param dataList
 * @param rootMax 是否是大根堆/大顶堆
 * @returns {*[]|*}
 */
export function heapSortA(dataList, rootMax = false){
    const [valid,result] = dataListCommonCheck(dataList)
    if(!valid){
        return result
    }

    const count = dataList.length
    const resultList = []

    if(rootMax){
        while (resultList.length !== count){
            heapify(dataList,rootMax)
            resultList.unshift(dataList[0])
            dataList.splice(0,1)
        }
    }else{
        while (resultList.length !== count){
            heapify(dataList,rootMax)
            resultList.push(dataList[0])
            dataList.splice(0,1)
        }
    }

    return resultList
}

```



##### 实现二 在原数组上建堆



### 7、桶排序

### 8、基数排序

### 9、希尔排序

### 10、计数排序

### 11、位图排序

### 12、其他排序

## 常见排序算法归类

### 交换类

#### 冒泡排序

#### 快速排序

### 插入类

#### 直接插入

#### 折半插入

#### 希尔排序

### 选择类

#### 简单选择

#### 堆排序

### 归并类

#### 二路归并

#### 多路归并

### 基数排序

### 桶排序

#### 计数排序

#### 桶排序



# 排序算法分析、评估、对比

## 时间复杂度

### 最好

### 最坏

### 平均

## 空间复杂度



## 稳定性



## 正确性



##  易维护性





# 关于测试

## jest 配置

随便选了几个项目看了看，都用`jest`,也在[`jest`官网](https://jestjs.io/)确认了，直接用就行了

默认支持的文件后缀

```
[
  "js",
  "jsx",
  "ts",
  "tsx",
  "json",
  "node"
]
```

默认支持的文件位置及文件名(这里是glob pattern)，不是正则表达式，语法可[参考这里](https://github.com/isaacs/node-glob#glob-primer)

```
 [
  "**/__tests__/**/*.[jt]s?(x)",
  "**/?(*.)+(spec|test).[tj]s?(x)"
]
```

所以我们要添加`.mjs`的支持的话，结果可能是这样的

```
 [
  "**/__tests__/**/*.?(m)[jt]s?(x)",
  "**/?(*.)+(spec|test).?(m)[tj]s?(x)"
]
```

添加`.mjs`就是想在Node 中直接执行ESModule的代码，对于jest来说需要特殊配置，[参考这里](https://jestjs.io/docs/ecmascript-modules)

咱们这里整个过程可以如下

### jest 安装及支持`.mjs`过程

- 先初始化`package.json` 用`npm init -y`，然后将script.test 改为 `jest`

- 安装`jest`包，使用`npm i jest -D` 或者 `yarn add -D`

- 完成上面步骤之后 可以按文件名和路径规则写测试用例了 比如 `tests/jest.test.js` ，然后用`npm test` 即可看结果了

  ```
  test('1+2=3',() => {
      expect(1 + 2).toBe(3)
  })
  
  test('array equal', () => {
      const a = [1,2]
      a.push(3)
      expect(a).toEqual([1,2,3])
  })
  
  ```

- 要支持`.mjs`的话还需要一些配置

  - 安装依赖包`npm i @babel/core @babel/preset-env -D`

  - 配置`babel` 在根目录下新建`.babelrc`，内容如下

    ```
    {
        "presets":[
            [
                "@babel/preset-env",{
                "targets":{
                    "node":"current"
                }
            }
            ]
        ]
    }
    
    ```

  - 创建`jest.config.js` 用`npx jest --init` 路上选择`babel`，变更如下字段

    ```
    {
      // An array of file extensions your modules use
      moduleFileExtensions: [
        "mjs",
        "js",
        "jsx",
        "ts",
        "tsx",
        "json",
        "node"
      ],
        // The glob patterns Jest uses to detect test files
      testMatch: [
        "**/__tests__/**/*.?(m)[jt]s?(x)",
        "**/?(*.)+(spec|test).?(m)[tj]s?(x)"
      ],
      // A map from regular expressions to paths to transformers
      "transform": {
        "^.+\\.m?js$": "babel-jest"
      },
    }
    ```

  - 完成之后就可以运行`.mjs`的测试用例了



# 举一反三

## 循环和递归的互换



## 自动化单元测试覆盖率不够



## 算法的灵活运用和整合



## 算法和结构的常见应用场景
