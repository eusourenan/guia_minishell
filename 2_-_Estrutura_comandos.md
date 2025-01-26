# Estrutura de Comandos

Uma das dificuldades para quem começa o minishell é entender como os comandos são estruturados, pois parece não ter uma lógica. Porém, existe uma lógica.

Da mesma forma que você diz "oi, bom dia!" ao invés de "dia! oi, bom", os comandos possuem uma sequência lógica.

A estrutura básica de comandos é:

```
cmd [-flags] [args]

cmd -> Comando que você quer executar
flags -> opções de execução que podem alterar a resposta final
args -> Argumentos para a execução do comando
```

Colchetes indicam partes opcionais.

Para um exemplo, execute os comandos abaixo:

```sh
ls # Comando simples
ls -l -a # Comando com flags
ls file1 file2 # Comando com argumentos
```

Flags e argumentos são opcionais. Dependendo do comando podem estar misturados em qualquer ordem. Porém, o **comando sempre será a primeira coisa que é digitada**.

```sh
# ls considera flag o que começa com - indepedente da ordem
ls -a file # Comando com flag e argumento
ls file -a # Comando com flag e argumento

# echo considera flag o que começa com - e está imediatamente após o comando
echo -n mensagem # Comando com flag e argumento
echo mensagem -n # Comando somente com argumentos
```

## Redirects

Redirects são, como o nome sugere, redirecionadores. Mudam o fluxo de leitura/escrita do comando.

    <
    >
    <<
    >>