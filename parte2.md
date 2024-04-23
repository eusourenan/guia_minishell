# 🎉🎉🎉 Chegamos agora à parte 2! 🥳🥳🥳

Até aqui, já executamos maravilhosamente comandos dos mais variados tipos!

Contudo, ainda temos que colocar __/usr/bin/__ antes do comando toda hora. Isso é um problema que temos que arrumar. Mas, como fazer isso?

## A forma que o bash encontra o caminho dos comandos

Nesse momento, você tem que se perguntar: como o bash faz isso? E bem, a resposta é simples, o caminho já está salvo. Como assim?!

Lembra da __envp__? Pois é, o caminho está lá. Na verdade todos os caminhos. E pra ser mais direto, se a envp não tiver os caminhos ali, o bash não executa comandos.

Se não acredita, vamos fazer um teste. Execute esse comando aqui:

```
unset PATH
```

Agora tente executar um comando __ls__. Que tal compilar seu projeto? Use o __cc__ pra compilar de novo. __Gcc__? __Clang__? __Cat__ da vida? 😱😱😱😱😱

```
Pra voltar ao normal, abra um novo terminal.
```

O comando unset remove coisas do envp. E nesse caso, removemos a variável que o bash usa para saber os caminhos.

Vamos entender o que tem dentro desse envp pra ficar tudo mais claro.

## Entendendo o envp

Bem, o envp é um ponteiro com ``environment variables`` (variáveis de ambiente). Como mencionado antes, basta você usar o comando ``env`` que você verá quais são as variáveis.

Sem enrolar mais, vamos para as explicações.

- Todas as variáveis são escritas no padrão __``nome=valor``__. Por exemplo, se eu tivesse uma variável para dizer uma comida, a variável seria algo como __``COMIDA=BOLO DE CHOCOLATE``__.
- O campo __nome__ segue as mesmas regras de nomes de variável na linguagem C:
	- não pode ter caracteres especiais;
	- têm que começar com letra, ou _;
	- pode ter números no meio do nome. Por exemplo, ``v4ri4v3l``.
- O campo valor pode ter qualquer coisa, pode não ter nada também. Por exemplo: __``COMIDA=``__ é válido.
- O igual sempre deve existir pra separar quem é quem.

Com isso dito, o envp é apenas uma variável com um monte de strings dentro que possuem o formato que discutimos acima.

Sobre definição de envp, encerramos aqui.

## E como raios o bash sabe o caminho?

Bem, dentro desse monte de variáveis, uma variável com o nome ``PATH`` tem todos os caminhos.

Para ver eles, execute esse comando:

```
env | grep PATH
```

Vai aparecer mais de um item, mas você vai ver apenas uma variável com o nome PATH e depois do igual, bem... isso tudo de caminhos.

Como é possível perceber, os ':' é que separam o caminho A do caminho B.

Então isso já vai fazendo o tico e teco se juntarem. De um lado eu tenho o comando que eu digitei, do outro tenho todos os caminhos possíveis para testar com aquele comando...

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
	execve(argumentos_do_comando[0], argumentos_do_comando, NULL); // Mudamos o primero argumento pra ser o comando ora!
	return (0);
}
```
E com isso, temos um minishel que executa perfeitamente 1 comando! Caramba! Que alegria de compartilhar esse momento. Esse minishell tá evoluindo pra caramba, né?!

Pra um começo de projeto, você está de parabéns. 👏👏👏👏👏👏

Vamos partir para o momento que o nosso minishell não pare depois de executar 1 comando.

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
		execve(argumentos_do_comando[0], argumentos_do_comando, NULL);
	}	
	return (0);
}
```
Execute um comando e vamos para a nossa diversão eterna!
