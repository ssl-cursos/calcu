# Analizador sintáctico con programas en Lex y Yacc

Durante el curso estudiamos gramáticas regulares, gramáticas independientes del contexto, la tabla de símbolos, las 3 etapas del compilador: análisis léxico, sintáctico y semántico.

Tenemos una calculadora sencilla que resuelve operaciones y permite definir variables.

Esta calculadora está hecha con dos herramientas que permiten construir scanners y parsers más generales que lo que vimos en el TP.

## Lex & Bison

Existe una herramienta llamada Lex que nos permite mediante ER consturir un scanner. En combinación con yacc (yet another compiler compiler) podemos construir un lexer y un parser y definirle la semántica que necesiten nuestro programa.

La implementación de yacc que usamos es bison.

Al igual que en Lex, un programa en Yacc se divide en 3 partes:

```bison
declaraciones
%%
reglas de producción
%%
código adicional
```

Vamos a estudiarlas.

### Declaraciones

La primera parte puede contener:

- Especificaciones escritas en el lenguaje destino, definidas entre%{ y %} (cada símbolo colocado al principio de
una línea)
- Declaraciones de tokens, con la palabra clave %token
- El tipo del terminal, con la palabra reservada %union
- Información sobre las prioridades de los operadores y su asociatividad.
- El axioma de la gramática, o símbolo inicial, usando la palabra reservada%start (si no se especifica, el axioma es la primera regla de la segunda parte del archivo).

La variable yylval, declarada mplicitamente en el tipo %union es importante ya que contiene la descripción del último token leído.

### Reglas de producción

Acá es donde ocurre la magia. Es donde yacc verifica las construcciones y permite interpretar el código. Puede contener:

- Declaraciones y/o definiciones encerradas entre %{ y %}.
- Reglas de producción de la gramática.

Las reglas de producción se definen así:

```
expresion_no_terminal:
    cuerpo_1 { sentencias acciones 1 }
    | cuerpo_2 { sentencias acciones 2 }
    | ...
    :
    | cuerpo_n { sentencias acciones n }
    ;
```

### Código adicional

Esta parte contiene código adicional. En nuestro caso debe contener la funciónmain() que debe llamar a la función yyparse(), y una función yyerror(char *mensaje), que será llamada cuando ocurra un error de sintáxis.

## La actividad

### Requisitos

Para hacer el trabajo necesitamos:

- gcc o clang
- flex
- bison
- make

### Puesta en marcha

Clonamos el repositorio y corremos:

```
bison -d calcu.y
flex -ocalcu.c calcu.l
```

Si estoy en Windows: ¡agregar \. delante de los nombres de archivo calcu. y calcu.l!

Verificamos que existan los archivos calcu.tab.c, calcu.tab.h y calcu.c.

Si existen, podemos compilar nuestro programa:

```
gcc calcu.tab.c calcu.c -o calcu -lm 
```

Y luego podemos verificar si tenemos un archivo calcu (en windows calcu.exe)

Lo corremos y le enviamos operaciones.

### Calcu

Calcu es una calculadora simple que acepta variables. Estas variables son letras de la 'a' a la 'z'

Supongamos que le enviamos la siguiente entrada:

```
x = 3
y = x + 2
```

Calcu va a devolver:
```
3
5
```

Podemos definir un archivo de operaciones.calcu donde le adjuntamos varias operaciones. Se usa así:

```
./calcu < operaciones.calcu
```

## Problemas

### En Windows no compila

Lo primero es verificar que tenemos instalado flex y bison.

Otro problema está entre flex y las llamadas al sistema de Windows a abrir archivos. Para que Windows busque en la carpeta correspondiente hay que adjuntar el prefijo .\ a la ruta del archivo de salida. 

Hay que cambiar en el Makefile la llamada.

```Makefile
calcu: calcu.y calcu.l
	bison -d calcu.y
	flex -o.\calcu.c calcu.l
	gcc calcu.tab.c calcu.c -o calcu -lm
```

Y si corremos a mano:

```sh
bison -d calcu.y
flex -o\calcu.c calcu.l
```
