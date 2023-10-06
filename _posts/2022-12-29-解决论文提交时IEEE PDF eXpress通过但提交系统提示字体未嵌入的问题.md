---
layout: post
title: "解决论文提交时IEEE PDF eXpress通过但提交系统提示字体未嵌入的问题"
catalog: true
header-style: text

---

## 正常流程

以Adobe Acrobat Pro 9 为例，这个老哥说的很清楚。
链接:[https://blog.csdn.net/wangh0802/article/details/116081856](https://blog.csdn.net/wangh0802/article/details/116081856)
如果打印里找不到 joboptions 的文件配置，需要在![Acrobat Distiller 9](/img/IEEEPDFEXpress/adobe.png)
里的 设置-》添加Adobe PDF 设置-》 找到 joboptions 的文件配置保存路径 -》导入。


## 如果还是提交不上
我按照那个流程弄了一边，然后属性里的字体也显示全部嵌入，我甚至还拿Express 查了一遍，都能通过，到了提交的时候还说我字体未嵌入。错误提示为："Document validation failed due to the following errors: Some fonts have not been embedded in the document"。也不告诉我啥字体未嵌入，也不告诉我位置，服了。
解决办法：自己找未迁入文字所在位置！然后改成别的通用的字体（英文可以是Times new roman）
1. 查找位置：使用**二分定位法**：
首先使用**福昕高级PDF编辑器**的 页面管理-》提取 把每一页拆开，那就一页一页给他交（通常最低提交两页，那就组合起来交）
    然后定位未嵌入文字所在页码。我的是第二页提交不通过，其中画了一个电路图，八成是这个图的问题。然后使用**福昕高级PDF编辑器**的 编辑-》编辑文本，把**正文和图片中的文字**分别删除提交。最终定位到问题出在电路图中。
2. 修改字体
这个时候就可以使用**福昕高级PDF编辑器**的 编辑-》编辑对象-》文本 选择相应的文本，查看字体格式。我的是电路图中的电压源的+-号使用的是微软雅黑，其他字符使用的是Times new roman。 这个时候想都不要想就是这个微软雅黑的问题，虽然我也不知道他为啥有问题而且他已经嵌入了，但相比于Times new roman他还是太不可靠了。然后再使用编辑文本功能把他改成Times new roman。
![电压源](/img/IEEEPDFEXpress/voltage.png)
3. 再次 Adobe 嵌入字体
走**正常流程**重新嵌入字体。问题解决。
