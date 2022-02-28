# 通用处理方式
## 文档
- 统一用markdown 表达，分为中英文两份文档入口
- 具体的内容只有一份中文demo文档
## 目录
-  style 样式
- demo 
## 代码
- 样式使用了统一的变量，比如`ant-prefix`，直接引用，没有使用CSS Modules
- 

## 测试
- 使用jest 测试,`__tests__`目录
- 子目录有`__snapshots__`

## 响应式配置
## 断点
```
xs: '(max-width: 575px)',  
sm: '(min-width: 576px)',  
md: '(min-width: 768px)',  
lg: '(min-width: 992px)',  
xl: '(min-width: 1200px)',  
xxl: '(min-width: 1600px)',
```


# 组件实现逻辑
## affix 图钉
- 暴露属性，设置固定的位置
- 监听resize 重新计算位置

## alert 警告
- 分为图标、消息、描述、操作几部分
- 定义了组件的ErrorBoundary，处理异常

## anchor 跳转到指定位置
- 子组件 `Link`
- 定义了自己的context共享数据，管理自己下面的，
- 监听滚动事件，跳转到不同的Link

## auto-complete 自动完成
- 使用了`Select`组件

## avatar 头像
- 子组件 `Group` 使用了`Popover`
- 使用 `img`标签属性来定义，配置`crossorigin` 和 `onError` 
- 监听`resize`事件

## back-top 回顶部
- 使用`div`实现，导出时包装了`React.memo`
- 监听滚动事件，加动效

## badge 角标数字
- 子组件`Ribbon`
- 处理数字、单位、文字等情况

## breadcrumb 面包屑
- 子组件 `BredcrumbProps` `BreadcrumbItemProps`
- 如果空间不够，显示为`Dropdown`

## button 按钮
- 子组件 `ButttonGroup`
- 使用 button 来实现，外层用`wave`组件包装了各种动效属性

## calendar 日历
- 基于moment来处理，处理国际化和时区
- 选年月的组件单独抽离
- 使用elment.contains 判断是否在当前dom区域
- 使用isClickOutside来判断是否需要隐藏当前区域
- 外部区域点击的blur 需要特殊处理

## card 卡片
- 分四个区块，head，cover，body，action
- 通过设置width百分比来均分


## carousel 轮播区域
- 基于[ant-design](https://github.com/ant-design)/**[react-slick](https://github.com/ant-design/react-slick)** 实现

## cascader 级联选择器
- 基于`rc-cascader`实现

## checkbox 复选框
- 基于`rc-checkbox`实现

## collapse 折叠框
- 基于`rc-collapse` 和 `rc-motion`实现

## comment 评论区块
- 处理了 rtl 方向

## config-provider 组件提供全局统一配置
- 共享context 实现

## date-picker 时间选择器
- 基于`rc-picker` 包装实现


## description 多个只读字段展示
- 使用table 实现

## divider 分隔组件
- 处理 rtl 

## drawer 抽屉
- mask配置
```css
.ant-drawer-mask {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.45);
  opacity: 0;
  filter: alpha(opacity=45);
  transition: opacity 0.3s linear, height 0s ease 0.3s;
  pointer-events: none;
}
```

## dropdown 下拉
- 基于`rc-dropdown`实现

## empty 占位图
- 处理 rtl 
- 图片，描述以及其他子项

## form 表单
- 基于`rc-field-form`实现
- 共享 context
- FormItem，FormItemInput，FormItemLabel，FormList

## grid 网格
- 定义`['xs', 'sm', 'md', 'lg', 'xl', 'xxl']` 响应式内容

## icon 空图标

## image 可预览图片
- 基于`rc-image`实现

## input 输入框
- input各种属性封装
- 包装 Group,Search,TextArea,Password

## input-number 数字输入
- 基于`rc-input-number`实现

## layout 布局
- 使用html5标签实现Header，Footer，Content，Sider
- 做响应式断点，使用`matchMedia`来处理断点，触发事件，重新渲染
- typescript 是非null 非undefined的类型断言

## list 列表
- 支持响应式配置
- 定义header，可loading content，footer，extra几个区域
- 共享context

## mention 提及@
- 基于`rc-mention`实现

## menu 菜单
- 基于 `rc-menu`实现


## message  操作反馈
- 基于`rc-notifaction` 实现


## modal 弹窗
- 基于`rc-dialog`实现

## notification 全局通知信息
- 基于`rc-notifaction` 实现

##  page-header 页面头部
- 面包屑，标题，子内容，附属信息等


## pagination 分页
- 基于 `rc-pagination`实现

## pop-confirm 气泡确认框
- 基于 `Tooltip`实现

## popover 鼠标移动浮层
- 基于`Tooltip`实现

##  progress 进度条
- 基于`rc-progress`实现 Circle
- Line 用百分比宽度实现
- Step 用色块个数来实现

## radio 单选框
- 基于`rc-checkbox`实现

## rate 评分
- 基于`rc-rate`实现

## result 结果页
- 用svg图实现的

## select 下拉选择
- 基于`rc-select`实现

## skeleton 骨架屏
- 实现了Avatar，Button，Element，Image，Input，Paragraph，Title

## slider 滑块
- 基于`rc-slider`实现

## space 水平组件间间距
- 使用flex 实现

## spin loading状态
- 使用`lodash/debounce` 节流
- 使用关键帧和元素克隆和旋转实现

## statistic 统计展示
- 处理数字类型及异常
- 处理千分位`int = int.replace(/\B(?=(\d{3})+(?!\d))/g, groupSeparator);`
- 处理定时刷新的数据，使用了`forceUpdate` 和 setInternal

## steps 分步骤
- 基于`rc-steps`实现

## switch 开关
- 基于`rc-switch`实现

## table 表格
- 基于`rc-table`实现

## tabs 标签页
- 基于`rc-tabs` 实现

## tag 标签
- 通过不同颜色的标和背景色来实现

## time-picker 时间选择器
-  基于`date-picker`实现

## timeline 时间线
- 使用ul 实现每个节点，在li中为每个节点加个`absolute`的边线

## tooltip 气泡提示框
- 使用`rc-tooltip`实现

## transfer 穿梭框
- 使用两个List组件和一个Operation组件实现
- List组件又分为ListBody和ListItem

## tree 树形结构
- 使用`rc-tree`实现

## tree-select 树形选择
- 使用`rc-tree-select`实现

## typography 文本格式化
- 使用了`copy-to-clipboard`
- 实现了Base，Ellipsis，EllipsisTooltip
- 基于Base实现了Title,Text,Paragraph,Link
- 基于Textarea实现了Editable

## upload 上传
- 基于`rc-upload`实现

