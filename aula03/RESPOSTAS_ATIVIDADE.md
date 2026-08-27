1. Linguagem escolhida: Python. Achei mais facil de achar a gramatica completa e o proprio enunciado aponta a documentacao oficial.

Fonte da gramatica: https://docs.python.org/3/reference/grammar.html
Pra entender a notacao tambem olhei https://docs.python.org/pt-br/3.13/reference/introduction.html

A pagina em portugues descreve uma BNF modificada (tipo EBNF), com ::= , | pra alternativa, * + e [ ] pra opcional. Ja a gramatica completa do CPython e PEG, tem corte (~), lookahead (& e !) e a ordem das alternativas importa. Os nomes em maiusculo tipo NAME NUMBER NEWLINE ENDMARKER sao tokens do lexer, as strings com aspas simples sao palavras chave. Eu reescrevi as regras no formato ::= do exemplo da aula pra ficar mais parecido, mas o conteudo veio dessa gramatica PEG.

2. Nao usei a gramatica toda, e enorme. Selecionei so o que precisa pra gerar x = 1 + 2, que junta atribuicao e expressao aritmetica, que era uma das sugestoes.

Regras (bem recortadas):

file ::= [statements] ENDMARKER
statements ::= statement+
statement ::= compound_stmt | simple_stmts
simple_stmts ::= simple_stmt NEWLINE
simple_stmt ::= assignment | star_expressions
assignment ::= star_targets "=" annotated_rhs
annotated_rhs ::= star_expressions
star_expressions ::= star_expression
star_expression ::= expression
expression ::= disjunction
star_targets ::= star_target
star_target ::= target_with_star_atom
target_with_star_atom ::= star_atom
star_atom ::= NAME
sum ::= sum "+" term | term
term ::= factor
factor ::= power
power ::= await_primary
await_primary ::= primary
primary ::= atom
atom ::= NAME | NUMBER

file e o simbolo inicial de um arquivo fonte. statements e um ou mais comandos. simple_stmt e um comando simples, assignment e a atribuicao com =. star_targets e o lado esquerdo (o x). annotated_rhs e o lado direito. A cadeia expression, disjunction, conjunction, inversion, comparison, bitwise_or ate chegar em sum existe por causa da precedencia dos operadores, cada nivel trata um tipo de conta. sum e a soma de verdade, term e multiplicacao e afins, atom e o pedaco mais basico tipo nome ou numero. NAME e NUMBER vem do lexer.

Tem mais uns niveis no meio (conjunction, bitwise_and, shift_expr etc) que neste codigo so encaminham pra regra de baixo, eu nao vou listar todos senao a derivacao vira um infinidade de setas iguais.

3. Codigo que vai ser derivado:

x = 1 + 2

E valido em Python, pequeno e bem parecido com o exemplo hipotetico do enunciado, so que agora com gramatica real.

4. Derivacao, comecando por file:

file
⇒ statements ENDMARKER
⇒ statement ENDMARKER
⇒ simple_stmts ENDMARKER
⇒ simple_stmt NEWLINE ENDMARKER
⇒ assignment NEWLINE ENDMARKER
⇒ star_targets "=" annotated_rhs NEWLINE ENDMARKER
⇒ star_target "=" annotated_rhs NEWLINE ENDMARKER
⇒ target_with_star_atom "=" annotated_rhs NEWLINE ENDMARKER
⇒ star_atom "=" annotated_rhs NEWLINE ENDMARKER
⇒ NAME "=" annotated_rhs NEWLINE ENDMARKER
⇒ x "=" annotated_rhs NEWLINE ENDMARKER
⇒ x "=" star_expressions NEWLINE ENDMARKER
⇒ x "=" star_expression NEWLINE ENDMARKER
⇒ x "=" expression NEWLINE ENDMARKER
⇒ x "=" sum NEWLINE ENDMARKER
⇒ x "=" sum "+" term NEWLINE ENDMARKER
⇒ x "=" term "+" term NEWLINE ENDMARKER
⇒ x "=" atom "+" term NEWLINE ENDMARKER
⇒ x "=" NUMBER "+" term NEWLINE ENDMARKER
⇒ x "=" 1 "+" term NEWLINE ENDMARKER
⇒ x "=" 1 "+" atom NEWLINE ENDMARKER
⇒ x "=" 1 "+" NUMBER NEWLINE ENDMARKER
⇒ x "=" 1 "+" 2 NEWLINE ENDMARKER

Do expression pro sum eu pulei a pilha de precedencia (disjunction, conjunction, inversion, comparison, bitwise_or, bitwise_xor, bitwise_and, shift_expr). Em todas essas regras a unica alternativa que serve aqui e ir descendo sem colocar operador nenhum. Do term ate atom a mesma coisa, factor, power, await_primary e primary so passam adiante. NEWLINE e ENDMARKER o lexer coloca no fim da linha e do arquivo, no codigo escrito a gente so ve x = 1 + 2.

5. Codigo final gerado:

x = 1 + 2

Comecei no file, desci ate um simple_stmt e escolhi assignment. A regra da atribuicao pede um alvo, o terminal "=" e uma expressao a direita. O alvo virou NAME e depois o lexema x. A expressao foi descendo ate sum, a alternativa sum "+" term e a que gera a soma, dai o 1 e o 2 saem de NUMBER. O mais "=" e o "+" ja eram terminais na producao, nao precisei inventar eles.

Nao terminais: file, statements, statement, simple_stmts, simple_stmt, assignment, star_targets, star_target, target_with_star_atom, star_atom, annotated_rhs, star_expressions, star_expression, expression, sum, term, factor, power, await_primary, primary, atom, NAME, NUMBER, NEWLINE, ENDMARKER. NAME NUMBER NEWLINE e ENDMARKER sao nao terminais da sintaxe mas terminais da analise lexica, o parser ja recebe eles prontos.

Terminais: "=", "+", e os lexemas "x", "1", "2".
