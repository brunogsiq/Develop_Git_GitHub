# Git — Estudos

## 1. Objetivo deste documento

Este documento consolida, de forma detalhada e autossuficiente, os principais conceitos discutidos no chat de estudos sobre Git e GitHub.

O conteúdo foi organizado para servir como material de consulta futura, mesmo após a exclusão da conversa original.

Os tópicos abordados são:

1. O que acontece ao executar `git add` em uma pasta;
2. O significado do aviso `LF will be replaced by CRLF`;
3. Diferença entre erro e warning no Git;
4. Conceito de staging area;
5. Como verificar a configuração `core.autocrlf`;
6. Diferença entre Branch e Tag no Git/GitHub;
7. Como criar e publicar tags;
8. Relação entre commit, branch e tag;
9. Exemplos práticos aplicados a projetos de desenvolvimento e QA.

---

# 2. Git add e a Staging Area

## 2.1. Comando utilizado

Durante o estudo foi utilizado o comando:

```bash
git add frontend/
```

Esse comando instrui o Git a adicionar à **staging area** as alterações existentes dentro da pasta `frontend/`.

Se a pasta contiver, por exemplo:

```text
frontend/
├── index.html
├── script.js
└── style.css
```

o comando:

```bash
git add frontend/
```

prepara as alterações desses arquivos para o próximo commit.

---

## 2.2. O que é a Staging Area

A Staging Area, também chamada de **Index**, é uma área intermediária entre os arquivos que estão sendo modificados localmente e o próximo commit.

O fluxo básico do Git pode ser entendido assim:

```text
Working Directory
      ↓
   git add
      ↓
Staging Area
      ↓
 git commit
      ↓
Repository
```

### Working Directory

É a pasta onde os arquivos estão sendo editados normalmente.

Exemplo:

```text
index.html
script.js
style.css
```

Ao editar `script.js`, a alteração inicialmente existe apenas no Working Directory.

### Staging Area

Quando é executado:

```bash
git add script.js
```

o Git registra aquela versão do arquivo como candidata para entrar no próximo commit.

### Repository

Quando é executado:

```bash
git commit -m "Atualiza script do frontend"
```

aquilo que estava na Staging Area passa a fazer parte do histórico oficial do repositório.

---

## 2.3. Como verificar o resultado do git add

Após executar:

```bash
git add frontend/
```

é recomendável utilizar:

```bash
git status
```

O Git poderá mostrar algo semelhante a:

```text
Changes to be committed:
  new file:   frontend/index.html
  new file:   frontend/script.js
  new file:   frontend/style.css
```

ou, caso os arquivos já existissem anteriormente:

```text
Changes to be committed:
  modified:   frontend/index.html
  modified:   frontend/script.js
  modified:   frontend/style.css
```

Isso confirma que os arquivos estão preparados para o próximo commit.

---

# 3. Aviso: LF will be replaced by CRLF

## 3.1. Exemplo do aviso

Ao executar:

```bash
git add frontend/
```

foram exibidos avisos semelhantes a:

```text
warning: in the working copy of 'frontend/index.html', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'frontend/script.js', LF will be replaced by CRLF the next time Git touches it
warning: in the working copy of 'frontend/style.css', LF will be replaced by CRLF the next time Git touches it
```

Esse conteúdo é um **warning**, não um erro.

O comando `git add` foi executado normalmente.

---

# 4. O que são LF e CRLF

LF e CRLF representam formas diferentes de registrar uma quebra de linha dentro de arquivos de texto.

Quando uma pessoa pressiona `Enter` em um arquivo, o editor grava um caractere especial indicando que uma linha terminou e outra começou.

Historicamente, diferentes sistemas operacionais adotaram convenções diferentes.

| Sistema operacional | Quebra de linha comum | Significado |
|---|---|---|
| Linux | `LF` | Line Feed |
| macOS moderno | `LF` | Line Feed |
| Windows | `CRLF` | Carriage Return + Line Feed |

---

## 4.1. LF

`LF` significa:

```text
Line Feed
```

É representado tecnicamente pelo caractere:

```text
\n
```

Exemplo conceitual:

```text
linha 1\n
linha 2\n
linha 3
```

---

## 4.2. CRLF

`CRLF` significa:

```text
Carriage Return + Line Feed
```

É representado por:

```text
\r\n
```

Exemplo conceitual:

```text
linha 1\r\n
linha 2\r\n
linha 3
```

---

# 5. Por que o Git exibiu esse warning

O arquivo estava utilizando quebras de linha `LF`, mas a configuração local do Git no Windows estava preparada para trabalhar com `CRLF` na cópia local.

O Git então informou algo equivalente a:

> O arquivo atualmente utiliza LF. Quando o Git precisar escrever novamente esse arquivo na cópia de trabalho, ele poderá utilizar CRLF.

Representação simplificada:

```text
Arquivo atual
    ↓
   LF
    ↓
Git no Windows
    ↓
  CRLF
```

Isso normalmente não altera a lógica do HTML, JavaScript ou CSS.

---

# 6. Warning não é Error

Uma distinção importante durante o uso do Git é:

```text
ERROR   → alguma operação falhou ou não pôde ser concluída.
WARNING → a operação ocorreu, mas existe alguma condição que merece atenção.
```

No caso estudado:

```bash
git add frontend/
```

funcionou.

Os arquivos foram adicionados à Staging Area.

O Git apenas avisou sobre a normalização das quebras de linha.

---

# 7. Configuração core.autocrlf

Uma das configurações responsáveis por esse comportamento é:

```bash
core.autocrlf
```

Para verificar a configuração global atual:

```bash
git config --global core.autocrlf
```

Se o resultado for:

```text
true
```

isso significa que o Git está configurado para realizar conversões automáticas de quebra de linha apropriadas ao fluxo típico do Windows.

---

## 7.1. O que normalmente significa core.autocrlf=true

Em termos simplificados:

```text
No repositório Git → LF
Na cópia de trabalho do Windows → CRLF
```

Isso ajuda equipes que possuem desenvolvedores trabalhando em sistemas operacionais diferentes.

Por exemplo:

```text
Desenvolvedor A → Windows
Desenvolvedor B → Linux
Desenvolvedor C → macOS
```

O objetivo é evitar que commits sejam poluídos apenas por diferenças de quebra de linha.

---

## 7.2. Estados comuns de core.autocrlf

### true

Muito utilizado no Windows.

```bash
git config --global core.autocrlf true
```

Comportamento simplificado:

```text
Checkout → CRLF
Commit   → LF
```

---

### input

Mais comum em Linux/macOS quando se deseja garantir LF no repositório.

```bash
git config --global core.autocrlf input
```

Comportamento simplificado:

```text
Checkout → não converte
Commit   → converte CRLF para LF
```

---

### false

Desativa a conversão automática.

```bash
git config --global core.autocrlf false
```

Nesse caso, o Git não fará a normalização automaticamente através desse mecanismo.

---

# 8. É necessário corrigir o warning?

Não necessariamente.

Se o projeto estiver funcionando corretamente e não houver uma convenção específica definida pela equipe, o warning pode ser apenas informativo.

No entanto, em projetos profissionais, é recomendável que o repositório tenha uma política explícita de line endings.

Uma solução comum é utilizar um arquivo:

```text
.gitattributes
```

Exemplo simples:

```gitattributes
* text=auto
```

Ou, para forçar LF em determinados arquivos:

```gitattributes
*.js   text eol=lf
*.html text eol=lf
*.css  text eol=lf
```

Dessa forma, a política fica associada ao próprio projeto e não depende apenas da configuração individual de cada desenvolvedor.

---

# 9. Fluxo básico depois do git add

Depois de executar:

```bash
git add frontend/
```

um fluxo comum seria:

```bash
git status
```

Depois:

```bash
git commit -m "Adiciona frontend"
```

E, caso exista um repositório remoto configurado:

```bash
git push
```

Fluxo conceitual:

```text
Editar arquivos
     ↓
git status
     ↓
git add
     ↓
git status
     ↓
git commit
     ↓
git push
```

---

# 10. Branches no Git e GitHub

No GitHub existe uma área relacionada às **Branches**.

Uma branch representa uma linha de desenvolvimento que pode continuar avançando à medida que novos commits são criados.

Exemplo:

```text
A ── B ── C ── D
              ↑
             main
```

Aqui `main` aponta para o commit `D`.

Se um novo commit `E` for criado:

```text
A ── B ── C ── D ── E
                   ↑
                  main
```

A branch se move para acompanhar o novo commit.

---

# 11. Criando uma branch

Exemplo moderno:

```bash
git switch -c feature/login
```

Isso cria uma branch chamada:

```text
feature/login
```

e muda imediatamente para ela.

Fluxo possível:

```text
main
 │
 A ─ B ─ C
         \
          D ─ E
              ↑
       feature/login
```

A branch de funcionalidade pode continuar recebendo novos commits sem alterar imediatamente a `main`.

---

# 12. O que é uma Tag

No GitHub, próximo à visualização de branches, também existe uma área de **Tags**.

Uma tag é utilizada para identificar um commit específico com um nome amigável e normalmente permanente.

Tags são muito utilizadas para marcar versões de software.

Exemplos:

```text
v1.0.0
v1.1.0
v1.2.0
v2.0.0
```

---

# 13. Branch versus Tag

A diferença principal é:

```text
Branch → acompanha o desenvolvimento.
Tag    → marca um ponto específico do histórico.
```

Exemplo:

```text
A ── B ── C ── D ── E ── F
         ↑         ↑       ↑
       v1.0.0    v1.1.0   main
```

A branch `main` continua avançando.

As tags `v1.0.0` e `v1.1.0` permanecem apontando para os commits onde foram criadas.

---

# 14. Analogia simples

Uma forma útil de memorizar é:

```text
Commit = fotografia do projeto em determinado momento.
Branch = linha de trabalho que continua avançando.
Tag    = etiqueta colocada em uma fotografia importante.
```

Exemplo:

```text
Commit: 8f71ac2
```

Para uma pessoa, esse identificador não é muito intuitivo.

Ao adicionar:

```text
v1.0.0
```

o significado passa a ser muito mais claro:

```text
O commit 8f71ac2 representa a versão 1.0.0 do sistema.
```

---

# 15. Comparação detalhada: Branch x Tag

| Característica | Branch | Tag |
|---|---|---|
| Objetivo | Desenvolvimento contínuo | Marcar um ponto específico |
| Avança com commits? | Sim | Normalmente não |
| Uso comum | Funcionalidades, correções, ambientes | Releases e versões |
| Exemplos | `main`, `develop`, `feature/login` | `v1.0.0`, `v1.1.0` |
| Pode receber novos commits? | Sim | Não diretamente |
| Ponteiro móvel? | Sim | Normalmente fixo |
| Uso em CI/CD | Muito comum | Muito comum para releases/deploys |

---

# 16. Exemplo de projeto real com tags

Considere a evolução de um projeto:

```text
Commit A → Criação inicial
Commit B → Frontend
Commit C → API
Commit D → Correção de bugs
Commit E → Nova funcionalidade
Commit F → Ajustes
```

Poderíamos marcar:

```text
A ── B ── C ── D ── E ── F
              ↑         ↑
            v1.0.0    v1.1.0
```

Nesse cenário:

```text
v1.0.0
```

representa a primeira versão estável.

Depois novos commits são criados.

Quando outra versão é considerada pronta:

```text
v1.1.0
```

é criada.

---

# 17. Criando uma Tag

Para criar uma tag simples no commit atual:

```bash
git tag v1.0.0
```

Isso cria a tag localmente.

Ela ainda não necessariamente foi enviada ao GitHub.

---

# 18. Enviando uma Tag ao GitHub

Para publicar uma tag específica:

```bash
git push origin v1.0.0
```

Depois disso ela poderá aparecer na área de Tags do GitHub.

---

# 19. Enviando várias Tags

Para enviar todas as tags locais que ainda não foram publicadas:

```bash
git push origin --tags
```

Esse comando deve ser utilizado com atenção, pois publica todas as tags locais ausentes no repositório remoto.

---

# 20. Listando Tags

Para visualizar as tags existentes localmente:

```bash
git tag
```

Exemplo de resultado:

```text
v1.0.0
v1.1.0
v1.2.0
v2.0.0
```

---

# 21. Tags anotadas

Além de tags simples, Git permite criar **annotated tags**.

Exemplo:

```bash
git tag -a v1.0.0 -m "Primeira versão estável"
```

Esse tipo de tag registra informações adicionais, como:

- autor da tag;
- data;
- mensagem;
- metadados próprios do objeto de tag.

Em projetos profissionais, annotated tags costumam ser preferidas para releases importantes.

---

# 22. Visualizando uma Tag

Para visualizar detalhes:

```bash
git show v1.0.0
```

O Git mostrará informações do commit associado àquela tag.

---

# 23. Criando uma Tag em um commit antigo

Uma tag não precisa ser criada apenas no commit atual.

Primeiro é possível visualizar o histórico:

```bash
git log --oneline
```

Exemplo:

```text
fa91c02 Corrige validação de login
8f71ac2 Finaliza versão inicial
31bb120 Cria frontend
```

Se o commit:

```text
8f71ac2
```

for a versão que deve receber `v1.0.0`, pode ser executado:

```bash
git tag v1.0.0 8f71ac2
```

Depois:

```bash
git push origin v1.0.0
```

---

# 24. Versionamento semântico

Tags de software frequentemente utilizam o padrão:

```text
MAJOR.MINOR.PATCH
```

Exemplo:

```text
v2.4.1
```

Pode ser interpretado como:

```text
2 → MAJOR
4 → MINOR
1 → PATCH
```

---

## 24.1. MAJOR

Incrementado quando existe mudança incompatível ou quebra relevante de compatibilidade.

Exemplo:

```text
v1.8.0 → v2.0.0
```

---

## 24.2. MINOR

Incrementado normalmente quando uma nova funcionalidade compatível é adicionada.

Exemplo:

```text
v1.3.0 → v1.4.0
```

---

## 24.3. PATCH

Incrementado normalmente para correções compatíveis.

Exemplo:

```text
v1.4.2 → v1.4.3
```

---

# 25. Aplicação prática em automação de testes

Em um projeto de QA com Cypress, Playwright ou outra ferramenta, uma estrutura poderia evoluir assim:

```text
Commit A → estrutura inicial de automação
Commit B → testes de login
Commit C → testes de cadastro
Commit D → correções
```

Depois:

```text
v1.0.0
```

poderia representar a primeira suíte estável.

Posteriormente:

```text
Commit E → testes da tela de usuários
Commit F → melhoria de Page Objects
Commit G → correções
```

Nova versão:

```text
v1.1.0
```

O histórico seria:

```text
A ─ B ─ C ─ D ─ E ─ F ─ G
            ↑           ↑
          v1.0.0      v1.1.0
                         ↑
                        main
```

---

# 26. Relação entre Git e GitHub

É importante separar os conceitos.

## Git

Git é o sistema de controle de versão.

Ele funciona localmente e possui conceitos como:

- repository;
- commit;
- branch;
- tag;
- staging area;
- merge;
- rebase;
- remote.

## GitHub

GitHub é uma plataforma online que hospeda repositórios Git e adiciona recursos colaborativos, como:

- Pull Requests;
- Issues;
- Actions;
- Releases;
- Code Review;
- gerenciamento de branches;
- visualização de tags;
- permissões;
- colaboração entre equipes.

Portanto:

```text
Git ≠ GitHub
```

Git pode existir sem GitHub.

GitHub utiliza Git como base.

---

# 27. Tags no GitHub

Quando uma tag é enviada ao GitHub:

```bash
git push origin v1.0.0
```

ela pode aparecer na aba/lista de Tags.

Essa área permite localizar rapidamente estados específicos do repositório associados a versões.

Em muitos projetos, tags também servem como base para criar **GitHub Releases**.

Exemplo conceitual:

```text
Tag: v1.2.0
       ↓
GitHub Release
       ↓
Notas da versão
Artefatos
Binários
Pacotes
```

---

# 28. Branches comuns em projetos

Alguns nomes encontrados com frequência:

```text
main
master
develop
feature/login
feature/cadastro
bugfix/login-validation
hotfix/payment-error
release/1.2.0
```

O padrão utilizado depende da estratégia da equipe.

Não existe obrigação universal de utilizar todas essas branches.

---

# 29. Exemplo de fluxo com feature branch

```text
main
 │
 A ── B ── C
         \
          D ── E ── F
                   ↑
             feature/login
```

Depois de concluir e revisar o trabalho, a feature pode ser integrada à `main`.

Exemplo conceitual:

```text
A ── B ── C ─────── G
         \          /
          D ── E ─ F
```

Após a integração, uma versão pode ser marcada:

```text
A ─ B ─ C ─ G
            ↑
          v1.1.0
            ↑
           main
```

---

# 30. Conceitos essenciais para memorizar

```text
Working Directory
= arquivos que estão sendo editados.

Staging Area
= alterações selecionadas para o próximo commit.

Commit
= registro permanente de um estado do projeto.

Branch
= ponteiro móvel utilizado para uma linha de desenvolvimento.

Tag
= marcador normalmente fixo para um commit específico.

Remote
= referência para outro repositório, normalmente hospedado remotamente.

GitHub
= plataforma que hospeda repositórios Git e fornece colaboração e automações.
```

---

# 31. Comandos estudados

## Ver status

```bash
git status
```

## Adicionar uma pasta à staging

```bash
git add frontend/
```

## Criar commit

```bash
git commit -m "Adiciona frontend"
```

## Ver configuração de line endings

```bash
git config --global core.autocrlf
```

## Criar branch e trocar para ela

```bash
git switch -c feature/login
```

## Criar tag

```bash
git tag v1.0.0
```

## Criar tag anotada

```bash
git tag -a v1.0.0 -m "Primeira versão estável"
```

## Listar tags

```bash
git tag
```

## Visualizar tag

```bash
git show v1.0.0
```

## Publicar tag

```bash
git push origin v1.0.0
```

## Publicar todas as tags

```bash
git push origin --tags
```

## Visualizar histórico resumido

```bash
git log --oneline
```

---

# 32. Resumo visual completo

```text
                     GIT
                      │
        ┌─────────────┼─────────────┐
        │             │             │
 Working Tree      Staging       Repository
        │             │             │
    arquivos       git add       git commit
    editados


HISTÓRICO

A ─── B ─── C ─── D ─── E
        ↑               ↑
      v1.0.0           main
        │
       Tag

main = Branch
v1.0.0 = Tag
A/B/C/D/E = Commits
```

---

# 33. Resumo final do aprendizado

Ao executar:

```bash
git add frontend/
```

o Git adiciona as alterações da pasta `frontend` à Staging Area.

O aviso:

```text
LF will be replaced by CRLF
```

não significa falha. Ele informa apenas que a convenção de quebras de linha utilizada nos arquivos poderá ser normalizada pelo Git de acordo com sua configuração e sistema operacional.

No GitHub, **Branches** representam linhas de desenvolvimento que continuam evoluindo, enquanto **Tags** normalmente representam marcadores permanentes associados a commits específicos, especialmente versões de software.

A associação mental mais importante é:

```text
Commit = fotografia.
Branch = linha de desenvolvimento.
Tag = etiqueta em uma fotografia importante.
```

Exemplo final:

```text
A ─ B ─ C ─ D ─ E ─ F
        ↑       ↑     ↑
      v1.0.0  v1.1.0 main
```

A `main` continuará avançando com novos commits.

As tags permanecerão marcando as versões anteriores.

---

# 34. Próximos assuntos recomendados para estudo

Como continuação natural deste conteúdo, os próximos tópicos recomendados são:

1. `git status` em profundidade;
2. `git diff`;
3. `git restore`;
4. `git reset`;
5. `git log`;
6. `git branch`;
7. `git switch`;
8. `git merge`;
9. conflitos de merge;
10. `git fetch`;
11. `git pull`;
12. `git push`;
13. `origin` e remotes;
14. Pull Requests no GitHub;
15. Code Review;
16. `.gitignore`;
17. `.gitattributes`;
18. Semantic Versioning;
19. GitHub Releases;
20. GitHub Actions e integração com CI/CD.

Esses assuntos formam uma progressão natural para compreender Git de forma prática e posteriormente conectar o conhecimento a processos de desenvolvimento, QA, automação e DevOps.
