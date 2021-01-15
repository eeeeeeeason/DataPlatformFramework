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
  - print 只能一次打印一行自带回车符，不同字段可能会分行，但printf可以共同在一行内打印，print 中不能使用%s ,%d 或%c
  - awk执行文件内容,   执行时 awk -f calc.awk 数据文件.txt
    - 1.分隔内容为空格时不需要填写分隔符与-F awk '{print $1,$2,$3}' stu.txt
    - 2.内容若以:分隔，需要填写分隔符 awk -F ':' '{print $1,$2,$3}' test_awk2.txt
    - 3.拼接分隔内容，上述print中修改 '{print $1"#"$2"#"$3}' 
      - 3.1 拼接法2，指定OFS awk -F ':' '{OFS="#"}{print $1,$2,$3}' test_awk.txt
    - 4.awk内使用正则相关，正则
      - 匹配
- shell
  - shell 中引号的使用
     - 单引号内变量直接原样输出，不论是否为变量
     - 双引号内$变量会去找整串匹配的内容，需要拼接的字符串可以直接写在括号外边
     - 
  - shell 中的括号使用 
     - echo中要上大括号 ${变量名}
     - 直接输出只用 $变量名
     - "${变量名}abcabc"
     - '${变量名}'单引号内存在该参数时内容会视为字符串输出
     - 处理数字类型时，1=9,10=2 ,读取$1 得9 但读取$10 会得到 90 而取不到2， 只有用${10}才能取到2
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
  - 算术运算
     - expr来进行计算，计算过程中加`  如   `expr 2 + 2` 且+号运算符两侧要有空格，乘法要加转译\* 因为*在正则中有另外的含义
     - $((a+b)) 两个括号会被认为是一个加法运算而一个括号会认为a+b是个变量名
     - 逻辑比较eq,ne,gt,lt,ge,le
     ```shell
        if 条件
        then 命令f i
     ```
     - case
     ```shell
        case $num in
        模式1）
            echo "";;
        模式2)
            echo "";;
        *)
            echo "默认"
        esac;;
     ```
     - linux处理mysql备份
      ```shell
        #mysqldump -u root -p 123456 --host
            
    
