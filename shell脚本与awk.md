
- shell
  - 数据类型：字符串，数字
  - 字符串的命令
     ```shell
       string="i am a boy"
       echo ${string}    #i am a boy
       echo ${#string}   # 长度10
       echo ${string:2:2} # am
       echo `expr index "$string" am` #找，a或m所在的索引较前的位置
  - shell 中引号的使用
     - 单引号内变量直接原样输出，不论是否为变量
     - 双引号内$变量会去找整串匹配的内容辨识其中变量，需要拼接的字符串可以直接写在括号外边
  - 变量
     - 变量类型
     - 直接赋值，等号两侧不能为空格，不能数字开头，不能与关键字相同； 删除变量
     - 配置环境变量 
       - export SERVICE_HOST=www.itcast.cn
       - /etc/profile  1. export 变量名=变量值   2. source /etc/profile 
       - 使用 $SERVICE_HOST
     - 特殊变量 $?返回值 , $n 参数, $# 变量个数
  - shell 中的括号使用 
     - echo中要上大括号 ${变量名}
     - 直接输出只用 $变量名
     - "${变量名}abcabc"
     - '${变量名}'单引号内存在该参数时内容会视为字符串输出
     - 处理数字类型时，1=9,10=2 ,读取$1 得9 但读取$10 会得到 90 而取不到2， 只有用${10}才能取到2
  - 特殊的
     -  |管道符 将内容输入到下一个命令 ， 重定向 > & >>  , env
  - 常用命令
     - sh文件中可以用$(命令) 或者 `命令`得到输出。例 print"$(pwd)" 执行该.sh时会输出当前路径
     - cut cat more less tail
     - wc -c 字节数 ,h 可以转k,m
     - df -lh 
     - du -b查看硬盘大小,h 可以转k,m
     - chmod +(-) rwx(读写执行，也可以用数值777，755，666) 文件名设置权限 
       - 执行时到当前目录sh 执行文件名，这样是不需要权限的，但直接运行.sh需要看权限
     - ps -ef | grep mysql  
     - netstat -nltp | grep port或pid
     - lsof -i | grep pid   和 lsof -i:port
  - 输出参数
     - x.sh文件中 打印$1,$2,$3,$4 执行命令 sh x.sh  a b c d  , 可输出 a b c d,
     - $0
  - 算术运算
     - expr来进行计算，计算过程中加`  如   `expr 2 + 2` 且+号运算符两侧要有空格，乘法要加转译\* 因为*在正则中有另外的含义
     - $((a+b)) 两个括号会被认为是一个加法运算而一个括号会认为a+b是个变量名，还可以使用$[]来执行方括号内公式，
     - \* 由于*有特殊含义，当需要用于乘法时需要转译
     - 逻辑比较eq,ne,gt,lt,ge,le
     ```shell
        if 条件       # if [ 条件1 ] && [ 条件2 ] 如  if [ a -eq b ] && [ b -lt c ]
           then 命令
        elif
           then
        fi
     ```
     - 字符比较
        -  -z 长度判断是否为0
        -  -n 不为0
     - 文件处理比较
        -  -f 文件是否存在 ， -d看目录是否存在
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
     - for ((i=1;i<5;i++)) do done 或 for xx in yy
     - while
     ```shell
     while  # while : 表示无限循环，需要在特定位置 找出口 exit或break
       do
       fi
       done
     ```
     - 交互：select 配合ps3提示,read,
     - 死循环: while :   while(())  for(())
     - 函数: 定义方式： function fun()  fun(), 调用方式: 当前sh下直接敲函数名
        - 传参和接收参数、
     - 数组： arr=(a b c d)  ${arr[index]}
     
     - linux处理mysql备份
      ```shell
        #mysqldump -u root -p 123456 --host
     - 创建用户
      ```shell
      useradd eason
      passwd eason
      visudo
      # 第100行
      eason      ALL=(ALL)       ALL
      #创建es文件夹且给eason用户权限
      mkdir -p /export/server/es
      chown -R itcast:itcast /export/server/es
      ```
  - awk
   - awk文本处理命令,命令要注意内外引号的时候，外边单引号里边双引号
    - $0, $1, $2 ... $n
    - awk  -F(分隔符) '命令' 文件名
    - 管道符号|将前一个命令的输出作为下一个命令的输入
    - OFS指定固定的输出连接符，在命令行前后加{} 如: {OFS=':'}
      - OFS的内容要放在awk条件判断前 如 awk '{OFS=":"} $3>20 {print $2,$3}' 文件名.txt
    - 内容匹配： 学会基本正则表达式。通常整行匹配，命令行里只需要谢'/正则/'
    - print 只能一次打印一行自带回车符，不同字段可能会分行，但printf可以共同在一行内打印，print 中不能使用%s ,%d 或%c
    - awk执行文件内容,   执行时 awk -f calc.awk 数据文件.txt
      - 1.分隔内容为空格时不需要填写分隔符与-F awk '{print $1,$2,$3}' stu.txt
      - 2.内容若以:分隔，需要填写分隔符 awk -F ':' '{print $1,$2,$3}' test_awk2.txt
      - 3.拼接分隔内容，上述print中修改 '{print $1"#"$2"#"$3}' 
        - 3.1 拼接法2，指定OFS awk -F ':' '{OFS="#"}{print $1,$2,$3}' test_awk.txt
      - 4.awk内使用正则相关，
        - 匹配命令基本无差异，但执行的方法要在单引号内 ~表示匹配
        - awk '$2~/^t/ {print $2}' stu.txt     # 表示匹配分隔空格后第二组字段首字母以t开头的单词
        - awk '$3>80 {print $2}' stu.txt
      - 5.计算行号，字段数目
        - NR，NF awk '{print NR" "$0}' 显示行号
        - nl 文件.txt 直接输出行号配合滚动符号|可以精简许多程序内容   nl 文件名.txt|head -2



