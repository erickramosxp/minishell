<div align="center">

# 🐚 Minishell

### *A handcrafted Unix shell written in C — bash reimagined from the ground up*
### *Um shell Unix construído à mão em C — o bash reimaginado do zero*

<br>

[![Language: C](https://img.shields.io/badge/Language-C-00599C?style=for-the-badge&logo=c&logoColor=white)](https://en.wikipedia.org/wiki/C_(programming_language))
[![Standard: C99](https://img.shields.io/badge/Standard-C99-00599C?style=for-the-badge)](https://en.wikipedia.org/wiki/C99)
[![Build: Make](https://img.shields.io/badge/Build-GNU%20Make-A42E2B?style=for-the-badge&logo=gnu&logoColor=white)](https://www.gnu.org/software/make/)
[![Platform: Linux](https://img.shields.io/badge/Platform-Linux%20%7C%20macOS-FCC624?style=for-the-badge&logo=linux&logoColor=black)](https://www.kernel.org/)
[![School: 42](https://img.shields.io/badge/42-School%20Project-000000?style=for-the-badge&logo=42&logoColor=white)](https://42.fr)
[![Score: 125/100](https://img.shields.io/badge/Score-101%2F100-brightgreen?style=for-the-badge)](https://42.fr)

<br>

**[🇧🇷 Português](#-português)** • **[🇺🇸 English](#-english)**

<br>

```
$ ./minishell
yufonten@minishell:~ $ echo "hello, world" | cat -e | tr a-z A-Z > out.txt
yufonten@minishell:~ $ cat out.txt
HELLO, WORLD$
```

</div>

---

## 🇧🇷 Português

### 📖 Sobre o projeto

**Minishell** é uma reimplementação minimalista do shell **bash**, escrita inteiramente em **C** sem dependências externas além da `libc` e da `GNU Readline`. O projeto reproduz o ciclo completo de um interpretador de comandos POSIX: leitura da linha de comando, análise léxica, análise sintática, expansão de variáveis, execução de processos, gestão de descritores de arquivos e tratamento de sinais — tudo isso com gestão manual de memória e zero *memory leaks*.

Este é um dos projetos mais desafiadores do currículo da **École 42**, exigindo domínio profundo de **chamadas de sistema POSIX**, **gerenciamento de processos** e **arquitetura de software** em baixo nível.

### ✨ Funcionalidades

| Categoria | Implementação |
|-----------|---------------|
| 🔧 **Builtins** | `cd`, `echo` (com flag `-n`), `env`, `exit`, `export`, `pwd`, `unset` |
| 🔀 **Pipes** | Encadeamento ilimitado de comandos via `\|` com gestão de FDs e *forks* |
| 📤 **Redirecionamentos** | `<` (input), `>` (output), `>>` (append), `<<` (heredoc) |
| 💲 **Expansão de variáveis** | `$VAR`, `$?` (exit status), preservação dentro de aspas duplas |
| 🗣️ **Aspas** | Aspas simples (literais) e duplas (com expansão) totalmente suportadas |
| ⚡ **Sinais** | `SIGINT` (Ctrl+C) e `SIGQUIT` (Ctrl+\\) tratados em foreground e durante heredoc |
| 🌍 **Variáveis de ambiente** | Manipulação completa via `export`/`unset`, herança em subprocessos |
| 🎨 **Prompt customizado** | Estilo bash colorido com usuário, host e diretório atual |
| 📜 **Histórico** | Navegação por comandos anteriores via setas (via Readline) |
| 🧹 **Zero leaks** | Validado com `valgrind --leak-check=full --show-leak-kinds=all` |

### 🏗️ Arquitetura

O projeto segue uma separação rigorosa de responsabilidades em módulos independentes:

```
minishell/
├── inc/                    # Headers públicos
│   └── minishell.h         # Definições centrais e structs
├── src/
│   ├── main.c              # Loop principal REPL
│   ├── prompt/             # 🎨 Renderização do prompt
│   ├── struct/             # 📦 Tokenização (linha → lista duplamente ligada)
│   ├── parser/             # 🧠 Análise léxica, sintática e expansão
│   │   ├── check_input.c   #    Validação de entrada
│   │   ├── check_syntax.c  #    Validação de sintaxe (pipes/redirects)
│   │   ├── analyzer.c      #    Classificação semântica de tokens
│   │   ├── expander.c      #    Expansão de variáveis ($VAR, $?)
│   │   └── remove_quotes.c #    Remoção de aspas pós-expansão
│   ├── executor/           # ⚙️ Engine de execução (fork/execve/wait)
│   │   ├── ft_pipe.c       #    Pipeline recursivo de processos
│   │   └── path_commad.c   #    Resolução de PATH
│   ├── builtins/           # 🔧 Implementações dos comandos internos
│   ├── redirect/           # 📤 Redirecionamentos e heredocs
│   ├── signals/            # ⚡ Handlers de SIGINT/SIGQUIT
│   └── utils/              # 🛠️ Helpers e gestão de ambiente
├── libft/                  # 📚 Biblioteca C customizada
└── ft_fprintf/             # 🖨️ Implementação própria de printf
```

#### Pipeline de execução

```
┌─────────────┐    ┌───────────┐    ┌──────────────┐    ┌──────────┐    ┌──────────────┐
│   readline  │───▶│  tokenize │───▶│   analyzer   │───▶│ expander │───▶│ check_syntax │
└─────────────┘    └───────────┘    └──────────────┘    └──────────┘    └──────────────┘
                                                                                │
                                                                                ▼
┌─────────────┐    ┌───────────┐    ┌──────────────┐    ┌──────────┐    ┌──────────────┐
│  cleanup    │◀───│  waitpid  │◀───│   execve     │◀───│   fork   │◀───│  redirections│
└─────────────┘    └───────────┘    └──────────────┘    └──────────┘    └──────────────┘
```

### 🧠 Conhecimentos técnicos demonstrados

Este projeto consolida um conjunto sólido e profissional de competências em **engenharia de sistemas**:

#### 💻 Programação em C de nível avançado
- Gerenciamento manual de memória (`malloc`/`free`) com **zero leaks**
- Estruturas de dados customizadas (listas duplamente ligadas, enums de estado)
- Manipulação intensa de ponteiros, ponteiros duplos e strings
- Modularização com headers e separação clara entre interface e implementação
- Conformidade estrita com **Norminette** (padrão de código da École 42)

#### 🐧 Programação de sistemas Unix/POSIX
- **Process management**: `fork()`, `execve()`, `wait()`, `waitpid()`, `exit()`
- **IPC**: `pipe()` para comunicação entre processos encadeados
- **File descriptors**: `dup()`, `dup2()`, `close()`, `open()`, `unlink()`
- **Signal handling**: `signal()`, `SIGINT`, `SIGQUIT`, `WIFSIGNALED`, `WTERMSIG`
- **Terminal control**: `ioctl()` com `TIOCSTI` para manipulação de TTY
- **Environment**: leitura, escrita e propagação do ambiente em subprocessos

#### 🔬 Teoria de compiladores aplicada
- **Tokenizer/Lexer** com reconhecimento de operadores, palavras, aspas e variáveis
- **Parser** sintático com validação de gramática (pipes/redirects/comandos)
- **AST-like token list**: estrutura encadeada com metadados semânticos
- **Expansion phase**: substituição de variáveis com precedência correta de aspas

#### 🛠️ Engenharia de software
- **Build system** modular com `Makefile` (compilação incremental, regras `re`/`fclean`)
- **Bibliotecas estáticas**: integração de `libft.a` e `libftprintf.a` (escritas do zero)
- **Tratamento robusto de erros**: códigos de saída fiéis ao bash
- **Debugging avançado** com `gdb` (`-ggdb3`) e `valgrind` (com supressões customizadas)
- **Versionamento Git** com histórico limpo e commits semânticos
- **Pair programming**: projeto desenvolvido em dupla, com divisão clara de responsabilidades

### 🚀 Como compilar e executar

#### Pré-requisitos

```bash
sudo apt install build-essential libreadline-dev   # Debian/Ubuntu
brew install readline                              # macOS
```

#### Build

```bash
git clone https://github.com/YuriFont/minishell.git
cd minishell
make
```

#### Execução

```bash
./minishell
```

#### Limpeza

```bash
make clean    # remove objetos
make fclean   # remove objetos + binário
make re       # rebuild completo
```

#### Auditoria de memória

```bash
make valgrind_supression
```

### 🧪 Exemplos de uso

```bash
# Pipes encadeados
$ ls -la | grep .c | wc -l

# Redirecionamento + heredoc
$ cat << EOF > greeting.txt
Hello from minishell!
EOF

# Expansão de variáveis e exit status
$ echo "User $USER finished with code $?"

# Variáveis de ambiente
$ export MY_VAR="42"
$ env | grep MY_VAR
```

### 👤 Autores

**Yuri Fontenele** — Estudante na [42 Rio](https://42.rio/)
- 🐙 GitHub: [@YuriFont](https://github.com/YuriFont)
- 🏫 Intra 42: [yufonten](https://profile.intra.42.fr/users/yufonten)
**Erick Ramos** — Estudante na [École 42](https://42.fr)
- 🐙 GitHub: [@erickramosxp](https://github.com/erickramosxp)
- 🏫 Intra 42: [yufonten](https://profile.intra.42.fr/users/erramos)
---

## 🇺🇸 English

### 📖 About the project

**Minishell** is a minimal reimplementation of the **bash** shell, written entirely in **C** with no external dependencies beyond `libc` and `GNU Readline`. The project reproduces the full lifecycle of a POSIX command interpreter: command-line reading, lexical analysis, syntactic parsing, variable expansion, process execution, file descriptor handling and signal management — all with manual memory management and zero memory leaks.

This is one of the most challenging projects in the **École 42** curriculum, requiring deep mastery of **POSIX system calls**, **process management** and low-level **software architecture**.

### ✨ Features

| Category | Implementation |
|----------|----------------|
| 🔧 **Builtins** | `cd`, `echo` (with `-n` flag), `env`, `exit`, `export`, `pwd`, `unset` |
| 🔀 **Pipes** | Unlimited command chaining via `\|` with FD and *fork* management |
| 📤 **Redirections** | `<` (input), `>` (output), `>>` (append), `<<` (heredoc) |
| 💲 **Variable expansion** | `$VAR`, `$?` (exit status), preserved inside double quotes |
| 🗣️ **Quotes** | Single (literal) and double (with expansion) quotes fully supported |
| ⚡ **Signals** | `SIGINT` (Ctrl+C) and `SIGQUIT` (Ctrl+\\) handled in foreground and heredoc |
| 🌍 **Environment variables** | Full manipulation via `export`/`unset`, inheritance to subprocesses |
| 🎨 **Custom prompt** | Bash-style colored prompt with user, host and current directory |
| 📜 **History** | Navigation of past commands via arrow keys (Readline integration) |
| 🧹 **Zero leaks** | Validated with `valgrind --leak-check=full --show-leak-kinds=all` |

### 🏗️ Architecture

The project enforces a strict separation of concerns across independent modules:

```
minishell/
├── inc/                    # Public headers
│   └── minishell.h         # Core definitions and structs
├── src/
│   ├── main.c              # Main REPL loop
│   ├── prompt/             # 🎨 Prompt rendering
│   ├── struct/             # 📦 Tokenization (line → doubly-linked list)
│   ├── parser/             # 🧠 Lexing, syntax and expansion
│   │   ├── check_input.c   #    Input validation
│   │   ├── check_syntax.c  #    Syntax validation (pipes/redirects)
│   │   ├── analyzer.c      #    Semantic token classification
│   │   ├── expander.c      #    Variable expansion ($VAR, $?)
│   │   └── remove_quotes.c #    Post-expansion quote removal
│   ├── executor/           # ⚙️ Execution engine (fork/execve/wait)
│   │   ├── ft_pipe.c       #    Recursive process pipeline
│   │   └── path_commad.c   #    PATH resolution
│   ├── builtins/           # 🔧 Built-in command implementations
│   ├── redirect/           # 📤 Redirections and heredocs
│   ├── signals/            # ⚡ SIGINT/SIGQUIT handlers
│   └── utils/              # 🛠️ Helpers and env management
├── libft/                  # 📚 Custom C library
└── ft_fprintf/             # 🖨️ Custom printf implementation
```

#### Execution pipeline

```
┌─────────────┐    ┌───────────┐    ┌──────────────┐    ┌──────────┐    ┌──────────────┐
│   readline  │───▶│  tokenize │───▶│   analyzer   │───▶│ expander │───▶│ check_syntax │
└─────────────┘    └───────────┘    └──────────────┘    └──────────┘    └──────────────┘
                                                                                │
                                                                                ▼
┌─────────────┐    ┌───────────┐    ┌──────────────┐    ┌──────────┐    ┌──────────────┐
│  cleanup    │◀───│  waitpid  │◀───│   execve     │◀───│   fork   │◀───│  redirections│
└─────────────┘    └───────────┘    └──────────────┘    └──────────┘    └──────────────┘
```

### 🧠 Skills demonstrated

This project consolidates a solid, professional set of **systems engineering** skills:

#### 💻 Advanced C programming
- Manual memory management (`malloc`/`free`) with **zero leaks**
- Custom data structures (doubly-linked lists, state enums)
- Heavy pointer, double-pointer and string manipulation
- Modular code with headers and clear interface/implementation split
- Strict compliance with **Norminette** (École 42's coding standard)

#### 🐧 Unix/POSIX systems programming
- **Process management**: `fork()`, `execve()`, `wait()`, `waitpid()`, `exit()`
- **IPC**: `pipe()` for inter-process communication in chained commands
- **File descriptors**: `dup()`, `dup2()`, `close()`, `open()`, `unlink()`
- **Signal handling**: `signal()`, `SIGINT`, `SIGQUIT`, `WIFSIGNALED`, `WTERMSIG`
- **Terminal control**: `ioctl()` with `TIOCSTI` for TTY manipulation
- **Environment**: reading, writing and propagating env across subprocesses

#### 🔬 Applied compiler theory
- **Tokenizer/Lexer** recognizing operators, words, quotes and variables
- **Syntactic parser** validating grammar (pipes/redirects/commands)
- **AST-like token list**: linked structure carrying semantic metadata
- **Expansion phase**: variable substitution honoring quote precedence

#### 🛠️ Software engineering
- Modular **build system** with `Makefile` (incremental builds, `re`/`fclean` rules)
- **Static libraries**: integration of `libft.a` and `libftprintf.a` (written from scratch)
- **Robust error handling**: exit codes faithful to bash
- **Advanced debugging** with `gdb` (`-ggdb3`) and `valgrind` (with custom suppressions)
- **Git versioning** with clean history and semantic commits
- **Pair programming**: developed in a duo with clearly split responsibilities

### 🚀 Build & Run

#### Prerequisites

```bash
sudo apt install build-essential libreadline-dev   # Debian/Ubuntu
brew install readline                              # macOS
```

#### Build

```bash
git clone https://github.com/YuriFont/minishell.git
cd minishell
make
```

#### Run

```bash
./minishell
```

#### Cleanup

```bash
make clean    # remove object files
make fclean   # remove objects + binary
make re       # full rebuild
```

#### Memory audit

```bash
make valgrind_supression
```

### 🧪 Usage examples

```bash
# Chained pipes
$ ls -la | grep .c | wc -l

# Redirection + heredoc
$ cat << EOF > greeting.txt
Hello from minishell!
EOF

# Variable expansion and exit status
$ echo "User $USER finished with code $?"

# Environment variables
$ export MY_VAR="42"
$ env | grep MY_VAR
```

### 👤 Authors

**Yuri Fontenele** — Estudante na [42 Rio](https://42.rio/)
- 🐙 GitHub: [@YuriFont](https://github.com/YuriFont)
- 🏫 Intra 42: [yufonten](https://profile.intra.42.fr/users/yufonten)
**Erick Ramos** — Estudante na [École 42](https://42.fr)
- 🐙 GitHub: [@erickramosxp](https://github.com/erickramosxp)
- 🏫 Intra 42: [yufonten](https://profile.intra.42.fr/users/erramos)
---

<div align="center">

### 🛠️ Tech Stack

![C](https://img.shields.io/badge/C-00599C?style=flat-square&logo=c&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=flat-square&logo=linux&logoColor=black)
![GNU Make](https://img.shields.io/badge/GNU%20Make-A42E2B?style=flat-square&logo=gnu&logoColor=white)
![Bash](https://img.shields.io/badge/Bash-4EAA25?style=flat-square&logo=gnubash&logoColor=white)
![Vim](https://img.shields.io/badge/Vim-019733?style=flat-square&logo=vim&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?style=flat-square&logo=git&logoColor=white)
![GDB](https://img.shields.io/badge/GDB-A42E2B?style=flat-square&logo=gnu&logoColor=white)
![Valgrind](https://img.shields.io/badge/Valgrind-512BD4?style=flat-square)

<br>

*Made with ❤️ and a lot of `fork()` calls.*

</div>
