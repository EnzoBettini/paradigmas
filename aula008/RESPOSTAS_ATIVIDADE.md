Exercicio em duplas: qual e a saida? (enunciado nas imagens ex1.png, ex2.png, ex3.png)

1 Python

```python
def adicionar(item, lista=[]):
    lista.append(item)
    return lista

print(adicionar(1))
print(adicionar(2))
```

Saida:
```
[1]
[1, 2]
```

Conceito: argumento padrao mutavel. A lista [] e criada uma vez so, nao a cada chamada. A segunda chamada usa a mesma lista da primeira.

2 Java

```java
static void zera(int[] v, int n) {
    v[0] = 0;
    n = 0;
}
int[] v = {5, 5}; int n = 5;
zera(v, n);
System.out.println(v[0] + " " + n);
```

Saida:
```
0 5
```

Conceito: passagem por valor. O array e referencia, entao v[0] muda de fora. O int n e copia, zerar dentro da funcao nao muda o n do main.

3 Python

```python
fs = [lambda: i for i in range(3)]
print([f() for f in fs])
```

Saida:
```
[2, 2, 2]
```

Conceito: closure e ligacao tardia. O i nao fica congelado em 0, 1 e 2 no momento do lambda. Quando chama f(), o i ja vale 2 (ultimo do range).

4 C

```c
int contador(void) {
    static int n = 0;
    return ++n;
}
// em main:
contador(); contador();
printf("%d\n", contador());
```

Saida:
```
3
```

Conceito: variavel estatica local. O n guarda valor entre chamadas. As duas primeiras chamadas somam sem imprimir, a terceira imprime 3.

5 Rust

```rust
fn dobra(v: Vec<i32>) -> Vec<i32> {
    v.iter().map(|x| x * 2).collect()
}
let v = vec![1, 2, 3];
let d = dobra(v);
println!("{:?} {:?}", v, d);
```

Saida: nao compila. Erro borrow of moved value: v. O vetor v foi movido pra dobra(v) e nao da pra usar de novo no println.

Conceito: ownership e move. Vec nao e Copy, quem recebe dobra fica dono do vetor.

6 Python

```python
total = 0

def adiciona(x):
    total = total + x
    return total

print(adiciona(5))
```

Saida: UnboundLocalError. Nao chega a imprimir nada.

Conceito: escopo. Como tem total = ... dentro da funcao, Python trata total como variavel local. Na hora de ler total + x ainda nao existe local, da erro. Pra somar no global precisaria global total.
