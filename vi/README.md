## Enter and leave

```
vi file.txt <ENTER>
:q <ENTER>
```
  
## Enter, insert text, save and leave

```
vi file.txt <ENTER>
i
My first text in VI.
<ESC>
:wq <ENTER>
```
  
## Enter, change text and leave without save

```
vi file.txt <ENTER>
dd
:q! <ENTER>
```
  
## Enter, change text, save in other file and leave
  
```
vi file.txt <ENTER>
yy
p
:w new-file.txt <ENTER>
:q <ENTER>
```
  
## Setting vi

```
vi ~/.vimrc <ENTER>
i
set vb ru nu <ENTER>
set ic is hls scs
<ESC>
:wq <ENTER>
```
  
## Repeat first line twice and erase last line

```
vi file.txt <ENTER>
gg
yy
p
p
G
dd
```

## Repeat commands

```
vi arquivo.txt <ENTER>
A
END
<ESC>
j
.
j
.
```
  
## Open two files simultaneosly with split

```
vi file.txt <ENTER>
:split ~/.vimrc <ENTER>
Ctrl+W
Ctrl+W
:q <ENTER>
:q <ENTER>
```
  
## Help

```
vi file.txt <ENTER>
:help <ENTER>
```

## Commands for files

```
:q	Exit
:w	Save changes
:wq	Save e exit
:q!	Exit without save
:w abc	Record file with name 'abc'
:r abc	Insert file 'abc' content
:e abc	Edit file 'abc'
```
  
## Insert mode
  
```
i	Enter in insert mode
a	Enter in insert mode, after cursor
o	Enter in insert mode, in new line
<ESC>	Exit to insert mode
```
  
## Copy, cut e paste
  
```
yy	Copy entire line
5yy	Copy next 5 lines
dd	Erase line
5dd	Erase 5 lines
x	Erase one letter
5x	Erase 5 letters
p	Paste copied text
V	Visual line selection
```
  
## Jumping
  
```
gg	Jump for first line
G	Jump for last line
44G	Jump for line 44
w	Jump for next word
b	Jump for previous word
{	Jump for previous paragraph
}	Jump for next paragraph
(	Jump for previous phrase
)	Jump for next phrase
f.	Jump for next dot (.) on the same line
``	Undo jump
```
  
## Erasing smartly
  
```
dgg	Apaga até o início do arquivo
d0	Apaga até o início da linha atual
dw	Apaga a palavra
d4b	Apaga as quatro palavras anteriores
df.	Apaga até o próximo ponto
d)	Apaga até o fim da frase
```
  
## Others
  
```
J	Junta a próxima linha com a atual
u	Desfaz o último comando
Ctrl+R	Refaz o último comando desfeito
.	Repete o comando anterior
hjkl	Movimenta o cursor
/	Pesquisa um texto
n	Vai para o próximo resultado da pesquisa
:%s/a/b/g	Troca 'a' por 'b' em todo o texto
:!cmd	Executa o comando externo 'cmd'
:r!cmd	Insere o resultado do comando externo 'cmd'
```
  
Source: https://aurelio.net/curso/material/vim-ref.html
