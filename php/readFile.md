###读取文件内容
```
<?php
define('DIR', dirname(__FILE__).'/');

handleDir(DIR);

function handleDir($hostDir)
{
    $filesnames = scandir($hostDir); //得到所有的文件

    foreach ($filesnames as $name) {
        if (in_array($name, [
            'readFile.php',
            'result.txt',
            '.',
            '..',
            '.idea',
            '.git',
            'readme.md'
        ])) {
            continue;
        }

        if (is_file($hostDir . $name) && strpos($name, '.php')) {
            file_put_contents(DIR . 'result.txt', file_get_contents($hostDir . $name), FILE_APPEND);
        } elseif (is_dir($hostDir . $name)) {
            handleDir($hostDir . $name . '/');
        }
    }
}

exit('success');
