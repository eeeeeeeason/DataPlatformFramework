- 重定向 > & >>
- cut cat more less tail
- wc -l -w -c -n
- chmod +(-) rwx(读写执行，也可以用数值777，755，666) 文件名设置权限
  - 到当前目录sh 执行文件名，这样是不需要权限的，但直接运行.sh需要看权限
- awk文本处理命令
  - $0, $1, $2 ... $n
  - awk  -F(分隔符) '命令' 文件名
  - OFS指定固定的输出连接符，在命令行前后加{} 如: {OFS=':'}
  - 内容匹配： 学会基本正则表达式。通常整行匹配，命令行里只需要谢'/正则/'
  - awk执行文件内容,   执行时 awk -f calc.awk 数据文件.txt
      ```shell
        #头部
        #!/bin/awk -f
        #内容
        BEGIN{printf "%-6s %-6s %4d %8d %8d\n",$1,$2,$3,$3,$5,$3+$4+$5}
        #运行中
        {}
        #运行后
        END{}
- shell
  - shell 中的括号使用 
     - echo中要上大括号 ${变量名}
     - 直接输出只用 $变量名
     - "${变量名}abcabc"
     - '${变量名}'单引号内存在该参数时内容会视为字符串输出
  - 常用命令
     - echo ${变量名:num1:num2} num1表示开始索引，num2表示长度
     - echo `expr index "$string" am`   a或m的最开始出现位置，expr为表达式计算工具
     - cut
     - wc -c 字节数 ,h 可以转k,m
     - df -lh 
     - du -b查看硬盘大小,h 可以转k,m
  - 配置环境变量 
     - export SERVICE_HOST=www.itcast.cn
     - /etc/profile  1. export 变量名=变量值   2. source /etc/profile 
     - 使用 $SERVICE_HOST
     
    
