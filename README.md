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

Esse começo pode ser um tanto "rude" na forma de ter sido escrito. Mas eu vejo que a ansiedade em cima do projeto pode ser a coisa que mais te atrapalha de avançar e de te fazer entender: ___A 42 não é sobre fazer projetos, é sobre aprender a aprender___. Foque no aprendizado que o projeto vai te trazer. Se você acha ele um monstro, significa que seus conhecimentos serão monstruosos quando você concluir esse projeto.

Nessa fase da vida, uma última coisa tem que ser dita: ``leia manuais``. Na fase 3 os manuais podem ser a diferença entre ``fazer um projeto bom, rápido e entendendo tudo`` e ``sofrer muito tempo no projeto e entender pouco do que você está fazendo``. Por isso, se acostume rápido, leia os manuais do projeto, RTFM! Se você não entende, leia de novo com ajuda. Depois leia sozinho(a) novamente.

# Bora finalmente!

Bom, aqui estamos, muitas coisas pra ver e nenhuma ideia do que tem que ser feito. Só sabemos que o bash tem que ser minimamente reproduzido (sim, minimamente. É MINI-shell).


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

Execute e veja a mágica que ocorre. Você acabou de começar seu projeto do minshell. Yay! \o/

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
``(char *[]){"ls", NULL}`` é uma criação de uma variável tmporária do tipo ``char*[]`` (igualzinha ao tipo do argv). O mesmo código pode ser substituído por:

```c
#include <unistd.h> // execve tá aqui ó
#include <readline/readline.h>

int main(void)
{
	char *comando_digitado[] = {"ls", NULL};

	readline("Pastel de Flango: ");
	execve("/usr/bin/ls", comando_digitado, NULL);
	return (0);
}
```

Bom, o que eu fiz mandar os argumentos que o execve exige:

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
	readline("Pastel de Flango: ");
	execve("/usr/bin/ls", (char *[]){"ls", NULL}, NULL);
	return (0);
}
```
