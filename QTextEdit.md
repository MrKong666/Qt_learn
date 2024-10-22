## 简介
QTextEdit是文本编辑器，支持富文本功能。就是可以往里面加文字或者表格图片等富文本。
## 文本块
在主窗口添加一个textedit插件，然后在构造函数中编写代码，改变textedit样式。
```
// 获取指向 QTextEdit 的 QTextDocument 对象的指针
QTextDocument* doc = ui->textEdit->document();

// 获取文档的根框架 (QTextFrame)，根框架是文档中最外层的框架
QTextFrame* root_frame = doc->rootFrame();

// 创建一个 QTextFrameFormat 对象，用于设置框架的格式
QTextFrameFormat format;

// 设置框架的边框颜色为红色
format.setBorderBrush(Qt::red);

// 设置框架的边框宽度为 3 像素
format.setBorder(3);

// 将上述格式应用到根框架，使文本框呈现指定的边框样式
root_frame->setFrameFormat(format);

```
通过textedit的document函数返回文本块，通过rootframe返回根框架，设置框架的边框样式。运行程序可以看到红色框，在加两个纯文本
```

ui->textEdit->insertPlainText("hello world!\n");
   ui->textEdit->insertPlainText("Hello Qt\n");
```
运行程序会看到纯文本在文本块内，可以在新建一个文本样式，插入到光标所在位置，在新加两个纯文本，这样就是两个文本块。
```
 QTextFrameFormat frameformat;
    frameformat.setBackground(Qt::gray);
    frameformat.setMargin(10);
    frameformat.setPadding(10);
    frameformat.setBorder(2);
    frameformat.setBorderStyle(QTextFrameFormat::BorderStyle_Dashed);
    QTextCursor cursor=ui->textEdit->textCursor();
    cursor.insertFrame(frameformat);
    cursor.insertText("hello innertext\n");
    cursor.insertText(" haha sligoywlb");
```
## 遍历文本块
我们可以遍历文本块和框架节点，先添加一个action，用来接收信号，绑定槽函数，再来实现槽函数。
```
    QAction* action_frame=new QAction("frame",this);
    connect(action_frame,&QAction::triggered,this,&MainWindow::showtextframe);
    ui->toolBar->addAction(action_frame);
```
这是槽函数
```
void MainWindow::showtextframe()
{
    auto doc=ui->textEdit->document();
    auto rootframe=doc->rootFrame();
    for(auto it=rootframe->begin();it!=rootframe->end();it++){
        auto cur_frame=it.currentFrame();
        auto cur_block=it.currentBlock();
        if(cur_frame){
            qDebug()<<"frame";
        }
        else if(cur_block.isValid()){
            qDebug()<<"block"<<cur_block.text();
        }
    }


}
```
如果指向打印文本块，还是一样的弄个action绑定槽函数，下面展示槽函数。
```
void MainWindow::showtextblock()
{
    QTextDocument* document=ui->textEdit->document();
    QTextBlock block=document->firstBlock();
    for(int i=0;i<document->blockCount();i++){
        qDebug()<<tr("文本块%1，首行行号%2，长度%3，内容%4")
                      .arg(i).arg(block.firstLineNumber())
                      .arg(block.length()).arg(block.text());
        block=block.next();
    }
}
```
## 设置样式
下面我们来做一个点击按钮改变文本块样式。
```
// 创建一个新的 QAction 对象，名称为 "字体"，并将其父对象设置为当前窗口 (this)
QAction* action_font = new QAction("字体", this);

// 设置该 QAction 可被选中（即可切换状态），用于表示字体的开/关状态
action_font->setCheckable(true);

// 将 QAction 添加到工具栏 ui->toolBar 中，使其在工具栏上可见
ui->toolBar->addAction(action_font);

// 连接 QAction 的 toggled 信号到 MainWindow 类的 settextfont 槽函数
// 当 action_font 的选中状态发生改变时，将自动调用 settextfont 方法
connect(action_font, &QAction::toggled, this, &MainWindow::settextfont);

```
下面是槽函数
```
void MainWindow::settextfont(bool check)
{
    if(check){
        QTextCursor curson=ui->textEdit->textCursor();
        QTextBlockFormat blockformat;
        blockformat.setAlignment(Qt::AlignCenter);
        curson.insertBlock(blockformat);
        QTextCharFormat charformat;
        charformat.setBackground(Qt::lightGray);
        charformat.setForeground(Qt::green);
        charformat.setFont(QFont(tr("宋体"),12,QFont::Bold,true));
        charformat.setFontUnderline(true);
        curson.setCharFormat(charformat);
        curson.insertText("插入字体");
    }
    else{
        QTextCursor curson=ui->textEdit->textCursor();
        QTextBlockFormat blockformat;
        blockformat.setAlignment(Qt::AlignLeft);
        curson.insertBlock(blockformat);
        QTextCharFormat charformat;


        curson.setCharFormat(charformat);
        curson.insertText("插入字体");
    }
}
```
## 插入表格列表图片
```
    QAction* action_table=new QAction("表格",this);
    QAction* action_list=new QAction("列表",this);
    QAction* action_image=new QAction("图片",this);
    ui->toolBar->addAction(action_table);
    ui->toolBar->addAction(action_list);
    ui->toolBar->addAction(action_image);
    connect(action_list,&QAction::triggered,this,&MainWindow::insertlist);
    connect(action_image,&QAction::triggered,this,&MainWindow::insertimage);
    connect(action_table,&QAction::triggered,this,&MainWindow::inserttable);
```
实现对应槽函数
```
void MainWindow::inserttable()
{
    QTextCursor cursor=ui->textEdit->textCursor();
    QTextTableFormat format;
    format.setCellSpacing(10);
    format.setCellSpacing(2);
    cursor.insertTable(2,2,format);
}

void MainWindow::insertimage()
{
    QTextImageFormat format;
    format.setName(":/20241005203041.jpg");
    ui->textEdit->textCursor().insertImage(format);
}

void MainWindow::insertlist()
{
    QTextListFormat format;
    format.setStyle(QTextListFormat::ListDecimal);
    ui->textEdit->textCursor().insertList(format);
}

```
## 实现查找功能
```
 QAction* actiontextfind=new QAction("查找",this);
    ui->toolBar->addAction(actiontextfind);
    connect(actiontextfind,&QAction::triggered,this,[this](){dialog->show();});
    dialog=new QDialog(this);
    lineedit=new QLineEdit(dialog);
    QPushButton* btn=new QPushButton(dialog);
    btn->setText("查找下一个");
    connect(btn,&QPushButton::clicked,this,&MainWindow::textfind);
    QVBoxLayout* layout=new QVBoxLayout( );
     layout->addWidget(lineedit);
    layout->addWidget(btn);
     dialog->setLayout(layout);
```
对应槽函数
```
void MainWindow::textfind()
{
    auto string=lineedit->text();
    bool isfind=ui->textEdit->find(string,QTextDocument::FindBackward);
    if(isfind){
        qDebug()<<tr("行号%1 列号%2 ").arg(ui->textEdit->textCursor()
                                                 .blockNumber()).arg(ui->textEdit->textCursor().columnNumber());
    }
}
```
