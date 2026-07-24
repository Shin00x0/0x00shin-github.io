---
layout: post
title:  "Vault Breaker"
date:   2026-06-24 03:54:26 -0300
categories: [reversing]
---

Buenas este es el primer post de mi blog donde subire el primer ctf resuelto asi que aqui les dejo el writeup de este ejercicio de ctf reversing.
El autor de es ShadowLegion's VaultBreaker

Bien vamos iniciando:
El ejercicio consiste en encontrar el codigo de acceso para desbloquear la aplicacion por lo que debemos usar algun programa podemos usar tenicas ya sea de analisis dinamico o analisis estatico.
voy a empezar un analisis estatico.

el archivo es una QAplicacion de Qt6 en este caso, como introduccion dabemos que una aplicaicon Qt esta hecha con el lenguaje de C++ eso significa que su lenguaje es C++.
*Nota: en este caso vamos a utilizar la herramienta de Ghidra para analisis estatico*

Primero haremos un filtrado por string dentro de ghidra en *windows->defined strings*
y vamos algunas strings que podrian ser interesantes.
encontramos dos
- ENTER ACCESS CODE
- VAULT UNLOJED!\Congratulations, you cracked

Al parecer parece ser un string de salida de texto, podemos construir mas arriba que valida este string.

*imagen*

Encontramos
```cpp
cVar2 = CheclPassword(this,(QString*)local_38);
```
vemos que se le esta pasando una varible *local_38* que al entrar dentro del checkPassword s QString, vamos que esta variable esta pasando como params_1 lo cambianos a *passu*

*imagen*

pasos para analisis final

Hay una variable interesante llamada (Encoded)
Analizando las instrucciones, vemos que esta en ensamblador x64

*Analizando*
tenemos la instruccion **mov** que compila el valor de la pila y lo mete en el registro rax, local_68 en el decompilado corresponde a un QArrayData, lo que significa que esta cargando direccion de memoria.

**movzx** (Esta instruccion es un move con Zero-expanded) toma el valor pequeño de 8 bits y lo copia en un registro mas grande EBP (que son de 32bits)

[R12 + RBX*0x1] (Estamos haciendo un direccionamieto indexado)
R12 es el inicio del **kencoded**
tenemos el contador que es el RBX y se multiplica por 0x1, al multiplicar significa que esta avanzando para indicar que el primer valor por ejemplo **kencoded** esta en R12. veamos que valor tiene el R12.
EBP giarda el caracter
el primer valor que tenemos en el registro r12 es el 0x15

Como solo avanzamos en la refireccion nos montamos un script en python para ir recorriendo esta direccion de memoria al aparecer esta es la clave **clave**.

su codigo hexadecomal es este x
```python
def main():
    pass

if __name__ == '__main__':
    main()
```