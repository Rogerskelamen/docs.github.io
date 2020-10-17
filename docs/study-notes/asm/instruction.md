# 通用数据传送指令
```
MOV（Move）传送
PUSH（Push onto the stack）进栈
POP（Pop from the stack）出栈
XCHG（Exchange）交换
```

# 累加器专用传送指令
```
IN(Input) 输入
OUT(Output) 输出
XLAT(Translate) 换码
这组指令只限于使用累加器AX 或AL 传送信息
```

# 有效地址送寄存器指令
```
LEA(Load effective address)有效地址送寄存器
LDS(Load DS with Pointer)指针送寄存器和DS
LES(Load ES with Pointer)指针送寄存器和ES
```

# 标志寄存器传送指令
```
LAHF(Load AH with flags)标志送AH
SAHF(store AH into flags)AH 送标志寄存器
PUSHF(push the flags) 标志进栈
POPF(pop the flags) 标志出栈
```

# 算术指令
## 加法指令
```
ADD(add)加法
ADC(add with carry)带进位加法
INC(increment)加1
```

## 减法指令
```
SUB(subtract)减法
SBB(subtract with borrow)带借位减法
DEC(Decrement)减1
NEG(Negate)求补
CMP(Compare)比较
```

## 乘法指令
```
MUL(Unsigned Multiple)无符号数乘法
IMUL(Signed Multiple)带符号数乘法
```

## 除法指令
```
DIV(Unsigned divide)无符号数除法
IDIV(Signed divide)带符号数除法
CBW(Convert byte to word)字节转换为字
CWD(Contert word to double word)字转换为双字
```

# 逻辑指令
## 逻辑运算指令
```
AND(and) 逻辑与
OR(or) 逻辑或
NOT(not) 逻辑非
XOR(exclusive or)异或
TEST(test) 测试
```

## 移位指令
```
SHL(shift logical left) 逻辑左移
SAL(shift arithmetic left) 算术左移
SHR(shift logical right) 逻辑右移
SAR(shift arithmetic right) 算术右移
ROL(Rotate left) 循环左移
ROR(Rotate right) 循环右移
RCL(Rotate left through carry) 带进位循环左移
RCR(Rotate right through carry) 带进位循环右移
```

# 串处理指令
1. 与REP 相配合工作的MOVS,STOS 和LODS 指令
2. 与REPE/REPZ 和REPNZ/REPNE 联合工作的CMPS 和SCAS 指令.

# 控制转移指令
## 无条件转移指令
> JMP(jmp) 跳转指令

## 条件转移指令
### 根据单个条件标志的设置情况转移
```
JZ(或JE)(Jump if zero,or equal) 结果为零(或相等)则转移
JNZ(或JNE)(Jump if not zero,or not equal) 结果不为零(或不相等)则转移
JS(Jump if sign) 结果为负则转移
JNS(Jump if not sign) 结果为正则转移
JO(Jump if overflow) 溢出则转移
JNO(Jump if not overflow) 不溢出则转移
JP(或JPE)(Jump if parity,or parity even) 奇偶位为1则转移
JNP(或JPO)(Jump if not parity,or parity odd) 奇偶位为0则转移
JB(或JNAE,JC)(Jump if below,or not above or equal,or carry) 低于,或者不高于或等于,或进位
位为1则转移
JNB(或JAE,JNC)(Jump if not below,or above or equal,or not carry) 不低于,或者高于或者等于,
或进位位为0则转移
```

### 比较两个无符号数,并根据比较的结果转移
```
JB(或JNAE,JC)
JNB(或JAE,JNC)
JBE(或JNA)(Jump if below or equal,or not above) 低于或等于,或不高于则转移
JNBE(或JA)(Jump if not below or equal,or above) 不低于或等于,或者高于则转移
```

### 比较两个带符号数,并根据比较的结果转移
```
JL(或LNGE)(Jump if less,or not greater or equal) 小于,或者不大于或者等于则转移

JNL(或JGE)(Jump if not less,or greater or equal)不小于,或者大于或者等于则转移

JLE(或JNG)(Jump if less or equal,or not greater) 小于或等于,或者不大于则转移
```

### 测试CX 的值为0则转移指令
```
JCXZ(Jump if CX register is zero) CX 寄存器的内容为零则转移
```

# 循环指令
```
LOOP 循环指令
LOOPZ/LOOPE 当为零或相等时循环指令
```

# 子程序
```
CALL 调用指令
RET 返回指令
```

# 中断
```
INT 指令
INTO 若溢出则中断
IRET 从中断返回指令
```

# 处理机控制指令
## 标志处理指令
```
CLC 进位位置0指令(Clear carry)CF<-0
CMC 进位位求反指令(Complement carry)CF<-CF
STC 进位位置1指令(Set carry)CF<-1
CLD 方向标志置0指令(Clear direction)DF<-0
STD 方向标志置1指令(Set direction)DF<-1
CLI 中断标志置0指令(Clear interrupt)IF<-0
STI 中断标志置1指令(Set interrupt)IF<-0
```

## 其他处理机控制指令
```
NOP(No Opreation) 无操作
HLT(Halt) 停机
WAIT(Wait) 等待
ESC(Escape) 换码
LOCK(Lock) 封锁
```
> 这些指令可以控制处理机状态.这们都不影响条件码.