# Qt 6 C++ 开发指南

## 目录

[第 1 章 认识 Qt](#第-1-章-认识-qt)  
[第 2 章 GUI 程序设计基础](#第-2-章-gui-程序设计基础)  
[第 3 章 Qt 框架功能概述](#第-3-章-qt-框架功能概述)

## 第 1 章 认识 Qt

Qt 实质上是用 C++编写的大型类库，它为跨平台应用开发提供了一个完整的框架。Qt 框架包含大量的类，支持 GUI、数据库、网络、多媒体等各种应用的编程。

Qt 的一个重要特点就是具有跨平台开发能力。Qt 能用于如下一些设备和平台的应用开发。

-   桌面应用开发，支持的桌面操作系统包括 Windows、桌面 Linux 和 macOS。
-   手机和平板计算机等移动设备的应用开发，支持的移动操作系统包括 Android、iOS 和 Windows。
-   嵌入式设备的应用开发，支持的嵌入式操作系统包括 QNX、嵌入式 Linux 和 VxWorks 等。
-   MCU 的应用开发，支持嵌入式实时操作系统 FreeRTOS 或无操作系统。

Qt 类库本身是用 C++语言编写的，所以 Qt 支持的基本开发语言是 C++。Qt 还对标准 C++语言进行了扩展，引入了信号与槽、属性等机制，为跨平台和 GUI 程序的对象间通信提供了极大的方便。

Qt 6.0 在 2020 年 12 月正式发布，它引入了很多新的特性，主要包括如下内容。

-   支持 C++ 17 标准。
-   Qt 核心库的改动。
-   新的图形架构。Qt 6 中抽象出 3D 图形的渲染硬件接口 RHI，以使用平台本地化的 3D 图形 API。
-   支持并建议使用 CMake 构建系统。

## 第 2 章 GUI 程序设计基础

### GUI 程序结构与运行机制

窗口界面设计和界面组件的事件处理是 GUI 程序设计的主要任务，与窗口相关的文件通常有 4 个。以窗口类 Widget 为例：

| 文件        | 说明                                                           |
| ----------- | -------------------------------------------------------------- |
| widget.h    | 定义了窗口类 Widget                                            |
| widget.cpp  | 实现 Widget 类的功能的源程序文件                               |
| widget.ui   | 窗口 UI 文件，用 XML 格式存储界面上各个组件的属性和布局内容    |
| ui_widget.h | UI 文件经过 UIC 编译后生成的文件，用 C++语言描述 UI 文件的内容 |

_提示：我们通常把处于设计阶段的 UI 称为窗体（form），处于运行阶段的 UI 称为窗口。_

在构建项目时：

- Qt C++ 头文件被**元对象编译器**（MOC）预编译，生成的一个元对象代码文件。
- 窗口 UI 文件被**用户界面编译器**（UIC）转换为一个 C++ 源程序文件。
- 资源文件（.qrc 文件）会被**资源编译器**（RCC）转换为 C++ 程序文件。

再经过标准 C++ 编译器编译链接为可执行文件。

后缀为 `.ui` 的文件是用于窗口界面可视化设计的文件，使用 Qt Designer 可以编辑该文件对窗口界面进行可视化设计，是一个 XML 文件。它存储了界面上所有组件的属性设置、布局、信号与槽函数的关联等内容。

在构建项目时，UI 文件 widget.ui 会被 Qt 的 UIC 编译为对应的 C++语言头文件 ui_widget.h。它的主要内容如下：

1. 定义了一个类 Ui_Widget，用于封装可视化设计的界面。

2. Ui_Widget 类的 public 部分为界面上每个组件定义了一个指针变量，变量的名称就是 UI 可视化设计时为组件设置的对象名称。

    ```cpp
    QLabel *labDemo;
    QPushButton *btnClose;
    ```

3. 定义并实现了一个函数 setupUi()。

    ```cpp
    void setupUi(QWidget *Widget)
        {
            // 设置窗体属性
        	if (Widget->objectName().isEmpty())
                Widget->setObjectName(QString::fromUtf8("Widget"));
            Widget->resize(240, 160);

            // 设置QLabel组件属性
        	labDemo = new QLabel(Widget);  // 将 Widget 作为 labDemo 的父容器
            labDemo->setObjectName(QString::fromUtf8("labDemo"));
            labDemo->setGeometry(QRect(40, 10, 171, 91));
            QFont font;
            font.setPointSize(24);
            font.setBold(true);
            font.setWeight(75);
            labDemo->setFont(font);
            labDemo->setTextFormat(Qt::MarkdownText);

        	// 设置QPushButton组件属性
            btnClose = new QPushButton(Widget);  // 将 Widget 作为 btnClose 的父容器
            btnClose->setObjectName(QString::fromUtf8("btnClose"));
            btnClose->setGeometry(QRect(140, 100, 81, 33));

            // 设置界面上各组件的文字属性
        	retranslateUi(Widget);

        	// 设置信号与槽的关联
        	QObject::connect(btnClose, SIGNAL(clicked()), Widget, SLOT(close()));
            QMetaObject::connectSlotsByName(Widget);
        } // setupUi
    ```

4. 定义名字空间 Ui，并定义一个从 Ui_Widget 继承的类 Widget。

    ```cpp
    namespace Ui {
        class Widget: public Ui_Widget {};  // 用命名空间区分封装界面的类 Ui::Widget 和 widget.h 中的同名类 Widget
    } // namespace Ui
    ```

文件 widget.h 是定义窗口类的头文件，有几个重要的组成部分。

1. 窗口 UI 类 Ui::Widget 的声明。

    ```cpp
    QT_BEGIN_NAMESPACE
    namespace Ui { class Widget; }  //一个名字空间 Ui，包含一个类 Widget
    QT_END_NAMESPACE
    ```

2. 窗口类 Widget 的定义。

    ```cpp
    class Widget : public QWidget
    {
        Q_OBJECT  // 插入了这个宏之后，Widget 类中就可以使用信号与槽、属性等功能。
    
    public:
        Widget(QWidget *parent = nullptr);
        ~Widget();
    
    private:
        Ui::Widget *ui;  // 用于访问可视化窗口界面的指针
    };
    ```

    _提示：如果一个文件的程序中用到 Qt 框架中的某个类，可以使用`#include <类名称>`的形式，例如`#include <QWidget>`。_

文件 widget.cpp 是对应于文件 widget.h 的源程序文件。

```cpp
#include "widget.h"
#include "./ui_widget.h"  // UI 文件 widget.ui 被 UIC 编译后生成的文件，这个文件里实现了窗口 UI 类

Widget::Widget(QWidget *parent)
    : QWidget(parent)
    , ui(new Ui::Widget)
{
    ui->setupUi(this);  // 创建窗口上所有的界面组件，并且以 Widget 窗口作为所有组件的父容器
}

Widget::~Widget(){ delete ui; }
```

在 Widget 类中编写程序时，可以通过 ui 指针访问窗口界面上的所有组件。例如修改 labDemo 和 btnClose 的显示文字：

```cpp
Widget::Widget(QWidget *parent) : QWidget(parent) , ui(new Ui::Widget)
{
	ui->setupUi(this);
	ui->labDemo->setText("欢迎使用 Qt6");
	ui->btnClose->setText("关闭");
}
```

### 可视化 UI 设计

GUI 应用程序设计的完整过程涉及一些关键技术：

-   UI 可视化设计的布局管理问题。
-   在可视化设计 UI 时，设置组件的内建信号与窗体上其他组件的公有槽函数关联。
-   在可视化设计 UI 时，为组件的内建信号创建槽函数，并且使其与组件的信号自动关联。
-   创建资源文件，将图片文件导入资源文件，为界面上的组件设置图标。

在 UI 可视化设计时，对于需要在程序中访问的界面组件，例如各个按钮、需要读取输入的编辑框、需要显示结果的标签等，应该修改其对象名称，以便在程序里区分。对于不需要在程序里访问的界面组件，例如用于组件分组的分组框、布局等，无须修改其对象名称，由 Qt Designer 自动命名即可。

对于界面组件的属性设置，需要注意以下几点：

1. 对象名称是窗口上的组件的实例名称，界面上的每个组件需要有一个唯一的对象名称，程序里访问界面组件时都是通过其对象名称进行的。
2. 窗体的对象名称会影响窗口 UI 类的名称。如果要重命名一个窗口，那么需要对窗口相关的 4 个文件都重命名。
3. 设置窗体的 font 属性后，界面上其他组件的默认字体就是窗体的字体，无须再单独设置，除非要为某个组件设置单独的字体。
4. 组件的属性都有默认值，一个组件的某个属性被修改后，属性编辑器里的属性名称会以粗体显示。如果要恢复属性的默认值，点击属性值右端的还原按钮即可。

Qt 的 UI 设计具有**布局**（layout）功能。所谓布局，就是指界面上组件的排列方式。使用布局可以使组件有规则地分布，并且使其随着窗口大小变化自动调整大小和相对位置。

1. 界面组件的层次关系

    为了将界面上的各个组件的分布设计得更加美观，我们经常使用一些容器类组件。例如，将 3 个复选框（QCheckBox 类）放置在一个分组框（QGroupBox 类）里，这个分组框就是这 3 个复选框的容器，移动这个分组框就会同时移动其中的 3 个复选框。

2. 布局管理

    Qt 为 UI 设计提供了丰富的布局管理功能。组件面板里有 Layouts 和 Spacers 两个分组，在上方的工具栏里有布局管理的按钮。

3. 伙伴关系与 Tab 顺序

    伙伴关系是指界面上一个标签和一个具有输入焦点的组件相关联。Tab 顺序是指在程序运行时，按下键盘上的 Tab 键时输入焦点的移动顺序。一个好的 UI，在按 Tab 键时焦点应该以合理的顺序在界面组件上移动。

**信号与槽**是 Qt 编程的基础，也是 Qt 的一大创新。有了信号与槽的编程机制，在 Qt 中处理界面上各个组件的交互操作就变得比较直观和简单。

-   信号（signal）是在特定情况下被发射的通知。

-   槽（slot）是对信号进行响应的函数，也称为槽函数。

    _槽函数与一般的函数不同的是：槽函数可以与信号关联，当信号被发射时，关联的槽函数被自动运行。_

**资源文件**的主要功能是存储图标和图片文件，以便在程序里使用。在资源文件里首先需要建一个前缀（prefix），例如 icons，前缀表示资源的分组。然后点击 Add Files 按钮添加图标文件。

_提示：图标文件需要在项目的子目录下。_

### 代码化 UI 设计

以 Dialog 类为例，头文件定义如下：

```cpp
class Dialog : public QDialog
{
    Q_OBJECT
public:
    Dialog(QWidget *parent = nullptr);
    ~Dialog();
private slots:                     // 槽函数
    void do_chkBoxUnderline(bool checked);
    void do_chkBoxItalic(bool checked);
    void do_chkBoxBold(bool checked);
    void do_setFontColor();
private:
    QcheckBox *checkBoxUnderline;  // 3个复选框
    QcheckBox *checkBoxItalic;
    QcheckBox *checkBoxBold;
    QRadioButton *radioButtonRed;  // 3个单选按钮
    QRadioButton *radioButtonBlue;
    QRadioButton *radioButtonBlack;
    QPushbutton *btnOk;            // 3个按钮
    QPushbutton *btnCancel;
    QPushbutton *btnClose;
    QPlainTextEdit *txtEdit;       // 文本框

    void initUi();                 // 创建界面组件并完成布局和属性设置
    void initSignalSlots();        // 完成信号与槽的关联
};
```

_注意：这个 Dialog 类里没有定义指向窗口界面的指针 ui，因为它没有对应的窗口 UI 文件。_

自定义槽函数在访问界面组件时无须使用 ui 指针，而是直接访问 Dialog 类里定义的界面组件的成员变量，例如 do_chkBoxUnderline()的代码如下：

```cpp
cvoid Dialog::do_chkBoxUnderline(bool checked) {
    QFont font = txtEdit->font();
    font.setUnderline(checked);
    txtEdit->setFont(font);
}
```

界面的创建，以及信号与槽函数的关联都在 Dialog 类的构造函数里完成，构造函数代码如下：

```cpp
Dialog::Dialog(QWidget *parent)
    : QDialog(parent)
{
    initUi();
    initSignalSlots();
    setWindowTitle("手工创建 UI");
}
```

函数 iniUI()实现界面组件的创建与布局，下面是函数 iniUI()的完整代码。

```cpp
void Dialog::initUi(){
    // 创建3个复选框并水平布局
    checkBoxUnderline = new QCheckBox("Underline");
    checkBoxItalic = new QCheckBox("Italic");
    checkBoxBold = new QCheckBox("Bold");
    QHBoxlayout *hLay1 = new QHBoxLayout();
    hLay1->addWidget(checkBoxUnderline);
    hLay1->addWidget(checkBoxItalic);
    hLay1->addWidget(checkBoxBold);
    // 创建3个单选按钮并水平布局
    radioButtonRed = new QRadioButton("Red");
    radioButtonBlue = new QRadioButton("Blue");
    radioButtonBlack = new QRadioButton("Black");
    QHBoxlayout *hLay2 = new QHBoxLayout();
    hLay2->addWidget(radioButtonRed);
    hLay2->addWidget(radioButtonBlue);
    hLay2->addWidget(radioButtonBlack);
    // 创建3个按钮并水平布局
    btnOk = new QPushButton("OK");
    btnCancel = new QPushButton("Cancel");
    btnClose = new QPushButton("Close");
    QHBoxlayout *hLay3 = new QHBoxLayout();
    hLay3->addWidget(btnOk);
    hLay3->addWidget(btnCancel);
    hLay3->addStretch();
    hLay3->addWidget(btnClose);
    // 创建文本编辑框
    txtEdit = new QPlainTextEdit();
    txtEdit->setPlainText("Hello, World!");
    QFont font = txtEdit->font();
    font.setPointSize(20);
    txtEdit->setFont(font);
    // 创建垂直布局并设置为主布局
    QVBoxLayout *vLay = new QVBoxLayout(this);
    vLay->addLayout(hLay1);
    vLay->addLayout(hLay2);
    vLay->addWidget(txtEdit);
    vLay->addLayout(hLay3);
    setLayout(vLay);
}
```

Dialog 类中定义的槽函数都是自定义槽函数，不会和组件的信号自动关联。函数 iniSignalSlots() 用于建立信号与槽的关联，其完整代码如下。

```cpp
void Dialog::initSignalSlots(){
    // 3个设置颜色的单选按钮
    connect(radioButtonRed,SIGNAL(clicked()),this,SLOT(do_setFontColor()));
    connect(radioButtonBlue,SIGNAL(clicked()),this,SLOT(do_setFontColor()));
    connect(radioButtonBlack,SIGNAL(clicked()),this,SLOT(do_setFontColor()));
    // 3个设置字体的复选框
    connect(checkBoxUnderline,SIGNAL(clicked(bool)),this,SLOT(do_chkBoxUnderline(bool)));
    connect(checkBoxItalic,SIGNAL(clicked(bool)),this,SLOT(do_chkBoxItalic(bool)));
    connect(checkBoxBold,SIGNAL(clicked(bool)),this,SLOT(do_chkBoxBold(bool)));
    // 3个按钮
    connect(btnOk,SIGNAL(clicked()),this,SLOT(accept()));
    connect(btnCancel,SIGNAL(clicked()),this,SLOT(reject()));
    connect(btnClose,SIGNAL(clicked()),this,SLOT(close()));
}
```

## 第 3 章 Qt 框架功能概述

### Qt 6 框架中的模块

Qt 框架中的模块分为基础模块 Qt Essentials 和附加模块 Qt Addons 两大类。

Qt 框架的基础模块提供了 Qt 在所有平台上的基本功能，Qt 6.2 框架的基础模块如表所示。

| 模块              | 功能                                                                            |
| ----------------- | ------------------------------------------------------------------------------- |
| Qt Core           | Qt 框架的核心，定义了元对象系统对标准 C++进行扩展，其他模块都依赖此模块。       |
| Qt GUI            | 提供用于 GUI 设计的一些基础类，可用于窗口系统集成、事件处理、字体和文字处理等。 |
| Qt Network        | 提供实现 TCP/IP 网络通信的一些类。                                              |
| Qt Widgets        | 提供用于创建 GUI 的各种界面组件类。                                             |
| Qt D-Bus          | 提供实现 D-Bus 通信协议的一些类。                                               |
| Qt Test           | 提供一些对应用程序和库进行单元测试的类。                                        |
| Qt QML            | 提供用 QML 编程的框架，它定义了 QML 和基础引擎。                                |
| Qt Quick          | 这个模块是用于开发 QML 应用程序的标准库，提供创建 UI 的一些基本类型。           |
| Qt Quick Controls | 提供一套基于 Qt Quick 的控件，可用于创建复杂的 UI。                             |
| Qt Quick Dialogs  | 提供通过 QML 使用系统对话框的功能。                                             |
| Qt Quick Layouts  | 提供用于管理界面布局的 QML 类型。                                               |
| Qt Quick Test     | 提供 QML 应用程序的单元测试框架。                                               |

Qt 附加模块是一些能够实现特定功能的模块，用户安装 Qt 时可以选择性地安装这些附加模块，Qt 6.2 框架的附加模块如表所示。

| 模块                     | 功能                                                                         |
| ------------------------ | ---------------------------------------------------------------------------- |
| Active Qt                | 用于开发使用 ActiveX 和 COM 控件的 Windows 应用程序。                        |
| Qt 3D                    | 支持二维和三维图形渲染，用于开发近实时的仿真系统。                           |
| Qt 5 Core Compatibility  | 提供一些 Qt 5 中有而 Qt 6 中没有的 API，这是为了兼容 Qt 5。                  |
| Qt Bluetooth             | 提供访问蓝牙硬件的功能。                                                     |
| Qt Charts                | 提供用于数据显示的一些二维图表组件。                                         |
| Qt Concurrent            | 提供一些类，使我们无须使用底层的线程控制就可以编写多线程应用程序。           |
| Qt Data Visualization    | 提供用于三维数据可视化显示的一些类。                                         |
| Qt Help                  | 提供一些在应用程序中集成帮助文档的类。                                       |
| Qt Image Formats         | 支持附加图片格式的插件，格式包括 TIFF、MNG 和 TGA 等。                       |
| Qt Multimedia            | 提供处理多媒体内容的一些类。                                                 |
| Qt Network Authorization | 使 Qt 应用程序能访问在线账号或 HTTP 服务，而又不暴露用户密码。               |
| Qt NFC                   | 提供访问近场通信（near field communication，NFC）硬件的功能。                |
| Qt OpenGL                | 提供一些便于在应用程序中使用 OpenGL 的类。                                   |
| Qt Positioning           | 通过 GPS 或 WiFi 定位，为应用程序提供定位信息。                              |
| Qt Print Support         | 提供一些用于打印控制的类。                                                   |
| Qt Remote Objects        | 提供一种进程间通信技术，可以在进程间或计算机之间方便地交换信息。             |
| Qt SCXML                 | 用于通过 SCXML（有限状态机规范）文件创建状态机。                             |
| Qt Sensors               | 提供访问传感器硬件的功能，传感器包括加速度计、陀螺仪等。                     |
| Qt Serial Bus            | 提供访问串行工业总线（如 CAN 和 Modbus 总线）的功能。                        |
| Qt Serial Port           | 提供访问兼容 RS232 引脚的串行接口的功能。                                    |
| Qt Shader Tools          | 提供用于三维图形着色的工具。                                                 |
| Qt SQL                   | 提供一些使用 SQL 操作数据库的类。                                            |
| Qt SVG                   | 提供显示 SVG 图片文件的类。                                                  |
| Qt UI Tools              | 提供一些类，可以在程序运行时加载用 Qt Designer 设计的 UI 文件以动态创建 UI。 |
| Qt Virtual Keyboard      | 实现不同输入法的虚拟键盘。                                                   |
| Qt Wayland Compositor    | 实现了 Wayland 协议，能创建用户定制的显示服务。                              |
| Qt WebChannel            | 用于实现服务器端与客户端进行 P2P 通信。                                      |
| Qt WebEngine             | 提供一些类和函数，通过 Chromium 浏览器项目实现在应用程序中嵌入显示动态网页。 |
| Qt WebSockets            | 提供 WebSocket 通信功能。                                                    |

### Qt 全局定义

头文件`<QtGlobal>`包含 Qt 框架中的一些全局定义，包括基本数据类型、函数和宏。

为了确保在各个平台上各种基本数据类型都有统一确定的长度，Qt 为各种常见数据类型定义了类型符号，数据类型如表所示：

| Qt 数据类型 | POSIX 标准等效定义     | 字节数 |
| ----------- | ---------------------- | ------ |
| qint8       | signed char            | 1      |
| qint16      | signed short           | 2      |
| qint32      | signed int             | 4      |
| qint64      | long long int          | 8      |
| qlonglong   | long long int          | 8      |
| quint8      | unsigned char          | 1      |
| quint16     | unsigned short         | 2      |
| quint32     | unsigned int           | 4      |
| quint64     | unsigned long long int | 8      |
| qulonglong  | unsigned long long int | 8      |
| uchar       | unsigned char          | 1      |
| ushort      | unsigned short         | 2      |
| uint        | unsigned int           | 4      |
| ulong       | unsigned long          | 8      |
| qreal       | double                 | 8      |
| qsizetype   | ssize_t                | 8      |
| qfloat16    | /                      | 2      |

头文件`<QtGlobal>`中包含一些常用函数的定义，这些函数多以模板类型作为输入和输出参数类型。

| 函数原型                                                    | 功能                                                 |
| ----------------------------------------------------------- | ---------------------------------------------------- |
| T qAbs(const T &value)                                      | 返回变量 value 的绝对值                              |
| const T &qBound(const T &min, const T &value, const T &max) | 返回 value 限定在 min～max 的值                      |
| T qExchange(T &obj, U &&newValue)                           | 将 obj 的值用 newValue 替换，返回 obj 的旧值         |
| int qFpClassify(double val)                                 | 返回 val 的分类，包括 FP_NAN、FP_INFINITE、FP_ZERO等 |
| bool qFuzzyCompare(double p1, double p2)                    | 若 p1 和 p2 近似相等，返回 true                      |
| bool qFuzzyIsNull(double d)                                 | 若参数 d 约等于 0，返回 true                         |
| double qInf()                                               | 返回无穷大的数                                       |
| bool qIsFinite(double d)                                    | 若 d 是一个有限的数，返回 true                       |
| bool qIsInf(double d)                                       | 若 d 是一个无穷大的数，返回 true                     |
| bool qIsNaN(double d)                                       | 若 d 为非数，返回 true                               |
| const T &qMax(const T &value1, const T &value2)             | 返回 value1 和 value2 中的较大值                     |
| const T &qMin(const T &value1, const T &value2)             | 返回 value1 和 value2 中的较小值                     |
| qint64 qRound64(double value)                               | 将 value 近似为最接近的 qint64 类型整数              |
| int qRound(double value)                                    | 将 value 近似为最接近的 int 类型整数                 |

`<QtGlobal>`中定义了很多宏，以下这些宏是比较常用的。

| 宏                               | 功能                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| QT_VERSION                       | 表示 Qt 版本。                                               |
| Q_BYTE_ORDER                     | 系统内存中数据的字节序。Q_BIG_ENDIAN 表示大端字节序，Q_LITTLE_ENDIAN 表示小端字节序。 |
| Q_DECL_IMPORT / Q_DECL_EXPORT    | 使用或设计共享库时用于导入或导出库的内容。                   |
| Q_UNUSED(name)                   | 用于声明函数中未被使用的参数。                               |
| foreach(variable, container)     | 用于遍历容器的内容。                                         |
| qDebug(const char *message, ...) | 用于在 debugger 窗口显示信息。                               |

### Qt 的元对象系统

Qt 中引入了元对象系统对标准 C++语言进行扩展，增加了信号与槽、属性系统、动态翻译等特性，为编写 GUI 应用程序提供了极大的方便。

Qt 的元对象系统的功能建立在以下 3 个方面：

1. QObject 类是所有使用元对象系统的类的基类。
2. 必须在一个类的开头部分插入宏 Q_OBJECT，这样这个类才可以使用元对象系统的特性。
3. MOC 为每个 QObject 的子类提供必要的代码来实现元对象系统的特性。

*提示：构建项目时，MOC 会读取 C++源文件，当它发现类的定义里有 Q_OBJECT 宏时，它就会为这个类生成另一个包含元对象支持代码的 C++源文件，这个生成的源文件连同类的实现文件一起被标准 C++编译器编译和连接。*

**信号与槽**是对象间通信所采用的机制，也是由 Qt 的元对象系统支持而实现的。

1. connect() 函数的不同参数形式的用法

   ```cpp
   // 使用宏 SIGNAL()和 SLOT()指定信号和槽函数
   connect(sender, SIGNAL(signal()), receiver, SLOT(slot()));
   connect(spinNum, SIGNAL(valueChanged(int)), this, SLOT(updateStatus(int)));  // 注明含参信号和槽函数的参数类型
   
   // 如果不存在参数不同的其他同名的信号，或具有默认参数的信号，可以使用函数指针形式进行关联
   connect(lineEdit, &QLineEdit::textChanged, this, &Widget::do_textChanged);      // 不存在参数不同的信号
   connect(ui->checkBox, &QCheckBox::clicked, this, &Widget::do_checked_NoParam);  // clicked信号具有默认参数
   
   // 如果槽函数经过重载，可以使用模板函数 qOverload()来明确参数类型
   connect(ui->checkBox, &QCheckBox::clicked, this, qOverload<bool>(&Widget::do_click));  // 有参数的槽函数
   connect(ui->checkBox, &QCheckBox::clicked, this, qOverload<>(&Widget::do_click));      // 无参数的槽函数
   
   // 还有一个作为 QObject 成员函数的 connect()
   this->connect(spinNum, SIGNAL(valueChanged(int)), SLOT(updateStatus(int)));  // 不常用
   ```

2. 使用 disconnect() 函数解除信号与槽的连接

   ```cpp
   // 解除与一个发射者所有信号的连接
   disconnect(myObject, nullptr, nullptr, nullptr);  // 静态函数形式
   myObject->disconnect();                           // 成员函数形式
   
   // 解除与一个特定信号的所有连接
   disconnect(myObject, SIGNAL(mySignal()), nullptr, nullptr);  // 静态函数形式
   myObject->disconnect(SIGNAL(mySignal()));                    // 成员函数形式
   
   // 解除与一个特定接收者的所有连接
   disconnect(myObject, nullptr, myReceiver, nullptr);  // 静态函数形式
   myObject->disconnect(myReceiver);                    // 成员函数形式
   
   // 解除特定的一个信号与槽的连接
   disconnect(lineEdit, &QLineEdit::textChanged, label, &QLabel::setText);  //静态函数形式
   ```

3. 使用函数 sender() 获取信号发射者

   ```cpp
   QPushButton *btn= qobject_cast<QPushButton*>(sender());  // 获取信号的发射者
   ```

4. 自定义信号及其使用

   ```cpp
   // 信号函数必须是无返回值的函数，但是可以有输入参数。
   class TPerson : public QObject 
   { 
   	Q_OBJECT 
   private: 
   	int m_age= 10; 
   public: 
   	void incAge(); 
   signals: 
   	void ageChanged(int value); 
   }
   // 信号函数无须实现，而只需在某些条件下被发射。
   void QPerson::incAge() 
   { 
   	m_age++; 
   	emit ageChanged(m_age);  //发射信号
   }
   ```

使用 QObject 及其子类创建的对象（统称为 QObject 对象）是以**对象树**的形式来组织的。创建一个 QObject 对象时若设置一个父对象，它就会被添加到父对象的子对象列表里。一个父对象被删除时，其全部子对象就会被自动删除。QObject 类的构造函数里有一个参数 parent，用于设置对象的父对象。

**属性系统**是 Qt C++的一个扩展的特性，是基于元对象系统实现的。在 QObject 的子类中，我们可以使用宏 Q_PROPERTY 定义属性。

### 容器类

Qt 提供了多个基于模板的容器类，这些容器类可用于存储指定类型的数据项。Qt 的容器类比标准模板库（standard template library，STL）中的容器类更轻巧、使用更安全且更易于使用。这些容器类是隐式共享和可重入的，而且它们进行了速度和存储上的优化，因而可以减小可执行文件大小。此外，它们是*线程安全*的，即它们作为只读容器时可被多个线程访问。

Qt 的容器类分为顺序容器（sequential container）类和关联容器（associative container）类。

Qt 6 的**顺序容器类**有 QList、QVector、QStack 和 QQueue。

1. QList 类

   QList 是 Qt 中较常用的容器类，它用连续的存储空间存储一个列表的数据，可以通过索引访问列表的数据。

   ```cpp
   QList<int> list= {1,2,3,4,5};
   QList<QString> list(10, "pass");
   list<<"Monday"<<"Tuesday"<<"Wednesday"<<"Thursday";  // 使用流操作符添加数据
   list.append("Friday");     // 使用函数 append()向列表添加数据
   QString str1= list[0];     // 索引访问
   QString str2= list.at(1);  // 使用函数 at()访问
   ```

   *注意：Qt 6 中的 QVector 是 QList 的别名。*

   QList 用于数据操作的常用函数如下：

   - append()：在列表末端添加数据。
   - prepend()：在列表始端加入数据。
   - insert()：在某个位置插入数据。
   - replace()：替换某个位置的数据。
   - at()：返回某个索引对应的元素数据。
   - clear()：清除列表的所有元素，元素个数变为 0。
   - size()：返回列表的元素个数，即列表长度。
   - count()：统计某个数据在列表中出现的次数，不带任何参数的 count()等效于 size()。
   - resize()：重新设置列表的元素个数。
   - reserve()：给列表预先分配内存，但是不改变列表长度。
   - isEmpty()：如果列表元素个数为 0，该函数返回 true，否则返回 false。
   - remove()、removeAt()、removeAll()、removeFirst()、removeLast()：从列表中移除数据。
   - takeAt()、takeFirst()、takeLast()：从列表中移除数据，并返回被移除的数据。

2. QStack 类

   QStack 是 QList 的子类。QStack是提供类似于栈的后进先出（LIFO）操作的容器，push() 和 pop()是其主要的接口函数。

   ```cpp
   QStack<int> stack; 
   stack.push(10); 
   stack.push(20); 
   stack.push(30); 
   while (!stack.isEmpty()) 
   	cout << stack.pop() << Qt::endl; 
   ```

3. QQueue 类

   QQueue 是 QList 的子类。QQueue是提供类似于队列的先进先出（FIFO）操作的容器， enqueue()和 dequeue()是其主要操作函数。

   ```cpp
   QQueue<int> queue; 
   queue.enqueue(10); 
   queue.enqueue(20); 
   queue.enqueue(30); 
   while (!queue.isEmpty()) 
   	cout << queue.dequeue() << Qt::endl;
   ```

Qt 还提供**关联容器类** QSet、QMap、QMultiMap、QHash、QMultiHash。

1. QSet 类

   QSet 是基于哈希表的集合模板类，内部是用 QHash 类实现的。测试一个值是否包含于某个集合，可以用 contains()函数。

   ```cpp
   QSet<QString> set; 
   set << "dog" << "cat" << "tiger";
   if (!set.contains("cat")) { ... }
   ```

2. QMap 类

   QMap<Key, T>定义字典（关联数组），一个键映射到一个值。QMap 是按照键的顺序存储数据的。

   ```cpp
   QMap<QString, int> map; 
   map["one"] = 1; 
   map["two"] = 2; 
   map["three"] = 3;
   map.insert("four", 4); 
   map.remove("two");
   // 要查找某个值，可以使用运算符“[ ]”或函数 value()
   int num1 = map["one"]; 
   int num2 = map.value("two");
   ```

3. QMultiMap 类

   QMultiMap<Key, T>定义一个多值映射表，即一个键可以对应多个值。

   ```cpp
   QMultiMap<QString, int> map1, map2, map3; 
   map1.insert("plenty", 100); 
   map1.insert("plenty", 2000);  // map1.size() == 2 
   map2.insert("plenty", 5000);  // map2.size() == 1 
   map3 = map1 + map2;           // map3.size() == 3
   QList<int> values = map.values("plenty");  // 获取一个键对应的所有值
   for (int i = 0; i < values.size(); ++i) 
   	cout << values.at(i) << Qt::endl;
   ```

4. QHash 类

   QHash 是基于哈希表的实现字典功能的模板类，查找 QHash<Key, T>存储的键值对的速度非常快。QHash 与 QMap 的功能和用法相似，区别如下。

   - QHash 比 QMap 的查找速度快。
   - 在 QMap 上遍历时，数据项是按照键排序的，而 QHash 的数据项是任意顺序的。
   - QMap 的键必须提供“<”运算符，QHash 的键必须提供“==”运算符和一个名为 qHash() 的全局哈希函数。

**容器迭代器**（iterator）用于遍历容器内的数据项，每一个容器类有两个 STL 类型的迭代器：一个用于只读访问，另一个用于读写访问。

| 容器类                            | 只读迭代器                    | 读写迭代器              |
| --------------------------------- | ----------------------------- | ----------------------- |
| QList<T>、QStack<T>、QQueue<T>    | QList<T>::const_iterator      | QList<T>::iterator      |
| QSet<T>                           | QSet<T>::const_iterator       | QSet<T>::iterator       |
| QMap<Key, T>、QMultiMap<Key, T>   | QMap<Key, T>::const_iterator  | QMap<Key, T>::iterator  |
| QHash<Key, T>、QMultiHash<Key, T> | QHash<Key, T>::const_iterator | QHash<Key, T>::iterator |

STL 类型的迭代器是数组的指针，“++”运算符表示迭代器指向下一个数据项，“*”运算符返回数据项内容。

```cpp
// 顺序容器类
QList<QString> list; 
list << "A" << "B" << "C" << "D"; 
QList<QString>::const_iterator i;    // 只读迭代器
for (i = list.constBegin(); i != list.constEnd(); ++i) 
	qDebug() << *i;
QList<QString>::reverse_iterator i;  // 读写迭代器（反向）
for (i = list.rbegin(); i != list.rend(); ++i) 
	*i = i->toLower();
// 关联容器类
QMap<int, int> map;
QMap<int, int>::const_iterator i; 
for (i = map.constBegin(); i != map.constEnd(); ++i) 
	qDebug() << i.key() << ':' << i.value();
```

*注意：对于 STL 类型的迭代器，对象的管理方法采用了隐式共享，因此当有一个迭代器在操作一个容器变量时，不要去复制这个容器变量。*

Qt 还提供了一个宏 foreach 用于遍历容器内的所有数据项。

```cpp
QList<QString> list; 
... 
foreach (const QString str, list) 
	qDebug() << str; 
// 对于多值映射，可以使用两重 foreach 语句
QMultiMap<QString, int> map; 
... 
foreach (const QString str, map.uniqueKeys()) 
{ 
	foreach (int i, map.values(str)) 
		qDebug() << str << ':' << i; 
}
```

*注意：foreach 遍历容器时创建了容器的副本，所以不能修改原来容器中的数据项。*

### 其他常用的基础类

QVariant 是 Qt 中的一种万能数据类型，它可以存储任何类型的数据。

```cpp
QVariant var(173); 
QString str= var.toString();  //str="173" 
int val= var.value<int>();    //val=173 
QStringList strList; 
strList<<"One"<<"Two"<<"Three"; 
var.setValue(strList);                  //给 var 赋值一个字符串列表
QStringList value= var.toStringList();  //转换为字符串列表
```

QFlags<Enum>是一个模板类，用于定义枚举值的或运算组合。

```cpp
QFlags<Qt::AlignmentFlag> flags= Qt::AlignLeft | Qt::AlignVCenter;  // 创建了一个临时枚举变量
ui->label->setAlignment(flags);
```

QRandomGenerator 类可以产生高质量的随机数。

```cpp
// 随机数发生器和随机数种子
QRandomGenerator *rand1= new QRandomGenerator(QDateTime::currentMSecsSinceEpoch()); 
QRandomGenerator *rand2= new QRandomGenerator(QDateTime::currentSecsSinceEpoch()); 
for(int i=0; i<5;i++) 
	qDebug("R1=%u, R2=%u",rand1->generate(), rand2->generate());  // 采用不同的随机数种子
// 全局的随机数发生器
quint32 rand = QRandomGenerator::global()->generate();
// QRandomGenerator 的接口函数
quint32 QRandomGenerator::generate()       //生成 32 位随机数
quint64 QRandomGenerator::generate64()     //生成 64 位随机数
double QRandomGenerator::generateDouble()  //生成[0,1)区间内的浮点数
```

## 第 4 章 常用界面组件的使用

在 Qt 类库中，所有界面组件类的直接或间接父类都是 QWidget。QWidget 的父类是 QObject 和 QPaintDevice，所以 QWidget 是多重继承的类。QObject 支持元对象系统，其信号与槽机制为 GUI 编程中对象间通信提供了极大的便利。QPaintDevice 是能使用 QPainter 类在绘图设备上绘图的类。所有从 QWidget 继承而来的界面组件被称为 widget 组件，它们是构成 GUI 应用程序的窗口界面的基本元素。
