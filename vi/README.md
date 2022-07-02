## Entrar e Sair

```
vi arquivo.txt <ENTER>
:q <ENTER>
```
  
## Entrar, Inserir Texto, Salvar e Sair

```
vi arquivo.txt <ENTER>
i
Minha primeira frase no VI.
<ESC>
:wq <ENTER>
```
  
## Entrar, Alterar Texto e Sair sem Salvar

```
vi arquivo.txt <ENTER>
dd
:q! <ENTER>
```
  
## Entrar, Alterar Texto, Salvar em Outro Arquivo e Sair
  
```
vi arquivo.txt <ENTER>
yy
p
:w arquivo-novo.txt <ENTER>
:q <ENTER>
```
  
## Configurar o vi

```
vi ~/.vimrc <ENTER>
i
set vb ru nu <ENTER>
set ic is hls scs
<ESC>
:wq <ENTER>
```
  
## Repetir a Primeira Linha 2 Vezes e Apagar a Última

```
vi arquivo.txt <ENTER>
gg
yy
p
p
G
dd
```

##Repetir Comandos

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
  
## Abrir Dois Arquivos Simultaneamente

```
vi arquivo.txt <ENTER>
:split ~/.vimrc <ENTER>
Ctrl+W
Ctrl+W
:q <ENTER>
:q <ENTER>
```
  
## Obter Ajuda

```
vi arquivo.txt <ENTER>
:help <ENTER>
```

## Comandos de Arquivo

```
:q	Sai do vi
:w	Salva alteraçoes feitas
:wq	Salva aterações e sai do VI
:q!	Sai sem salvar
:w abc	Grava arquivo com o nome 'abc'
:r abc	Insere o conteúdo do arquivo 'abc'
:e abc	Edita o arquivo 'abc'
```
  
## Modo de Inserção
  
```
i	Entra no modo de Inserção
a	Entra no modo de Inserção, após o cursor
o	Entra no modo de Inserção, em uma nova linha
<ESC>	Sai do modo de Inserção
```
  
## Copiar, Cortar e Colar
  
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
  
## Pulos
  
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
  
## Apagando com esperteza
  
```
dgg	Apaga até o início do arquivo
d0	Apaga até o início da linha atual
dw	Apaga a palavra
d4b	Apaga as quatro palavras anteriores
df.	Apaga até o próximo ponto
d)	Apaga até o fim da frase
```
  
## Outros
  
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
  
Fonte: https://aurelio.net/curso/material/vim-ref.html
