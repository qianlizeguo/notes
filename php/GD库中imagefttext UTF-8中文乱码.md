##imagefttext中文乱码
虽然 imagettftext()文档标明只接受UTF-8编码，但如果PHP编译时启用–enable-gd-jis-conv选项的话，那么非ASCII字符(例如汉字、拼音、希腊文和箭头) 会被当成EUC-JP编码(phpinfo中美其名曰“支持JIS编码的字体”), 从而导致乱码(由于西文字体没有假名或汉字，一般表现为全部是方框)。

php 编译时不要–enable-gd-jis-conv选项
如果已经编译需要重新编译，重新解压包