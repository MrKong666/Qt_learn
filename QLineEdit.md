## QLineEdit简介
QLineEdit属于单行控件，可以录入输入的数据。
有几种输入模式，可以使用setEchoMode来改变输入模式。
Normal是正常输入，也是默认的输入模式，会显示在QLineEdit上。
Password是密码输入方式，就和输密码一样，只显示黑色圆点。
NoEcho不显示录入，有点像linux的登录那样
PasswordEchoOnEdit表示输入的时候能看到，不输入的时候就是原点。
## 实战练习
然后我们实战一手，先创建一个qt项目，在ui界面添加4个lable和4个lineedit，像这样。为了整齐美观我还使用了布局。布局和弹簧在这里提一嘴，后面就不专门写了，蛮简单反正。
![](https://github.com/MrKong666/Qt_learn/blob/main/img/2024-10-12%20164444.png?raw=true)

设置ipedit的mask规定输入只能0-9的数字，macedit也是大差不差。
```
    QString ipmask="000.000.000.000";
    ui->ip_edit->setInputMask(ipmask);
    QString macmask="HH:HH:HH:HH:HH:HH;_";
    ui->mac_edit->setInputMask(macmask);
```
除了通过mask来约束输入样式，还可以通过正则表达式来进行约束。下面是用正则表达式过滤邮箱。
```
  QRegularExpression regx("[a-zA-Z0-9-_]+@[a-zA-Z0-9]+\.[A-Za-z0-9]+");
    QValidator* validator=new  QRegularExpressionValidator(regx,ui->emil_edit);
    ui->emil_edit->setValidator(validator);
```
先创建一个QRegularExpression类型的筛选条件，然后绑定筛选条件和作用对象，最后设置Validator。
值得一提的是新版本用QRegularExpression代替了
 QRegExp，用法是一样的，Validator这里用了多态写法，用QRegularExpressionValidator*也是可以的。那本节的笔记就到这里，感谢各位。

