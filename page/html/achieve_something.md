# 一些页面效果的实现逻辑

#### 1. 加载框
- Web页面加载时会出现一个动态的提示，比如`正在加载`、`正在提交`等等

###### 组成
1. 模态框
2. gif动态图片

###### 应用
- Ajax发送请求时经常会用到加载框，比如再`会议室预订`项目中，用户点击预定之后，到预订成功返回成功信息之间这段时间，就会弹出加载框。