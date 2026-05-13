# psmux Repository

Este repositório contém a configuração personalizada do psmux (terminal multiplexer para Windows) e documentação completa de instalação e uso.

## Sobre o psmux

psmux é um terminal multiplexer nativo para Windows, escrito em Rust, que oferece compatibilidade completa com tmux. Ele permite:

- Dividir painéis horizontalmente e verticalmente
- Múltiplas janelas com abas na barra de status
- Gerenciamento de sessões (desconectar e reconectar)
- Suporte completo a mouse
- Temas tmux
- Compatibilidade com arquivos `.tmux.conf`

## Instalação

### Opção 1: WinGet (Recomendado)

```powershell
winget install marlocarlo.psmux
```

### Opção 2: Scoop

```powershell
scoop bucket add psmux https://github.com/psmux/scoop-psmux
scoop install psmux
```

### Opção 3: Chocolatey

```powershell
choco install psmux
```

### Opção 4: Cargo

```powershell
cargo install psmux
```

### Opção 5: Script de Instalação (One-liner)

```powershell
irm https://raw.githubusercontent.com/psmux/psmux/master/scripts/install.ps1 | iex
```

### Requisitos

- Windows 10 ou Windows 11
- PowerShell 7+ (recomendado) ou cmd.exe

Para instalar o PowerShell 7:
```powershell
winget install --id Microsoft.PowerShell
```

## Configuração

O psmux é compatível com arquivos de configuração do tmux (`.tmux.conf`). Este repositório inclui um arquivo de configuração de exemplo.

### Usando o arquivo de configuração deste repositório

1. Copie o arquivo `.tmux.conf` para seu diretório home:

```powershell
# No PowerShell
Copy-Item .tmux.conf $HOME\.tmux.conf

# Ou no bash/cygwin
cp .tmux.conf ~/.tmux.conf
```

2. Recarregue a configuração (se o psmux já estiver rodando):

```powershell
psmux source-file ~/.tmux.conf
```

Ou pressione `Prefix + :` e digite:
```
:source-file ~/.tmux.conf
```

### Usando o arquivo psmux.conf personalizado

Este repositório também inclui o arquivo `psmux.conf` que contém sua configuração personalizada existente. Para usá-lo:

```powershell
# No PowerShell
Copy-Item psmux.conf $HOME\.tmux.conf

# Ou no bash/cygwin
cp psmux.conf ~/.tmux.conf
```

Ou você pode especificar o arquivo de configuração ao iniciar o psmux:

```powershell
psmux -f psmux.conf
```

### Localização do arquivo de configuração

O psmux procura o arquivo de configuração nos seguintes locais (em ordem):

1. `~/.tmux.conf` (Unix-style path)
2. `:USERPROFILE\.tmux.conf` (Windows PowerShell path)
3. Arquivo especificado com `-f` flag: `psmux -f /caminho/para/config.conf`

### Comandos Básicos

```powershell
psmux                        # Inicia uma nova sessão
psmux new-session -s work    # Sessão nomeada
psmux ls                     # Lista sessões
psmux attach -t work        # Conecta à sessão
psmux kill-session -t work   # Mata sessão
psmux --help                 # Mostra ajuda
```

**Nota**: psmux instala com aliases `tmux` e `pmux`. Você pode usar qualquer um dos três comandos:
- `psmux`
- `pmux`
- `tmux`

## Atalhos de Teclado (Key Bindings)

**Prefix padrão**: `Ctrl+b`

Para mudar o prefix, adicione ao seu `.tmux.conf`:
```
set -g prefix C-a
```

### Gerenciamento de Janelas

| Atalho | Ação |
|--------|------|
| `Prefix + c` | Criar nova janela |
| `Prefix + n` | Próxima janela |
| `Prefix + p` | Janela anterior |
| `Prefix + l` | Última janela ativa |
| `Prefix + w` | Seletor interativo de sessão/janela/painel |
| `Prefix + &` | Matar janela atual (com confirmação) |
| `Prefix + ,` | Renomear janela atual |
| `Prefix + '` | Prompt para índice da janela |
| `Prefix + 0-9` | Selecionar janela por número |

### Divisão de Painéis

| Atalho | Ação |
|--------|------|
| `Prefix + %` | Dividir painel esquerda/direita (horizontal) |
| `Prefix + "` | Dividir painel cima/baixo (vertical) |

### Navegação entre Painéis

| Atalho | Ação |
|--------|------|
| `Prefix + Arrow` | Navegar entre painéis (Cima/Baixo/Esquerda/Direita) |
| `Prefix + o` | Selecionar próximo painel (rotacionar) |
| `Prefix + ;` | Último painel ativo |
| `Prefix + q` | Mostrar números dos painéis (digite para mudar) |

### Gerenciamento de Painéis

| Atalho | Ação |
|--------|------|
| `Prefix + x` | Matar painel atual (com confirmação) |
| `Prefix + z` | Alternar zoom do painel (tela cheia) |
| `Prefix + {` | Trocar painel para cima |
| `Prefix + }` | Trocar painel para baixo |
| `Prefix + !` | Separar painel em nova janela |

### Redimensionamento de Painéis

| Atalho | Ação |
|--------|------|
| `Prefix + Ctrl+Arrow` | Redimensionar painel por 1 célula |
| `Prefix + Alt+Arrow` | Redimensionar painel por 5 células |

### Layouts

| Atalho | Ação |
|--------|------|
| `Prefix + Space` | Ciclar para próximo layout |
| `Prefix + Alt+1` | Layout even-horizontal |
| `Prefix + Alt+2` | Layout even-vertical |
| `Prefix + Alt+3` | Layout main-horizontal |
| `Prefix + Alt+4` | Layout main-vertical |
| `Prefix + Alt+5` | Layout tiled |

### Sessão

| Atalho | Ação |
|--------|------|
| `Prefix + d` | Desconectar da sessão |
| `Prefix + $` | Renomear sessão |
| `Prefix + s` | Seletor de sessões |
| `Prefix + (` | Mudar para sessão anterior |
| `Prefix + )` | Mudar para próxima sessão |

### Copiar/Colar

| Atalho | Ação |
|--------|------|
| `Prefix + [` | Entrar no modo de cópia/scroll |
| `Prefix + ]` | Colar do buffer |
| `Prefix + =` | Seletor interativo de buffers |

### Diversos

| Atalho | Ação |
|--------|------|
| `Prefix + :` | Prompt de comando |
| `Prefix + ?` | Listar atalhos (ajuda) |
| `Prefix + i` | Mostrar informações da janela/painel |
| `Prefix + t` | Modo relógio |

### Modo de Cópia/Scroll (Vi)

Entrar no modo de cópia com `Prefix + [`.

#### Movimento do Cursor

| Atalho | Ação |
|--------|------|
| `h` / `Left` | Mover cursor para esquerda |
| `j` / `Down` | Mover cursor para baixo |
| `k` / `Up` | Mover cursor para cima |
| `l` / `Right` | Mover cursor para direita |

#### Movimento de Palavras

| Atalho | Ação |
|--------|------|
| `w` / `b` / `e` | Próxima palavra / palavra anterior / fim da palavra |
| `W` / `B` / `E` | Variantes WORD (delimitadas por espaço) |

#### Movimento de Linhas

| Atalho | Ação |
|--------|------|
| `0` / `Home` | Início da linha |
| `$` / `End` | Fim da linha |
| `^` | Primeiro caractere não-branco |

#### Scroll

| Atalho | Ação |
|--------|------|
| `Ctrl+u` / `Ctrl+d` | Meia página para cima / baixo |
| `Ctrl+b` / `PageUp` | Página completa para cima |
| `Ctrl+f` / `PageDown` | Página completa para baixo |
| `g` | Topo do scrollback |
| `G` | Fundo (saída ao vivo) |

#### Seleção

| Atalho | Ação |
|--------|------|
| `Space` | Iniciar seleção de caracteres |
| `v` | Alternar seleção retangular |
| `V` | Seleção de linha |
| `Ctrl+v` | Alternar seleção retangular |

#### Copiar (Yank)

| Atalho | Ação |
|--------|------|
| `y` / `Enter` | Copiar seleção e sair |
| `D` | Copiar até fim da linha e sair |
| `A` | Adicionar seleção ao buffer |

#### Busca

| Atalho | Ação |
|--------|------|
| `/` | Buscar para frente |
| `?` | Buscar para trás |
| `n` / `N` | Próxima / anterior ocorrência |

#### Sair

| Atalho | Ação |
|--------|------|
| `Esc` / `q` | Sair do modo de cópia |
| `Ctrl+C` / `Ctrl+G` | Sair do modo de cópia |

### Bindings de Mouse

Quando `mouse on` (padrão):

| Ação | Comportamento |
|------|---------------|
| Clique esquerdo na aba | Mudar para janela clicada |
| Clique esquerdo no painel | Focar aquele painel |
| Clique/arrastar na borda | Redimensionar split interativamente |
| Scroll para cima/baixo | Scroll do painel |
| Arrastar mouse no modo de cópia | Selecionar texto → copiar automaticamente |
| Clique direito | Colar da área de transferência |

### Navegação em Seletores

Quando um seletor estiver aberto (`Prefix + s`, `Prefix + w`, `Prefix + =`, `Prefix + ?`):

| Atalho | Ação |
|--------|------|
| `Up` / `k` / `h` | Mover seleção para cima |
| `Down` / `j` / `l` | Mover seleção para baixo |
| `g` / `Home` | Ir para primeira entrada |
| `G` / `End` | Ir para última entrada |
| `PageUp` / `PageDown` | Página para cima / baixo |
| `1`..`9`, `0` | Adicionar dígito ao buffer de salto |
| `Backspace` | Editar o buffer de salto |
| `Enter` | Mudar para entrada selecionada |
| `p` | Alternar preview ao vivo |
| `x` | Matar sessão selecionada |
| `d` / `Delete` | Deletar buffer selecionado |
| `Esc` / `q` | Fechar o seletor |

## Recursos Adicionais

### Plugins

psmux suporta plugins compatíveis com tmux, incluindo:

- **psmux-resurrect**: Salvar/restaurar sessões
- **psmux-continuum**: Salvar/restaurar automático periódico
- **tpm** (Tmux Plugin Manager): Gerenciador de plugins

### Documentação Oficial

- Site oficial: https://psmux.pages.dev/
- GitHub: https://github.com/psmux/psmux
- Documentação de features: https://github.com/psmux/psmux/blob/master/docs/features.md
- Referência de comandos: https://github.com/psmux/psmux/blob/master/docs/scripting.md

### Suporte

- Issues: https://github.com/psmux/psmux/issues
- Discussões: https://github.com/psmux/psmux/discussions

## Licença

Este projeto de configuração é fornecido como está. O psmux é licenciado sob MIT License.
