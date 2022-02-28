# 通用处理方式
- 包装网络请求
- 包装loading
- 各个组件深度整合，不行就新实现包装组件来整合


# 组件实现逻辑
## card => ProCard
- 实现了如下子组件
	- Actions
	- Card
	- CheckCard
	- Divider
	- Loading
	- Operation
	- Statistics
	- StatisticCard
	- TabPane


## description => ProDescription

## field  统一ProForm，ProTable，ProList，Filter等
- 内部使用
- 封装了如下组件
	- Cascader
	- Checkbox
	- Code
	- ColorPicker
	- DatePicker
	- Digit
	- DigitRange
	- FromNow
	- Image
	- IndexColumn
	- Money
	- Options
	- Password
	- Percent
	- Progress
	- Radio
	- RangePicker
	- Rate
	- Second
	- Select
	- Status
	- Switch
	- Text
	- TextArea
	- TimePicker
	- TreeSelect

## form => ProForm
- 封装了如下组件
	- Captcha
	- Cascader
	- Checkbox
	- ColorPicker
	- DatePicker
	- DateRangePicker
	- DateTimePicker
	- DateTimeRangePicker
	- Dependency
	- Digit
	- DigitRange
	- Field
	- FieldSet
	- FormItem
	- Group
	- List
	- ModalForm
	- Money
	- QueryFilter
	- Radio
	- Rate
	- SchemaForm
	- Select
	- Slider
	- StepsForm
	- Submitter
	- Switch
	- Text
	- TextArea
	- TimePicker
	- TreeSelect
	- UploadButton
	- UploadDragger
	- layouts
		-  DrawerForm
		-  LightFilter
		-  LoginForm
		-  ModalForm
		-  ProForm
		-  QueryFilter
		-  StepsForm

## layout => ProLayout
- 封装了如下组件
	- FooterToolbar
	- GlobalFooter
	- GlobalHeader
	- GridContent
	- PageContainer
	- PageLoading
	- SettingDrawer
	- SiderMenu
	- TopNavHeader
	- WaterMark

## list => ProList
- 基于ProTable、ProCard实现

## skeleton => ProSkeleton
- 封装了如下组件
	- Descriptions
	- List
	- Result

## table => ProTable
- 封装了如下组件
	- Alert
	- ColumnSetting
	- DragSortTable
	- Dropdown
	- EditableTable
	- Form
	- ListToolBar
	- ToolBar


