## Enter and leave

```
vi arquivo.txt <ENTER>
:q <ENTER>
```
  
## Enter, insert text, save and leave

```
vi arquivo.txt <ENTER>
i
Minha primeira frase no VI.
<ESC>
:wq <ENTER>
```
  
## Enter, change text and leave without save

```
vi arquivo.txt <ENTER>
dd
:q! <ENTER>
```
  
## Enter, change text, save in other file and leave
  
```
vi arquivo.txt <ENTER>
yy
p
:w arquivo-novo.txt <ENTER>
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
  
## Repeat first line twice and delete last line

```
vi arquivo.txt <ENTER>
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
FIM
<ESC>
j
.
j
.
```
  
## Open two files simultaneosly

```
vi arquivo.txt <ENTER>
:split ~/.vimrc <ENTER>
Ctrl+W
Ctrl+W
:q <ENTER>
:q <ENTER>
```
  
## Help

```
vi arquivo.txt <ENTER>
:help <ENTER>
```

## Commands for files

```
:q	Sai do vi
:w	Salva alteraçoes feitas
:wq	Salva aterações e sai do VI
:q!	Sai sem salvar
:w abc	Grava arquivo com o nome 'abc'
:r abc	Insere o conteúdo do arquivo 'abc'
:e abc	Edita o arquivo 'abc'
```
  
## Insert mode
  
```
i	Entra no modo de Inserção
a	Entra no modo de Inserção, após o cursor
o	Entra no modo de Inserção, em uma nova linha
<ESC>	Sai do modo de Inserção
```
  
## Copy, cut e paste
  
```
yy	Copia a linha inteira
5yy	Copia as 5 próximas linhas
dd	Apaga a linha
5dd	Apaga 5 linhas
x	Apaga uma letra
5x	Apaga 5 letras
p	Cola o trecho copiado ou apagado
V	Seleção visual de linhas
```
  
## Jumping
  
```
gg	Pula para a primeira linha
G	Pula para a última linha
44G	Pula para a linha número 44
w	Pula para a próxima palavra
b	Pula para a palavra anterior
{	Pula para o parágrafo anterior
}	Pula para o próximo parágrafo
(	Pula para a frase anterior
)	Pula para a próxima frase
f.	Pula até o próximo ponto (.), na mesma linha
``	Desfaz o pulo, volta
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
