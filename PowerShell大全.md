PowerShell是一种基于.NET平台的脚本语言，借助.NET环境的强大功能，通过PowerShell几乎可以控制计算机的一切。Windows中内置一个非常好用的PowerShell编辑及执行环境 - Windows PowerShell ISE。强大的提示功能使编写PowerShell脚本就像在安装有ReSharper的Visual Studio中编写C#那样流畅、方便且智能。  
通过Visual Studio Code安装PowerShell插件，也可以方便的编写PowerShell脚本。  
PowerShell脚本的语法与C#有一点点形似，有C#基础的同学可以很容易掌握PowerShell脚本编程。  
  
### 常用命令 ###  
  
`alias` 显式命令的别名  
`echo` 输出，`Write-Output`的别名  
`man` 查看命令帮助，`help`的别名  
  
`sleep` 休眠等待指定秒数 `Start-Sleep`的别名  
  
  
`Get-Host` 获取宿主(PowerShell宿主)信息，作用即查看当前运行的PowerShell的版本  
  
**文件相关**  
`New-Item` 缩写`ni` 新建一个项目 `New-Item Path&Name` 选项 `-ItemType directory/file`新建目录还是文件 `-force` 是否在文件存在时覆盖  
`get-childitem` 获取文件集合   
`get-content` 获取文件内容  
`set-content` 写入文件内容  
  
### 惯用法 ###  
  
### 管道 ###  
  
`|` 管道，用于将一个命令的输出传递到`|`的下一个命令  
  
**管道命令**  
  
`select` 用于投影管道输入中的部分列  
`where {condition}` 缩写 `? {condition}` 指定过滤管道输入条件  
`foreach {condition}` 缩写 `% {condition}` 循环处理管道输入中的每一项  
  
### 运算 ###  
  
**逻辑运算**  
  
这些逻辑运算符所接受的变量都有两种情况，**值与值**的计算，**集合与值**的计算。这两种逻辑运算方式有比较大的差别，注意区分。  
  
`-eq` 判断是否等于(equal) `-ieq` 大小写不敏感，忽略大小写(case-insensitive) `-ceq` 大小写敏感(case-sensitive) 左为集合则返回与右侧相等的成员，负责比较左右并返回Bool值  
`-ne` 判断是否不等于(no equal)  
`-lt` 判断时候小于(less than)  
`-gt` 判断是否大于(greater than)  
`-ge` 判断是否大于或等于(greater of equal)  
`-le` 判断是否小于或等于(less or equal)  
  
`-contains` 包含，多用于集合与元素的判断  
`-notcontains` 不包含，多用于集合与元素的判断  
  
**布尔运算符**  
  
`-and` 和  
`-or` 或  
`-xor` 异或  
`-not` 取反  
  
### 重定向 ###  
  
**常用重定向运算**  
  
`>` 将输出发送到指定文件  
`>>` 将输出追加到指定的文件  
`2>` 将输出中的错误发送到指定的文件  
`2>>` 将输出中的错误追加到指定的文件  
`2>&1` 将错误输出和正确输出组合输出到正确输出（因为正常情况下，正确输出会被显示，但错误输出不会）。与Linux Shell同写法类似（但不完全一样）  
`3>` 将警告信息重定向到指定的文件  
`4>>` 将输出的详细信息追加到指定的文件。  
`5>&1` 将调试信息输出到成功流  
`*>` 将所有信息(5种类别)重定向到指定的文件  
  
**输出类别**  
  
`1` 输出/成功输出(Output/Success)  
`2` 错误信息(Error)  
`3` 警告信息(Warning)  
`4` 详细信息(Verbose)  
`5` 调试信息(Debug)  
  
### 变量 ###  
  
PowerShell变量是大小写不敏感的（几乎整个PowerShell都是大小写不敏感的），`$a`与`$A`被认为是相同的变量  
  
`$a=1` 变量不需要特殊方式声明，使用`$`加名字就定义了一个变量。如果变量名字有特殊字符，使用`{}`将变量名字包裹起来。使用`=`对变量赋值，变量可以连续赋值，如`$a=$b=1`  
  
> 不像Cmd或Linux Shell设置变量时`=`左右不能有空格，PowerShell允许变量赋值的`=`左右有空格。个人感觉这是很大的进步。  
  
`$a` 直接写变量就会将变量输出到标准输出，也可以直接使用变量进行数值运算等  
获取用户输入：`$a=Read-Host`  
  
**参数**  
  
`$args` 数组，表示参数变量，一种是传递给脚本文件的参数，另一种是传递给PowerShell函数的参数。以索引方式使用，如`$args[0]`  
  
**预置变量**  
  
`$_` 表示当前管道对象，一般用在代码段，过滤器等中，如`command | ? {$_ -match "somestr"}`  
`$?` 上个语句的状态，成功或失败  
`$^` 输入到shell最后一行的第一个词语  
`$$` 输入到shell最后一行的最后一个词语  
`$null` 表示null的变量  
`$true` 布尔值true  
`$false` 布尔值false   
  
**字符串**  
  
字符串使用双引号，不能使用单引号  
字符串字面量使用`@@`包围表示，如`$s=@"this is string"@`，字符串字面量中可以有换行等  
  
### 字符串 ###  
  
字符串插值，即在字符串中嵌入变量，使用`$()`  
如：`echo "Hello $($var)"`  
  
### 操作符 ###  
  
`-replace` 替换文本，用法 `"stringcontent" -replace "oldstr","newstr"`，在一个命令中可以设置多个-replace  
  
### 基本语法 ###  
  
**判断 - if系**  
  
语法：  
  
    if (expression1)  
    {  
       Statement(s)  
    }  
    elseif (expression2)  
    {  
       Statement(s)  
    }  
    else  
    {  
       Statement(s)  
    }  
  
**判断 - switch系**  
  
    switch (expression)  
    {  
      val1 {Statement}  
      val2 {Statement}  
      default {Statement}  
    }  
  
如果把分支放在同一行中，每个分支间使用逗号分隔  
  
**循环**  
  
`continue` 继续循环  
`break` 跳出循环  
  
**for系**  
  
    for (<init> ,<condition>; <repeat>)  
    {  
       Statement(s)  
    }  
  
**while系**  
与所有语言的while一致  
  
    while(condition)  
    {  
        Statement(s)  
    }  
  
**do-while系**  
只要条件满足，循环会一致运行  
  
    do  
    {  
        Statement(s)  
    }  
    while(condition)  
  
**do-until系**  
直到条件满足循环才停止  
  
    do  
    {  
        Statement(s)  
    }  
    until(condition)  
  
> 如果语句是一行，可以把整个循环写到一行中  
  
### 脚本 ###  
  
PowerShell脚本是以ps1作为扩展名的文件。  
PowerShell脚本文件中可以编写函数等。  
  
基本原则：  
  
 - 以换行区分语句无需`;`，同一行内以`;`分隔语句  
 - 单行注释`#`，块注释`<# #>`  
   
  
**权限**  
执行PowerShell脚本需要权限，权限级别（称为执行策略ExecutionPolicy）有如下几类：  
  
 - Restricted 未显式指定情况下的默认策略(Win8,Win10)，这策略下可以执行单条命令，但不能执行所有脚本文件(.ps1及.ps1xml的配置文件和.psm1的模块文件)  
 - AllSigned 所有运行脚本无论本地还是远程都需要签名  
 - RemoteSigned 可以执行任意本地脚本，或已签名及取消阻止（使用Unblock-File）的来自Internet的脚本。(WinServer2012R2默认策略)，对于Win8，Win10更改执行策略时，这个也是最常用的  
 - Unrestricted 不限制未签名Internet脚本运行但会给出提示  
 - Bypass 不做任何限制  
 - Undefined 未定义，将采用对应系统的默认策略  
  
权限也有作用范围（Scope），即上面的执行策略影响的范围  
  
> 这些范围的优先级逐渐降低  
  
 - Process 只影响当前会话  
 - CurrentUser 仅对当前用户有效，配置存储于注册表  
 - LocalMachine 对当前计算机上所有用户有效  
  
也可以通过组策略配置PowerShell脚本的执行策略，详见[此文档](https://technet.microsoft.com/zh-CN/library/hh847748.aspx)。  
  
`Get-ExecutionPolicy -List` 查看所有范围对应的执行策略  
`Get-ExecutionPolicy` 当前会话采用的执行策略  
`Get-ExecutionPolicy -Scope CurrentUser` 查看指定范围的执行策略  
`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned [-Scope CurrentUser]` 更改执行策略，更改执行策略会有一个提示让用户确认  
`Set-ExecutionPolicy Undefined [-Scope CurrentUser]` 删除执行策略  
  
### 与Cmd交互 ###  
  
PowerShell与Cmd有很好的交互性。在PowerShell中可以使用很大一部分Cmd的应用，甚至是在管道中使用，如：  
  
    command1 | findstr some-string  
  
在管道中我们使用了Cmd中的`findstr`  
  
### CentOS7 安装PowerShell ###  
  
```  
curl https://packages.microsoft.com/config/rhel/7/prod.repo | sudo tee /etc/yum.repos.d/microsoft.repo #添加yum源  
sudo yum install -y powershell  
powershell #运行powershell  
```  
  
### 提示&技巧 ###  
  
1. PowerShell环境中要多用Tab自动补全功能，自动补全支持大小写不敏感的命令补全，非常好用。  
2. Linux中grep的等价实现  
  grep是Linux中非常常用的字符串过滤命令，在PowerShell中可以用三种方式实现等价功能：  
  1. `command | findstr string_to_find` 利用Cmd中的findstr  
  2. `command | Select-String string_to_find` 利用PowerShell原生Cmdlet  
  3. `command | ? {$_ -match "string_to_find"}` 利用管道命令`where`(缩写为`$`)，注意`"`不能省略  
3. 可以使用**\`**来将一条命令跨行编写，在`ps1`脚本中有效  
4. Linux中屏蔽输出一般使用`>/dev/null`，在PowerShell可以使用`>$null`实现同样效果，如果是管道可以这样：`| Out-Null`。另外`&>/dev/null`等价于`>$null 2>&1`  
  
