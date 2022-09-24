# 工具
## Chrome Performance

## why-did-you-update 插件

## React Developer Tools

# React自身性能优化
## 优化方式
### 使用高效的diff算法
### 进行batch操作
### 摒弃脏检测更新方式
### 使用事件代理将事件绑定到document上
### 包装合成事件利用池化的缓存
### recomciler 和 Fiber
### 静态元素提升
### 执行时期删除propTypes
### 执行时期删除内联函数
### 使用无状态组件
### prepack优化处理

## 基础假设
### 跨层级DOM节点移动忽略不计
## 相同组件相似树，不同组件树不相同
- 只比较统一层级的树节点信息


# 编程方式React性能优化
## shouldComponentUpdate
## 缓存组件
- lodash memorize
- recompose onlyUpdateForKeys
## 避免inline-function
## 使用PureComponent