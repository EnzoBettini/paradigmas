1. Trecho: preco = 12 + taxa

Passo 1, o front end le o texto cru. Passo 2, a analise lexica quebra em lexemas e classifica tokens: preco (id), = (igual), 12 (num), + (mais), taxa (id). Passo 3, a analise sintatica confere se essa sequencia casa com uma gramatica tipo <atrib> ::= id = <expr>. Casa, entao o trecho e aceito.

Pra verificar, eu rodaria o lexer e conferia a lista de tokens na mao, depois tentaria derivar <atrib> ate chegar nesses tokens. Se a derivacao fecha e nao sobra token, esta certo.

2. O erro e achar que escrever FNB e uma fase solta do compilador. FNB nao processa entrada. Ela so descreve quais sequencias de tokens sao validas. Sem ligar isso na entrada e no parser, a gramatica vira um desenho no papel.

Conclusao reescrita: FNB e a especificacao da sintaxe. O analisador sintatico usa essas producoes pra aceitar ou rejeitar o fluxo de tokens que veio do lexico.

3. Entrada: soma(2, 3)

Lexer: soma -> id, ( -> abre, 2 -> num, , -> virgula, 3 -> num, ) -> fecha.
Parser decide se id ( num , num ) casa com <chamada> ::= id "(" <args> ")".
Saida: aceito, arvore com chamada no topo, id soma e dois nums embaixo. Se fosse soma(2,) o parser rejeitaria.

4. Sem separacao o parser le caractere a caractere, tem que pular espaco, comentario e montar nome/numero no meio da sintaxe. Um erro em 12x vira um diagnostico confuso, nao se sabe se falhou o numero ou a gramatica.

Com separacao o lexico ja entrega NUM e ID. O parser so olha categorias. 12x pode ser recusado no lexico (numero malformado) e o parser nem entra. Reconhecimento fica mais simples e o erro aponta a etapa certa.

5. Analise lexica e a etapa que le o fonte como sequencia de caracteres e devolve tokens.

Exemplo: no texto if x then o lexer emite IF, ID(x), THEN. O if deixa de ser duas letras e vira palavra reservada.

Se relaciona com a analise sintatica: o parser nao ve letra, ve token. Sem lexico bom o parser nao tem entrada utilizavel.

6. Entrada: total := 8 * qtd

Passo a passo:
total -> lexema "total", token ID
:= -> lexema ":=", token ATRIB
8 -> lexema "8", token NUM
* -> lexema "*", token MULT
qtd -> lexema "qtd", token ID

Lexema e o pedaco escrito. Token e a classe.

Verificacao: marcar no texto cada pedaco, conferir se a classe bate (letra+letra = ID, digito = NUM, simbolo conhecido = operador). Se sobrar caractere sem classe, errei.

7. Existem tres jeitos comuns de fazer o lexer: codigo manual com diagrama de estados, gerador a partir de regex (lex/flex) ou tabela de transicao. Tratar isso como etapa isolada e o erro. A abordagem so existe pra produzir tokens que o parser e o lookup de reservadas vao consumir.

Conclusao reescrita: a escolha da abordagem do lexer depende da entrada (alfabeto, palavras reservadas, formato de numero) e do que o sintatico espera receber. Nao e um modulo solto.

8. Classes: LETRA = [a-zA-Z], DIGITO = [0-9], BRANCO = espaco/tab.

Entrada: n2
Decisao: 'n' cai em LETRA, comeca ID, '2' cai em DIGITO e continua o mesmo ID (letra depois digito ainda e identificador).
Saida: um token ID com lexema n2.

Se a primeira classe fosse DIGITO, 2n pararia o numero no 2 e o n viraria outro token, ou daria erro lexico, dependendo da regra.

9. Sem tabela de reservadas, if e then sao so ID. O parser ve ID ID e nao reconhece um if. Diagnostico sai como "esperava comando", bem vago.

Com reconhecimento, if vira token IF. O parser escolhe a producao do if na hora. Se o aluno escreve iff, continua ID e o erro fica claro: nao e a palavra reservada.

10. getChar le o proximo caractere e ja classifica (letra, digito, desconhecido). addChar cola esse caractere no buffer do lexema. lookup consulta se o lexema montado e palavra reservada.

Exemplo: lendo else. getChar pega e, l, s, e. addChar vai formando "else". No fim lookup acha ELSE e o token nao fica ID.

Isso fecha com lexema/token e com o reconhecimento de reservadas: sem essas tres funcoes o diagrama de estados nao vira analisador de verdade.

11. Objetivos: ver se a cadeia de tokens e sentenca da gramatica, montar a arvore (ou equivalente) e apontar erro sintatico.

Trecho: media = nota1 + nota2
Tokens: ID ATRIB ID MAIS ID.
Gramatica: <atrib> ::= ID ATRIB <expr>    <expr> ::= <expr> MAIS ID | ID
Derivacao: <atrib> => ID ATRIB <expr> => ID ATRIB <expr> MAIS ID => ID ATRIB ID MAIS ID. Fecha.

Verificacao: se cada token foi consumido na ordem e a derivacao chega so em terminais, o objetivo de reconhecimento foi cumprido. Se eu trocar o + por =, a derivacao trava e o analisador tem que acusar erro.

12. Descendente nao e um metodo solto. Ela parte do simbolo inicial e tenta derivar a entrada token a token, sempre olhando o lookahead que o lexico entregou. Isolar isso e fingir que a arvore nasce sem consumir entrada.

Conclusao reescrita: analise descendente e uma estrategia do parser. Ela so funciona em cima da FNB e do fluxo de tokens, expandindo nao terminais da raiz ate casar o que o lexer mandou.

13. Gramatica: S ::= ( S ) | a
Entrada: ( a )

Pilha começa vazia. Desloca '(', desloca 'a', reduz a para S, desloca ')', reduz ( S ) para S. Pilha fica S e a entrada acaba.

Decisao: cada passo e shift ou reduce olhando o topo e o proximo token.
Saida: aceito, arvore com S na raiz, filhos (, S, ), e o S interno e a.

Se a entrada fosse ( a a ) sobra token depois da reducao e rejeita.

14. Sem a ideia de deslocar/reduzir um parser ascendente nao tem operacao. Ele nao sabe se guarda o token ou se troca um cabo (handle) por um nao terminal. O reconhecimento fica empirico e o erro nao aponta se faltou simbolo ou se a reducao era outra.

Com shift/reduce o diagnostico e preciso: shift em token inesperado = "sobrou coisa", reduce sem handle = "faltou estrutura". E o mecanismo do LR/SLR que o Sebesta descreve.

15. Analise sintatica geral pra qualquer GLC pode ser cubica no tamanho da entrada. Linguagem de programacao nao aguenta isso, entao a gramatica e restringida (LL, LR) e o parse fica linear.

Exemplo: um if aninhado de 200 linhas num compilador real e O(n). Se eu usasse um algoritmo geral tipo CYK na mesma gramatica, o tempo explodia.

Relaciona com descendente/ascendente: a gente escolhe essas familias justamente pra fugir da complexidade generica.

16. Gramatica sem recursao a esquerda:
<expr> ::= <term> <resto>
<resto> ::= + <term> <resto> | ε
<term> ::= id

Entrada: id + id

expr() chama term(), casa o primeiro id, chama resto(). resto ve + , consome, chama term(), casa o segundo id, chama resto() de novo, lookahead acaba, escolhe ε. Volta tudo.

Verificacao: cada funcao so avanca token quando casa um terminal. No fim nextToken tem que estar no fim da entrada. Se sobrar + sem id, term() falha.

17. FNBE tipo <expr> ::= <term> { + <term> } nao e enfeite. Ela existe pra virar o laco do parser descendente. Tratar como etapa isolada e desenhar chave sem ligar no processamento do lado direito da regra.

Conclusao reescrita: a FNBE comprime repeticao e opcao da FNB pra que o analisador recursivo saiba onde iterar e onde decidir. Sem isso, a regra nao vira codigo.

18. Regra: <atrib> ::= id = <expr>
Entrada de tokens: id = num

Processa o lado direito da esquerda pra direita. Primeiro simbolo e terminal id, casa com o token atual. Segundo e =, casa. Terceiro e nao terminal <expr>, chama a funcao de expr, que casa num.

Decisao: terminal => match, nao terminal => chamada.
Saida: atribuicao reconhecida. Se o segundo token fosse +, o match de = falha na hora.

19. A convencao e: nextToken sempre guarda o proximo token ainda nao usado. Toda funcao assume isso ao entrar e ao sair.

Sem a convencao uma funcao consome e a outra nao sabe. Lookahead fica velho, o parser casa o token errado ou pula um. Diagnostico aponta erro numa posicao que ja passou.

Com a convencao, depois de um match a gente ja le o seguinte. O erro cai no token atual de verdade.

20. Lookahead e olhar um ou mais tokens a frente sem consome-los de vez, pra escolher a producao.

Exemplo: <cmd> ::= id = <expr> | id ( <args> )
Os dois comecam com id. O lookahead de 2 (o token depois do id) decide: = e atribuicao, ( e chamada.

Relaciona com FIRST e com nextToken: o FIRST diz o que pode aparecer, o nextToken e o valor concreto desse lookahead.

21. Gramatica ruim: <expr> ::= <expr> + id | id
Entrada qualquer, tipo id + id.

Num descendente, expr() chama expr() de novo antes de ler token. Nao termina. A recursao a esquerda explode a pilha.

Reescrita: <expr> ::= id <resto>    <resto> ::= + id <resto> | ε
Agora a mesma entrada casa: id, depois + id, depois ε.

Verificacao: se a funcao do nao terminal e chamada sem consumir token e o primeiro simbolo da regra e ele mesmo, tem recursao a esquerda. Depois da reescrita, cada alternativa de <resto> comeca com terminal ou ε.

22. FIRST nao e uma tabelinha decorativa. FIRST(α) e o conjunto de tokens que podem comecar uma derivacao de α. O parser descendente usa isso na hora de ver o lookahead e escolher a alternativa. Isolar FIRST e calcular conjunto sem ligar na entrada.

Conclusao reescrita: FIRST existe pra decidir, dado o token atual, qual lado direito aplicar. Se o token nao esta em nenhum FIRST da regra, e erro sintatico.

23. Regra: <S> ::= if <E> then <S> | if <E> then <S> else <S>
FIRST das duas alternativas contem if. Intersecao nao e vazia, o teste de disjuncao par a par falha.

Entrada: a propria gramatica (os dois lados direitos).
Decisao: FIRST(alt1) ∩ FIRST(alt2) = {if} ≠ ∅, entao um lookahead de 1 nao escolhe.
Saida: gramatica nao e LL(1). Precisa reescrever (fatorar o if) ou aumentar o lookahead.

Se fosse <S> ::= a B | b C, FIRST = {a} e {b}, intersecao vazia, o teste passa e o parser decide no primeiro token.
