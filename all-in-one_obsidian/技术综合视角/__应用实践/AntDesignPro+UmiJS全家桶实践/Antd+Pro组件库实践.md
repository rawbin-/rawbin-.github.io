# 简介
## Ant Design
是一套设计风格、设计规范，加上一整套关联的React组件库统称，也被社区实现到Vue，提供区块级别的组件。
-  现代浏览器和 IE11（需要 [polyfills](https://ant.design/docs/react/getting-started-cn#%E5%85%BC%E5%AE%B9%E6%80%A7)）。  
-   支持服务端渲染。
-   [Electron](https://www.electronjs.org/)

## ProComponents
是基于Ant Design开发的模板组件，提供了更高级别的抽象支持，可以显著提高后台界面CURD的开发效率，提供页面级的组件。

## And Design Pro
是基于Ant Design和ProComponents，利用UmiJS而提供的一整套企业级后台系统的开发脚手架，开箱即用。同时提供了UmiJS UI，来实现低代码地操作。

## 社区评选
### [社区精选组件](https://ant.design/docs/react/recommendation-cn)


# Ant Design 基础组件库 `antd` 
## 基础组件
### Button 按钮
- 大、中（默认）、小尺寸
- 可设置图标和loading状态

### Icon 图标
- @ant-design/icons 图标组件库

### Typography 排版
#### Typography.Text 文本排版
#### Typography.Title 标题样式
#### Typography.Paragraph  段落样式

## 布局组件
### Divider 分割线
### Grid 栅格
- 24栅格系统
- 基于行列来定义信息区块
- 基于Flex布局
- 可以设置间隔，支持响应式设置
#### Row
#### Col
### Layout 布局
- 支持响应式布局
- 可嵌套
#### Layout.Sider
#### Layout.Header
#### Layout.Footer
#### Layout.Content
### Space 间距
- 行内元素水平间距

## 导航组件
### Affix 固钉
- 页面内容长，需要滚动，这部分的操作始终要展现的时候，比如编辑按钮，菜单收起等

###  Breadcrumb 面包屑
-  显示当前页面系统层级结构，并能向上返回
- 当系统拥有超过两级以上的层级结构时，或者要告知用户当前位置或者向上导航时
- 可以配合路由使用，定义显示的名称

#### Breadcrumb.Item
#### Breadcrumb.Separator

### Dropdown 下拉菜单
- 页面上操作命令过多时进行收纳
####  Dropdown.Button

### Menu 导航菜单
- 使用ul，只直接支持li和支持脚本的元素script,template

#### Menu.Item
#### Menu.SubMenu
#### Menu.ItemGroup
#### Menu.Divider

### PageHeader 页头
- 位于页容器中，起内容概览和引导页级操作的作用，包括由面包屑、标题，页面内容简介，页面级操作等页面级导航组成
- 当需要用户快速理解当前页是什么以及方面用户使用页面功能时使用

### Pagination 分页

### Steps 步骤条
#### Steps.Step

## 数据录入
### AutoComplete 自动完成

### Cascader 级联选择

### Checkbox 多选框
#### Checkbox.Group

### DatePicker  日期选择器
- year，quarter，month，week

#### RangePicker
#### TimePicker

### Form 表单
- 输入的数据类型可以进行校验
#### Form.Item
- 用户数据双向绑定、校验、布局等
#### Form.List
- 为字段提供数据化管理
- 下面的字段不应该单独设置`initialValue`应该统一设置
#### Form.ErrorList
#### Form.Provider
- 提供表单间联动
#### 接口定义
##### FieldData
- errors
- name
- touched
- validating
- value
##### Rule
- defaultField
- enum
- fields
- len
- max
- message
- min
- pattern
- required
- transform
- type
- validateTrigger
- validator
- warningOnly
- whitespace
### Input 输入框
#### Input.TextArea
#### Input.Search
#### Input.Group
#### Input.Password
#### Input.Method

### InputNumber 数字输入框

###  Mentions 提及@

### Radio 单选框
#### Radio.Button
#### Radio.Group

### Rate 评分

### Select 选择器
#### Select.Option
#### Select.OptGroup

### Slider 滑动输入条

### Switch 开关

### TimePicker 时间选择框

### Transfer 穿梭框
- 双拦穿梭

### TreeSelect 树选择
#### TreeSelect.TreeNode

### Upload 上传

## 数据展示
###  Avatar 头像
#### Avatar.Group

### Badge 徽标数/角标
#### Badge.Ribbon

### Calendar 日历

###  Card 卡片
- 最基础的卡片容器，可承载文字、列表、图片、段落，常用于后台概览页面。
#### Card.Grid
#### Card.Meta

### Carousel 走马灯

### Collapse 折叠面板
#### Collapse.Panel 

### Comment 评论

### Descriptions 描述列表
- 成组展示多个字段
#### Descriptions.Item

### Empty 空状态

### Image 图片

### List 列表
- 最基础的列表展示，可承载文字、列表、图片、段落，常用于后台数据展示页面。

#### List.Item
##### List.Item.Meta

### Popover 气泡卡片
- 点击/鼠标移入元素，弹出气泡式的卡片浮层。

### Statistics 统计数值
- 当需要突出某个或某组数字是使用
- 当需要展示带描述的统计类数据时使用

#### Statistics.Countdown

### Table 表格
#### Table.Column
#### Table.ColumnGroup

### Tabs 标签页
#### Tabs.TabPane

### Tag 标签
- 进行标记和分类的小标签
#### Tag.CheckableTag

### Timeline 时间轴
#### Timeline.Item 

#### Tooltip 文字提示

### Tree 树形控件
- 文件夹、组织架构、生物分类、国家地区等等，世间万物的大多数结构都是树形结构。

#### Tree.TreeNode

## 反馈组件
###  Alert 警告提示
- 非浮层的静态展现形式，始终展现，不会自动消失，用户可以点击关闭
#### Alert.ErrorBoundary

### Drawer 抽屉

### Message 全局提示
- 顶部信息提示，弱提示

### Modal 对话框
- 需要用户处理事务，又不希望跳转页面以致打断工作流程时
- 确认询问框
#### Modal.info()
#### Modal.success()
#### Modal.error()
#### Modal.warning()
#### Modal.confirm()
#### Modal.useModal()
###  Notification 通知提醒框
- 在系统四个角显示通知提醒信息。经常用于以下情况：
	-   较为复杂的通知内容。
	-   带有交互的通知，给出用户下一步的行动点。
	-   系统主动推送。
#### Notification.success()
#### Notification.error()
#### Notification.info()
#### Notification.warning()
#### Notification.warn()
#### Notification.open()
#### Notification.close()
#### Notification.destory()

### Popconfirm 气泡确认框
- 目标元素的操作需要用户进一步的确认时，在目标元素附近弹出浮层提示，询问用户
- 更轻量的确认框

### Progress 进度条
在操作需要较长时间才能完成时，为用户显示该操作的当前进度和状态。
-   当一个操作会打断当前界面，或者需要在后台运行，且耗时可能超过 2 秒时；
-   当需要显示一个操作完成的百分比时。
- 进度条，百分比，仪表盘
#### line
#### circle
#### dashboard

### Result 结果
- 当有重要操作需告知用户处理结果，且反馈内容较为复杂时使用。

### Skeleton 骨架屏
-   网络较慢，需要长时间等待加载处理的情况下。
-   图文信息内容较多的列表/卡片中。
-   只在第一次加载数据的时候使用。
-   可以被 Spin 完全代替，但是在可用的场景下可以比 Spin 提供更好的视觉效果和用户体验。
#### Skeleton.Avatar
#### Skeleton.Title
#### Skeleton.Paragraph
#### Skeleton.Button
#### Skeleton.Input

### Spin 加载中

## 其他组件
### Anchor 锚点
- 需要展现当前页面上可供跳转的锚点链接，以及快速在锚点之间跳转。
#### Anchor.Link

### BackTop 回到顶部

### ConfigProvider 全局化配置
- 为组件提供统一的全局化配置。
- 使用React的`context`特性，外围包裹就全局生效
#### ConfigProvider.config()


# 高级抽象组件库 ProComponents 
## 布局组件
### ProLayout
- 相对灵活的标准布局
#### 自定义布局
##### headerRender 页头
##### footerRender 页脚
##### menuRender 菜单区域
##### menuHeaderRender 菜单头区域
##### menuExtraRender 菜单头和菜单之间
#### menu
#### SettingDrawer
#### PageLoading
#### GridContent
#### getMenuData
#### getPageTitle
#### Settings
#### MenuDataItem
#### Route
#### Footer
#### RouteContext

### PageContainer 页容器
- 可以自带页头和面包屑
- header  => PageHeader
- tabList => Tabs
#### FooterToolbar
#### ProBredCrumb 

### ProCard  高级卡片
- 封装Col，Row，Card，Tabs等组件，用一个组件完成各种布局
#### ProCard.Group
#### ProCard.TabPane
#### ProCard.Divider

### WaterMark 水印组件

### StatisticCard 指标卡
#### Statistics
#### Divider
#### StatisticCard.Group

### CheckCard 多选卡片
#### CheckCard.Group

## 数据录入
### ProForm 高级表单
- 分步表单，Modal 表单，Drawer 表单，查询表单，轻量筛选等多种 layout 可以覆盖大部分的使用场景
- 快速实现表单而不太关注布局时使用
#### ProForm
- 标准 Form，增加了 `onFinish` 中自动 `loading` 和 根据 `request` 自动获取默认值的功能。

#### ModalForm | DrawerForm
- 在 ProForm 的基础上增加了 `trigger` ，无需维护 `visible` 状态
#### QueryFilter
- 一般用于作为筛选表单，需要配合其他数据展示组件使用
#### LightFilter
- 一般用于作为行内内置的筛选，比如卡片操作栏和 表格操作栏。
#### StepsForm
- 分步表单，需要配置 StepForm 使用。

### ProFormFields 表单项
#### ProFormText
#### ProFormCaptcha
#### ProFormDigit
#### ProFormText.Password
#### ProFormTextArea
#### ProFormCaptcha
#### ProFormDatePicker
#### ProFormDateTimePicker
#### ProFormDateRangePicker
#### ProFormDateTimeRangePicker
#### ProFormSelect
#### ProFormTreeSelect
#### ProFormCheckbox
#### ProFormRadio.Group
#### ProFormRate
#### ProFormSlider
#### ProFormSwitch
#### ProFormUploadButton
#### ProFormUploadDragger
#### ProFormMoney

### ProFormList 数据结构化
#### ProFormFieldSet
#### ProFormDependency

### ProFormDependency 数据联动

### SchemaForm JSON表单
- SchemaForm 是根据 JSON Schema 来生成表单的工具。SchemaForm 会根据 valueType 来映射成不同的[表单项](https://procomponents.ant.design/components/schema)。
- SchemaForm 表单最重要就是 Schema 的类型定义，我们使用了与 table 的相同的表单定义，同时扩展了部分字段。
- SchemaForm 提供了与 [ProForm](https://procomponents.ant.design/components/form#proform) 相同的 API，并且增加了部分 API，以下的 SchemaForm 新增的 API。
	- layoutType
	- steps
	- columns
#### ProFormLayoutType
- Form
- ModalForm
- DrawerForm
- StepsForm|StepForm
- LightFilter
- QueryFlter
- Embed

### QueryFilter/LightFilter 筛选表单
### StepsForm 分步表单
- StepsForm 通过 Provider 来管理子表单的数据，每个字表单都是完整的数据，在 StepsForm 组合成最后的数据。同时自带了一个进度条，和管理进度条的相关 API.
#### StepsForm.StepForm

### ModalForm/DrawerForm 浮层表单
- ModalForm 和 DrawerForm 是 ProForm 的一个变体，本质上仍然是个表单。所以无法通过 `footer` 来自定义页脚,如果要定义页脚需要使用 `submitter.render` 来进行自定义。这两个表单的表现与 ProForm 相同，可以从 ProForm 直接修改而来。
- ModalForm 和 DrawerForm 都提供了 trigger 来减少 state 的使用，如果你需要使用 state 来控制可以使用 `visible` 和 `onVisibleChange` 来控制打开与关闭。

## 数据展示
### ProTable 高级表格
- ProTable 的诞生是为了解决项目中需要写很多 table 的样板代码的问题，所以在其中做了封装了很多常用的逻辑。这些封装可以简单的分类为预设行为与预设逻辑。
- 依托于 ProForm 的能力，ProForm 拥有多种形态，可以切换查询表单类型，设置变形成为一个简单的 Form 表单，执行新建等功能。
- 当你的表格需要与服务端进行交互或者需要多种单元格样式时，ProTable 是不二选择。
#### 自定义
##### 搜索表单自定义
##### 表单操作自定义
##### Toolbar 自定义
##### 表格主体自定义
#### ListToolBarProps
#### Setting
#### ListToolBarMenu
#### ListToolBarTabs
#### TableDropdown

### EditableProTable 可编辑高级表格

### DragSortTable 拖动排序表格

### ProList 高级列表
- ProList 与 antd 的 [List](https://ant.design/components/list-cn/) 相比，API 设计上更像 Table，使得可以通过配置化的方式快速定义数据项的展现形式。也使得 Table 和 List 的切换变得更加容易。**另外 ProList 基于 ProTable 实现，除了 Table 相关的 API 以外 ProList 支持大部分 ProTable 的 API**。
#### Metas
- type
- title
- subTitle
- description
- avatar
- actions
- content
- extra

### ProDescriptions 高级定义列表
#### ProDescriptions.Item

## 通用
### ProSkeleton 骨架屏
- 页面级别的骨架屏

## ProField 原子组件
- 原子信息组件，统一 ProForm、ProTable、ProList、Filter 等组件里面的字段定义。


# 图表库 @ant-design/charts 
图表包分类：
- 统计图表：`@ant-design/plots`
- 地图：`@ant-design/maps`
- 流程图：`@ant-design/flowchart`
- 关系图：`@ant-design/graphs`

## 统计图 `@ant-design/plots`
### Area
### Bar 
### Box
### Bullet
### Column
### Funnel
### Histogram
### Line
### Liquid
### Heatmap
### Pie
### Progress
### Radar
### Facet
### RingProgress
### Rose
### Chord
### Scatter
### TinyArea
### TinyColumn
### TinyLine
### Waterfall
### WordCloud
### Sunburst
### DualAxes
### Stock
### RadialBar
### Gauge
### CirclePacking
### Sankey
### Treemap
### Violin
### Venn
### MultiView
### Mix
### BidirectionalBar
### getCanvasPattern
### FUNNEL_CONVERSATION_FIELD
### G2
### flow
### measureTextWidth
### adaptors

## 流程图 `@ant-design/flowchart`
### Flowchart
### FormPanel
### FormWrapper
### EditorPanels
### NodeService
### EdgeService
### GroupService
### CanvasService
### WorkspacePanel
### XFlowNodeCommands
### XFlowGraphCommands
### usePanelContext
### FormItemWrapper
### IconStore
### XFlowAppProvider
### useXFlowApp
### XFlowEdgeCommands



## 关系图 `@ant-design/graphs`
### FlowAnalysisGraph
### RadialTreeGraph
### DecompositionTreeGraph
### OrganizationGraph
### FundFlowGraph
### OrganizationTreeGraph
### DagreGraph
### IndentedTree
### DagreFundFlowGraph
### IndentedTreeGraph
### OrganizationalGraph
### RadialGraph
### G6

## 地图 `@ant-design/maps`
### DotMap
### HeatMap
### GridMap
### HexbinMap
### PathMap
### FlowMap
### AreaMap 区域地图
### ChoroplethMap 行政区域