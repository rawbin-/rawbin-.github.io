# antd 组件库
## Form 组件
### Form
- 控制表单整体布局，初始值、字段、事件、验证模板等

### Form.Item 
- 控制单个表单项的标签、初始值、布局、校验规则、依赖项等

### Form.List
- children为渲染函数，传入表单字段、表单的操作（add,remove,move）
- 可以设置默认值，name(支持数组) ，校验规则
- 可配合Form.ErrorList 和rules 一起使用。

### Form.Provider
- 提供表单间联动，会作为父组件处理每个子Form的 onFormChange,onFormFinish

### 对象实例
#### Form实例
- getFieldXXX,isFieldXXX,setFieldsXXX 等，触发表单校验、提交等

#### Field实例
- name,errors,value,validating,touched

#### Rule实例
- type、required、min-max，message、validator等等

## Input 组件
### Input
- label,disabled,type onChange,defautlValue,onChange

### Input.TextArea
- defaultValue,maxLength、showCount、value + textarea的属性

### Input.Search
- enterButton、loading、onSearch

### Input.Group
- compact、size

### Input.Password
- iconRender、visibilityToggle

### 相关方法
- blur、focus

## InputNumber
- Input组件的属性 + min-max、formatter、parser、precision

## Radio 组件
### Radio/Radio.Button
- autoFocus、checked、defaultChecked、disabled、value

### RadioGroup
- defaultValue,name,options,valuel,onChange

## CheckBox 组件
### CheckBox
-  autoFocus、checked、defaultChecked、disabled、value
### CheckBox.Group
- defaultValue,name,options,valuel,onChange


# ProComponents
## 通用配置
### 类table组件配置 较通用
- key
- dataIndex
- valueType
- title
- tooltip
- valueEnum
- fieldProps
- formItemProps
- renderText
- render
- renderFormItem
- request
- params
- dependencies
- hideInDescriptions
- hideInForm
- hideInTable
- hideInSearch

### valueType设置灵活调用 并支持Function更灵活渲染
- password
- money
- textarea
- date
- dateTime
- dateWeek
- dateMonth
- dateQuarter
- dateYear
- dateRange
- dateTimeRange
- time
- timeRange
- text
- select
- treeSelect
- checkbox
- rate
- radio
- radioButton
- progress
- percent
- digit
- second
- avatar
- code
- switch
- fromNow
- image
- jsonCode
- color
- cascader
### valueEnum
- value-{text,status}
## ProFrom 组件
### ProForm
- formRef
- onFinish,onReset,submitter,syncToUrl,syncToInitialValues,dateFormatter,omitNil,params,request,isKeyPressSubmit,autoFocusFirstInput
- submiter: onSubmit,onReset,searchConfig,submitButtonProps,resetButtonProps
### ProForm.Group
- children表单控件或其他元素

### 对象实例
#### ProFormInstance
-  在FormInstance的基础上增加了如下方法：
	- getFieldsFormatValue 类似 getFieldsValue
	- getFieldFormatValue 类似 getFieldValue
	- validateFieldsReturnFormatValue 类似 validateFields

## ProFormFields
### ProFormText
- 同 Input
### ProFormDigit/ProFormDigitRange
- 同 InputNumber，自带格式化2为小数
### ProFormText.Password
- 同 Input.Password
### ProFormTextArea
- 同 Input.TextArea
### ProFormCaptcha
- onGetCaptcha,captchaProps,countDown,captchaTextRender
### ProFormDatePicker/ProFormDateTimePicker/ProFormTimePicker
- 同 DatePicker
### ProFormDateRangePicker/ProFormDateTimeRangePicker
- 同 DatePicker.RangePicker
### ProFormSelect
- 同Select，支持 request，valueEnum，params，fieldProps，debounceTime
### ProFormTreeSelect
- 同TreeSelect 支持 request,valueEnum
### ProFormCheckBox
- 同 Checkbox 支持options,与 layout
### ProFormRadio.Group
- 同 Radio，支持options, request,params,fieldProps
### ProFormCascader
- 同 cascader 通过fieldProps 配置数据
### ProFormSlider
- 同 Slider，通过fieldProps配置数据
### ProFormSwitch
- 同 Swith，通过fieldProps配置数据
### ProFormRate
- 同 Rate，通过fieldProps配置数据
### ProFormUploadButton
- 同Upload，预设了Button样式
### ProFormUploadDragger
- 同Upload，预设了Dragger样式
### ProFormMoney
- locale,min-max,numberFormatOptions,nubmerPopoverRender
## ProFormList
- 同 Form.List 增加了删除和复制操作和新增一行的按钮
- itemRender,creatorRecord,creatorButtonProps,label,name,alwaysSHowItemLabel,min,max
- actionGuard,itemContainerRender
## ProFormFieldSet
- 可以将内部多个children的值组合并存储在ProForm中，通过`transform`在提交时转化
## ProFormDependency
- 是一个简化版本的Form.Item,
- name参数必须要是一个数组，配置的name值会在renderProps中传入

## SchemaForm
- 同 ProForm的API，增加了layoutType,steps,columns,shouldUpdate
- ProFormLayoutType:Form,ModalForm,DrawerForm,StepsForm,StepForm,LightFilter,QueryFilter,Embed
- schema定义：
	- key
	- dataIndex
	- valueType
	- title
	- tooltip
	- valueEnum
	- fieldProps
	- formItemProps
	- proFieldProps
	- renderText
	- render
	- renderFormItem
	- request
	- params
	- dependencies
	- hideInDescriptions
	- hideInForm
	- hideInTable
	- hideInSearch
	- columns

## StepsForm
- current,onCurrentChange,onFinish,stepsProps,stepFormRender,stepsFormRender,stepsRender,formRef
- StepForm 与ProForm完全相同，只是onFinish支持了Promise，如果返回false，就不会调到下一步

## Query/LightFilter
- collapsed,defaultCollapsed,onCollapse,hideRequiredMark,defaultColsNumber,labelWidth,span,split,preserve
- 默认响应式规则：
	- 默认布局 513,710,1062,1352 对应断点为 1，2，3，3，4列
	- 强制上下布局 513,785,1057 对应断点分别为 1，2，3，4列

## Modal/Drawer
- tirgger,visible,onVisibleChange,title,width,onFinish,submitter