# Partiu executar múltiplos comandos? 

E pensar que no começo nós não tínhamos a menor idéia do que fazer, mas agora estamos pensando em como fazer múltiplos comandos no nosso projeto... como o tempo voa.

Talvez, durante a nossa jornada, você tentou executar comandos com pipes e mandou pro execve tentar executar. Caso não tenha feito, execute isso aqui no nosso minishell:

```bash
echo oi | ls
```

Como deu pra perceber, ele considera tudo que vem depois do comando como sendo argumento. Portanto, fica claro que __o execve não lida com pipes__.

Os pipes terão que ser feitos no código, o que indica que você será 100% reponsável pelas execuções de cada comando.

___
__Mas afinal, o que é um pipe?__
___

Pipe é um canal para a comunicação entre 2 processos, um meio de fazer o processo A mandar informações para o processo B.

Ele faz isso abrindo dois espaços para comunicação, espaços com comportamentos específicos.

Para termos uma melhor ideia, imagine que você comprou um Walkie-Talkie, mas ele veio com defeito. Ana pegou um, Gumercindo pegou o outro.

Por algum motivo:

- Gumercindo consegue ouvir, mas ninguém escuta quando ele fala alguma coisa.
- Ana consegue falar, mas não escuta nada que vem do outro lado.

Em resumo, Gumercindo só ouve, Ana só emite o som.

O pipe vai se comportar como esse walkie-talkie. Ele vai abrir 2 vias de comunicação, mas em uma só conseguiremos ler e na outra só conseguiremos escrever. Simples assim.

Vamos para nosso exemplo:

```c
#include <unistd.h> // Essa daqui tem tudo mesmo hein?!
#include <stdio.h>

int main(int argc, char *argv[], char *envp[])
{
	int	all_pipes[2]; // Variável com 2 posições

	pipe(all_pipes); // A função pipe cria o canal
		// de comunicação e define os valores
		// da variável

	// all_pipes[0] -> Somente ouve
	// all_pipes[1] -> Somente escreve

	/***** Exemplo de uso *****/
	// Escrever no pipe
	write(all_pipes[1], "Lasanha de presunto", 19); // Como não é o fd 1, ele não printa no terminal

	// Ler do pipe
	char	texto_salvo[20]; // um byte pro \0

	read(all_pipes[0], texto_salvo, 19); // Essa função já é nossa conhecida
	texto_salvo[19] = '\0'; // Index começa do zero

	printf("Texto que foi lido pelo pipe[0]: %s\n", texto_salvo);

	return (0);
}
```

Debaixo dos panos, o pipe cria um arquivo no limbo do pc e te dá dois fds pra acessar. O fd zero apenas tem permissão pra ler, o fd 1 apenas tem permissão pra escrever.

## Exemplo básico para 2 comandos

Para executar dois comandos, vamos fazer 2 execve. O primeiro execve para o primeiro comando, o segundo execve para o segundo comando.

Algo como:

```c
#include "libft.h"
#include <readline/readline.h>
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main(int argc, char *argv[], char *envp[])
{
	char	*retorno_readline;
	char	**comandos; // Vai receber os dois comandos separados
	int		retorno_fork;

	retorno_readline = readline("Prompt$ ");
	comandos = ft_split(retorno_readline, '|'); // Sim, splitamos pelo pipe


	/******* Execução do primeiro comando *******/
	retorno_fork = fork();

	if (retorno_fork == 0)
	{
		// Faço o processo normal de executar, mas mando o primeiro comando, em vez da readline.
		char	**comando_picotado = ft_split(comandos[0], ' ');

		// sua ideia pra ter o comando com o caminho inteiro
			
		execve(comando_picotado[0], comando_picotado, envp);
		exit(1); // Caso o comando não seja executado
	}
	
	wait(NULL); // Espera até a execução acontecer

	/******* Execução do segundo comando *******/
	retorno_fork = fork();

	if (retorno_fork == 0)
	{
		// Igual antes, mas agora é o segundo comando
		char	**comando_picotado = ft_split(comandos[1], ' ');

		// sua ideia pra ter o comando com o caminho inteiro
			
		execve(comando_picotado[0], comando_picotado, envp);
		exit(1); // Caso o comando não seja executado
	}
	wait(NULL); // Espera novamente

	return (0);
}
```

Aqui voltamos para uma única execução apenas para que você veja que ele executou 2 comandos ao invés de 1, quando você usou o pipe! \o/

Colocando tudo dentro do loop temos:

```c
#include "libft.h"
#include <readline/readline.h>
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main(int argc, char *argv[], char *envp[])
{
	char	*retorno_readline;
	char	**comandos; // Vai receber os dois comandos separados
	int		retorno_fork;

	while (42)
	{
		retorno_readline = readline("Prompt$ ");
		comandos = ft_split(retorno_readline, '|'); // Sim, splitamos pelo pipe


		/******* Execução do primeiro comando *******/
		retorno_fork = fork();

		if (retorno_fork == 0)
		{
			// Faço o processo normal de executar, mas mando o primeiro comando, em vez da readline.
			char	**comando_picotado = ft_split(comandos[0], ' ');

			// sua ideia pra ter o comando com o caminho inteiro
				
			execve(comando_picotado[0], comando_picotado, envp);
			exit(1); // Caso o comando não seja executado
		}
		
		wait(NULL); // Espera até a execução acontecer

		/******* Execução do segundo comando *******/
		if (comandos[1] != NULL) // Verificação de segurança
		{
			retorno_fork = fork();

			if (retorno_fork == 0)
			{
				// Igual antes, mas agora é o segundo comando
				char	**comando_picotado = ft_split(comandos[1], ' ');

				// sua ideia pra ter o comando com o caminho inteiro
					
				execve(comando_picotado[0], comando_picotado, envp);
				exit(1); // Caso o comando não seja executado
			}
			wait(NULL); // Espera novamente
		}
	}

	return (0);
}
```

Com isso, executamos comandos digitados com o pipe. Mas, para repassar as informações de um comando para o outro comando, iremos conhecer uma função que é necessária, além do pipe.

# Dup

Como você pode notar até o momento, dois comandos são executados, mas eles não se comunicam de verdade. O comando 1 não envia dados para o comando 2.

Isso acontece porque quando os comandos são criados para printar o terminal (fd 1 do write).

Os programas não são adaptados para printarem em fds diferentes, como os fds de pipes, por exemplo (se você já codou algo assim, me ensine, please).

Então, os programadores criaram algo para nos ajudar nesse momento de dificuldade, um duplicador de fds.

Em especial, irei falar da função dup2 (leia _``duplicate to``_, "duplique para..." em português).

Essa função duplica qualquer fd que passarmos como argumento para outro fd que definirmos.

Ela é prototipada assim (no manual é diferente):
```c
int dup2(int fd1, int fd2)
```

O que ela faz é substituir o fd2 pelo fd1.

Sim, ela faz o fd2 deixar de existir e coloca o fd1 no lugar dele.

Se fizermos no código

```c
dup2(all_pipes[1], 1);
```

É o mesmo que dizer que ``write(1, "oi", 2)`` vira ``write(all_pipes[1], "oi", 2)``.

O código continua igual, mas o write printa no pipe em vez de printar no terminal. Ali só foi uma demonstração do que acontece no comportamento.

Vejamos como fica noss código com isso montado:

```c
#include "libft.h"
#include <readline/readline.h>
#include <stdio.h>
#include <unistd.h> // O dup também está aqui
#include <sys/wait.h>

int main(int argc, char *argv[], char *envp[])
{
	char	*retorno_readline;
	char	**comandos;
	int		retorno_fork;
	int		all_pipes[2]; // Para uma execução simples

	while (42)
	{
		retorno_readline = readline("Prompt$ ");
		comandos = ft_split(retorno_readline, '|');


		// crio os pipes se tenho 2 comandos
		if (comandos[1] != NULL)
		{
			pipe(all_pipes);
		}

		/******* Execução do primeiro comando *******/
		retorno_fork = fork();

		if (retorno_fork == 0)
		{
			char	**comando_picotado = ft_split(comandos[0], ' ');

			// sua ideia pra ter o comando com o caminho inteiro

			
			if (comandos[1] != NULL)
			{
				// Faço a troca do fd 1 pelo pipe
				dup2(all_pipes[1], 1);
			}


			execve(comando_picotado[0], comando_picotado, envp);
			exit(1); // Caso o comando não seja executado
		}
		
		// Agora não existe mais esse wait

		/******* Execução do segundo comando *******/
		if (comandos[1] != NULL) // Verificação de segurança
		{
			retorno_fork = fork();

			if (retorno_fork == 0)
			{
				char	**comando_picotado = ft_split(comandos[1], ' ');

				// sua ideia pra ter o comando com o caminho inteiro

				// Leio o que veio do comando anterior
				dup2(all_pipes[0], 0);

				execve(comando_picotado[0], comando_picotado, envp);
				exit(1);
			}
		}
	}

	return (0);
}
```

E com isso conseguimos fazer com que os programas enviem os dados através do pipe. 🥳🥳🥳🥳

____
__Para finalizar__
____

A responsabilidade que você tem com o pipe é a de fechar todos os fds que ficaram abertos. 

Lembre-se que ``dup`` vem de __duplicate__, seu fd está duplicado, por isso, fechar-lo-emos:

```c
#include "libft.h"
#include <readline/readline.h>
#include <stdio.h>
#include <unistd.h> // O dup também está aqui
#include <sys/wait.h>

int main(int argc, char *argv[], char *envp[])
{
	char	*retorno_readline;
	char	**comandos;
	int		retorno_fork;
	int		all_pipes[2];

	while (42)
	{
		retorno_readline = readline("Prompt$ ");
		comandos = ft_split(retorno_readline, '|');


		if (comandos[1] != NULL)
		{
			pipe(all_pipes);
		}

		/******* Execução do primeiro comando *******/
		retorno_fork = fork();

		if (retorno_fork == 0)
		{
			char	**comando_picotado = ft_split(comandos[0], ' ');

			// sua ideia pra ter o comando com o caminho inteiro

			
			if (comandos[1] != NULL)
			{
				dup2(all_pipes[1], 1);


				// Fecho os pipes no primeiro filho
				// Sempre depois dos dup's
				close(all_pipes[0]);
				close(all_pipes[1]);
			}


			execve(comando_picotado[0], comando_picotado, envp);
			exit(1);
		}
		

		/******* Execução do segundo comando *******/
		if (comandos[1] != NULL)
		{
			retorno_fork = fork();

			if (retorno_fork == 0)
			{
				char	**comando_picotado = ft_split(comandos[1], ' ');

				// sua ideia pra ter o comando com o caminho inteiro

				dup2(all_pipes[0], 0);

				// Fecho no segundo filho
				close(all_pipes[0]);
				close(all_pipes[1]);


				execve(comando_picotado[0], comando_picotado, envp);
				exit(1);
			}

			// Fecho no pai
			close(all_pipes[0]);
			close(all_pipes[1]);
		}
	}
	return (0);
}
```

Aqui eu te mostrei uma maneira de fazer com que dois comandos sejam executados e que o comando 1 envie informações para o comando 2. Com isso, o assunto sobre pipes está concluído. Você consegue executar 2 comandos!
