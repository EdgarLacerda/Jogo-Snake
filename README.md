# 🐍 Snake Game — Game Design Document (GDD)

> Projeto desenvolvido em Python com Pygame  
> Versão: 1.0 | Plataforma: Desktop | Gênero: Arcade / Casual

---

## 📋 Sumário

1. [Visão Geral](#visão-geral)
2. [Objetivos do Jogo](#objetivos-do-jogo)
3. [Mecânicas de Jogo](#mecânicas-de-jogo)
4. [Controles](#controles)
5. [Regras e Condições de Fim de Jogo](#regras-e-condições-de-fim-de-jogo)
6. [Elementos Visuais](#elementos-visuais)
7. [Sistema de Pontuação](#sistema-de-pontuação)
8. [Arquitetura do Código](#arquitetura-do-código)
9. [Tecnologias Utilizadas](#tecnologias-utilizadas)
10. [Como Executar](#como-executar)
11. [Melhorias Futuras](#melhorias-futuras)

---

## 🎮 Visão Geral

Snake é um jogo arcade clássico em que o jogador controla uma cobra que se move continuamente pelo mapa. O objetivo é comer a maior quantidade possível de comidas para crescer e acumular pontos, evitando colidir com as bordas da tela ou com o próprio corpo.

| Atributo | Valor |
|---|---|
| Linguagem | Python 3 |
| Biblioteca | Pygame |
| Resolução | 600 × 400 px |
| Tamanho do bloco | 20 × 20 px |
| Velocidade base | 10 quadros/seg |

---

## 🎯 Objetivos do Jogo

**Objetivo principal:** Comer o máximo de comidas possível antes de morrer, acumulando a maior pontuação.

**Objetivo secundário:** Bater o próprio recorde pessoal de pontos.

**Loop de jogo:**
```
Mover → Comer comida → Crescer → Desviar de si mesmo → Mover...
```

---

## ⚙️ Mecânicas de Jogo

### Movimentação da Cobra
- A cobra se move em blocos discretos de **20×20 pixels**
- O movimento ocorre em **4 direções**: cima, baixo, esquerda, direita
- A cobra se move continuamente — o jogador apenas muda a direção
- A velocidade é fixa em **10 movimentos por segundo**

### Sistema de Crescimento
- A cobra começa com tamanho **1 bloco**
- Ao comer uma comida, o tamanho aumenta em **+1 bloco**
- O corpo é representado como uma lista de posições (`pixels[]`)
- A cauda é removida a cada frame, exceto quando a cobra come

### Geração de Comida
- A comida aparece em uma posição **aleatória** dentro dos limites da tela
- A posição é sempre alinhada à grade de 20×20 pixels
- Uma nova comida é gerada imediatamente após ser consumida
- Nunca há mais de **1 comida** na tela ao mesmo tempo

---

## 🕹️ Controles

| Tecla | Ação |
|---|---|
| `↑` Seta Cima | Move a cobra para cima |
| `↓` Seta Baixo | Move a cobra para baixo |
| `←` Seta Esquerda | Move a cobra para a esquerda |
| `→` Seta Direita | Move a cobra para a direita |
| Fechar janela | Encerra o jogo |

> ⚠️ **Nota:** Atualmente não há verificação de direção oposta — a cobra pode inverter o sentido e colidir com o próprio corpo imediatamente.

---

## ☠️ Regras e Condições de Fim de Jogo

O jogo termina (`fim_jogo = True`) quando:

1. **Colisão com a parede** — a cobra sai dos limites da tela (`x < 0`, `x >= 600`, `y < 0`, `y >= 400`)
2. **Colisão com o próprio corpo** — a cabeça ocupa a mesma posição de qualquer segmento do corpo
3. **Fechar a janela** — o jogador encerra o processo manualmente

Ao terminar, o jogo é encerrado sem tela de game over ou opção de reiniciar (melhoria prevista).

---

## 🎨 Elementos Visuais

| Elemento | Cor | Código RGB |
|---|---|---|
| Fundo | Preto | `(0, 0, 0)` |
| Cobra | Branca | `(255, 255, 255)` |
| Comida | Verde | `(0, 255, 0)` |
| Pontuação (HUD) | Vermelha | `(255, 0, 0)` |

### Layout da Tela

```
┌─────────────────────────────────────────────────────┐
│ Pontos: X                                           │  ← HUD (fonte Helvetica 30px)
│                                                     │
│                        🟩                           │  ← Comida (verde)
│                                                     │
│              ⬜⬜⬜⬜⬜                              │  ← Cobra (branca)
│                                                     │
│                                                     │
└─────────────────────────────────────────────────────┘
  600 × 400 px | fundo preto
```

---

## 🏆 Sistema de Pontuação

- Pontuação atual = **tamanho da cobra − 1**
- Cada comida ingerida vale **+1 ponto**
- A pontuação é exibida em tempo real no canto superior esquerdo da tela
- Não há sistema de recorde salvo (melhoria prevista)

---

## 🏗️ Arquitetura do Código

```
snake.py
│
├── Configurações globais
│   ├── Resolução da tela (600×400)
│   ├── Cores RGB
│   └── Parâmetros da cobra (tamanho bloco, velocidade)
│
├── desenhar_comida(tamanho, comida_x, comida_y)
│   └── Renderiza o quadrado verde da comida
│
├── gerar_comida()
│   └── Retorna posição aleatória alinhada à grade
│
├── desenhar_cobra(tamanho, pixels)
│   └── Itera sobre a lista de segmentos e renderiza cada bloco
│
├── desenhar_pontuacao(pontuação)
│   └── Exibe o HUD com a pontuação atual
│
├── selecionar_velocidade(tecla)
│   └── Mapeia tecla pressionada para (velocidade_x, velocidade_y)
│
└── rodar_jogo()            ← Loop principal
    ├── Inicialização do estado
    ├── Loop principal (while not fim_jogo)
    │   ├── Captura de eventos (QUIT, KEYDOWN)
    │   ├── Verificação de colisão com bordas
    │   ├── Atualização de posição
    │   ├── Atualização da lista de pixels
    │   ├── Verificação de colisão com o corpo
    │   ├── Verificação de coleta de comida
    │   └── Renderização (comida, cobra, HUD, display)
    └── Controle de FPS (relogio.tick)
```

---

## 🛠️ Tecnologias Utilizadas

- **Python 3** — linguagem principal
- **Pygame** — biblioteca para criação de jogos 2D (eventos, renderização, tempo)

---

## ▶️ Como Executar

**1. Instale as dependências:**
```bash
pip install pygame
```

**2. Execute o jogo:**
```bash
python snake.py
```

**Requisitos mínimos:**
- Python 3.7+
- Pygame 2.0+

---

## 🚀 Melhorias Futuras

| Melhoria | Descrição |
|---|---|
| Tela de Game Over | Exibir mensagem ao perder com opção de reiniciar |
| Bloqueio de direção oposta | Impedir que a cobra inverta o sentido instantaneamente |
| Recorde salvo | Guardar o high score entre partidas |
| Aumento de velocidade | Cobra acelerar conforme a pontuação cresce |
| Tela de menu inicial | Tela com título e botão de iniciar |
| Sons e efeitos | Adicionar áudio ao comer e ao morrer |
| Correção do bug de colisão | Verificar colisão com borda *após* atualizar posição |

---

## 📝 Notas de Desenvolvimento

> **Bug conhecido:** A verificação de colisão com as bordas ocorre *antes* da atualização da posição, fazendo com que a cobra "morra" um frame antes do esperado visualmente. A correção é mover a checagem para após `x += velocidade_x` e `y += velocidade_y`.
