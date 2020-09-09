在爬虫过程中出现`UnicodeEncodeError: 'gbk' codec can't encode character '\ufeff' in position 44085: illegal multibyte sequence`错误的解决方法

- 这个错误出现在write(html)方法运行时，说明读取网页的代码没有问题
- 首先查询读取网页的代码的编码方式是utf-8还是gbk
- 如果是window的是gbk在写入的时候不会出错，因为write()方法在windows系统下的默认编码是gbk，在Linux系统下是utf-8
- 所以在windows下的write(html,encoding="utf-8"),linux系统下write(html,encoding="gbk")，改成与默认相反的编码方式即可解决错误。