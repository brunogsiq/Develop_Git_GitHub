No GitHub, **Branches** e **Tags** apontam para estados do seu código, mas têm objetivos diferentes.

### Branch

Uma **branch** é uma linha de desenvolvimento que **continua evoluindo**.

Por exemplo:

```text
main
 │
 ├── commit A
 ├── commit B
 ├── commit C
 │
 └── commit D  ← main
```

Você pode criar uma branch para desenvolver uma funcionalidade:

```bash
git switch -c feature/login
```

E continuar fazendo commits nela:

```text
feature/login
 │
 ├── commit C
 ├── commit D
 ├── commit E
 └── commit F  ← branch avança
```

Conforme você faz commits, o ponteiro da branch acompanha os commits mais recentes.

---

### Tag

Uma **tag** normalmente serve para **marcar permanentemente um commit importante**, frequentemente uma versão publicada.

Imagine:

```text
A ── B ── C ── D ── E ── F
         ↑         ↑
       v1.0.0    v1.1.0
```

As tags `v1.0.0` e `v1.1.0` identificam exatamente aqueles commits.

Enquanto a branch continua:

```text
main
 ↓
A ─ B ─ C ─ D ─ E ─ F ─ G ─ H
        ↑       ↑
      v1.0.0  v1.1.0
```

as tags continuam apontando para os commits que foram marcados.

### Um exemplo próximo de um projeto real

Imagine que você está desenvolvendo uma aplicação:

```text
main
 │
 ├── Criação do projeto
 ├── Criação do frontend
 ├── Criação da API
 ├── Correção de bugs
 │
 ├── v1.0.0  ← primeira versão
 │
 ├── Nova funcionalidade
 ├── Correção
 │
 ├── v1.1.0  ← segunda versão
 │
 └── ...
```

Você poderia criar uma tag assim:

```bash
git tag v1.0.0
```

E enviá-la ao GitHub:

```bash
git push origin v1.0.0
```

Na aba **Tags** do GitHub apareceria `v1.0.0`.

### Branch × Tag

| Branch                             | Tag                               |
| ---------------------------------- | --------------------------------- |
| Linha de desenvolvimento           | Marca uma versão/ponto específico |
| Continua avançando                 | Normalmente permanece fixa        |
| Recebe novos commits               | Aponta para um commit determinado |
| `main`, `develop`, `feature/login` | `v1.0.0`, `v1.1.0`, `v2.0.0`      |
| Usada durante desenvolvimento      | Muito usada para releases/versões |

Uma forma simples de memorizar é:

**Branch = onde estou desenvolvendo.**
**Commit = uma fotografia do código naquele momento.**
**Tag = um nome colocado em uma fotografia importante.**

Por exemplo, `commit 8f71ac2` não é muito amigável para uma pessoa. Colocar a tag `v1.0.0` naquele commit deixa claro: **"este commit representa a versão 1.0.0 do sistema."**
