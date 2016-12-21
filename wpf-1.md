学习XAML
======
## 什么是XAML
XAML是一种特殊的XML，用于设计用户界面布局。它是一种生命是的语言。通常和C#等语言结合起来开发。

基于XAML的技术包括：
- WPF
- Windows Store开发
- Windows Phone开发

简体中文  Fijian
由于 XAML是一种基于XML的声明性标记语言，它提供了开发Windows 的用户界面 (UI)的新范式。它具有良好的可读性。XAML解析器可以将XAML代码编译成二进制形式（BAML）。
BAML减少了XAML代码的大小，提高了性能。
### 用户界面隔离
可以将XAML界面设计和展现层逻辑完全隔离开来。MVVM设计模式。

| 设计模式名称 | 相关网址|
---|---
|MVC|https://msdn.microsoft.com/en-us/library/ff649643.aspx|
|MVVM|https://msdn.microsoft.com/en-us/magazine/cc188690.aspx|
### 声名式和命令式编程
声名式重点在定义要什么，而不是如何构建。指令式则描述如何去做。
### code-behind和非code-behind
XAML文件和WinForm程序一样也由code-behind文件，但是很多MVVM会建议你不要在code-behind文件中加入任何代码。这样可以在你的界面设计层和展现层完全隔离。
举例来说，如果你在code-behind指定了一个`Button`的click事件处理函数,就将code-behind和xaml紧耦合在一起，很难单元测试。

一个选择就是使用数据绑定将`button`的`Command`属性绑定到一个独立的类的ICommand实现上。
### ＭＶＶＭ设计模式
MVVM使得你可以通过加入一个抽象层来将所有的界面需要的逻辑封装起来，从而将领域模型的逻辑和用户界面隔离开来。这一层处理用户请求或者从模型获得数据。
其中，View就是XAML，在ViewModel中不允许出现任何界面上的元素。ViewModel的公开接口必需好好设计。对于ViewModel的任何改动都会影响到视图。
ViewModel会和应用服务层配合起来，从模型中获取数据或者修改模型中的数据。
模型应该值包含和业务模型相关的逻辑。不允许用户界面相关的代码如视图或者视图模型直接访问领域模型。领域模型应该和任何其它代码隔离开来，只允许业务领域访问。

视图使用声明的方式创建了视图模型的实例，视图模型的属性都绑定到视图所用的控件上。数据绑定自动更新视图控件的展示的值，也可以绑定用户界面控件的事件和命令。
视图模型根据用户交互事件和和模型交互。视图模型和模型通信来做出修改并向模型汇报变更。这样讲用户界面层，展示层，领域层隔离开来。
### WPF基本MVVM实现
1. xaml
```
<Window
xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presenta
xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
xmlns:d="http://schemas.microsoft.com/expression/blend/2008
xmlns:mc="http://schemas.openxmlformats.org/markupcompatibility/2006" mc:Ignorable="d"
x:Class="BasicMVVMWPF.MainWindow"
xmlns:vm="clr-namespace:BasicMVVMWPF"Title="MainWindow" Height="350" Width="525">
<Window.Resources>
<vm:MainWindowViewModel x:Key="viewModel" />
</Window.Resources>
<Grid>
<Grid.RowDefinitions>
<!--Header row-->
<RowDefinition />
<!--Content row-->
<RowDefinition />
<!--Footer row-->
<RowDefinition />
</Grid.RowDefinitions>
<TextBox x:Name="txtInstructions"
Text="This is the View of the application."
FontSize="20" Grid.Row="0" />
<Grid DataContext="{StaticResource
ResourceKey=viewModel}" Height="236"
VerticalAlignment="Top" Grid.RowSpan="3"
Margin="0,54,0,0">
<Grid.RowDefinitions>
<RowDefinition Height="44*" />
<RowDefinition Height="34*" />
<RowDefinition Height="40*" />
<RowDefinition Height="39*" />
<RowDefinition Height="40*" />
<RowDefinition Height="39*" />
</Grid.RowDefinitions>
<TextBlock x:Name="txtFullNameDescription"
Text="The field below represents the person's full name"
FontSize="20" Grid.Row="0" />
<TextBlock x:Name="txtPersonFullName" Text="
{Binding FullName}"
FontSize="20" Grid.Row="1" />
<TextBlock x:Name="txtFirstNameDescription"
Text="The field below represents the person's first name"
FontSize="20" Grid.Row="2" />
<TextBox x:Name="txtFirstName"
Text="{Binding FirstName}" FontSize="20"
Grid.Row="3" />
<TextBlock x:Name="txtLastNameDescription"Text="The field below represents the person's last name"
FontSize="20" Grid.Row="4" />
<TextBox x:Name="txtLastName"
Text="{Binding LastName}" FontSize="20"
Grid.Row="5" />
</Grid>
</Grid>
</Window>
```
2. 模型
```
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
namespace BasicMVVMWPF
{
public class PersonModel : INotifyPropertyChanged
{
private string _FirstName;
private string _LastName;
public string FirstName
{
get { return _FirstName; }
set
{_FirstName = value;
OnPropertyChanged("FirstName");
}
} p
ublic string LastName
{
get { return _LastName; }
set
{
_LastName = value;
OnPropertyChanged("LastName");
}
} p
ublic event PropertyChangedEventHandler
PropertyChanged;
public void OnPropertyChanged(string propertyName)
{
if (PropertyChanged != null)
PropertyChanged(this, new
PropertyChangedEventArgs(propertyName));
}
}
}
```
3. 视图模型
```
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Linq;
using System.Text;using System.Threading.Tasks;
namespace BasicMVVMWPF
{
public class MainWindowViewModel
: INotifyPropertyChanged
{
private PersonModel _Model;
private string _FullName;
public MainWindowViewModel()
{
_Model = new PersonModel
{
FirstName = "Buddy",
LastName = "James"
};
this.FullName = string.Format("{0} {1}",
_Model.FirstName, _Model.LastName);
} 
public string FirstName
{
get { return _Model.FirstName; }
set
{
_Model.FirstName = value;
this.FullName = string.Format("{0} {1}",
_Model.FirstName, _Model.LastName);
OnPropertyChanged("FirstName");
}
} 
public string LastName
{
get { return _Model.LastName; }
set
{
_Model.LastName = value;
this.FullName = string.Format("{0} {1}",
_Model.FirstName, _Model.LastName);OnPropertyChanged("LastName");
}
} 
public string FullName
{
get { return _FullName; }
set
{
_FullName = value;
OnPropertyChanged("FullName");
}
} 
public event PropertyChangedEventHandler PropertyChanged;
public void OnPropertyChanged(string propertyName)
{
if (PropertyChanged != null)
PropertyChanged(this, new
PropertyChangedEventArgs(propertyName));
}
}
}
```

## 软件艺术
要设计和开发鲁棒，可复用，业务领域相关的软件解决方案来解决企业中的问题，通过使用有效的设计方法，设计模式，其他工具。

最终目标是描述如何设计应用程序，将应用程序分成多个逻辑层，每个层有清晰独立的关注点。换句话说，每个曾都表示精确，灵活，可扩充，松耦合的类库。
### SOLID面向对象设计
S表示单一职能准则--一个类应该值有一个或者一个主要的责任。比如，Order类不应该关注年报如何打印。这个责任是一个叫做ReportPrintService的类的责任。Order类应该只负责描述系统中的订单。

O表示开闭准则。表示设计的代码应该对扩展开放，对修改关闭。思考`该怎样才能让类可以在不修改代码下对扩展开放？`可以使用基类，也可以使用装饰模式。
装饰模式的标准定义： http://sourcemaking.com/design_patterns/decorator

> 装饰模式动态的为一个对象附加额外的职责。使用一种和子类模式不同的灵活的方式来扩充功能。

L代表里氏准则，白送和i你应该可以在任何使用基类实例的地方都可以使用子类实例。

I表示接口隔离，只要可能，你都应该是用接口而不是具体的实现。

D表示依赖转置，两种模式：依赖注入和控制反转

### 测试驱动开放
步骤：
1. 创建一个空的工程
2. 创建一个为程序提供功能的类库
3. 创建一格单元测试项目，并引用上面的类库。

### 开发组会议
- 让团队的人相互认识
- 讨论团队要解决的问题
- 定义每个人的角色
- 探讨项目的deadline
- 定义团队发展计划
- 创建会议计划
- 定义团队沟通方式
- 定义使用的技术
 
## 领域驱动设计
领域驱动设计是一个复杂的概念，推荐看`Domain-Driven Design: Tackling Complexity in the Heart of Software `
### 是什么
领域驱动设计是一个设计和开发软件方案的框架，在这个框架中，设计围绕着实体来组成业务领域。DDD提供了一系列的规范来使你的设计和实现能达到客户的预期。

当你在核心实体的基础上构建了一个领域模型，接下来就是为实体间的关联以及它们之间如何交互提供支持。

包括当前处理流程，规则，限制，领域的术语。
## 设计模式
## 单元测试
## 单元测试进阶和测试驱动开发
## 异常和日志
## WPF用户界面
## Windows phone界面
## Windows用户界面
## 部署和维护
