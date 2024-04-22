# Guia pro Minishell
Este é um guia para quem está começando no minishel. O objetivo é te ajudar a dar os primeiros passos e deixar você confiante pra buscar o resto e fazer o seu projeto.

## Antes de tudo, parabéns! 🥳🥳
Você conseguiu! Metade da trilha de fundamentos foi superada e agora você chegou na metade do common core. Que feito! Comemore!

### Ah! Mas...
Se este guia está sendo lido, é porque a ansiedade pegou você de jeito.

Provavelmente foi devido à pressão que você criou em cima do projeto e de ver os outros quebrando cabeça (como se isso não fosse feito em todos os projetos).

Além disso, se você fez o Minitalk, vai ouvir frases do tipo "o pipex era mais útil", como se esse fosse o único requisito aceitável pra fazer o minishell. Sua ansiedade vai nas alturas.

Estou aqui pra dizer: calma, vou te mostrar que esse projeto não é o monstro que sua imaginação está inventando é muito ``MENOR``. Sua imaginação é que é sua inimiga.

Saiba: eu fiz o minitalk e fiz parceria com quem fez minitalk também. O minshell foi entregue com sucesso, foi feito. Simples assim, o projeto foi feito.

Com a cabeça mais calma, bora começar então?

# Importante: O que tem que ser dito, tem que ser dito

Esse começo pode ser um tanto "rude" na forma de ter sido escrito. Mas eu vejo que a ansiedade em cima do projeto pode ser a coisa que mais te atrapalha de avançar no projeto e te atrapalha em te fazer entender: ___A 42 não é sobre fazer projetos, é sobre aprender a aprender___. 

Foque no aprendizado que o projeto vai te trazer. Se você acha ele um monstro, significa que seus conhecimentos serão monstruosos quando você concluir esse projeto.

Nessa fase da vida, uma última coisa tem que ser dita: ``leia manuais``. Na fase 3 os manuais podem ser a diferença entre ``fazer um projeto bom, rápido e entendendo tudo`` e ``sofrer muito tempo no projeto e entender pouco do que você está fazendo``. Por isso, se acostume rápido, leia os manuais do projeto, RTFM! Se você não entende, leia de novo com ajuda. Depois leia sozinho(a) novamente.

# Bora finalmente!

Bom, aqui estamos, muitas coisas pra ver e nenhuma idéia do que tem que ser feito. Só sabemos que o bash tem que ser minimamente reproduzido (sim, minimamente. É MINI-shell).


Eis uma luz: a readline vai te dar o respiro inicial que você precisa e será o nosso ponto de partida. Quer ver o como? Toma um exemplo:

```c
#include <readline/readline.h>

int main(void)
{
	readline("Pastel de Flango: ");
	return (0);
}
```

Para executar esse código, você tem que inserir uma flag a mais para linkar seu executável com a readline, ``depois do nome dos arquivos``:
```
cc file.c -l readline
```

Pode testar com a flag antes e ver o que ocorre. (O pc não explode, eu garanto).

Execute e veja a mágica que ocorre. Você acabou de começar seu projeto do minshell. Yay! 🎉🎊 \o/ 👏👏👏

________
### Um pequeno passo para o projeto, um grande passo para um Human Coder

Caso você tenha executado (se não fez isso, executa... tô esperando...) você vai perceber que ele simplesmente espera você dar um enter e o programa acaba. Mas ele fica lá paradinho. (Que coisa mais linda 😆💖).

Com isso, a dificuldade de imaginar como o prompt ficaria parado na tela esperando um comando está resolvida. Mas, como executo um comando? (Se você fez minitalk também).

Partimos para o nosso segundo elemento, o ``execve``, ele executa comandos. No código ele vai ficar assim:

```c
#include <unistd.h> // execve tá aqui ó
#include <readline/readline.h>

int main(void)
{
	readline("Pastel de Flango: ");
	execve("/usr/bin/ls", (char *[]){"ls", NULL}, NULL);
	return (0);
}
```

Se o código for executado, vai ser melhor pra você entender o que irei explicar. Executa isso e veja o que acontece.

#### Explicações
_____
``(char *[]){"ls", NULL}`` é uma criação de uma variável tmporária do tipo ``char*[]`` (igualzinha ao tipo do argv). Coloquei os colchetes porque se fosse ``char **`` daria erro na hora de compilar. No fim, é apenas uma variável e só.

O mesmo código pode ser substituído por:

```c
#include <unistd.h> // execve tá aqui ó
#include <readline/readline.h>

int main(void)
{
	// char *argumentos_do_comando[] = {"ls", NULL}; // Esse é o correto
	char **argumentos_do_comando = {"ls", NULL}; // Teste com esse para ver a diferença

	readline("Pastel de Flango: ");
	execve("/usr/bin/ls", argumentos_do_comando, NULL);
	return (0);
}
```

Bom, o que eu fiz foi mandar os argumentos que o execve exige:

- O comando que irei executar
- Os argumentos do comando _(Basicamente, é o que você digita no terminal)_.
- Variáveis de ambiente (falarei delas mais tarde).

__O comando__ que você executa é um executável (igual ao nosso amigo __a.out__). Esse comando está na pasta ``/bin/usr/``. Para checar alguns dos comandos, execute isso no terminal:

```sh
ls /usr/bin
```
Esses são comandos que você pode executar à vontade com o execve.

__Os argumentos__ são o que você digita no terminal, literalmente qualquer comando que você digita. Eles vêm no formato array de arrays igual ao nosso já conhecido ``argv da main`` (por isso que chamei de argumentos).

__Envp__: O terceiro argumento são as variáveis de ambiente. Se você não sabe o que é isso, execute o comando ``env`` no seu terminal e ele vai te mostrar o que são elas.

- Você não precisa implementar nada pra ter esses valores, a função __main__ vai dar eles pra você. Irei explicar como.

	- A main pode ser chamada assim: ``main(void)`` ou assim ``main(int argc, char *argv[])``, certo? Com ou sem argumentos ela funciona.
	- O que você provavelmente não sabia é que na verdade a main pode ser chamada com 3 argumentos, assim: ``main(int argc, char *argv[], char *envp[])``.

Vamos apenas trocar o protótipo da main. Se você executar agora, vai perceber que não houve erro nenhum (não estou executando com as flags de erros).

```c
#include <unistd.h> // execve tá aqui ó
#include <readline/readline.h>

int main(int argc, char *argv[], char *envp[])
{
	char *argumentos_do_comando[] = {"ls", NULL};

	readline("Pastel de Flango: ");
	execve("/usr/bin/ls", argumentos_do_comando, NULL);
	return (0);
}
```

Da mesma forma que o programa coloca os valores no __argc__ e no __argv__ sem que você tenha que fazer nada. A variável __envp__ será inicializada automaticamente pelo sistema. Você só tem que usar a variável.

E conforme o manual do execve, você vai perceber que o envp é o terceiro argumento. Coloque ele no código, você verá que o programa executa da mesma forma.

___
__Entendi até aqui, mas o meu programa só executa um ls e para. O que eu digito não importa?__
___

Nesse exato momento do aprendizado, não importa. Sua readline pega um texto e o execve não tá nem aí pro texto, ele sempre vai executar o ls.

Para conseguir usar melhor a readline, temos que aprender a pegar o retorno dela. Para isso, o manual diz tudo: a função readline retorna o que você digitou como um ``char *``.

O novo código fica assim então:

```c
#include <stdio.h> // Bora usar printf!!!
#include <unistd.h> // execve tá aqui ó
#include <readline/readline.h>

int main(int argc, char *argv[], char *envp[])
{
	char	*retorno_readline;
	char	*argumentos_do_comando[] = {"ls", NULL};

	retorno_readline = readline("Pastel de Flango: ");
	printf("Isso é o que a readline retornou: %s\n", retorno_readline);
	printf("-------------\n"); // print estético apenas (fica melhor pra separar o que é o quê).
	execve("/usr/bin/ls", argumentos_do_comando, NULL);
	return (0);
}
```

Executamos o código e "ó só!". A readline retorna certinho o que digitamos.

Um detalhe para ser reparado é que a linha é retornada pra você sem um ``\n`` no final dela, apenas o ``\0`` de praxe. Você não tem que se preocupar com algum caracter diferenciado no meio do caminho. (ufa...!)
___
__Quero aproveitar a farra e juntar tudo. Como juntar a readline com o execve?__
___

Bem simplão. Faz o seguinte:
- splitar o retorno da readline por espaços. Afinal, o segundo parâmetro (que é o que você digita no terminal) é do tipo ``char *``, porém o execve pega o seu segundo argumento como ``char **``. A split resolve esse problema pra nós, por enquanto.

Split feita, basta usarmos o nosso comando splitado como argumento.

Para fins didáticos, irei apenas apagar o ``{"ls", NULL}`` da variável _argumentos_do_comando_ e utilizarei a mesma variável para receber o retorno da split.

Com isso dito, nosso código agora está assim:

```c
int main(int argc, char *argv[], char *envp[])
{
	char	*retorno_readline;
	char	**argumentos_do_comando;

	retorno_readline = readline("Pastel de Flango: ");
	printf("Isso é o que a readline retornou: %s\n\n", retorno_readline);
	
	// O que foi acrescentado
	argumentos_do_comando = ft_split(retorno_readline, ' ');
	printf("Foi tudo picotado!\n");
	
	
	printf("-------------\n"); // print estético apenas (fica melhor pra separar o que é o quê).
	execve("/usr/bin/ls", argumentos_do_comando, NULL);
	return (0);
}
```

Veja que maravilha! Você consegue executar o comando ls da forma que você quiser agora! 🥳🥳🥳

Tá sentindo o progresso?! Antes você tinha zero projeto, agora você já está executando o comando ls de tudo quanto é jeito. Parabéns! 👏👏👏👏👏👏👏

Vamos para o próximo passo então?

___
### Execve executando o outros comandos, não apenas o ls

Vamos direto pro código que nos parece mais natural pensar, depois para as explicações:

```c
int main(int argc, char *argv[], char *envp[])
{
	char	*retorno_readline;
	char	**argumentos_do_comando;

	retorno_readline = readline("Pastel de Flango: ");
	printf("Isso é o que a readline retornou: %s\n\n", retorno_readline);
	
	// O que foi acrescentado
	argumentos_do_comando = ft_split(retorno_readline, ' ');
	printf("Foi tudo picotado!\n");
	
	
	printf("-------------\n"); // print estético apenas (fica melhor pra separar o que é o quê).
	execve(argumentos_do_comando[0], argumentos_do_comando, NULL);
	return (0);
}
```

Quando digitamos o comando, bem... a primeira coisa é o comando, certo?

Bora testar esse código! Afinal, ele compila de boa.

___
### E aê tiozão! Tá tirando?!

Não, não estou zoando contigo, mas deixando claro que não basta pegar o que vem da readline de qualquer jeito e mandar para o execve. O primeiro argumento tem que ser o caminho completo do executável.

Execute o comando assim:
```
/usr/bin/ls -l
```

Você vai perceber que o execve executou seu comando lindamente.

Repito: o primeiro argumento do execve é o caminho inteiro de onde o comando está escondido.

Quando executamos coisas no bash, ele completa o pedaço do caminho que falta. Depois de fazer o caminho ficar completo é que ele manda o argumento para o execve executar.

Como a maioria dos comandos que você conhece está na pasta ``/usr/bin/`` você pode começar qualquer comando assim, com as flags que você conhece que o execve vai executar de boa.

```
Caso você queira facilitar um pouco seus testes, você pode só colocar /bin/comando_qualquer.

/bin é uma pasta com um link simbólico pra /usr/bin. Dá para executar comandos com menos digitação.
```

# Pausa pra conferir o progresso!

Percebeu que suas dúvidas mudaram? Agora é algo do tipo: "Como eu vou fazer pra colocar o diretório antes do nome do comando?" Caramba! Quanto progresso! 👏👏👏👏👏

Tome uma água, estique as pernas e sinta o quanto você já entendeu do começo (ler o comando) e do final (executar o comando).

Esse projeto tem 1 mês e meio, você está aqui há alguns minutos e já aprendeu bastante. Seu cérebro merece uma pausa.

