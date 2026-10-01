Alunos
Enzo Ayres Bettini 241955272
João Paulo Candido 240007972
Miguel Falcão Costacurta 240113022

---

JavaScript

```javascript
console.log(0.1 * 3 === 0.3);
console.log(9007199254740993);
```

Saida:

```
false
9007199254740992
```

Python

```python
p = "maçã"
print(len(p), len(p.encode()))
```

Saida:

```
4 6
```

Go

```go
var b byte = 255
b++
fmt.Println(b)
```

Saida:

```
0
```

Java

```java
int[] v = new int[3];
System.out.println(v[0]);
System.out.println(v[3]);
```

Saida:

```
0
```

Depois da erro na execucao: `ArrayIndexOutOfBoundsException` (indice 3 nao existe).

Rust

```rust
let s = String::from("oi");
let t = s;
println!("{} {}", s, t);
```

Saida: nao roda. Erro na compilacao (`borrow of moved value: s`), porque `s` foi movido pra `t`.

C

```c
union { int i; float f; } u;
u.f = 1.0f;
printf("%d\n", u.i);
```

Saida:

```
1065353216
```
