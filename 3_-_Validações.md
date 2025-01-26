# Validações

Uma vez que você consegue ter o input que o usuário digitou, temos que garantir que o que ele digitou está certo, de acordo com o pdf do minishell.

Para a parte mandatória, temos que validar 3 itens no input:

- Aspas
- Redirects
- Pipes

## Aspas

"argumento 1" 'Argumento 2' "Mesmo sendo uma frase grande   esse é o argumento 3"

Podemos validar as aspas simplesmente procurando por seus pares. Para cada aspas que inicia, você tem que encontrar a aspas que termina.

Uma solução possível é:

```c
char	*ft_strchr(char *str, char c); // Sim, a sua da libft

int	tem_aspas_que_nao_fecham(char *input)
{
	char	quote;
	char	*next;

	while (*input)
	{
		if (*input == '"' || *input == '\'')
		{
			quote = *input;
			input++;
			next = ft_strchr(input, quote);
			if (next == NULL)
				return 1; // Sim, tem aspas que não fecham (input errado)
			else
				input = next;
		}
		input++;
	}
	return 0; // Tá tudo certo
}
```

## Redirects

Para um redirect ser válido, o nome do arquivo aparece após ele.

Exemplos de redirects válidos:

	< file
	> file
	<< file
	>> file

Uma pseudo-solução possível para validar os redirects é:

```c
int	redirect_mal_digitado(char *input)
{
	if (input[0] == '>' && (input[1] == '<' || input[1] == '|'))
		return 1;
	else if (input[0] == '<' && (input[1] == '>' || input[1] == '|'))
		return 1;
	else
		return 0;
}

int	redirect_sem_arquivo(char *input)
{
	if (input[0] == '>' || input[0] == '<')
	{
		// Avanço os caracteres do redirect
		input++;
		if (input[0] == '>' || input[0] == '<')
			input++;

		// Avanço o espaço depois do redirect (se houver)
		if (*input == ' ')
			input++;

		// Verifico se estou no fim da string
		if (*input == '\0')
			return 1; // Erro de digitação
	}
	return 0;
}

int	tem_redirect_errado(char *input)
{
	char	quote;
	char	*next;

	while (*input)
	{
		if (redirect_mal_digitado(input))
			return 1; // tem redirect errado
		else if (redirect_sem_arquivo(input))
			return 1;
		input++;
	}
	return 0; // Tá tudo certo
}
```

## Pipes

Pipes separam 2 comandos. 

Um código simplificado para validar pipes é:

```c

int	tem_pipe_invalido(char *input)
{
	while (*input)
	{
		if (input[0] == '|')
		{
			if(input[1] == '\0' || (input[1] == ' ' && input[2] == '\0'))
				return 1;
		}
		input++;
	}
	return 0;
}

```

# Inventar um título para esse B.O.

Com as funções de validação feitas, basta fazer a validação da main.

```c
#include <readline/readline.h>
#include <stdio.h>

int	main(void)
{
	char	*input = readline("prompt> ");

	if (tem_aspas_que_nao_fecham(input))
	{
		printf("Arrume as aspas!\n");
		return (1);
	}
	else if (tem_redirect_errado(input))
	{
		printf("O redirect tá com defeito!\n");
		return (1);
	}
	else if (tem_pipe_invalido(input))
	{
		printf("Separar os comandos com excelência é o segredo!\n");
		return (1);
	}
	return 0;
}
```

