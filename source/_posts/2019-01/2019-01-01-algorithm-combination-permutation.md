---
layout: post
title: "排列组合算法那些事"
categories: [Web开发,前端开发]
tags: [数据结构,算法,排列,组合]
---

# 前言

是有些时间没有真正跟算法打交道了，除了学习理论的过程，在实践中能用到算法的地方确实少，但这个**要考**,本着**在哪里跌倒就在哪里躺下然后再爬起来**的原则，我们来看看这里面相关的问题。

参考书籍 《组合数学 第五版》

相关代码:

- [js代码](https://github.com/rawbin-/combination-permutation-js)
- [python代码](https://github.com/rawbin-/combination-permutation-python)

# 组合

对于任意的集合A 比如`[1,2,3,4,5,6]` 和集合B比如`['A','B','C','D','E','F']`,限制类型主要是消除整数元素的特殊性，增加实现代码的可扩展性和实用性

## 生成包含R个元素的组合或者生成R子集

```
需求: 生成集合A和集合B的3元子集
```

### 算法一 全集筛选法

这只能算一种思路，但不那么有效。

先生成幂集，也就是所有的子集，这个也是《组合数学》上给出的方法，用`n`个二进制位来表示`n`个元素是否存在，而这`n`个二进制位从全`0`到全`1`的组合刚好是`n`个元素的幂集的组合。

然后从幂集中选择长度为`r`的子集，得到的就是`r`子集。



```javascript
import {combinationsAll} from "./combinations-all.mjs";

export function combinations(baseList,subSetLen){
    if(!Array.isArray(baseList)){
        return
    }
    if(baseList.length < subSetLen){
        return
    }
    const allCombinations = combinationsAll(baseList)
    return allCombinations.filter(item => {
        return item.length === subSetLen
    })
}
```





### 算法二  字典序生成法

参考《组合数学》中【生成r子集】部分，算法如下：

- 假定集合为[1,2,...n]
- 初始条件，设第一个`r`子集 a<sub>1</sub>a<sub>2</sub>...a<sub>r</sub>=12...r
- 当a<sub>1</sub>a<sub>2</sub>...a<sub>r</sub> 不等于 (n-r+1)(n-r+2)...n的时候，执行如下操作
  - 确定最大的整数 `k` 使得a<sub>k</sub> + 1 <= `n` 且 a<sub>k</sub> + 1 不是 a<sub>1</sub>,a<sub>2</sub>...a<sub>r</sub>中的一个
  - 用`r`子集 a<sub>1</sub>...a<sub>k-1</sub>(a<sub>k</sub>+1)(a<sub>k</sub>+2)...(a<sub>k</sub>+r-k+1) 替换  a<sub>1</sub>a<sub>2</sub>...a<sub>r</sub>

这个算法看起来有点`离散`哈，但他是利用在字典序中求一个序列的直接后继得到所有的`r`序列，所以避免了【算法一 全集筛选法】中的多余计算的问题。这里面`k`可以理解为`r`前序变后继的过程中要变化的位置的索引起始值，它的最大值为`r`(最后一个元素变)，最小值为`1`(第一个元素变)。

```javascript

// 获取最大的K，也就是要变化的位置
// a[k] + 1 <= maxEl, a[k] 不在 prevList中
const getMaxKPosition = (prevList, maxEl, subSetLen) => {
    let maxK = subSetLen
    let maxKValue = prevList[maxK - 1] + 1
    while (prevList.includes(maxKValue) || maxKValue > maxEl){
        maxK--
        maxKValue = prevList[maxK - 1] + 1
    }
    console.log(maxK)
    return maxK
}

const getNextCombination = (prevList,maxK,subSetLen) => {
    let nextList = [...prevList]
    if(maxK === subSetLen){
        nextList[maxK - 1] = nextList[maxK - 1] + 1
    }else{
        nextList[maxK - 1] = nextList[maxK - 1] + 1
        for(let i = maxK; i < subSetLen; i++){
            nextList[i] = nextList[i - 1] + 1
        }
    }
    return nextList
}

function combinationsDict(baseLen, subSetLen){
    if(baseLen < subSetLen){
        return
    }

    const baseList = []
    for(let i = 1; i <= baseLen; i++){
        baseList.push(i)
    }

    let prevList = baseList.slice(0,subSetLen)
    let lastList = baseList.slice(baseLen - subSetLen)

    const resultList = [
        prevList
    ]
    let maxEl = baseList[baseLen - 1]
    let maxK = getMaxKPosition(prevList,maxEl,subSetLen)
    while (prevList.join() !== lastList.join()){
        prevList = getNextCombination(prevList,maxK,subSetLen)
        resultList.push(prevList)
        maxK = getMaxKPosition(prevList,maxEl,subSetLen)
    }
    return resultList
}


export function combinations(baseList, subSetLen){
    const indicateList = combinationsDict(baseList.length,subSetLen)
    return indicateList.map((itemList,itemListIndex) => {
        return itemList.map((item,index) => {
            return baseList[item - 1]
        })
    })
}

console.log(combinationsDict(6,3))
console.log(combinations([1,2,3,4,5,6],3))
console.log(combinations(['A','B','C','D','E','F'],3))

```



### 算法三 递归加入法

算组合的时候的直接思路

- 先选一个
- 再从剩下的中选`r-1`个
- 将先选的那一个和每一个`r-1`的组合组合起来，就是`r`子集，以此类推

```javascript
export function combinations(baseList,subSetLength){
    if(!Array.isArray(baseList)){
        return
    }

    if(subSetLength === 1){
        return baseList.map(baseItem => [baseItem])
    }

    const resultList = []
    baseList.forEach((baseItem,baseIndex) => {
        const lessCombi = combinations(baseList.slice(baseIndex + 1),subSetLength - 1)

        lessCombi.forEach((lessCombiItem,lessCombiIndex) => {
            resultList.push([baseItem,...lessCombiItem])
        })
    })

    return  resultList
}
```



### 算法四 非递归加入法

也是算组合的时候的直接思路：

- 先选一个
- 再从没选过的剩下的里面选一个，以此类推直到选够
- 为了避免重复，按字典序选择，同时选的时候把最后面的空位要留出来
  - 比如说，[1,2,3,4,5,6] 中选三个的话，那么第一个只能选到4，剩下的5和6 不能选，选了就凑不够三个了，还要回过头来选前面的，这样会造成重复
  - 同样的选第`n`个的时候，序列小的不能选，一定是被选过的，因为是按字典序选的
- 因为是组合，所以这里按字典序只能选比当前集合中最大的还要大的。

```javascript
export function combinations(baseList,subSetLength){
    if(!Array.isArray(baseList)){
        return
    }

    if(subSetLength > baseList.length){
        return
    }

    // 先选一个的选法
    let baseSelection = baseList.reduce((result, item ,index ) => {
        if(index <= baseList.length - subSetLength){
            result.push([item])
        }
        return result
    },[])
    for(let i = 1; i < subSetLength; i++){
        baseSelection = baseSelection.reduce((result,baseSeleItem) => {
            const maxIndexInBase = baseList.indexOf(baseSeleItem[baseSeleItem.length - 1])
            const options = baseList.slice(maxIndexInBase + 1)
            options.forEach(optionItem => {
                result.push([...baseSeleItem,optionItem])
            })
            return result
        }, [])
    }

    return baseSelection;
}

```



### 算法五 字典序生成法-生成器版-参考答案

这个是经过【举一反三】里面的源码学习，造的仿Python的版本，可以看到用Python写起来其实更加的简洁，

```javascript
function* combinationDict(baseListLength,subSetLength){
    const delta = baseListLength - subSetLength
    let indicator = [...Array(subSetLength).keys()]  // 初始指示器 [0,1,2...subSetLength-1]
    let firstChangeIndex = subSetLength - 1  // 从左到右第一个要变化索引的位置
    yield indicator
    while (true){
        // 默认从最后一位开始变化
        for(firstChangeIndex = subSetLength; firstChangeIndex--;){
            if(indicator[firstChangeIndex] !== firstChangeIndex + delta){
                break
            }
        }
        // 咱们这里也用一个循环穿透来判断，
        if(firstChangeIndex < 0){
            break
        }
        indicator[firstChangeIndex] += 1
        for(let i = firstChangeIndex + 1, len = indicator.length; i < len; i++){
            indicator[i] = indicator[i - 1] + 1
        }
        yield indicator
    }
}
export function* combinations(baseList,subSetLength){
    if(!Array.isArray(baseList)){
        return
    }
    if(baseList.length < subSetLength){
        return
    }
    const indicatorGen = combinationDict(baseList.length,subSetLength)
    while (true){
        const {value,done} = indicatorGen.next()
        if(!done){
            yield value.map(itemIndex => {
                return baseList[itemIndex]
            })
        }else{
            break
        }
    }
}


```



### Python 简单实现(利用标准库)

`itertools`参考文档[中文](https://docs.python.org/zh-cn/3/library/itertools.html), [英文](https://docs.python.org/3/library/itertools.html) 

 [python代码地址](https://github.com/rawbin-/combination-permutation-python)

```python
from itertools import combinations
import string
setA = range(1,7)  # [1,2,3,4,5,6]
setB = list(string.ascii_uppercase[0:6]) # ['A','B','C','D','E','F']

print(list(combinations(setA,3)))
print(list(combinations(setB,3)))
```





## 生成幂集

```
需求: 生成集合A和集合B的所有可能的子集
```

### 算法一 二进制位图法

这个也是《组合数学》上给出的方法，用`n`个二进制位来表示`n`个元素是否存在，而这`n`个二进制位从全`0`到全`1`的组合刚好是`n`个元素的幂集的组合。

```javascript
export function combinationsAll(baseList){
    if(!Array.isArray(baseList)){
        return
    }

    const totalCount = Math.pow(2,baseList.length)
    let resultList = [[]]
    for(let i = 1; i < totalCount; i++){
        const flagList = i.toString(2).split('').reverse() // 转成二进制字符串
        let tmpResult = []
        flagList.forEach((item,index) => {
            if(item === '1'){
                tmpResult.push(baseList[index])
            }
        })
        resultList.push(tmpResult)
    }
    return resultList
}

```





### Python 简单实现(利用标准库)

`itertools`参考文档[中文](https://docs.python.org/zh-cn/3/library/itertools.html), [英文](https://docs.python.org/3/library/itertools.html) 

 [python代码地址](https://github.com/rawbin-/combination-permutation-python)

```python
from itertools import combinations
import string
setA = range(1,7)  # [1,2,3,4,5,6]
setB = list(string.ascii_uppercase[0:6]) # ['A','B','C','D','E','F']
resultA = []
resultB = []

for i in range(len(setA) + 1):
    resultA += combinations(setA,i)
print(resultA)

for i in range(len(setB) + 1):
    resultB += combinations(setB,i)
print(resultB)
```





# 排列

对于任意的集合A 比如`[1,2,3,4,5,6]` 和集合B比如`['A','B','C','D','E','F']`,限制类型主要是消除整数元素的特殊性，增加实现代码的可扩展性和实用性



## 生成一个指定集合的全排列

### 算法一 递归插入法

这里恰好能用上在《组合数学》中提到的插入法，因为这里的方法是依赖于递归的思路，`n+1`元排列是基于`n`元排列来生成的，而如果是生成`r`排列的话，我们还不能用这样的方法，因为这样生成了多余的结果，还需要剔除，自然不是我们算法想要实现的结果。这里刚好需要所有的结果，何乐不为呢？算法思想也很简单：

- 可以直接生成一个`n`元排列，比如`0`元排列，`1`元排列

- 对于`n+1`元排列即是在`n`元排列的所有可能位置插入所有可能的元素

  怎么理解呢，`0`元排列就是一个空集,有一个可能插入的位置来形成排列，所以`1`元排列就是在`0`元排列的那唯一一个可能插入的位置插入各个可能的元素，也就得到的所有`1`元排列(也是只有一个元素的子集)，而对于`1`元排列来说，有两个可能插入的位置(元素左边和右边)，这样除去自身外的所有可能元素分别插入左边和右边即可得到`2`元排列，以此类推

或者换个描述方式，基本就是我们做排列的时候的思路:

- 先取一个
- 再将剩下`n-1`进行全排列
- 然后将先取的那个插入到剩下`n-1`全排列可能的位置上，形成n的排列，以此类推



```javascript
export function permutations(baseList){
    if(!Array.isArray(baseList)){
        return
    }

    if(baseList.length <= 1){
        return [baseList];
    }

    const lessPermutation = permutations(baseList.slice(1))
    const firstEl = baseList[0]

    const resultList = []
    lessPermutation.forEach(lessPermuItem => {
        // n + 1 个可插入的位置
        for(let i = 0, len = lessPermuItem.length; i <= len; i++){
            const tempResult = [...lessPermuItem]
            tempResult.splice(i,0, firstEl)
            resultList.push(tempResult)
        }
    })
    return resultList
}
```



### 算法二  非递归插值法

- 先取一个排好
- 然后再从剩下的里面取一个，插入到已经排好的可插入的位置（每个元素的前后），以此类推，直到排列个数够为止
- 为了避免重复，选择元素的时候，只能选择比当前排列中最大索引还要大的元素



```javascript
export function permutations(baseList){
    if(!Array.isArray(baseList)){
        return
    }
    // 一元子集
    let baseSubSet = baseList.map(item => {
        return [item]
    })

    for(let i = 1, len = baseList.length; i < len; i++){
        baseSubSet = baseSubSet.reduce((result, subsetItem,index) => {
            const candidates = baseList.filter((baseItem, baseIndex) => {
                // 当前集合中元素的最大元素的索引之后的元素
                const maxIndex = subsetItem.reduce((result,item) => {
                    const indexInBase = baseList.indexOf(item);
                    return indexInBase > result?indexInBase: result
                },0)
                return baseIndex > maxIndex
            })

            if(!candidates.length){
                return  result
            }

            // 插入位置为n + 1, 一个元素可以有两个插入位置（前后）
            for(let i = subsetItem.length + 1; i--;){
                candidates.forEach(candiItem => {
                    let tmpItem = [...subsetItem]
                    tmpItem.splice(i,0,candiItem)
                    result.push(tmpItem)
                })
            }
            return result
        },[])
    }
    return baseSubSet
}

```



### 算法三 位置移动法

这个也是《组合数学》【生成排列】部分提到的方法，为了解决插值法需要一次生成所有排列的问题，这里希望一次生成一个排列，也就是我们实现过程中使用生成器的玩法了，算法内容：

- 给定一个整数`k`,我们赋予它一个方向，即在这个证书的上方画出一个指向有或者向左的箭头，考虑[1,2,...n]的一个排列，其中的每一个整数都给定一个方向。如果一个整数`k`的箭头指向一个与其相邻但比它要小的整数，那么称这个整数`k`是可移动的。这里可以得出1始终是不能移动的（因为不管是哪个方向，都没有比它小的数），除了下面两种情况，整数`n`总是可以移动的（很明显）：
  - `n`是第一个整数，而它的箭头指向左边
  - `n`是最后一个整数，而它的箭头指向右边
- 设置一开始，所有的整数方向都向左
- 当存在一个可移动的整数时，完成下面的事情：
  - 求出最大的可移动数`m`
  - 交换`m`和它的箭头所指向的与它相邻的整数
  - 交换所有满足`p` > `m`的整数p上的箭头的方向

```javascript
export function* permutationsDict(baseListLength){
    // 初始化方向全部向左
    const indictor = [...Array(baseListLength).keys()].map(item => {
        return {
            value: item,
            direction: false, // false 左边，true，右边，反向的时候直接取非
            movable: item > 0 // 左边第一个不能动
        }
    })

    yield indictor.map(item => item.value)
    // 存在可移动时候循环
    while (indictor.some(item => item.movable)){
        // 求最大的可移动数
        const maxMovable = indictor.filter(item => item.movable).reduce((result,item,itemIndex) => {
            return result.value > item.value ? result: item
        },{value: -1})
        // 从indicator里面找到的索引才是真实的
        const maxMovableIndex = indictor.findIndex((item)=> {
            return item.value === maxMovable.value
        })

        // 交换相同方向上的相邻两个
        if(maxMovable.direction === false){ // 向左边
            [indictor[maxMovableIndex - 1],indictor[maxMovableIndex]] = [indictor[maxMovableIndex],indictor[maxMovableIndex - 1]]
            // 左边第一个不能移动
            if(maxMovableIndex - 1 === 0){
                maxMovable.movable = false
            }

        }else{ // 向右边
            [indictor[maxMovableIndex + 1],indictor[maxMovableIndex]] = [indictor[maxMovableIndex],indictor[maxMovableIndex + 1]]
            if(maxMovableIndex + 1 === baseListLength - 1){
                maxMovable.movable = false
            }
        }

        //重置方向
        indictor.filter(item => item.value > maxMovable.value).map(item => {
            // 这里不会出现 value 为0的，因为有过滤条件
            item.direction = !item.direction
            item.movable = true
            return item
        })

        // 重新判定受影响的movable
        indictor.reduce((item1,item2,item2Index) => {
            //  可移动的定义
            //  在首尾，除了向左在第一个和向右在最后一个都可移动
            //  在中间，比在当前方向上的相邻元素值大即为可移动
            if(item1.direction === true){ // 向右
                item1.movable = item1.value > item2.value
            }

            if(item2.direction === false){ // 向左
                item2.movable = item2.value > item1.value
            }
            return item2
        })

        yield indictor.map(item => item.value)
    }
}

export function* permutations(baseList){
    if(!Array.isArray(baseList)){
        return
    }

    const permuGen = permutationsDict(baseList.length)
    while (true){
        const {value, done} = permuGen.next()
        if(!done){
            yield value.map(item => baseList[item])
        }else{
            break
        }
    }
}

```



### 算法四 位置交换法

- 从Python标准库中学来的
- 记录每一位可变的次数，通过类似进位的方式，当前位遍历完成后，重置当前位的可变更次数，并开始变更左边一位

```javascript
function* permutationsDict(baseListLength,subLength){
    let indicator = [...Array(baseListLength).keys()]  // 初始指示器 [0,1,2...subLength-1]
    let helper = [...Array(subLength).keys()].map(item => baseListLength - item) // 记录从左到右每一位可变的次数
    yield indicator.slice(0,subLength)
    while (true){
        let i = subLength
        for(;i--;){
            helper[i]--
            if(helper[i] === 0){
                // 重置当前位的变更过程到初始状态
                const changed = [...indicator.slice(i+1),...indicator.slice(i,i+1)]
                indicator.splice(i,changed.length,...changed)
                helper[i] = baseListLength - i
            }else{
                let j = helper[i];
                [indicator[i],indicator[baseListLength - j]] = [indicator[baseListLength - j], indicator[i]]
                yield indicator.slice(0,subLength)
                break
            }
        }

        if(i < 0){ // 循环透出，表示从右到左的所有位的可变更次数都为0，也就是都过了一遍了，即结束
            break
        }
    }
}

export function* permutationsR(baseList,subLength = baseList.length){
    if(!Array.isArray(baseList)){
        return
    }

    if(baseList.length < subLength){
        return;
    }

    const permuGen = permutationsDict(baseList.length,subLength)
    while (true){
        const {value ,done} = permuGen.next()
        if(done){
            break
        }else{
           yield value.map(item => baseList[item])
        }

    }
}

```





### Python 简单实现(利用标准库)

`itertools`参考文档[中文](https://docs.python.org/zh-cn/3/library/itertools.html), [英文](https://docs.python.org/3/library/itertools.html) 

 [python代码地址](https://docs.python.org/3/library/itertools.html)

```python
from itertools import permutations
import string
setA = range(1,7)  # [1,2,3,4,5,6]
setB = list(string.ascii_uppercase[0:6]) # ['A','B','C','D','E','F']

print(list(permutations(setA)))
print(list(permutations(setB)))
```





## 生成包含R个元素的组合或者生成R子集的所有可能排列

```
需求: 生成集合A和集合B的3元子集的所有可能排列
```

### 算法一 直接子集全排列

- 利用前面的递归插入法可以生成一个集合的全排列
- 将所有的R子集应用上面的过程
- 再合并结果就得到了所有的R子集的全排列

```javascript
import {combinations} from "./combinations-2.mjs";
import {permutations} from "./permutations.mjs";

export function permutationsR(baseList, subSetLength){
    const combinationsR = combinations(baseList,subSetLength)
    return combinationsR.reduce((result,item) => {
        result.push(...permutations(item))
        return result
    },[])
}
```



### 算法二 位置交换法

- 从Python 标准库里面学来的

```javascript
function* permutationsDict(baseListLength,subLength){
    let indicator = [...Array(baseListLength).keys()]  // 初始指示器 [0,1,2...subLength-1]
    let helper = [...Array(subLength).keys()].map(item => baseListLength - item) // 记录从左到右每一位可变的次数
    yield indicator.slice(0,subLength)
    while (true){
        let i = subLength
        for(;i--;){
            helper[i]--
            if(helper[i] === 0){
                // 重置当前位的变更过程到初始状态
                const changed = [...indicator.slice(i+1),...indicator.slice(i,i+1)]
                indicator.splice(i,changed.length,...changed)
                helper[i] = baseListLength - i
            }else{
                let j = helper[i];
                [indicator[i],indicator[baseListLength - j]] = [indicator[baseListLength - j], indicator[i]]
                yield indicator.slice(0,subLength)
                break
            }
        }

        if(i < 0){ // 循环透出，表示从右到左的所有位的可变更次数都为0，也就是都过了一遍了，即结束
            break
        }
    }
}

export function* permutationsR(baseList,subLength = baseList.length){
    if(!Array.isArray(baseList)){
        return
    }

    if(baseList.length < subLength){
        return;
    }

    const permuGen = permutationsDict(baseList.length,subLength)
    while (true){
        const {value ,done} = permuGen.next()
        if(done){
            break
        }else{
           yield value.map(item => baseList[item])
        }

    }
}
```





### Python 简单实现(利用标准库)

`itertools`参考文档[中文](https://docs.python.org/zh-cn/3/library/itertools.html), [英文](https://docs.python.org/3/library/itertools.html) 

 [python代码地址](https://docs.python.org/3/library/itertools.html)

```python
from itertools import permutations
import string
setA = range(1,7)  # [1,2,3,4,5,6]
setB = list(string.ascii_uppercase[0:6]) # ['A','B','C','D','E','F']

print(list(permutations(setA,3)))
print(list(permutations(setB,3)))
```





## 生成幂集排列

```
需求：生成集合A和集合B的幂集的所有可能的排列
```



### 算法一 循环收集整合法

这里恰好能用上在《组合数学》中提到的插入法，因为这里的方法是依赖于递归的思路，`n+1`元排列是基于`n`元排列来生成的，而如果是生成`r`排列的话，我们还不能用这样的方法，因为这样生成了多余的结果，还需要剔除，自然不是我们算法想要实现的结果。这里刚好需要所有的结果，何乐不为呢？算法思想也很简单：

- 可以直接生成一个`n`元排列，比如`0`元排列，`1`元排列

- 对于`n+1`元排列即是在`n`元排列的所有可能位置插入所有可能的元素

  怎么理解呢，`0`元排列就是一个空集,有一个可能插入的位置来形成排列，所以`1`元排列就是在`0`元排列的那唯一一个可能插入的位置插入各个可能的元素，也就得到的所有`1`元排列(也是只有一个元素的子集)，而对于`1`元排列来说，有两个可能插入的位置(元素左边和右边)，这样除去自身外的所有可能元素分别插入左边和右边即可得到`2`元排列，以此类推

- 那么我们可以搜集每一步的结果的并集即是幂集的全排列

```javascript
export function permutationsAll(baseList){
    if(!Array.isArray(baseList)){
        return
    }
    // 先放入空集
    const resultList = [[]]
    // 一元子集
    let baseSubSet = baseList.map(item => {
        return [item]
    })
    resultList.push(...baseSubSet)

    for(let i = 0, len = baseList.length; i < len; i++){
        baseSubSet = baseSubSet.reduce((result, subsetItem,index) => {
            const candidates = baseList.filter((baseItem, baseIndex) => {
                // 当前集合中元素的最大元素的索引之后的元素
                const maxIndex = subsetItem.reduce((result,item) => {
                    const indexInBase = baseList.indexOf(item);
                    return indexInBase > result?indexInBase: result
                },0)
                return baseIndex > maxIndex
            })

            if(!candidates.length){
                return  result
            }
            // 插入位置为n + 1, 一个元素可以有两个插入位置（前后）
            for(let i = subsetItem.length + 1; i--;){
                candidates.forEach(candiItem => {
                    let tmpItem = [...subsetItem]
                    tmpItem.splice(i,0,candiItem)
                    result.push(tmpItem)
                })
            }
            return result
        },[])
        resultList.push(...baseSubSet)
    }
    return resultList
}
```



### 算法二 直接幂集全排列

- 先利用生成幂集的方法生成幂集
- 再对每一个幂集生成对应的全排列
- 整个所有的全排列得到幂集全排列



```javascript
import {combinationsAll} from "./combinations-all.mjs";
import {permutations} from "./permutations.mjs";

export function permutationsAll(baseList){
    const allCombinations = combinationsAll(baseList)
    return allCombinations.reduce((result,item) => {
        result.push(...permutations(item))
        return result
    },[])
}

```





### Python 简单实现(利用标准库)

`itertools`参考文档[中文](https://docs.python.org/zh-cn/3/library/itertools.html), [英文](https://docs.python.org/3/library/itertools.html) 

 [python代码地址](https://docs.python.org/3/library/itertools.html)

```python
from itertools import permutations
import string
setA = range(1,7)  # [1,2,3,4,5,6]
setB = list(string.ascii_uppercase[0:6]) # ['A','B','C','D','E','F']
resultA = []
resultB = []

for i in range(len(setA) + 1):
    resultA += permutations(setA,i)
print(resultA)

for i in range(len(setB) + 1):
    resultB += permutations(setB,i)
print(resultB)
```



# 举一反三

## 虽不为我所有，但能为我所用，不会写咋玩？

计算机本身就是人类发明的解决计算问题的工具，我们不需要都去搞CPU，不需要都去搞操作系统，我们也不需要都去搞浏览器（前端最常用的工具，我敢说没有之一么），同样的，算法，数据结构，不也是工具么？不知道什么时候，算法给人一种高精尖不可获取的感觉了，是受各大厂面试的影响还是怎么着我不知道，但我觉得可能有点过了。作为开发人员，尤其是前端开发人员来说，这么多年来，真没多少地方真的要特意的去用个什么算法， 如果对一个`95%`的时间都用不上的工具上纲上线，是不是有点过了？

但不管什么法，不外乎解决问题的思路，我们的目标不是什么算法，而是解决问题，而算法只是工具或手段。这样一来，我们的焦点重新回到解决问题上来。如果只是为了解决问题，那么用最高效便捷的方法解决了就ok，比如用上面的Python实现。如果项目里面确实要用，那么当然可以自己写，也可以通过转换、引用等的方式来搞。引用的这里不必说了`npm i` ，这里咱们试试转换。既然有标准库里面的内容，自然是值得借鉴的，当然我这里侧重的不是说它们是权威是大牛写的，而是说既然是标准库一定是经过时间和实践检验的，这才是我们想要的。所以权威和大牛不是重点（人类历史上权威和大牛翻车的已然不在少数），经得住时间和实践验证更可靠。

### 小目标 把Python标准库里面的排列组合用到咱们JavaScript环境里面来

#### 找工具

- 一开始我想到的是可不可以用`WebAssembly` ,简单验证了下，一方面兼容性不够好，另一方面工具也不那么好使

- 然后就找到了直接将Python转JavaScript的工具[Transcrypt](https://github.com/QQuick/Transcrypt), 也简单容易上手，当然可能遇到安装包的时候的问题，比如Python版本的问题，这也算Python的基础问题，搞了就完了。

  简单安装或者[照着文档操作](http://www.transcrypt.org/docs/html/installation_use.html#installation)

  ```shell
  pip3 install transcrypt 
  ```

  网络不好也可以直接[下载.whl 文件](https://pypi.org/project/Transcrypt/)本地安装

  ```shell
  pip3 install Transcrypt-3.9.0-py2.py3-none-any.whl
  ```

  

#### 写点Python代码

后面发现这里面的包装可以不用，但作为转换过程的引子或者媒介，辅助把`itertools` 撬出来。

```python
# combinations-permutations-all.py

from itertools import combinations,permutations

class CombPerm:
    @staticmethod
    def combinations(*args):
        return list(combinations(*args))
    @staticmethod
    def permutations(*args):
        return list(permutations(*args))

if __name__ == '__main__':
    print(CombPerm.combinations([1,2,3,4,5,6],3))
    print(CombPerm.permutations([1,2,3,4,5,6],3))

```



#### 将Python代码转为JavaScript

```
transcrypt combinations-permutations-all.py
```

就会在当前目录下生成对应的JavaScript代码，不过是ES标准的，所以我全部改成了`.mjs`并替换了相关引用的地方

```
├── __target__
|  ├── combinations-permutations-all.mjs
|  ├── combinations-permutations-all.project
|  ├── itertools.mjs
|  ├── org.transcrypt.__runtime__.mjs

```

这里就生成了对应的库文件和我们代码对应的文件，其实只用库文件就OK了

#### 使用新生成的JavaScript版本的库

把代码挪到我们想要的地方去，这样就可以直接在JavaScript环境使用Python `itertools`标准库了

```
├── code-from-python
|  ├── itertools.mjs
|  ├── org.transcrypt.__runtime__.mjs
|  └── test.mjs
```

```javascript
import {permutations,combinations} from "./itertools.mjs";

const combinationsResult = combinations([1,2,3,4,5,6],3)
const permutationsResult = permutations([1,2,3,4,5,6],3)

// 发现新库的数组多了一个__class__字段，不影响使用，为了不影响打印，这里删掉了
combinationsResult.forEach(combiItem => {
    delete combiItem.__class__
    return combiItem
})

permutationsResult.forEach(permuItem => {
    delete permuItem.__class__
    return permuItem
})

console.log(combinationsResult)
console.log(permutationsResult)

```

这样我们就拥有了一套靠谱的经过验证的排列组合的第三方库了。

### 各种第三方库

- 基本能想得到的都能找得到
- 实在找不到，就写一个，让别人能找得到吧



## 你写的好，我看见了，我写的也好了

### 什么是好？

对于一个算法来说，一说到评价标准，我们首先冒出来的就是什么`时间复杂度`,`空间复杂度` 对吧，这个也是经常考试的内容，这里咱们不想细说，在另一篇关于`Chrome数组排序`的里面再说，当然`时空复杂度`其实对应了一个很重要的标准，那就是`实用性`。上面我们也写了不少的算法了，都可以作为解决问题的思路，但某些算法或者思路，看起来就像一个玩具，只能用来解题玩玩，一到具体的使用环境就歇菜了，这也就是`时空复杂度`的要求。当然除了这俩，还有我们默认都具备的特性那就是`正确性`,还有一个类似的被默认但有可能出岔子的特性是`稳定性`排序的时候就有用了，`Chrome`数组排序，多年都存在这个问题，[排序不稳定](https://bugs.chromium.org/p/v8/issues/detail?id=90)，问题从08年提出，到18年解决，有种十年如一日的感觉。除了这些还有吗？思想很精妙，我们普通人就是没法懂，咋办？思想很简单，写出来的代码我们普通人看不懂，咋办？所有评价算法的标准或者说角度至少可以有如下的内容：

- 正确性
- 稳定性/健壮性
- 时间复杂度
- 空间复杂度
- 易读性/易维护性

作为算法或代码是这样的，我们作为打工仔，何尝不是被这样的标准评价的呢？能干活，干活靠谱，考勤要好，干活要快，工资要低，不出岔子，容易相处/服从管理，哈哈，好像突然之间打开了代码和生活之间的通道，让他们融为一体，代码就是生活，生活就是代码。



### 看看Python标准库排列组合咋写的

#### 找呀找呀找源码

##### `itertools`文档中的说明(Python版本)

- [combinations](https://docs.python.org/3/library/itertools.html#itertools.combinations)
- [permutations](https://docs.python.org/3/library/itertools.html#itertools.permutations)



##### `itertools` 在Python源码中的实现(C语言版本)

- [combinations](https://github.com/python/cpython/blob/main/Modules/itertoolsmodule.c#L2598)
- [permutations](https://github.com/python/cpython/blob/main/Modules/itertoolsmodule.c#L3262)



##### `itertools`的JavaScript实现

- [itertools.mjs](https://github.com/rawbin-/combination-permutation-js/blob/main/src/code-from-python/itertools.mjs)
- 就是我们上面搞出来的版本



#### 找到源码开看啦

`C版本`，`Python版本`，`JavaScript版本`各有韵味，咱们就找软的(柿子)先捏一捏，倒过来从后到前，由浅入深吧。C语言我现在是感觉是不会了。`JavaScript版本`的对咱们前端来说看着就有亲近感，但我还想看看`Python版本的`,因为简单扫了一眼，Python版本的使用了现在各大语言中都大行其道的`生成器`这个可以在空间复杂度上面有思路的优化，我们要自己写一个版本的话，自然要用上这样的特性（比如通过前序生成后继的时候，空间复杂度可以直接降下来），同时也可以看看有没有办法直接用上一个生成下一个，就像字典序生成的那个一样，当然这是相对理想的YY，也属于特殊场景特殊情况。

说实话我也没啥看源码的方法论，都躺那了，睁开眼睛看呗，不然怎么看，闭着眼睛肯定不行。

找你最喜欢的代码编辑器，把代码敲敲打打，分段分块格式化，先把状态切换过来。

##### `combinations`JavaScript源码

```javascript
export var combinations = function (iterable, r) {
    let tail = list(iterable);

    function recurse(tail, molecule, rNext) {
        for (let index = 0; index < len(tail) - rNext; index++) {
            let newMolecule = molecule.concat(tail.slice(index, index + 1));
            if (rNext)
                recurse(tail.slice(index + 1), newMolecule, rNext - 1);
            else
                result.append(tuple(newMolecule))
        }
    }

    let result = [];
    recurse(tail, tail.slice(0, 0), r - 1);
    return list(result)
};
```

##### `combinations`JavaScript源码解析

- 做了数据转换（这个因为是Python转过来的缘故），所以可以当做没有
- 主体就是定义了一个递归函数，然后给了个容器，在递归的过程中把正确的内容塞容器里面
- 再看这个递归函数
  - 传入了三个参数，第一个是元素列表，第二个是当前临时组合，第三个是还需要迭代的次数（或者说还需要选几个）
  - 执行的过程是
    - 先选一个
    - 如果不够（迭代次数不为0），那就从剩下的里面把剩下的选上（递归）
    - 够了的话就存一个
- 这里用循环来控制的最终结尾，而我们前面实现的过程中，没关注是否够数，只关注了是否只剩一个元素

##### `permutations` JavaScript源码

```javascript
export var product = function () {
    let args = [].slice.apply(arguments);
    if (args.length && args[args.length - 1].hasOwnProperty("__kwargtrans__"))
        var repeat = args.pop()["repeat"];
    else
        var repeat = 1;

    let oldMolecules = [tuple([])];
    for (let i = 0; i < repeat; i++) 
    for (let arg of args) {
        let newMolecules = [];
        for (let oldMolecule of oldMolecules)
            for (let atom of arg)
                newMolecules.append(tuple(oldMolecule.concat(atom)));
        oldMolecules = newMolecules
    }
    return list(oldMolecules)
};
export var permutations = function (iterable, r) {
    if (r == undefined) try {
        r = len(iterable)
    } catch (exception) {
        r = len(list(iterable))
    }

    let aProduct = product(iterable, __kwargtrans__({repeat: r}));

    let result = [];
    for (let molecule of aProduct)
        if (len(set(molecule)) == r)
            result.append(molecule);
    return list(result)
};
```

##### `permutations` Javascript 源码解析

- 前后格式转化不用说了
- 先调用了一个`product` 算出了一个接过来
- 然后把每一个结果去重，把没有重复的（去重后没元素丢失的）加入最终结果
- 再来看看这个`product`笛卡尔积，如果知道笛卡尔积的概念，那就什么都知道了。如果不知道可以调试看看它的结果也就知道了。还是想知道就看看源码，看看这个四重循环都干了啥。
  - 这里其实用笛卡尔积做了可重复元素的全排列，也就是第一个元素从集合A中取，第二个还从A中取，第r个还从A中取，最后得到r的全排列，但这里面有重复元素，所以要去重，去掉有重复的
  - 重复遍数用一个循环没问题；笛卡尔积的参数是多个集合，所以循环集合没有问题；笛卡尔积是要在原有的结果上加内容，所以循环原有结果没问题；最后一个循环就是从每一个集合里面取每一个元素。类似于咱们前面的`baseXXX = baseXXX.reduct`
- 这个过程是搞多了，然后再去除多余的，类似于咱们的`全集筛选法`,只是利用了已有工具快速完成，但算法思路本身效率一般。



##### `combination` Python源码

```python
def combinations(iterable, r):
 # combinations('ABCD', 2) --> AB AC AD BC BD CD
 # combinations(range(4), 3) --> 012 013 023 123
 pool = tuple(iterable)
 n = len(pool)
 if r > n:
  return
 indices = list(range(r))
 yield tuple(pool[i] for i in indices)
 while True:
  for i in reversed(range(r)):
   if indices[i] != i + n - r:
    break
  else:
   return
  indices[i] += 1
  for j in range(i+1, r):
   indices[j] = indices[j-1] + 1
  yield tuple(pool[i] for i in indices)

```



##### `combination` Python源码解析

- 扫一眼可以看到，使用了咱们在【字典序生成法】里面用到了的数据映射的方法，只需要变化位置数组，需要的数据按照标志位从对应源数据中对应位置取就行，这样就可以不用关心数据类型和数据结构了，都可以按整形数处理
- 同时这里是根据一定的规律生成前序和后继的结果，知道前一个就知道后一个，这样就只计算和存储一个
- 这里非常巧妙的利用了for-else 穿透条件结束算法
- 算法解析
  - 比如[1,2,3,4,5,6]中选择，三个数的组合，那么第一个组合是[1,2,3],最后一个组合是[4,5,6]
  - 同时根据我们的思维逻辑，[1,2,3]的下一个是[1,2,4]（这里面默认包含了字典序的，这样不容易出错）这里变化的只是第 `i` 个元素
  - 而到了[1,2,6]的下一个是[1,3,4]，这时第一个变化的数之后的每一个数都会跟着变，二期是等差递增的，所以就有了`j`循环
  - 剩下的就是根据下标生成对应的产出，也就是`yield`语句对应的内容

- 既然都学了，也可以自己造一个了，参考前面的【字典序生成法-生成器版-参考答案】，可以作为一个参考答案



##### `permutation` Python源码

```python
def permutations(iterable, r=None):
 # permutations('ABCD', 2) --> AB AC AD BA BC BD CA CB CD DA DB DC
 # permutations(range(3)) --> 012 021 102 120 201 210
 pool = tuple(iterable)
 n = len(pool)
 r = n if r is None else r
 if r > n:
  return
 indices = list(range(n))
 cycles = list(range(n, n-r, -1))
 yield tuple(pool[i] for i in indices[:r])
 while n:
  for i in reversed(range(r)):
   cycles[i] -= 1
   if cycles[i] == 0:
    indices[i:] = indices[i+1:] + indices[i:i+1]
    cycles[i] = n - i
   else:
    j = cycles[i]
    indices[i], indices[-j] = indices[-j], indices[i]
    yield tuple(pool[i] for i in indices[:r])
    break
  else:
   return

```

##### `permutation` Python源码解析

- 扫一眼可以看到，使用了咱们在【字典序生成法】里面用到了的数据映射的方法，只需要变化位置数组，需要的数据按照标志位从对应源数据中对应位置取就行，这样就可以不用关心数据类型和数据结构了，都可以按整形数处理
- 这里可以看出做了两件事，一件是全排列，一件是`r`子集的全排列，可以直接先让`r = n` 得到一个集合的全排列的算法
- 算法解析
  - 看起来是交换的思路，这里运用了一个辅助结构来记录当前每一位可变的次数，从左到右排列的每一位可选的次数一次递减，刚好是一个倒序的值
  - 从右边起第一个可变的开始，依次替换可选的值，直到当前位置的可替换值都替换完成，这时候需要类似进位那样变更左边一位了，变更左边一位的同时，右边的可变位数会重置；重置的过程也会重置整个序列，回到本轮开始的状态




#### 看完源码学到啦

有了前面的体验，发现我们原来写的算法还有提升空间，相应的问题也解决了，优化版本的代码还是写到前面吧。

同时也发现，标准库的代码也不是都那么的高精尖，也有普通人，这样就放心了，要去对开源库做贡献，现在就去吧。



## 排列组合还有哪些可以折腾的？

如果元素可重复，那么排列组合是一个什么样的情况，是不是笛卡尔积就可以作为一种全排列的思路了？

### 可重复元素组合

#### 算法一 递归加入法

算组合的时候的直接思路

- 先从n个中选一个
- 再从n个中选`r-1`个
- 将先选的那一个和每一个`r-1`的组合组合起来，就是`r`子集，以此类推
- 重复和不重复元素算法之间差别只是选择`r-1` 子集的范围

```javascript
export function combinationsRepeat(baseList,subSetLength){
    if(!Array.isArray(baseList)){
        return
    }

    if(subSetLength === 1){
        return baseList.map(baseItem => [baseItem])
    }

    const resultList = []
    baseList.forEach((baseItem,baseIndex) => {
        const lessCombi = combinationsRepeat(baseList.slice(baseIndex),subSetLength - 1)

        lessCombi.forEach((lessCombiItem,lessCombiIndex) => {
            resultList.push([baseItem,...lessCombiItem])
        })
    })

    return  resultList
}

```



### 可重复元素全排列

#### 算法一 笛卡尔积

- `r`个相同集合，按顺序每个取一个，有多少种取法

```javascript
function production(argList, repeat = 1){
    let base = argList.map(item => [item])
    for (let i = 0; i < repeat - 1; i++){
        base = argList.reduce((result,argItem) => {
            base.forEach(baseItem => {
                result.push([...baseItem,argItem])
            })
            return result
        },[])
    }
    return base
}

export function permutationsRepeat(baseList){
    return production(baseList,baseList.length)
}
```



#### 算法二 递归排列法

- 将每一个元素与`r-1`个元素的全排列进行排列，形成新的`r`排列，以此类推
- 有元素重复，不能用插入法，也不能直接用数组元素个数来判断，所以需要加个长度来结束递归

```javascript
export function permutationsRepeat(baseList, subPermuLength = baseList.length){
    if(!Array.isArray(baseList)){
        return
    }

    if(subPermuLength === 1){
        return baseList.map(baseItem => [baseItem])
    }

    const lessPermutation = permutationsRepeat(baseList,subPermuLength - 1)

    const resultList = []
    // 因为有重复，所以这里不能用插入，而要用拼接
    baseList.forEach(baseItem => {
        lessPermutation.forEach(lessItem => {
            resultList.push([baseItem,...lessItem])
        })
    })
    return resultList
}
```



## 简单总结

### 排列组合的几类需求

- 求一个集合的`r` 子集，也就是从`n`个里面选`r`个的选法，这个是组合
- 求一个集合的所有子集，也就是幂集，是`r`从`0`到`n`的`r`子集的并集，这也是组合
- 求一个集合的`r`子集的全排列，也就是从`n`个里面选`r`个的每一个选法的全排列的并集，这当然是排列
-  求一个集合的所有子集的全排列，也就是幂集的全排列，是`r`从`0`到`n`的`r`子集的全排列并集，这当然也是排列
- 对于所有的排列组合，元素可重复和不可重复，是两种不同的场景，所以上面的场景可翻倍



### 实现/求解方式

- 从上面的例子可以看出来，不同的场景都可以有不同的处理方式，也有些方式感觉就像是为某些场景量身定制的一样
- 可以有几个原子实现，其他实现可以基于此来实现
  - 求集合的`r`子集的方法，用此即可求幂集
  - 求集合的`r`排列的方法，用此即可求幂集的全排列
  - 求集合的全排列的方法，用此配合求`r`子集的方法，可以求`r`全排列，也可以求幂集全排列
- 可以利用生成器的思想，做到随用随取
- 具体问题具体分析