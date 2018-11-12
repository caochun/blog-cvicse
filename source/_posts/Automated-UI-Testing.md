---
title: 使用Selenium进行Web UI自动化测试
date: 2018-08-21 10:23:54
tags:
---

### **一、写在前面**
  - **语言**：Python/Java（本文以Python为例）
  - **下载及配置**：
    ``` sh
    conda install selenium / pip install selenium
    ```
    Selenium这个web自动化测试工具支持多平台（Windows/Linux/MacOS）、多浏览器（IE/Safari/Firefox/Chrome），对于不同的浏览器只需下载相应的浏览器driver即可。
    
    以Chrome为例，在 http://chromedriver.chromium.org/downloads 下载Chrome对应版本的驱动，将下载好的驱动文件放入/usr/local/bin目录下。
    
    测试是否正确：

    ``` python
    from selenium import webdriver
    dr = webdriver.Chrome()
    ```

    如果能呼出Chrome浏览器，则说明安装正确。

### **二、准备工作**
 - **浏览器窗口最大化**
    
    将浏览器最大化可以便于在同一分辨率下进行图像比对和坐标点选等操作。

    ``` python
    dr.maximize_window()
    ```

 - **中文字符编码问题**
    
    python的默认编码是ascii，会遇到中文编码报错问题，解决方法如下：

    ``` python
    # -*- coding: utf-8 -*-
    import sys
    reload(sys)
    sys.setdefaultencoding('utf8')
    ```

    在输入中文时，需要在中文前加一个字符`u`，如下：

    ``` python
    dr.find_element_by_id("...").send_keys(u"自动化测试")
    ```

### **三、访问链接**

 进行Web UI自动化测试，首先最基本的事就是用 `get` 方法访问一个链接：

 ``` python
 dr = webdriver.Chrome()
 url = 'http://www.google.com'
 dr.get(url)
 ```

 此外，在实际测试时，常常会出现页面重定向或是跳转到一个新的页面的情况，此时需要加上一段等待时间，再判断新的页面的url或title是否符合预期。尤其需要注意的是，若等待时间太短，可能出现页面还未跳转好的情况，因此需要合理设置等待时长。

 ``` python
 from selenium import webdriver
 from time import sleep
 dr = webdriver.Chrome()
 url = 'http://www.location.com'
 dr.get(url)
 sleep(2)
 assert dr.title == "Login"
 assert dr.current_url == "http://www.location.com/login"
 ```

### **四、定位元素**
 UI自动化测试的核心内容是对元素的定位，只有准确地定位到了所需的元素，才能对其进行一系列的操作，并进而验证功能的正确性。
 
 Selenium对元素的定位提供了如下8种方式：
 - find_element(s)_by_id
 - find_element(s)_by_name
 - find_element(s)_by_xpath
 - find_element(s)_by_link_text
 - find_element(s)_by_partial_link_text
 - find_element(s)_by_tag_name
 - find_element(s)_by_class_name
 - find_element(s)_by_css_selector

 这8种方式都支持单个和一组对象的定位，若是单个元素的定位，则会返回**第一个**满足条件的元素；若是用 `find_elements` 进行多个元素的定位，则会返回所有满足条件的元素的list。若是没有找到符合条件的元素，则会抛出 `NoSuchElementException` 异常。

 #### （一）定位单个元素

 1. **find_element_by_id/name/tag_name/class_name**
    
    这四种定位方式非常直接，都是通过html代码中的元素属性定位。

    假设html代码如下：
    ``` html
    <html>
    <body>
        <h1>Welcome</h1>
        <form id="loginForm">
            <input name="username" type="text" />
            <input name="password" type="password" />
            <input name="continue" type="submit" value="Login" />
        </form>
        <p class="content">Site content goes here.</p>
    </body>
    <html>
    ```

    则对应的元素定位可能如下：

    ``` python
    heading1 = dr.find_element_by_tag_name('h1')
    login_form = dr.find_element_by_id('loginForm')
    username = dr.find_element_by_name('username')
    password = dr.find_element_by_name('password')
    content = dr.find_element_by_class_name('content')
    ```

 2. **find_element_by_link_text/partial_link_text**
    
    这两种方式都是通过文本链接的内容的匹配来定位元素，略有区别的就是一个是全部匹配而另外一个则是部分匹配。

    假设html代码如下：

    ``` html
    <html>
    <body>
        <p>Are you sure you want to do this?</p>
        <a href="continue.html">Continue</a>
        <a href="cancel.html">Cancel</a>
    </body>
    <html>
    ```

    则对应的元素定位可以是这样：

    ``` python
    continue_link = dr.find_element_by_link_text('Continue')
    cancel_link = dr.find_element_by_partial_link_text('Can')
    ```
 3. **find_element_by_xpath**

    由于在实际测试时，很多元素的 `id/name/tag name/class name/link text` 这些值都不是整个网页中唯一的，而通过这些属性值定位单个元素只能匹配符合条件的第一个，又或者有些元素根本就没有这些属性。种种情况都会导致以上几种方式存在很大的局限性，Selenium支持一种非常强大、几乎可以说能由此定位到任何元素的方式——通过XPath定位元素。

    在我看来，层级定位是XPath的核心。先定位父元素，再通过父元素定位到具体的子元素，一方面通过将元素定位约束在父元素以下的一个较小模块中，解决了属性值在全局中可能不唯一的问题，另一方面，也避免陷入使用绝对路径带来的不便修改难以维护的窘境。此外，在选择匹配属性时XPath也不局限于name、id这些属性，为元素定位提供了更多可能。

    XPath的写法十分多样，下文只介绍几种比较基本的用法。
    
    假设有一份html代码如下：

    ``` html
    <html>
    <body>
        <form id="loginForm">
            <input name="username" type="text" />
            <input name="password" type="password" />
            <input name="continue" type="submit" value="Login" />
            <input name="continue" type="button" value="Clear" />
        </form>
        <div class="main-container main-container-component ant-col-xs-24 ant-col-sm-24 ant-col-md-24 ant-col-lg-18 ant-col-xl-19 ant-col-xxl-20">
        </div>
    </body>
    <html>
    ```

    则各个元素可以这样用XPath定位：

    ``` python
    login_form = dr.find_element_by_xpath("//form[@id='loginForm']")
    username = dr.find_element_by_xpath("//form[@id='loginForm']/input[1]")
    password = login_form.find_element_by_xpath(".//input[@type='password']")
    div = dr.find_element_by_xpath("//div[contains(@class, 'main-container-component')]")
    ```

    使用XPath时，可以指定某个属性的值；可以用数字（从1开始）表示当前路径下的第几个元素；可以从父元素开始使用find_element_by_xpath，路径以`.`开头；如果某些属性值太长，可以使用`contains`关键字匹配其中的部分内容……

    这些只是XPath使用方法的一小部分，实际使用时可能会遇到各种各样的复杂情况，在这里推荐一些有关XPath的教程和工具：
    - [W3Schools XPath Tutorial](https://www.w3schools.com/xml/xpath_intro.asp)
    - [XPath Tutorial](http://zvon.org/xxl/XPathTutorial/General/examples.html)
    - [XPath Helper](https://chrome.google.com/webstore/detail/hgimnogjllphhhkhlmebbmlgjoejdpjl) - Chrome 插件

 4. **find_element_by_css_selector**
    
    前端开发人员正是用CSS Selector设置页面上每一个元素的样式的，无论它们的位置有多复杂，都能定位到。因此，这也是一种强大的元素定位方式，与XPath结合使用可以非常精准的定位每一个element。

    教程：[Selenium Tips: CSS Selectors](https://saucelabs.com/resources/articles/selenium-tips-css-selectors)

 #### （二）定位一组元素
 
 对于一组元素的定位方法大致与单个元素相同，所不同的是有两种场景需要一些特殊的操作：
 
 1. 需要批量操作对象：通过对批量定位结果list的循环遍历解决。

    ``` python
    # 勾选所有的checkbox
    checkboxex = dr.find_elements_by_css_selector("input[type=checkbox]")
    for checkbox in checkboxes
        checkbox.click()
    ```

 2. 关注一些特定位置的元素（通常为首尾元素）：通过list自带的方法可以定位到。

    ``` python
    # 使用pop方法，得到list的末尾元素
    lastCheckbox = dr.find_elements_by_css_selector("input[type=checkbox]").pop()

    # 使用单个元素的定位也可以达到定位最后一个元素的效果：
    newConsign = dr.find_element_by_xpath("//tbody[@class='ant-table-tbody']/tr[last()]")
    ```

### **五、操作元素**
 
 定位元素的目的无非就是为了获取这个元素的信息或是对它进行一些操作。

 获取元素信息的方法十分简单，列举几个常见的信息的获取方法如下：

 ``` python
 # 获取属性值
 String classOfForm = dr.find_element_by_tag_name("form").get_attribute("class")

 # 获取文本text
 String consignText = dr.find_element_by_xpath("//div[@class='ant-layout-sider-children']/ul/li[2]/div/span/span").text

 # 获取元素的数量（适用于一组元素）
 int numOfOptions = dr.find_elements_by_tag_name("select").size()
 ```

 元素操作也并不复杂，主要有以下几种常用方法：
 - `click` - 点击对象
 - `send_keys` - 模拟输入
 - `clear` - 清除内容

 ``` python
 # click
 dr.find_element_by_link_text("link").click()
 time.sleep(2)

 # send_keys
 inputItem = dr.find_element_by_tag_name('input')
 inputItem.send_keys("selenium")
 time.sleep(2)

 # clear
 inputItem.clear()
 time.sleep(2)

 # 添加sleep等待时间，一是为了给浏览器预留响应时间，二也是方便测试人员直观看到操作效果。
 ```

 前面举的例子都比较简单，但实际测试的过程中会碰到许多复杂的情况，其中有一种较为常见的是在页面上进行某些操作后，页面上会出现或隐藏一些元素，这时需要使用Wait类的until方法等待这些效果完成以便进行后续的操作。

 以点击触发按钮呼出下拉菜单为例，需要先等待下拉菜单出现才能点击菜单中的选项：

 ``` python
 # 点击触发按钮
 dr.find_element_by_xpath("...").click()

 # 等待下拉框出现，超时时间timeout=10
 WebDriverWait(self.dr, 10).until(
    lambda the_driver:
    the_driver.find_element_by_xpath("...").is_displayed()) 

 menu = self.dr.find_element_by_xpath("...") # 列表ul
 menu.find_element_by_xpath("...").click() # 列表中具体项目li
 ```

### **六、测试框架**
 
 前面的几部分都是在介绍Selenium工具的使用方法，但是除了测试工具，UI自动化测试还需要将多个测试用例组织起来并执行测试，也就是测试框架。在这里我选择使用的是Python的Unittest框架。

 unittest中最核心的四个概念是：**test case, test suite, test runner, test fixture**。
 - **test case**
    
    一个TestCase的实例就是一个测试用例，包括测试前准备环境的搭建(setUp)，执行测试代码(run)，以及测试后环境的还原(tearDown)。
 
    继承了unittest.TestCase的类是一组测试用例，其中的每个以 `test` 开头的方法都会在load的时候生成一个TestCase实例，通过TextTestRunner会执行每一个用例。

    PS：设置`unittest.TextTestRunner(verbosity=2)`可以输出每个测试用例的详细信息，包括名字、注释和测试结果；注释写在用例定义的第一句，用`'''`扩起来。

 - **test suite**

    TestSuite是多个测试用例的集合，同时也支持嵌套TestSuite。

    测试用例之间有可能会有执行的先后顺序，但框架在执行用例时是按照字典序先后执行的，要想使测试用例按照我们所希望的顺序执行，一种办法是定义时在表示测试用例的 `test` 后加上字典序序号 `abcd……`；另一种办法是手动调用TestSuite的 `addTest`、`addTests` 方法来动态添加TestCase进TestSuite，会按添加顺序执行各个用例。

 - **test runner**
    
    TestRunner是运行测试用例的驱动类，可以执行TestCase，也可执行TestSuite。执行后TestCase和Testsuite会自动管理TestResult。

 - **test fixture**

    test fixture可以做一些测试过程中需要准备的东西，比如创建临时的数据库，文件和目录等，其中 `setUp()` 和 `tearDown()` 是最常用的方法。
    
    这两个方法在每个测试方法执行前以及执行后执行一次，`setUp` 用来为测试准备环境，`tearDown` 用来清理环境，已备之后的测试。如果想要在所有case执行之前准备一次环境，并在所有case执行结束之后再清理环境，我们可以用 `setUpClass()` 与 `tearDownClass()`，并在前面标注 `@classmethod` 。

 介绍完了测试框架的核心工作原理和使用，如何判断测试用例执行的结果是否符合预期呢？

 显然，通过将获取到的元素信息等内容打印到控制台，再靠人去辨别输出信息是否符合期望结果的方式是十分愚蠢的，Unittest中提供了很多种assert语句，足以满足测试时的需要。常用的几条如下：
 - assertEqual(a,b，[errorMsg])：若a = b，则通过
 - assertNotEqual(a,b，[errorMsg])：若a != b，则通过
 - assertTrue(x，[errorMsg])：若x为True，则通过
 - assertFalse(x，[errorMsg])：若x为False，则通过
 - assertIsNone(x，[errorMsg])：若x为None，则通过
 - assertIsNotNone(x，[errorMsg])：若x不为None，则通过

---
#### 参考资料
- [Selenium docs](http://selenium-python.readthedocs.io/locating-elements.html)
- [Selenium Webdriver 从入门到提高](https://www.gitbook.com/book/easonhan007/selenium-webdriver)
- [Python单元测试框架——Unittest](https://huilansame.github.io/huilansame.github.io/archivers/python-unittest)
