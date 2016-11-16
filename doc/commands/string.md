## string相关命令
### APPEND
<b>时间复杂度</b>：O(N)  
APPEND key value  
在字符串尾部添加内容  
```bash
redis> get foo
"bar"
redis> append foo "foo"
(integer) 6
redis> get foo
"barfoo"
```

### BITCOUNT
<b>时间复杂度</b>：O(N)  
BITCOUNT key [start end]  
统计字符串被设置为1的bit数量
```bash
redis> GET foo 
"bar"
# 统计foo中所有为1的bit之和
redis> BITCOUNT foo
(integer) 10
# 统计foo[0,0]
redis> BITCOUNT foo 0 0
(integer) 3
# 统计foo[0,1]
redis> BITCOUNT foo 0 1
(integer) 6
# 统计foo[0,-1] 符号代表倒数 -1是最后一个 -2是倒数第2个
# BITCOUNT foo 0 -1相当于BITCOUNT foo
redis> BITCOUNT foo 0 -1
(integer) 10
# 统计foo[-2,-1]
redis> BITCOUNT foo -2 -1
(integer) 7
```

### BITOP
<b>时间复杂度</b>：O(N)  
BITOP operation destkey key [key ...]  
#### 与
BITOP AND dest key1 [key2 key3 ...]
#### 或
BITOP OR dest key1 [key2 key3 ...]
#### 异或
BITOP XOR dest key1 [key2 key3 ...]
#### 取反
BITOP NOT dest key
ADN,OR,XOR都可以接受>=1个key的逻辑运算,最终的运算结果会被保存到dest中  
BITOP命令处理不同长度的字符串的时候,长度以最长的为准，长度不够的字符串在计算的时候不足的部分被当作0  
#### 返回值
dest的字符串长度,同时也是输入key中最长的字符串长度

### BITPOS
<b>时间复杂度</b>：O(N)  
BITPOS key bit [start] [end]  
返回字符串里第一个1或0的二进制位的位置  
```bash
#"bar"的二进制排列是
redis> GET foo
"\xff\xf0\x00"
redis> BITPOS foo 0
(integer) 12
redis> BITPOS foo 1
(integer) 0
redis> BITPOS foo 1 0 1
(integer) 0
#越界
redis> BITPOS foo 1 3 4
(integer) -1

redis> GET zero
"\x00x00"
redis> BITPOS zero 1
(integer) -1
redis> GET one
"\xffxff"
redis> BITOPS one 0
(integer) 16
```
####返回值
如果找到，返回的是找到1或0的bit位，如果没有找到1，返回-1,如果没有找到0，返回最高位

### DECR
<b>时间复杂度</b>：O(1)  
DECR key  
对key对应的数字做减1操作  
#### 返回值
减1后的value

### DECRBY
<b>时间复杂度</b>：O(1)  
DECR key decrement  
对key对应的数字做减decrement操作
#### 返回值
减decrement后的value

### GET
<b>时间复杂度</b>：O(1)  
GET key  
获取key对应的value

#### 返回值
返回对应的value或nil(value不存在)
