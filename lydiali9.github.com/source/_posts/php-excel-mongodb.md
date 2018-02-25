title: php导入excel到数据库———MongoDB
date: 2015-12-27 13:12:38
tags: ['php', 'excel', '导入', 'MongoDB', 'bom', 'xls', 'csv', 'xlsx', '编码']
categories: ['php']
---
时光飞逝，转眼间从前台转到后台也有四个月了；在这四个月里，没能为项目组分担太多的任务，但是也有很大的收货；起初在接到做excel文档导入的task时，没有想到会遇到这么多问题；但是随着一个一个问题被解决。此刻，多少都会有一些知识的积累。现在主要从以下几个方面进行总结：

1. 上传excel文档不同文件格式的类型
2. 什么是BOM以及去掉BOM的方法
3. csv，xls和xlsx文件格式的上传编码以及时间属性的文本类型处理方式
4. 如何高效的读取excel文档
5. redis的相关内容



### 1. 上传excel文档不同文件格式的类型
| filetype   | linux（wps）   |  window（wps） |
| :--------   | :-----:  | :----:  |
| csv        |   text/csv   |   application/vnd.ms-excel   |
| xls        | application/wps-office.xls |   application/vnd.ms-excel   |
| xlsx        | application/wps-office.xlsx |   application/vnd.openxmlformats-officedocument.spreadsheetml.sheet   |

### 2. 什么是BOM以及去掉BOM的方法
##### 2.1. 什么是BOM？
> 在Windows系统下用记事本之类的工具保存的编码为UTF-8的文本文件，此时在文件头前面会加几个不可见的字符（如：EF BB BF），这个就是BOM头；它占用三个字节，用来标示该文件属于UTF-8编码，一般的软件可以识别这种BOM头，但是PHP却无法识别，此时如果不去除这个BOM头，系统会默认这个文件就是UTF-8的编码，从而导致错误；

##### 2.2. 用PHP如何去除BOM？
```
/**
 * curl exec
 * @param string $url
 * @param bool $removeBom, weather remove BOM
 * @throws Exception
 */
 private function _exec($url, $removeBom = true)
 {
    Yii::trace('Curl excute curl ' . $url, 'system.ext.curl');
    curl_setopt($this->_ch, CURLOPT_URL, $url);
    $c = curl_exec($this->_ch);
    if (!curl_errno($this->_ch)) {
        if ($removeBom) {
            //remove the BOM header
            return substr($c, strpos($c, '{'));
        } else {
            return $c;
        }
    } else {
        throw new Exception(curl_error($this->_ch));
    }
    return false;
}

/**
 * Methord get
 * @param string $url
 * @param array $params
 * @param boot $removeBom, weather remove BOM, remove bom because download excel data
 */
 public function get($url, $params = array(), $removeBom = true)
 {
    $this->resetMethodOptions();
    curl_setopt($this->_ch, CURLOPT_HTTPGET, true);
    return $this->_exec($this->buildUrl($url, $params), $removeBom);
 }
 
 /**
  * Reset all the method options before sending requests
  * @return [type] [description]
  */
 private function resetMethodOptions()
 {
    curl_setopt($this->_ch, CURLOPT_PUT, false);
    curl_setopt($this->_ch, CURLOPT_POST, 0);
    curl_setopt($this->_ch, CURLOPT_CUSTOMREQUEST, NULL);
    curl_setopt($this->_ch, CURLOPT_HTTPGET, false);
 }
 // Remove bom when get file from qiniu.
 $qiniuFile = Yii::$app->curl->get($args['filePath'], [], false);
```

### 3. csv，xls和xlsx文件格式的上传编码以及时间属性的文本类型处理方式

##### 3.1 常用编码介绍

> UTF-8：是一种针对Unicode的可变长度字符编码，用1到6个字节编码UNICODE字符；允许含BOM，但通常不含BOM。是用以解决国际上字符的一种多字节编码，它对英文使用8位（即一个字节），中文使用24为（三个字节）来编码。UTF-8包含全世界所有国家需要用到的字符，是国际编码，通用性强。支持简体中文字、繁体中文字、英文、日文、韩文等语言（支持文字更广）；
#
> GBK：是GB2312的扩展,除了兼容GB2312外，它还能显示繁体中文等；而GB2312是中国规定的汉字编码，也可以说是简体中文的字符集编码;
 #
> ANSI：一种字符代码，为使计算机支持更多语言，通常使用 0x80~0xFF 范围的 2 个字节来表示1个字符；不同的国家和地区制定了不同的标准，由此产生了 GB2312、GBK、GB18030、Big5、Shift_JIS等各自的编码标准。这些使用多个字节来代表一个字符的各种汉字延伸编码方式，称为 ANSI 编码。在简体中文Windows操作系统中，ANSI 编码代表 GBK 编码；在繁体中文Windows操作系统中，ANSI编码代表Big5；

注释：GBK、Unicode、Unicode big endian和UTF-8等编码之间的转换方式，使用Windows记事本的“另存为”，然后选择相应的编码保存方可；

##### 3.2 常用的文件格式

> CSV：由任意数目的记录组成，记录间用一些特殊的符号(如：逗号，制表符等)分割的以纯文本形式存储的表格数据（数字和文本）；每条记录由字段组成，字段间的分隔符是其它字符或字符串，最常见的是逗号或制表符。通常，所有记录都有完全相同的字段序列；使用的字符编码同样没有被指定，.csv文件默认是被Excel打开的，嵌入式换行符必须封闭在双引号字符；
#
> XLS和XLSX：就是 Microsoft Excel 工作表，是一种非常常用的电子表格格式;

##### 3.3 文件格式CSV、XLS和XLSX上传编码（以windows和linux系统为主）
| 项目   | 编码  |  bom  |
| :-------:   | :-----:  | :----:  |
| csv    | 必须为UTF-8  |   支持（代码中进行了处理）  |
| xls    |  ANSI(GBK) |   同上  |
| xlsx   |  同上 |   同上  |

> 注释：如果上传的文件为CSV格式，那么用户一定要手动设置上传CSV文件的编码为UTF-8；这是无法通过代码去识别并且进行转换的。
> 如果是linux用户，用记事本保存的.CSV文件默认编码为UTF-8；
> 如果是window用户，用记事本保存的.CSV文件默认是ANSI编码，也就是GBK编码；此时，可以通过记事本的"另存为"，然后选择要保存的编码UTF-8保存；
> 上传的以上三种格式的文件，不能通过修改文件的后缀名去保存文件，这样文件的后缀名改变了，但是文件的实质还是修改前文件保存的格式；


##### 3.4 时间的文本格式(以window为主)

 支持文本格式 YYYY-MM-DD

|text type | General |  text   | Custome | Date |
| :--------   | :-----:  |  :----:  | :----:  | :----:  |
| csv    | yes | yes | no | no |
| xls    | no | yes | no | no |
| xlsx   | no | yes | no | no |


 支持文本格式 MM/DD/YYYY

|text type | General | text | Custome | Date |
| :--------   | :-----:  |  :----:  | :----:  | :----:  |
| csv    | yes | yes | yes | yes |
| xls    | yes | yes | yes | yes |
| xlsx    | yes | yes | yes | yes |


建议：csv文件格式中的时间文本格式设为text；xls和xlsx文件格式中的时间文本格式设置date下的相应的类型；


### 4. 如何高效的读取excel文档
##### 4.1 如何读取excel文档以及编码的转换

```
$phpreader = new \PHPExcel_Reader_Excel2007();
// The path where the excel is stored.
$filePath = $args['locationPath']; 
if (!$phpreader->canRead($filePath)) {  // read xlsx
    $phpreader = new \PHPExcel_Reader_Excel5();

    if (!$phpreader->canRead($filePath)) { // read xls
        $phpreader= \PHPExcel_IOFactory::createReader('CSV') // read csv
        ->setDelimiter(',')
        ->setInputEncoding('UTF-8')
        ->setEnclosure('"')
        ->setLineEnding("\r\n")
        ->setSheetIndex(0);

        if (!$phpreader->canRead($filePath)) {
            // Failed to read excel.
            return false; 
        }
    }
}
$phpexcel = $phpreader->load($filePath);
```

##### 4.2 如何将excel文档转换为二维数组

 方法一：

```
/**
* Convert a comma separated file into an associated array.
* The first row should contain the array keys.
*
* Example:
*
* @param file $file to the CSV file
* @return array
*/
public function csvToArray($file, $length = 0, $delimiter = ',', $enclosure = '"', $escape = '\\')
{
   $data = [];
   if (($handle = fopen($file, 'r')) !== false) {
       while (($row = fgetcsv($handle, $length, $delimiter, $enclosure, $escape)) !== false) {
           $data[] = $row;
       }
       fclose($handle);
   } else {
       ResqueUtil::log(['message' => 'Fail to open excel file', 'data' => $file]);
   }
   return $data;
}

$fileArray = Yii::$app->file->csvToArray($filePath);// Call method.
```
优点：如果文件是以特殊符号分割的纯文本文件，可以用读取文件流的方式进行转换；效率非常高；
缺点：读取的数据中会出现一些特殊的符号，如“name”，读取之后为““name””，可能会导致一些错误；其次这种方式只能读取一种特殊符号分割的纯文本文件，如需读取制表符等符号分割的文件，每次都需要改动代码，代码的复用率比较低；

 方法二：

```
/**
 * Read excel and converted into an array.
 * The first row should contain the array keys.
 *
 * Example:
 *
 * @param PHPExcel_Reader_Excel2007 $phpreader
 * @param file $phpexcel
 * @param string $filePath
 * @return array
 */
public function excelToArray($phpreader, $phpexcel, $filePath)
{
    $data = [];
    $phpexcel = $phpreader->load($filePath);
    $currentSheet = $phpexcel->getSheet(0);
    $allColumn = $currentSheet->getHighestColumn();
    $allRow = $currentSheet->getHighestRow();
    ResqueUtil::log(['allRow:' => $allRow, 'allColumn:' => $allColumn]);

    $allColumnNum = \PHPExcel_Cell::columnIndexFromString($allColumn);
    for ($rowIndex = 1; $rowIndex <= $allRow; $rowIndex++) {
        $columnValue = [];
        for ($columnIndex = 0; $columnIndex < $allColumnNum; $columnIndex++) {
            $columnString =  \PHPExcel_Cell::stringFromColumnIndex($columnIndex);
            $value = trim((string)$currentSheet->getCell($columnString.$rowIndex)->getValue());
            $columnValue[] = $value;
        }
        $data[] = $columnValue;
    }
    return $data;
}
$fileArray = Yii::$app->file->excelToArray($phpreader, $phpexcel, $filePath);
```

注释：excel文件存储数据是以二维数组的形式存储的；为了提高效率，可以先将读取的excel转换为二维数组，接下来就可以对数组进行业务逻辑相关的处理，这样也可以使用数组相关的方法；同时，避免了很多读取excel的边界问题；

##### 4.3 读取excel行标的转换
excel的行标是以字母为标识的，如：A, B, C, D...AA, AB, AC等；

字母索引转换为数字索引
```
$allColumnNum = \PHPExcel_Cell::columnIndexFromString('A');
```

数字索引转换为字母索引
```
$columnString =  \PHPExcel_Cell::stringFromColumnIndex(3);
```
### 5. resque相关的内容
Redis 命令参考 » http://doc.redisfans.com/

