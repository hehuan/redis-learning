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

### GETBIT
<b>时间复杂度</b>：O(1)  
GETBIT key offset  
返回key对应的string在offset处的bit值
```bash
redis> GET foo
"\xff\xf0\x00"
redis> GETBIT foo 1
(integer) 1
redis> GETBIT foo 12
(integer) 0
# 查出范围的当作0
redis> GETBIT foo 30
```
#### 返回值
offset处的bit值

### GETRANGE
<b>时间复杂度</b>：O(N)  
GETRANGE key start end  
返回子串
```bash
redis> GET foo
"\xff\xf0\x00"
redis> GETRANGE foo 0 0
"\xff\xf0"
redis> GETRANGE foo 0 -1
"\xff\xf0\x00"
redis> GETRANGE foo -2 -1
"\xf0\x00"
```
#### 返回值
返回子串

### GETSET
<b>时间复杂度</b>：O(1)  
GETSET key value  
给key设置新值，返回之前的旧值  
```bash
redis> INCR counter
(integer) 1
redis> GETSET counter "0"
"1"
redis> GET counter
"0"
```
#### 返回值
返回key的旧值

### INCR
<b>时间复杂度</b>：O(1)  
INCR key  
key的值自加1  
#### 返回值
返回加1后的值

### INCRBY
<b>时间复杂度</b>：O(1)  
INCRBY key increment  
key自加increment
#### 返回值
自加increment后的key

### INCRBYFLOAT
<b>时间复杂度</b>：O(1)  
INCRBYFLOAT key increment  
INCRBY的浮点数版本
```bash
redis> GET val
"0.22"
redis> INCREBYFLOAT val 0.55
"0.77"
redis> GET vale
"2.3e-1"
redis> INCREBYFLOAT vale 0.79
"1.02"
```
#### 返回值
自加increment后的key

### MGET
<b>时间复杂度</b>：O(N)  
MGET key [key ...]  
相对于GET，MGET可以获取多个key的值
```bash
redis> set foo "bar"
OK
redis> set bar "foo"
OK
redis> mget foo bar
1) "bar"
2) "foo"
```
#### 返回值
返回对应value的list

### MSET
<b>时间复杂度</b>：O(N)  
MSET key value [key value ...]  
相比SET，支持多对key-value的设置
#### 返回值
设置成功返回OK

### MSETNX
<b>时间复杂度</b>：O(N)  
MSETNX key value [key value ...]  
功能同MSET，如果其中任意一个key存在，便不会进行操作  
```bash
redis> MSETNX foo bar bar foo
(integer) 1
redis> MGET foo bar
1) "bar"
2) "foo"
redis> MSETNX foo bar bar foo
(integer) 0
```

### PSETEX
<b>时间复杂度</b>：O(1)  
PSETEX key milliseconds value  
设置key的值为value，生命周期为milliseconds，以毫秒为单位
```bash
redis> PSETEX mykey 5000 "Hello"
OK
# 获取key的剩余生效时间
redis> PTTL mykey
(integer) 2611
# 如果还生效，返回key；失效返回nil
redis> GET mykey
"Hello"
```
#### 返回值
OK

### SET
<b>时间复杂度</b>：O(1)  
SET key value [EX seconds] [PX milliseconds] [NX|XX]  
设置key的值为value  
#### 选项
EX seconds – 设置键key的过期时间，单位时秒  
PX milliseconds – 设置键key的过期时间，单位时毫秒  
NX – 只有键key不存在的时候才会设置key的值  
XX – 只有键key存在的时候才会设置key的值  
SET命令加上这些选项，已经可以完成SETNX SETEX PSETEX的功能
#### 返回值
设置成功返回OK，设置失败返回nil

### SETBIT
<b>时间复杂度</b>：O(1)  
SETBIT key offset value  
对key的某一位进行置1或清0
#### 返回值
offset处原来的bit值

### SETEX
<b>时间复杂度</b>：O(1)  
SETEX key seconds value  
同PSETEX，PSETEX的单位是微妙，SETEX的单位是秒
#### 返回值
OK

### SETNX
<b>时间复杂度</b>：O(1)  
SETNX key value  
设置key的值为value，如果key已经存在，则不设置
#### 返回值
设置成功1，设置失败0


### SETRANGE
<b>时间复杂度</b>：O(1)  
SETRANGE key offset value  
设置offset处的子串为value
#### 返回值
新字符串的长度

### STRLEN
<b>时间复杂度</b>：O(1)  
STRLEN key  
返回key的长度
#### 返回值
字符串的长度
