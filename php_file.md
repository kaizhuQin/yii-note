### 关于php文件I/O操作的相关记录   

####[fopen](http://cn2.php.net/manual/en/function.fopen.php)   

```php
fopen($filename, $mode)
```
mode类型：
`'r' `只读方式打开，将文件指针指向文件头。   
`'r+'` 读写方式打开，将文件指针指向文件头。   
`'w' `写入方式打开，将文件指针指向文件头并将文件大小截为零。如果文件不存在则尝试创建之。   
`'w+'` 读写方式打开，将文件指针指向文件头并将文件大小截为零。如果文件不存在则尝试创建之。   
`'a' `写入方式打开，将文件指针指向文件末尾。如果文件不存在则尝试创建之。   
`'a+'` 读写方式打开，将文件指针指向文件末尾。如果文件不存在则尝试创建之。   
`'x' `创建并以写入方式打开，将文件指针指向文件头。如果文件已存在，则 fopen() 调用失败并返回 FALSE。   
`'x+'` 创建并以读写方式打开，将文件指针指向文件头。如果文件已存在，则 fopen() 调用失败并返回 FALSE。   

```php
$handle = fopen('./log.txt','a+');
foreach ($result as $key => $data) {
$excel->addRow($data);
fwrite($handle,$data[0]." ".$data[12]."\r\n");
fclose($handle);
```