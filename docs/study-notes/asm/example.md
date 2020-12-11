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



## *对输入的字符串进行词频统计*

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



## *输入十个十进制数，输出逆序的十六进制数*

```asm
;数据段
datas SEGMENT
    arr dw 10 dup(?)
    inputer db 'please input ten numbers: $'
    res db 0dh,0ah,'result is:',0dh,0ah,'$'
datas ENDS

;堆栈段
stacks SEGMENT
    dw 100 dup(0)
stacks ENDS

;代码段
codes SEGMENT
    assume cs:codes,ds:datas,ss:stacks
;/*********************/
;主程序
main PROC far
start:
    mov ax,datas
    mov ds,ax
    ;此处输入代码
    ;提示输入
    lea dx,inputer
    mov ah,9
    int 21h

    ;设置输入10次的循环
    mov cx,10
    mov si,0
loop_input:
    call decibin
    mov arr[si],bx
    mov ax,2
    add si,ax
    loop loop_input

    ;用冒泡法进行排序
    mov ax,0
    call buble

    ;输出提示
    call crlf
    lea dx,res
    mov ah,9
    int 21h

    ;进行十个数的输出
    mov cx,10
    mov si,0
loop_output:
    mov bx,arr[si]
    call binihex
    call crlf
    mov ax,2
    add si,ax
    loop loop_output

    mov ah,4ch
    int 21h
main ENDP

;/**********************/
;子程序功能：采用冒泡法实现逆序排序
;入口参数：所有的数是在arr中存储的
;返回值：void
buble PROC

    mov ax,datas
    mov ds,ax

    mov cx,10
    dec cx
loop1:
    mov di,cx
    mov bx,0
loop2:
    mov ax,arr[bx]
    cmp ax,arr[bx+2]
    jge continue
    xchg ax,arr[bx+2]
    mov arr[bx],ax
continue:
    add bx,2
    loop loop2

    mov cx,di

    loop loop1

    ret
buble ENDP

;/******************************/
;子程序功能：输入十进制转化为内存中的数据
;入口参数：none
;返回值：输入的数字数据会储存在bx中，循环cx弹出
decibin PROC near
    push cx
    mov bx,0

newchar:
    mov ah,1
    int 21h
    sub al,30h
    jl exit     ;只要输入非数字字符就退出
    cmp al,9d
    jg exit
    cbw
    ;现在数字就在ax中
    xchg ax,bx
    mov cx,10d
    mul cx  ;乘十操作
    xchg ax,bx
    add bx,ax
    jmp newchar

exit:
    pop cx
    ret
decibin ENDP
;/************************/

;/************************/
;子程序功能：内存中的数据以十六进制输出
;入口参数：输出的数据会储存在bx中
;返回值：void
binihex PROC near
    push cx
    mov ch,4

rotate:
    mov cl,4
    rol bx,cl
    mov al,bl
    and al,0fh
    add al,30h
    cmp al,3ah
    jl printit
    add al,7h

printit:
    mov dl,al
    mov ah,2
    int 21h
    dec ch
    jnz rotate

    pop cx
    ret
binihex ENDP
;/*********************/

;/**********************/
;子程序功能：简单的换行功能
crlf PROC near
    mov dl,0dh
    mov ah,2
    int 21h

    mov dl,0ah
    mov ah,2
    int 21h

    ret
crlf ENDP
;/*******************/

codes ENDS
    end start
```

## 移除‘*’

```asm
;数据段
datas SEGMENT
    inputer db 'Please input the string: $'
    arr db 81,0,81 dup(?)
    res db 'the result:',0ah,0dh,'$'
    ui db '/*************************************/',0ah,0dh
       db 'Please choose one option to operate the string',0ah,0dh
       db '1. Remove the ahead *',0ah,0dh
       db '2. Remove the * in tail',0ah,0dh
       db '3. Remove the * in middle',0ah,0dh
       db '4. Remove all the *',0ah,0dh
       db '5. Quit the program',0ah,0dh
       db '/**************************************/',0ah,0dh
       db 'Please input your choice: $'
    quiting db 'Exiting$'
    quited db 'Exited!$'
datas ENDS

;堆栈段
stacks SEGMENT
    db 100 dup(0)
stacks ENDS

;代码段
codes SEGMENT
    assume cs:codes,ds:datas,ss:stacks

start:
;主程序
main PROC far
    mov ax,datas
    mov ds,ax

    ;进行提示输出
    lea dx,inputer
    mov ah,9
    int 21h

    lea dx,arr
    mov ah,10
    int 21h
    
showui:
    call crlf
    call crlf
    call callui

    mov ah,1
    int 21h
    mov bl,al

    call crlf
    call crlf

    mov al,bl
    cmp al,31h
    jz choice1
    cmp al,32h
    jz choice2
    cmp al,33h
    jz choice3
    cmp al,34h
    jz choice4
    cmp al,35h
    jz choice5

choice1:
    lea dx,res
    mov ah,9
    int 21h
    call choose1
    jmp showui
choice2:
    lea dx,res
    mov ah,9
    int 21h
    call choose2
    jmp showui
choice3:
    lea dx,res
    mov ah,9
    int 21h
    call choose3
    jmp showui
choice4:
    lea dx,res
    mov ah,9
    int 21h
    call choose4
    jmp showui
choice5:
    call quitdelay
    ;在quit子程序调用后，直接跳出循环
    call crlf

    ;中断指令，程序结束
    MOV AH,4CH
    INT 21H
main ENDP

;/****************************/
;子程序功能：实现缓慢退出效果
quitdelay PROC
    push cx
    push bx
    push si

    mov bx,5

    lea dx,quiting
    mov ah,9
    int 21h

inputagain:
    mov cx,1000h    ;使外层循环1000h次
lpagain:
    mov si,cx
    mov cx,2000h    ;内层循环2000h次
lpquit:
    loop lpquit

    mov cx,si
    loop lpagain

    mov dl,'.'
    mov ah,2
    int 21h

    dec bx
    cmp bx,0
    jnz inputagain

    call crlf
    lea dx,quited
    mov ah,9
    int 21h

    pop si
    pop bx
    pop cx
    ret
quitdelay ENDP

; /**************************/
;子程序功能：实现删除中间的*
; 先是将除了前面星号外的星号全部删除，然后补上后面的星号
choose3 PROC near
    push cx
    push bx
    push si
    push di

    ;首先初始化一波
    mov ax,0
    mov bx,0
    mov cx,0

again3:
    mov dl,arr[bx+2]
    mov ah,02h
    int 21h
    cmp arr[bx+2],'*'
    jnz delall
    inc bx
    jmp again3

delall:
    inc bx
    cmp arr[bx+2],'*'
    jz delall
    cmp arr[bx+2],20h
    jl nextadd
    mov dl,arr[bx+2]
    mov ah,02h
    int 21h
    jmp delall

nextadd:
    mov bx,0
    lea si,arr
    mov bl,[si+1]
    dec bl

circle:
    cmp arr[bx+2],'*'
    jnz exit3
    mov dl,'*'
    mov ah,02h
    int 21h
    dec bx
    jmp circle 

exit3:
    pop di
    pop si
    pop bx
    pop cx
    ret
choose3 ENDP

;/*************************/
;子程序功能：实现删除后面的*
choose2 PROC near
    push ax
    push cx
    push bx
    push si

    mov bx,0
    lea si,arr
    mov bl,[si+1]               ;这里将字符个数给si
    dec bl

again2:

    cmp arr[bx+2],'*'
    jnz count
    dec bx
    jmp again2

count:
    mov cx,bx
    inc cx
    mov bx,0

lp2:
    mov dl,arr[bx+2]
    mov ah,02h
    int 21h
    inc bx

    loop lp2

    pop si
    pop bx
    pop cx
    pop ax
    ret
choose2 ENDP

;/*************************/
;子程序功能：实现删除前面的*
choose1 PROC near
    push si
    mov si,2
    dec si

again1:
    inc si
    cmp arr[si],'*'
    jz again1

lp:
    cmp arr[si],20h
    jl exit1
    mov dl,arr[si]
    mov ah,2
    int 21h

    inc si
    jmp lp

exit1:
    pop si
    ret
choose1 ENDP

; /********************/
;子程序功能：删除所有的*
choose4 PROC near
    push si
    mov si,2
    dec si

again4:
    inc si
    cmp arr[si],'*'
    jz again4
    cmp arr[si],20h
    jl exit4

    mov dl,arr[si]
    mov ah,2
    int 21h
    jmp again4
exit4:
    pop si
    ret
choose4 ENDP

; /***********************/
;子程序功能：显示用户界面
;程序返回值：通过ax来返回一个用户选项
callui proc near
    lea dx,ui
    mov ah,9
    int 21h
callui endp

; /************************/
;子程序功能：简单的换行功能
crlf proc near
    push ax

    mov dl,0dh
    mov ah,2
    int 21h

    mov dl,0ah
    mov ah,2
    int 21h

    pop ax
    ret
crlf endp
; /**********************/

codes ENDS
    end start
```

## 乐曲播放（JOJO）

```asm
STACK SEGMENT    
    db 64 dup(0)
STACK ENDS

dseg SEGMENT para 'data'
    ;此处输入数据段代码  
mus_freq dw 175
         dw 311,262,208,175
         dw 311,262,208,175
         dw 311,262,208,175
         dw 294,311,330,349
         dw 311,262,208,175
         dw 311,262,208,175
         dw 311,262,208,175
         dw 294,311,330,349

         dw 262,311,330,349
         dw 262,311,330,349
         dw 262,311,330,349
         dw 311,349,370,349
         dw 262,311,330,349
         dw 262,311,330,349
         dw 262,311,330,349
         dw 311,349,370

         dw 175
         dw 208,262,349,392
         dw 415,466,494,523
         dw 494,415,349
         dw 262,523,494,415,349,262
         dw 415,311,294,-1

mus_time dw 30
         dw 8 dup(25,10,25,30)
         dw 7 dup(30,15,30,30)
         dw 30,15,30
         dw 160
         dw 60,50,45,160
         dw 30,20,15,30
         dw 15,25,35
         dw 15,25,30,25,20,40
         dw 30,15,100

dseg ENDS


cseg SEGMENT para 'code'
    ASSUME CS:cseg,DS:dseg,SS:STACK
music proc far 
    MOV AX,dseg
    MOV DS,AX
    ;此处输入代码段代码
    lea si,mus_freq
    lea bp,ds:mus_time
freq:
	mov di,[si]
	cmp di,-1
	je end_mus
	mov bx,ds:[bp]
	call soundf
	add si,2
	add bp,2
	jmp freq
end_mus:
    MOV AH,4CH
    INT 21H
music endp



soundf proc near

       mov al,0b6h
       out 43h,al
       mov dx,12h
       mov ax,348ch
       div di
       out 42h,al
       mov al,ah
       out 42h,al
       in al,61h
       mov ah,al
       or al,3
       out 61h,al
wait1:
       mov cx, 663*5
       call waitf
       dec bx
       jne wait1
       mov al,ah
       out 61h,al
       ret
soundf endp
;/***************************/
waitf proc near
       push ax
waitf1:
       in al,61h
       and al,10h
       cmp al,ah
       je waitf1
       mov ah,al

       loop waitf1
       pop ax 
       ret
waitf endp
cseg ENDS
    END music
```
