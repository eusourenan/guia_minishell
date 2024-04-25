# 🎉🎉🎉 Chegamos agora à parte 2! 🥳🥳🥳

Até aqui, já executamos maravilhosamente comandos dos mais variados tipos!

Contudo, ainda temos que colocar __/usr/bin/__ antes do comando toda hora. Isso é um problema que temos que arrumar. Mas, como fazer isso?

## A forma que o bash encontra o caminho dos comandos

Nesse momento, você tem que se perguntar: como o bash faz isso? E bem, a resposta é simples: o caminho já está salvo a gente só tem que pegá-lo... isso é tudo.

Como assim?!

Lembra da __envp__? Pois é, o caminho está lá. Na verdade todos os caminhos. E pra ser mais direto, se a envp não tiver os caminhos ali, o bash não executa comandos.

Se não acredita, vamos fazer um teste. Execute esse comando aqui:

```
unset PATH
```

Agora tente executar um comando __ls__. Que tal compilar seu projeto? Use o __cc__ pra compilar de novo. __Gcc__? __Clang__? __Cat__ da vida? 😱😱😱😱😱

```
Pra voltar ao normal, abra um novo terminal.
```

O comando __``unset``__ remove coisas do envp. E nesse caso, removemos a variável que o bash usa para saber os caminhos a __PATH__.

Vamos entender o que tem dentro desse envp pra ficar tudo mais claro.

## Entendendo o envp

Bem, o envp é um ponteiro de ``environment variables`` (variáveis de ambiente). Como mencionado antes, basta você usar o comando ``env`` que você verá quais são as variáveis.

Sem enrolar mais, vamos para as explicações.

- Todas as variáveis são escritas no padrão __```nome=valor```__.

	- Por exemplo, se eu tivesse uma variável para dizer uma comida, a variável seria algo como __``COMIDA=BOLO DE CHOCOLATE``__.

- O campo __nome__ segue as mesmas regras de nomes de variável na linguagem C:
	- Não pode ter caracteres especiais;
	- Têm que começar com letra, ou _;
	- Pode ter números no meio do nome. Por exemplo, ``v4ri4v3l``.

- O campo valor pode ter qualquer coisa, pode não ter nada também. Por exemplo: __``COMIDA=``__ é válido.

- O igual __SEMPRE__ deve existir pra separar quem é quem.

Com isso dito, o envp é apenas uma variável com um monte de strings dentro que possuem o formato que discutimos acima.

Sobre definição de envp, encerramos aqui.

## E como raios o bash sabe o caminho?

Bem, dentro desse monte de variáveis, uma variável com o nome ``PATH`` tem todos os caminhos.

Para ver eles, execute esse comando:

```
env | grep PATH
```

Vai aparecer mais de um item, mas você vai ver apenas uma variável com o __nome__ PATH.

Sobre o valor depois do igual, bem... é isso tudo de caminhos.

Como é possível perceber, os ':' é que separam o caminho A do caminho B.

Então isso já vai fazendo o tico e teco se juntarem. Porque, se de um lado eu tenho o comando que eu digitei e do outro tenho todos os caminhos possíveis para buscar onde está aquele comando, então...?

A implementação dessa parte já fica por sua conta. Eu disse que iria ajudar, mas não vou te dar tudo de mão beijada.

___
__Revisão do código__

Até aqui, nosso cógido está mais ou menos assim:

```c
#include "libft.h"
#include <stdio.h>
#include <unistd.h>
#include <readline/readline.h>

int main(int argc, char *argv[], char *envp[])
{
	char	*retorno_readline;
	char	**argumentos_do_comando;

	retorno_readline = readline("Pastel de Flango: ");
	printf("Isso é o que a readline retornou: %s\n\n", retorno_readline);
	
	argumentos_do_comando = ft_split(retorno_readline, ' ');
	printf("Foi tudo picotado!\n");
	
	// sua ideia pra ter o comando com o caminho inteiro
	
	printf("-------------\n"); 
	execve(argumentos_do_comando[0], argumentos_do_comando, envp); //argumentos_do_comando[0] agora tem o caminho completo
	return (0);
}
```
E com isso, temos um minishel que executa perfeitamente 1 comando! Caramba! Que alegria de compartilhar esse momento. Esse minishell tá evoluindo pra caramba, né?!

Pra um começo de projeto, você está de parabéns. 👏👏👏👏👏👏

Vamos partir para o momento que o nosso minishell não pare depois de executar 1 comando.

## Cara, dá um help... não quero parar agora

Vou te dar um passo a passo pra ajudar:

- Você vai percorrer a variável __envp__ até achar onde está a PATH.

- Uma vez que você achou, pegue somente o valor dela e manda pra split, o separador são os dois pontos ":".

- Concatena o caminho com o seu comando (lembre-se de incluir a / que falta).

- Use a funcão access pra verificar se o arquivo existe. 
	- Leia o manual, ele tem 5 parágrafos apenas (você só precisa ler 2), depois é só pular pro return value. E tudo está bem escrito.

- Se não foi o caminho atual, contatena o próximo caminho com o seu comando e manda pra access de novo.

E você vai descobrir qual é o caminho correto. \o/

Com o caminho encontrado e conseguindo executar 1 comando qualquer, passamos para a pergunta: como fazer o programa não acabar e o prompt pegar o próximo comando?

# O prompt infinito

Sem muita enrolação, você consegue imaginar bem que para o minishell executar infinitamente, basta ter um loop infinito.

```c
#include "libft.h"
#include <stdio.h>
#include <unistd.h>
#include <readline/readline.h>

int main(int argc, char *argv[], char *envp[])
{
	char	*retorno_readline;
	char	**argumentos_do_comando;

	while (42) // InfiniTO! InfiniTO!
	{
		retorno_readline = readline("Pastel de Flango: ");
		printf("Isso é o que a readline retornou: %s\n\n", retorno_readline);
		
		argumentos_do_comando = ft_split(retorno_readline, ' ');
		printf("Foi tudo picotado!\n");
		
		// sua ideia pra ter o comando com o caminho inteiro
		
		printf("-------------\n"); 
		execve(argumentos_do_comando[0], argumentos_do_comando, envp);
	}	
	return (0);
}
```
Execute um comando e vamos para a nossa diversão eterna!

___
__Mas o que foi isso que aconteceu?__
___

Bem... aqui depende do quanto você conhece do execve e do quanto você leu o manual.

O execve mata o programa que está em execução atualmente pra conseguir executar o comando que você quer. Vamos para um exemplo simples:

```c
#include <stdio.h>
#include <unistd.h>

int main(int argc, char *argv[], char *envp[])
{
	execve("/bin/ls", (char *[]){"ls", "-a", NULL}, envp);

	printf("Essa é a mensagem que será printada depois do comando executar\n");
	return (0);
}
```

Sem inventar moda, ele executa um ls e faz um printf em seguida. Mas quando você executa isso, aparece apenas o ls. Testa aí.

Agora, vamos modificar um pouco:

```c
#include <stdio.h>
#include <unistd.h>

int main(int argc, char *argv[], char *envp[])
{
	// Coloquei um caminho totalmente inválido
	execve("caminho_totalmente_invalido", (char *[]){"ls", "-a", NULL}, envp);
	printf("Essa é a mensagem que será printada depois do comando executar\n");
	return (0);
}
```

Nesse caso, não houve execução e a mensagem foi printada na tela. Isso aconteceu porque o execve toma somente essas 2 ações:

- Ou ele __executa__ o comando porque está tudo certo e mata o programa em execução.

- Ou ele __não executa__ e o programa continua normalmente.

No manual, o programa vai ter o nome __``processo``__ que é o nome técnico mais correto. Por isso, irei chamar nosso programa de processo daqui pra frente.

___
__Execve mata nosso processo, e agora?__
___

Agora partimos para usar outra das nossas funções permitidas, a __fork__.

A função fork duplica nosso processo. Basicamente ela cria um clone do nosso processo com ele em plena execução.

Agora sim! Basta fazer o clone executar e o clone é o que morre.

Vamos usar o nosso programa bem simples como exemplo:

```c
#include <stdio.h>
#include <unistd.h>

int main(int argc, char *argv[], char *envp[])
{
	int retorno_do_fork;

	retorno_do_fork = fork(); // Daqui pra frente, 2 processos irão ser executados ao mesmo tempo

	if (retorno_do_fork == 0) // clone é sempre == 0
	{
		// O que estiver aqui, é responsabilidade do clone apenas. O processo original não consegue entrar aqui. NUNCA!
		execve("/bin/ls", (char *[]){"ls", "-a", NULL}, envp);
	}
	else // O original é igual a 1
	{
		printf("Essa é a mensagem que será printada depois do comando executar\n");
	}
	return (0);
}
```

Com isso, conseguimos executar um comando e fazer o nosso printf acontecer também! 🎉🎉🎉

Se adaptarmos nosso novo conhecimento com o código que já temos. Algo assim se forma:

```c
#include "libft.h"
#include <stdio.h>
#include <unistd.h>
#include <readline/readline.h>

int main(int argc, char *argv[], char *envp[])
{
	char	*retorno_readline;
	char	**argumentos_do_comando;
	int		retorno_do_fork;

	while (42) // Agora vai Gerson!
	{
		retorno_readline = readline("Pastel de Flango: ");
		printf("Isso é o que a readline retornou: %s\n\n", retorno_readline);
		
		argumentos_do_comando = ft_split(retorno_readline, ' ');
		printf("Foi tudo picotado!\n");
		
		retorno_do_fork = fork(); // Vou colocar aqui pra facilitar pro futuro
		// mas você pode deixar coladinho com o execve, caso queira (não afeta em nada o que fazemos por hora)

		if (retorno_do_fork == 0)
		{
			// sua ideia pra ter o comando com o caminho inteiro
			
			execve(argumentos_do_comando[0], argumentos_do_comando, envp);
		}

		printf("-------------\n");// O processo original é que separa o print na tela agora.
	}
	return (0);
}
```

O código está ganhando forma, mas como diz a frase: 

____
__Com grandes poderes...__
____

Existem erros que podem acontecer com essa criação e execução de processos. O erro mais rápido de perceber é: e se o comando que eu digitar for inválido e o clone continuar a execução?

Existe até um nome pra isso __processo zumbi__. Basicamente, é um processo clone que __você__ deixou rodar pelo sistema e que continua rodando mesmo quando seu processo original já acabou de executar.

Sim, você será o(a) principal responsável se um processo zumbi acontecer. Afinal, o fork não se faz sozinho.

Pra resolver, faremos o processo clone se auto-destruir, caso o execve não seja executado. Para isso, nossa função amiga será a __exit__.

```c
#include "libft.h"
#include <stdio.h>
#include <unistd.h> // O unistd também tem a função exit aqui
#include <readline/readline.h>

int main(int argc, char *argv[], char *envp[])
{
	char	*retorno_readline;
	char	**argumentos_do_comando;
	int		retorno_do_fork;

	while (42)
	{
		retorno_readline = readline("Pastel de Flango: ");
		printf("Isso é o que a readline retornou: %s\n\n", retorno_readline);
		
		argumentos_do_comando = ft_split(retorno_readline, ' ');
		printf("Foi tudo picotado!\n");
		
		retorno_do_fork = fork();

		if (retorno_do_fork == 0)
		{
			// sua ideia pra ter o comando com o caminho inteiro
			
			execve(argumentos_do_comando[0], argumentos_do_comando, envp);
			exit(0); // Prefiro morrer do que perder a vida
		}

		printf("-------------\n");
	}
	return (0);
}
```

E com isso, garantimos uma execução infinita de 1 comando! 🎉🎉🎉

___
__Quase celebrando: Por que o print do comando está estranho?__
___

Você pode ter achado que o programa bugou, caso você tenha executado porque ele printa o prompt, logo em seguida o resultado do seu comando e só depois espera você digitar. Algo como:

```
Pastel de Flango: . .. a.out file.c libft
█ <- Seu cursor aqui esperando o comando
```

Saiba que o programa não deu pane, ele só executou rápido demais.

Seu processo original refez o prompt da readline, daí o clone executou o comando e printou o resultado no terminal, seu cursor acabou ficando no fim da fila, foi isso que ocorreu.

Para resolver, vamos fazer o programa esperar um pouco:

```c
#include "libft.h"
#include <stdio.h>
#include <unistd.h>
#include <readline/readline.h>
#include <sys/wait.h> // Agora temos um novo include

int main(int argc, char *argv[], char *envp[])
{
	char	*retorno_readline;
	char	**argumentos_do_comando;
	int		retorno_do_fork;

	while (42)
	{
		retorno_readline = readline("Pastel de Flango: ");
		printf("Isso é o que a readline retornou: %s\n\n", retorno_readline);
		
		argumentos_do_comando = ft_split(retorno_readline, ' ');
		printf("Foi tudo picotado!\n");
		
		retorno_do_fork = fork();

		if (retorno_do_fork == 0)
		{
			// sua ideia pra ter o comando com o caminho inteiro
			
			execve(argumentos_do_comando[0], argumentos_do_comando, envp);
			exit(0);
		}
		wait(NULL);// Pare! Até quando você quer mandar e mudar minha vida?...
		printf("-------------\n");
	}
	return (0);
}
```

No código acima pedimos para o processo original esperar um pouco. Daí o clone executa e... ta-dam! O código funciona lindamente.

_____
__Nomes técnicos__
____

Para concluir esse ponto, sabe o processo original e o processo clone? Eles são chamados de processo pai e processo filho.

- Pai porque o programa começou com ele (primeiro o pai).
- Filho porque ele foi gerado com o fork (depois o filho).

É assim que você achará mais coisas na internet e conseguirá se comunicar com os parceiros na hora de falar sobre códigos.

Concluímos então a segunda parte.

# Co-me-mo-ra-ção 🥳🥳🥳

Parabéns! Minha nossa! Já parou pra refletir no tamanho do progresso feito até agora?!

O minishell já executa 1 comando perfeitamente. Além disso, ele está funcionando em loop infinito. \o/

Olha o curto espaço de tempo no qual estamos aprendendo sobre o projeto e o quanto já foi feito. É incrível!

Tire um momento pra esticar as pernas, beber água e revisar o conteúdo aprendido até aqui.
