# psmux Configuration

Este repositório contém a configuração personalizada do psmux (terminal multiplexer para Windows) otimizada para uso no Windows.

## Sobre o psmux

psmux é um terminal multiplexer nativo para Windows, escrito em Rust, que oferece compatibilidade completa com tmux. Ele permite:

- Dividir painéis horizontalmente e verticalmente
- Múltiplas janelas com abas na barra de status
- Gerenciamento de sessões (desconectar e reconectar)
- Suporte completo a mouse
- Compatibilidade com arquivos `.tmux.conf`

## Instalação

### Instalação via Scoop

```powershell
scoop bucket add psmux https://github.com/psmux/scoop-psmux
scoop install psmux
```

### Requisitos

- Windows 10 ou Windows 11
- PowerShell 7+ (recomendado) ou cmd.exe

Para instalar o PowerShell 7:
```powershell
scoop install powershell
```

## Configuração

### Configuração Manual

1. **Copiar o arquivo de configuração:**
```powershell
Copy-Item psmux.conf $HOME\.psmux.conf
```

2. **Recarregar a configuração (se o psmux já estiver rodando):**
```powershell
psmux source-file ~/.psmux.conf
```

Ou pressione `Ctrl+a + :` e digite:
```
:source-file ~/.psmux.conf
```

### Configuração Incluída

Esta configuração inclui:

- **Prefix**: `Ctrl+a` (mais comum que o padrão Ctrl+b)
- **Barra de status**: No topo com separadores entre janelas
- **Janela atual**: Destacada em verde escuro
- **Shell padrão**: PowerShell
- **Suporte a mouse**: Ativado
- **Keybindings personalizados**: `Ctrl+a + h` (split horizontal), `Ctrl+a + v` (split vertical)
- **Renumeramento automático**: Janelas são renumeradas quando uma é fechada
- **Histórico**: 5000 linhas
- **Plugins**: PPM (gerenciador de plugins) + psmux-resurrect (salvar/restaurar sessões)

## Plugins

O psmux suporta plugins gerenciados pelo **PPM** (Psmux Plugin Manager), inspirado no `tpm` do tmux.

### PPM — Psmux Plugin Manager

Repositório: [psmux-plugins/ppm](https://github.com/psmux/psmux-plugins/tree/main/ppm)

O PPM é o gerenciador oficial de plugins do psmux. Ele lê as declarações `@plugin` do `psmux.conf`, clona os repositórios em `~/.psmux/plugins/` e carrega cada plugin na inicialização.

#### Instalação do PPM

```powershell
git clone https://github.com/psmux/psmux-plugins.git "$env:TEMP\psmux-plugins"
Copy-Item "$env:TEMP\psmux-plugins\ppm" "$env:USERPROFILE\.psmux\plugins\ppm" -Recurse
Remove-Item "$env:TEMP\psmux-plugins" -Recurse -Force
```

#### Atalhos do PPM

| Atalho | Ação |
|--------|------|
| `Ctrl+a + I` | Instalar plugins declarados |
| `Ctrl+a + U` | Atualizar todos os plugins |
| `Ctrl+a + M` | Remover plugins não utilizados |

#### Formato de plugin no psmux.conf

```conf
set -g @plugin 'psmux-plugins/psmux-sensible'      # GitHub: psmux-plugins/psmux-sensible
set -g @plugin 'someone/their-plugin'               # GitHub: someone/their-plugin
set -g @plugin 'https://gitlab.com/user/plugin.git' # Qualquer URL git
```

---

### psmux-resurrect

Repositório: [psmux-plugins/psmux-resurrect](https://github.com/psmux/psmux-plugins/tree/main/psmux-resurrect)

Port do [tmux-resurrect](https://github.com/tmux-plugins/tmux-resurrect) para o psmux. Salva e restaura sessões completas entre reinicializações do sistema.

#### O que é salvo

- Todas as sessões e seus nomes
- Todas as janelas e seus nomes
- Layout exato dos painéis (splits horizontais e verticais com tamanhos)
- Diretório de trabalho de cada painel
- Janela ativa por sessão e painel ativo por janela
- Estado de zoom do painel e títulos de painéis
- Comandos de processos em execução (para restauração de processos)

#### Atalhos do psmux-resurrect

| Atalho | Ação |
|--------|------|
| `Ctrl+a + Ctrl+s` | Salvar sessão |
| `Ctrl+a + Ctrl+r` | Restaurar sessão |

#### Opções disponíveis no psmux.conf

```conf
# Diretório de salvamento personalizado (padrão: ~/.psmux/resurrect)
set -g @resurrect-dir '~/.psmux/resurrect'

# Salvar conteúdo dos painéis
set -g @resurrect-capture-pane-contents 'on'

# Processos adicionais para restaurar (separados por espaço)
# Padrão: python python3 node npm ssh wsl htop vim nvim less more tail
set -g @resurrect-processes 'ssh python node'

# Desativar restauração de processos
set -g @resurrect-processes 'false'

# Restaurar TODOS os processos (usar com cautela)
set -g @resurrect-processes ':all:'
```

#### Indicador de progresso na barra de status

Adicione `#{@resurrect-status}` ao `status-right` para exibir o progresso da restauração:

```conf
set -g status-right '#{@resurrect-status} | %H:%M %d-%b-%y'
```

Durante a restauração, a barra mostra:
```
psmux-resurrect: restoring [######--------] 3/7  devbox
```

---

### Instalação manual dos plugins (sem PPM)

Para instalar o psmux-resurrect diretamente sem usar o PPM:

```powershell
git clone https://github.com/psmux/psmux-plugins.git "$env:TEMP\psmux-plugins"
Copy-Item "$env:TEMP\psmux-plugins\psmux-resurrect" "$env:USERPROFILE\.psmux\plugins\psmux-resurrect" -Recurse
Remove-Item "$env:TEMP\psmux-plugins" -Recurse -Force
```

## Comandos Básicos

### Sessões

```powershell
psmux                        # Inicia uma nova sessão
psmux new-session -s work    # Sessão nomeada
psmux ls                     # Lista sessões ativas
psmux attach -t work        # Conecta à sessão específica
psmux kill-session -t work   # Mata sessão específica
psmux kill-server            # Mata todas as sessões e o servidor
```

**Nota**: psmux instala com aliases `tmux` e `pmux`. Você pode usar qualquer um dos três comandos.

### Janelas

```powershell
psmux new-window              # Cria nova janela na sessão atual
psmux new-window -n nome     # Cria nova janela com nome específico
psmux select-window -t 1     # Seleciona janela por número
psmux next-window            # Vai para próxima janela
psmux previous-window        # Vai para janela anterior
```

### Painéis

```powershell
psmux split-window -h         # Divide painel horizontalmente
psmux split-window -v         # Divide painel verticalmente
psmux select-pane -U         # Seleciona painel acima
psmux select-pane -D         # Seleciona painel abaixo
psmux select-pane -L         # Seleciona painel à esquerda
psmux select-pane -R         # Seleciona painel à direita
```

## Atalhos de Teclado (Key Bindings)

**Prefix**: `Ctrl+a`

### Gerenciamento de Janelas

| Atalho | Ação |
|--------|------|
| `Ctrl+a + c` | Criar nova janela |
| `Ctrl+a + n` | Próxima janela |
| `Ctrl+a + p` | Janela anterior |
| `Ctrl+a + l` | Última janela ativa |
| `Ctrl+a + w` | Seletor interativo de janelas |
| `Ctrl+a + &` | Matar janela atual (com confirmação) |
| `Ctrl+a + ,` | Renomear janela atual |
| `Ctrl+a + 0-9` | Selecionar janela por número |

### Divisão de Painéis

| Atalho | Ação |
|--------|------|
| `Ctrl+a + h` | Dividir painel horizontalmente (esquerda/direita) |
| `Ctrl+a + v` | Dividir painel verticalmente (cima/baixo) |
| `Ctrl+a + %` | Dividir painel horizontalmente (padrão tmux) |
| `Ctrl+a + "` | Dividir painel verticalmente (padrão tmux) |

### Navegação entre Painéis

| Atalho | Ação |
|--------|------|
| `Ctrl+a + ↑` | Navegar para painel acima |
| `Ctrl+a + ↓` | Navegar para painel abaixo |
| `Ctrl+a + ←` | Navegar para painel à esquerda |
| `Ctrl+a + →` | Navegar para painel à direita |
| `Ctrl+a + o` | Selecionar próximo painel (rotacionar) |
| `Ctrl+a + ;` | Último painel ativo |
| `Ctrl+a + q` | Mostrar números dos painéis (digite para mudar) |

### Gerenciamento de Painéis

| Atalho | Ação |
|--------|------|
| `Ctrl+a + x` | Matar painel atual (com confirmação) |
| `Ctrl+a + z` | Alternar zoom do painel (tela cheia) |
| `Ctrl+a + {` | Trocar painel para cima |
| `Ctrl+a + }` | Trocar painel para baixo |
| `Ctrl+a + !` | Separar painel em nova janela |

### Redimensionamento de Painéis

| Atalho | Ação |
|--------|------|
| `Ctrl+a + Ctrl+↑` | Redimensionar painel 1 célula para cima |
| `Ctrl+a + Ctrl+↓` | Redimensionar painel 1 célula para baixo |
| `Ctrl+a + Ctrl+←` | Redimensionar painel 1 célula para esquerda |
| `Ctrl+a + Ctrl+→` | Redimensionar painel 1 célula para direita |

### Sessões

| Atalho | Ação |
|--------|------|
| `Ctrl+a + d` | Desconectar da sessão (detach) |
| `Ctrl+a + s` | Seletor interativo de sessões |
| `Ctrl+a + $` | Renomear sessão atual |

### Copiar/Colar

| Atalho | Ação |
|--------|------|
| `Ctrl+a + [` | Entrar no modo de cópia/scroll |
| `Ctrl+a + ]` | Colar do buffer |
| `Ctrl+a + =` | Seletor interativo de buffers |

### Diversos

| Atalho | Ação |
|--------|------|
| `Ctrl+a + :` | Prompt de comando |
| `Ctrl+a + ?` | Listar todos os atalhos (ajuda) |
| `Ctrl+a + t` | Modo relógio |

### Modo de Cópia/Scroll

Entrar no modo de cópia com `Ctrl+a + [`.

#### Movimento do Cursor

| Atalho | Ação |
|--------|------|
| `h` / `←` | Mover cursor para esquerda |
| `j` / `↓` | Mover cursor para baixo |
| `k` / `↑` | Mover cursor para cima |
| `l` / `→` | Mover cursor para direita |

#### Scroll

| Atalho | Ação |
|--------|------|
| `Ctrl+u` | Meia página para cima |
| `Ctrl+d` | Meia página para baixo |
| `Ctrl+b` / `PageUp` | Página completa para cima |
| `Ctrl+f` / `PageDown` | Página completa para baixo |
| `g` | Topo do scrollback |
| `G` | Fundo (saída ao vivo) |

#### Seleção e Cópia

| Atalho | Ação |
|--------|------|
| `Space` | Iniciar seleção |
| `Enter` / `y` | Copiar seleção e sair |
| `Esc` / `q` | Sair do modo de cópia |

## Workflows de Uso

### Workflow 1: Desenvolvimento com Múltiplos Serviços

```powershell
# Iniciar sessão de desenvolvimento
psmux new-session -s dev

# Criar janelas para diferentes serviços
Ctrl+a + c          # Nova janela (serviço 1)
Ctrl+a + ,          # Renomear para "api"
npm run dev

Ctrl+a + c          # Nova janela (serviço 2)
Ctrl+a + ,          # Renomear para "frontend"
npm start

Ctrl+a + c          # Nova janela (serviço 3)
Ctrl+a + ,          # Renomear para "database"
mongod

# Dividir janela para logs
Ctrl+a + h          # Dividir horizontalmente
# Navegar para novo painel e executar logs
```

### Workflow 2: Monitoramento de Logs

```powershell
# Iniciar sessão de monitoramento
psmux new-session -s monitor

# Dividir tela em 4 painéis
Ctrl+a + h          # Dividir horizontalmente
Ctrl+a + v          # Dividir verticalmente (painel esquerdo)
Ctrl+a + v          # Dividir verticalmente (painel direito)
# Navegar para painel inferior esquerdo
Ctrl+a + v          # Dividir verticalmente

# Em cada painel, monitorar logs diferentes
tail -f /var/log/app1.log
tail -f /var/log/app2.log
tail -f /var/log/app3.log
tail -f /var/log/app4.log

# Navegar entre painéis com as setas
Ctrl+a + ↑↓←→
```

### Workflow 3: Sessão Persistente (SSH)

```powershell
# Conectar ao servidor remoto
ssh user@server

# Iniciar psmux no servidor
psmux new-session -s work

# Trabalhar normalmente com múltiplas janelas
Ctrl+a + c          # Nova janela para editor
vim arquivo.py

Ctrl+a + c          # Nova janela para testes
python test.py

Ctrl+a + d          # Desconectar da sessão (processos continuam rodando)

# Mais tarde, reconectar
ssh user@server
psmux attach -t work
```

### Workflow 4: Pair Programming

```powershell
# Compartilhar sessão via tmate (se disponível)
psmux new-session -s pair

# Dividir tela para pair programming
Ctrl+a + h          # Dividir horizontalmente

# Ambos podem ver e interagir com os mesmos painéis
```

### Workflow 5: Administração de Sistema

```powershell
# Sessão de administração
psmux new-session -s admin

# Janela 1: Monitoramento de sistema
Ctrl+a + c
htop

# Janela 2: Logs do sistema
Ctrl+a + c
tail -f /var/log/syslog

# Janela 3: Processos
Ctrl+a + c
ps aux

# Janela 4: Rede
Ctrl+a + c
iftop

# Navegar rapidamente entre janelas
Ctrl+a + 1-4
```

### Workflow 6: Desenvolvimento Web Full Stack

```powershell
# Sessão full stack
psmux new-session -s fullstack

# Janela 1: Backend
Ctrl+a + c
Ctrl+a + ,          # Renomear para "backend"
cd backend
npm run dev

# Janela 2: Frontend
Ctrl+a + c
Ctrl+a + ,          # Renomear para "frontend"
cd frontend
npm start

# Janela 3: Database
Ctrl+a + c
Ctrl+a + ,          # Renomear para "database"
docker-compose up

# Janela 4: Git/Tests
Ctrl+a + c
Ctrl+a + ,          # Renomear para "git"
# Para commits e testes

# Dividir janela git para testes
Ctrl+a + h
cd backend
npm test
```

## Dicas Úteis

### Atualizar Configuração

Se você modificar o arquivo `psmux.conf`, recarregue a configuração:

```powershell
psmux source-file ~/.psmux.conf
```

Ou dentro do psmux:
```
Ctrl+a + :
:source-file ~/.psmux.conf
```

### Listar Comandos Disponíveis

```powershell
psmux list-commands
```

### Mostrar Informações da Sessão

Dentro do psmux:
```
Ctrl+a + i
```

### Mouse

Com o suporte a mouse ativado, você pode:
- Clicar em abas para mudar de janela
- Clicar em painéis para focar
- Arrastar bordas para redimensionar
- Usar scroll para navegar no histórico

## Documentação Oficial

- Site oficial: https://psmux.pages.dev/
- GitHub: https://github.com/psmux/psmux
- Documentação de features: https://github.com/psmux/psmux/blob/master/docs/features.md
- Referência de comandos: https://github.com/psmux/psmux/blob/master/docs/scripting.md

## Licença

Este projeto de configuração é fornecido como está. O psmux é licenciado sob MIT License.
