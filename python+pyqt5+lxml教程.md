# Python3 + PyQt5 + lxml 教程

## 安装开发环境

* pycharm 安装

​	上网下载默认安装

* Python3 安装

​	上网下载默认安装

* PyQt5安装

​	![1552108384725](./images\1552108384725.png)

 在settings--> Project -->Interpreter 中安装PyQt5,注意需要安装5.8.2 版本，为了后期pyinstall打包成exe。

## 开发插件配置

* pyqt5

  ![1552110185287](./images\1552110185287.png)



​	Program:D:\workspace\python\crossroad\venv\Lib\site-packages\pyqt5_tools\designer.exe

​	Working directory:$FileDir$



* pyuic

  ![1552110261030](./images\1552110261030.png)



* 使用pyqt5

  ![1552111231817](./images\pyqt5shiyong.png)

* 使用pyuic

  ![1552111261579](.\images\pyuicshiyong.png)



## 开发小工具

文件比对复制：

* 需求：已知2个xml文件（a和b）,需要把a文件中的节点复制或替换到b文件中。替换规则，当节点属性id、name、type相同的时候替换，不同的时候新增。

* 实现方案：使用lxml插件读取a、b文件，分别把这两个文件显示到界面QTreeView中，左侧是需要复制的文件a,实现可以勾选执行需要复制或新增的记录。右侧为结果文件b,实现可以勾选删除。

  ![1552108921001](./images\1552108921001.png)



## lxml 知识点

* 读取文件

  ```python
  parserRight = etree.XMLParser(remove_blank_text=True)
  xmlRight = etree.parse(self.rightPath, parserRight)
  self.rightRoot = xmlRight.getroot()  # 获取根节点
  ```



* 查询节点

  ```python
  roadEleList = root.xpath("//OpenDRIVE/road")
  ```

* 按照属性查询节点

  ```python
  roadEleList = root.xpath("//OpenDriveData/road[@id='" + filterStr + "']")
  
  # 按多个属性查询
  objectList = self.rightRoot.xpath(
                      "//OpenDriveData/road[@id='" + roadId + "']/objects/object[@id='" +
                      id + "'and @type='" + type + "' and @name='" + name + "' ]")
  ```

* 删除节点

  ```python
  objectList = self.rightRoot.xpath(
              "//OpenDriveData/road[@id='" + roadId + "']/objects/object[@id='" +
              id + "'and @type='" + type + "' and @name='" + name + "']")
  rightObjectsList = self.rightRoot.xpath("//OpenDriveData/road[@id='" + roadId + "']/objects
                                          
  # 删除节点
  rightObjectsList[0].remove(objectList[0])
  ```

* 新增节点

  ```python
  # 新增已有节点
  rightObjectsList[0].append(leftObjectList[0])
  
  junction = etree.SubElement(self.root, 'junction', attrib={"id": junctionID})
  links = etree.SubElement(junction, "links")
  link = etree.SubElement(links, "link")
  # 使用set方法添加属性，属性值不排序，而etree.SubElement(links, "link", attrib={})设置，属性值导出时是有序的
  link.set("type", type)
  link.set("fromroad", fromroad)
  link.set("toroad", toroad)
  link.set("id", id)
  
  
  ```

* 保存文件

  ```python
   # 节点转为tree对象
      tree = etree.ElementTree(self.rightRoot)
      '''
      各个参数含义如下：
      1）第1个参数是xml的完整路径(包括文件名)；
      2）pretty_print参数是否美化代码；
      3）xml_declaration参数是否写入xml声明，就是我们看到xml文档第1行文字；
      4）encoding参数很明显是保存的编码。
      '''
      tree.write("b.xml", pretty_print=True, xml_declaration=True, encoding='utf-8')
      QMessageBox.information(self, "温馨提示", "xml生成成功！", QMessageBox.Yes, QMessageBox.Yes)
  
      
  except Exception as err:
      print('错误信息：{0}'.format(err))
      QMessageBox.information(self, "温馨提示", "生成xml失败！", QMessageBox.Yes, QMessageBox.Yes)
  ```





## 打包

* 安装pyinstall

  ![1552110396317](./images\1552110396317.png)

  

* pyinstall 命令：

  * -F, –onefile  打包一个单个文件，如果你的代码都写在一个.py文件的话，可以用这个，如果是多个.py文件就别用
  * -D, –onedir   打包多个文件，在dist中生成很多依赖文件，适合以框架形式编写工具代码，我个人比较推荐这样，代码易于维护
  * -K, –tk   在部署时包含 TCL/TK
  * -a, –ascii    不包含编码.在支持Unicode的python版本上默认包含所有的编码.
  * -d, –debug    产生debug版本的可执行文件
  * -w,–windowed,–noconsole   使用Windows子系统执行.当程序启动的时候不会打开命令行(只对Windows有效)
  * -c,–nowindowed,–console

例子：pyinstall -F -w CrossRoadTool5.py



* 注意事项
  * PyQt5 安装时注意版本，需要安装5.8.2（亲测）版本或以下（未测），最新版本的pyinstall打包报错
  * pycharm中配置的python.exe，确认是哪个，是C中的，还是本项目中的，影响tool配置。

