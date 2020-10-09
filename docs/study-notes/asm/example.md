# 汇编入门

**先看例子知道怎么玩**：

```
DATAS SEGMENT
    ;此处输入数据段代码  
	
    STR1 DB 'please input a character:$'
    COUNT DW 29
    str2 db 0dh, 0ah,'please input a string:$'
    ;通过buf缓冲区存字符串
    buf db 81,0,81 dup(0)
DATAS ENDS

STACKS SEGMENT
    ;此处输入堆栈段代码
STACKS ENDS

CODES SEGMENT
    ASSUME CS:CODES,DS:DATAS,SS:STACKS
START:
    MOV AX,DATAS
    MOV DS,AX
    ;此处输入代码段代码
    
    ;输出变量里的字符串（9号调用）
    LEA DX, STR1
    MOV AH, 9
    INT 21H
    
    ;输入单个字符（1号调用）
    mov ah, 1
    int 21h
    
    ;输出单个字符（2号调用）（5号是打印机输出）
    ;mov dl, al
    ;mov ah, 2
    ;int 21h
    
    lea dx, str2
    mov ah, 9
    int 21h
    
    ;通过buf缓冲区输入字符串（10号调用）
    lea dx, buf
    mov ah, 10
    int 21h
    
    
    ;结束程序（4CH调用）
    MOV AH,4CH
    INT 21H
CODES ENDS
    END START
```





