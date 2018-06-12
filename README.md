%include "io.inc"

;macro para imprimir texto
%macro imprimir_string 1
push %1     ;pega formatacao de saida
call printf ;chama printf para jogar na tela
add esp,4   ;limpa pilha de memoria
%endmacro

;macro para imprimir resultado final
%macro imprimir_resultado 1
 push eax    ;pega o resultado da subrotina
 push %1     ;pega formatacao de saida
 call printf ;chama printf para jogar na tela
 add esp, 8  ;limpa pilha de memoria
%endmacro

SECTION .data

       ;Labels para perguntas/pedir entrada/gerar saida
       askTriangulo: db 'Digite 1 Para calcular area do triangulo', 10,0.
       askTrapezio:  db 'Digite 2 Para calcular area do Trapezio',10,0.
       askRetangulo: db 'Digite 3 para calcular area do retangulo',10,0.
       askBase:      db 'Entre com o valor da base',10,0.
       askBaseMaior: db 'Entre com o valor da base maior',10,0.
       askBaseMenor: db 'Entre com o valor da base menor',10,0.
       askAltura:    db 'Entre com o valor da altura',10,0.
       resTriangulo: db "A area do triangulo e %d" ,10 ,0.
       resTrapezio:  db "A area do trapezio e %d" ,10 ,0.
       resRetangulo: db "A area do retangulo e %d" ,10 ,0.
       msgFinal:     db "Aperte 1 para finalizar",10,0
       formatin: db "%d",0
       
       ;variavel global que guarda figura escolhida
       escolha: times 4 db 0 ;32 bits inteiro 4by
       
       ;variaveis globais para realizar as contas
                area: times 4 db 0 ;32 bits inteiro 4by
       basebasemaior: times 4 db 0
           basemenor: times 4 db 0
              altura: times 4 db 0
      
section .text
    extern scanf
    extern printf
    
global CMAIN
CMAIN:
    mov ebp, esp; for correct debugging
    ;imprimindo o menu na tela
    imprimir_string askTriangulo
    imprimir_string askTrapezio
    imprimir_string askRetangulo
    
    ;pedindo para entrar com a escolha
    push escolha
    push formatin
    call scanf
    add esp, 8
    
    ;acessando o valor da variavel, pra comparacao
    mov ebx, [escolha]
    
    ;comparando o valor com o n1
    jmp else_block1

     if_block1: 
      
       ;pedindo para entrar valor da base
       imprimir_string askBase
       push basebasemaior
       push formatin
       call scanf
       add esp, 8
       
       ;pedindo para entrar valor da altura
       imprimir_string askAltura
       push altura
       push formatin
       call scanf
       add esp, 8
      
      ;chama subrotina para calculo do triangulo
      CALL TRIANGULO
      
      ;imprime o resultado na tela
      imprimir_resultado resTriangulo
      
      ;chama macro para imprimir mensagem de saida do console
      imprimir_string msgFinal
      push formatin
      call scanf
      add esp, 8
          
      jmp end_if
      
      else_block1:
           ;comparando o valor com o n1
           cmp ebx, 1  
           je if_block1 ;se for igual a 1 pula para if_block 1
           jmp else_block2 ;se não for, pula para else_block 2

     if_block2:
     
        ;pedindo para entrar valor da base maior
       imprimir_string askBaseMaior
       push basebasemaior
       push formatin
       call scanf
       add esp, 8
       
       ;pedindo para entrar valor da base menor
       imprimir_string askBaseMenor
       push basemenor
       push formatin
       call scanf
       add esp, 8
       
       ;pedindo para entrar valor da altura
       imprimir_string askAltura
       push altura
       push formatin
       call scanf
       add esp, 8
       
      ;Chamando Subrotina para calcular area do trapezio
      CALL TRAPEZIO
      
      ;imprime o resultado na tela
      imprimir_resultado resTrapezio
      
      ;chama macro para imprimir mensagem de saida do console
      imprimir_string msgFinal
      push formatin
      call scanf
      add esp, 4
      
     jmp end_if
          else_block2:
           ;comparando o valor com o n2
           cmp ebx, 2
           je if_block2 ;se for igual a 2 pula para if_block 2
           jmp else_block3 ;se não for, pula para else_block 3
           
     
     if_block3:  
      
       ;pedindo para entrar valor da base
       imprimir_string askBase
       push basebasemaior
       push formatin
       call scanf
       add esp, 8
       
       ;pedindo para entrar valor da altura
       imprimir_string askAltura
       push altura
       push formatin
       call scanf
       add esp, 8
       
       ;Chamando Subrotina para calcular area do retangulo
       CALL RETANGULO
      
       ;imprime o resultado na tela
       imprimir_resultado resRetangulo
       
      imprimir_string msgFinal
      
      push formatin
      call scanf
      add esp, 4
       
      jmp end_if
           else_block3:
           ;comparando o valor com o n3
           cmp ebx, 3
           je if_block3 ;se for igual a 3 pula para if_block 3
           jmp else_block1 ;se não for, volta para else_block 1
           
     end_if:
      
     
    xor eax, eax
    ret
    
;subrotina para calcular a area do triangulo    
TRIANGULO:     
        mov ebx,[basebasemaior] ;manda valor escolhido da base
        mov eax,[altura]        ;manda valor escolhido da altura
        mul ebx                 ;eax = b * a  
        mov ebx, 2              ;diz que valor sera dividido por 2
        div ebx                 ;ebx = eax/2;
        
        mov [area], eax         ;joga valor do eax no [area] para saida
        ret 

;subrotina para calcular a area do retangulo
RETANGULO:

        mov ebx,[basebasemaior] ;manda valor escolhido da base
        mov eax,[altura]        ;manda valor escolhido da altura
        mul ebx                 ;eax = b * a      
        mov ebx, 2              ;diz que valor sera dividido por 2
        div ebx                 ;ebx = eax/2;
        
        mov [area], eax         ;joga valor do eax no [area] para saida
        ret     
        
;subrotina para calcular a area do trapezio           
TRAPEZIO:
        mov eax,[basemenor]     ;manda valor escolhido da base menor
        mov ebx,[basebasemaior] ;manda valor escolhido da base maior
        add eax, ebx            ;eax = (bma + bme)     
        mov ecx,[altura]        ;manda valor escolhido da altura
        mul ecx,                ;ecx = ecx * eax
        mov ecx, 2              ;diz que valor sera dividido por 2
        div ecx,                ;eax = eax/2
        
        mov [area], eax         ;joga valor do eax no [area] para saida
   
        ret
