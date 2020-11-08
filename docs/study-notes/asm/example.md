# 汇编入门

**先看例子知道怎么玩**：

```asm
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



然后我们来实现难一点的程序：两个两位数相加的结果输出问题

```asm
DATAS SEGMENT
    ;此处输入数据段代码  
    data1 db 255 dup('$')
    data2 db 255 dup('$')
    input1 db 'please input the first number: ','$'
    input2 db 'please input the second number: ','$'
    entering db 0DH,0AH,'$'
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
    
    lea dx,input1
    mov ah,9
    int 21h
    
    lea dx,data1
    mov ah,10
    int 21h
    
    lea dx,entering
    mov ah,9
    int 21h
    
    MOV BL, [data1+2];十位
	SUB BL,'0'
	MOV BH, [data1+3];个位
	SUB BH,'0'
	
	;输入第二数
	lea dx,input2
	mov ah,9
	int 21h
	
	lea dx,data2
	mov ah,10
	int 21h
	
	lea dx,entering
	mov ah,9
	int 21h
	
	mov cl,[data2+2]
	sub cl,'0'
	mov ch,[data2+3]
	sub ch,'0'
	
	;直接开加
	add bl,cl
	add bh,ch
	
	;判断进位与否
	cmp bh,10
	jge single_add

;对于十位进位的处理
tens_add:
	cmp bl,10
	jge input_num
	jmp input_num2
	
;对于个位进位的处理
single_add:
	sub bh,10
	add bl,1
	jmp tens_add
	
input_num:
	sub bl,10
	mov dl,31h ;调用2号中断，输出最高位的数字
	mov ah,02h
	int 21h
	
input_num2:
	mov dl,bl
	add dl,30h
	mov ah,02h ;输出十位上的数字
	int 21h
	
	mov dl,bh
	add dl,30h
	mov ah,02h 	;输出个位上的数字
	int 21h
    
    MOV AH,4CH
    INT 21H
CODES ENDS
    END START
```



*对输入的字符串进行词频统计*

```asm
DATAS SEGMENT
    ;此处输入数据段代码  

	inputer db 'please input the string: $'
	buff db 18,?,18 dup(?)
	entering db 0ah,0dh,'$'
	outnumber db 'the numbers: $'
	uppercase db 'the uppercase letters: $'
	lowercase db 'the lowercase letters: $'
	otherletter db 'the other letters: $'
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
    lea dx,inputer
    mov ah,9
    int 21h
    
    lea dx,buff
	mov ah,10
	int 21h
	
	lea dx,entering
	mov ah,9
	int 21h
	
	;读入要查找的字符个数
	mov cl,buff
	xor ch,ch
	;bh专门用来存储除其他字符的个数
	xor bh,bh
	lea si,buff+2
	xor dl,dl
	
num_again: 
	mov al,[si]
	cmp al,30h
	jl num_next
	cmp al,39h
	jg num_next
	inc dl
	
	num_next: inc si
	loop num_again
	;统计个数记录在dl中
	mov bl,dl
	add bh,bl
	
	;换行
	lea dx,entering
	mov ah,9
	int 21h
	
	;数字输出提示
	lea dx,outnumber
	mov ah,9
	int 21h
	
	add bl,30h
	mov dl,bl
	mov ah,2
	int 21h
	;显示，只能是10以内的数，如果超过10，就要用除以10再分别显示了
    
    mov cl,buff
	xor ch,ch
	lea si,buff+2
	xor dl,dl
	
up_again: 
	mov al,[si]
	cmp al,41h
	jl up_next
	cmp al,5ah
	jg up_next
	inc dl
	
	up_next: inc si
	loop up_again
	;统计个数记录在dl中
	mov bl,dl
	add bh,bl
	
	;换行
	lea dx,entering
	mov ah,9
	int 21h
	
	;数字输出提示
	lea dx,uppercase
	mov ah,9
	int 21h
	
	add bl,30h
	mov dl,bl
	mov ah,2
	int 21h
	
	mov cl,buff
	xor ch,ch
	lea si,buff+2
	xor dl,dl
	
low_again: 
	mov al,[si]
	cmp al,61h
	jl low_next
	cmp al,7ah
	jg low_next
	inc dl
	
	low_next: inc si
	loop low_again
	;统计个数记录在dl中
	mov bl,dl
	add bh,bl
	
	;换行
	lea dx,entering
	mov ah,9
	int 21h
	
	;数字输出提示
	lea dx,lowercase
	mov ah,9
	int 21h
	
	add bl,30h
	mov dl,bl
	mov ah,2
	int 21h
	
	lea dx,entering
	mov ah,9
	int 21h
	
	;其他字符的个数统计
	lea dx,otherletter
	mov ah,9
	int 21h
    
    ;用cl减去bh就得到其他字符
    mov cl,buff
    xor ch,ch
	lea si,buff+2
	xor dl,dl
	
other_again: 
	mov al,[si]
	cmp al,21h
	jge other_next
	inc dl
	
	other_next: inc si
	loop other_again 
    
    ;这里的算术关系有点复杂
    ;是用字符总个数减去非字符个数，再减去非其他字符个数，得其他字符个数
    mov cl,buff
    sub cl,bh
    sub cl,dl
    
    add cl,30h
    mov dl,cl
    mov ah,2
    int 21h
    
    MOV AH,4CH
    INT 21H
CODES ENDS
    END START
```
