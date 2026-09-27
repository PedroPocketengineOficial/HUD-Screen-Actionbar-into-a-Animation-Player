# HUD ActionBar Animation Player --- Minecraft Bedrock

Ferramenta HTML para transformar um `hud_screen.json` baseado em
ActionBar em um **Animation Player usando Script API**.

O objetivo é facilitar a criação de animações de HUD sem precisar
escrever manualmente centenas de comandos `/title`.

------------------------------------------------------------------------

# ✨ O que esta ferramenta faz?

O **HUD ActionBar Animation Player** lê um `hud_screen.json` e procura
controles:

``` json
"type": "image"
```

que tenham uma condição `visible` baseada em texto.

Exemplo:

``` json
"visible": "($gde_actionbar_text = 'test_0001')"
```

A ferramenta interpreta:

``` text
test_0001
```

como um frame.

Uma sequência como:

``` text
test_0001
test_0002
test_0003
test_0004
```

vira:

``` text
Frame 1
Frame 2
Frame 3
Frame 4
```

O Script API então pode enviar esses textos usando:

``` text
/title <seletor> actionbar <texto>
```

------------------------------------------------------------------------

# 📥 Entrada

A ferramenta possui três formas principais de receber o HUD.

## 1. Colar o JSON

Cole diretamente o conteúdo de:

``` text
hud_screen.json
```

na área de entrada.

Depois use o botão de análise.

------------------------------------------------------------------------

## 2. Importar um `hud_screen.json`

Use:

**📄 Importar hud_screen.json**

Isso permite selecionar o arquivo diretamente.

É especialmente útil quando o JSON é muito grande para copiar e colar no
celular.

------------------------------------------------------------------------

## 3. Importar vários JSONs

Use:

**📚 Importar vários JSONs**

A ferramenta pode ler vários arquivos JSON e adicionar os frames
encontrados.

------------------------------------------------------------------------

# 🔎 Como os frames são encontrados

A ferramenta percorre o JSON procurando objetos com:

``` json
"type": "image"
```

Depois verifica a propriedade:

``` json
"visible"
```

Quando encontra uma expressão com um texto, por exemplo:

``` text
($gde_actionbar_text = 'test_0001')
```

extrai:

``` text
test_0001
```

Também pode guardar informações associadas ao frame, como:

-   textura;
-   tamanho;
-   layer;
-   arquivo de origem;
-   caminho dentro do JSON.

------------------------------------------------------------------------

# 🎞️ Frames

Depois da importação, os frames aparecem na lista do Animation Player.

Exemplo:

``` text
001  test_0001
002  test_0002
003  test_0003
004  test_0004
```

A ferramenta evita adicionar novamente uma combinação já existente de
texto + textura.

------------------------------------------------------------------------

# 🎯 Seletor

O campo:

**Seletor do comando**

define quem receberá o ActionBar.

Exemplo:

``` text
@a
```

gera a ideia:

``` text
/title @a actionbar test_0001
```

Você também pode configurar outro seletor compatível com o comando
`/title`.

Exemplos:

``` text
@p
```

ou:

``` text
@a[tag=cinema]
```

------------------------------------------------------------------------

# ⏱️ Ticks por frame

O campo:

**Ticks por frame**

define o intervalo utilizado para trocar os frames.

Exemplo:

``` text
3
```

A sequência será avançada utilizando esse intervalo.

Valores menores:

``` text
1
2
3
```

fazem a troca acontecer mais rapidamente.

Valores maiores:

``` text
10
20
30
```

mantêm cada frame por mais tempo.

------------------------------------------------------------------------

# 🔁 Modo de reprodução

## Loop infinito

A sequência continua repetindo:

``` text
001
 ↓
002
 ↓
003
 ↓
001
 ↓
002
 ↓
003
 ↓
...
```

------------------------------------------------------------------------

## Executar uma vez

Executa:

``` text
001
 ↓
002
 ↓
003
 ↓
parar
```

------------------------------------------------------------------------

## Repetir N vezes

Permite configurar quantas vezes a sequência completa será repetida.

Exemplo:

``` text
Repetições: 3
```

Resultado:

``` text
001 → 002 → 003
001 → 002 → 003
001 → 002 → 003
```

e depois para.

------------------------------------------------------------------------

# 🎬 Nome da animação

O Animation Player possui um nome para a animação gerada.

Esse nome é usado para organizar as funções no JavaScript.

Por exemplo:

``` text
hudAnimation
```

pode gerar funções relacionadas à reprodução dessa animação.

------------------------------------------------------------------------

# 📡 Script Events

A ferramenta permite configurar eventos para iniciar e parar a animação.

Exemplo:

``` text
hud_anim:play
```

e:

``` text
hud_anim:stop
```

Depois, dentro do mundo:

``` text
/scriptevent hud_anim:play
```

pode iniciar a animação.

Para parar:

``` text
/scriptevent hud_anim:stop
```

Os nomes podem ser configurados no editor.

------------------------------------------------------------------------

# 💻 Script gerado

O arquivo gerado é:

``` text
main.js
```

O projeto utiliza:

``` js
import { system, world } from "@minecraft/server";
```

e foi configurado para a dependência:

``` text
@microsoft/server 2.10.0
```

O código usa o sistema de agendamento de ticks para avançar os frames.

A ideia geral é:

``` text
iniciar animação
       ↓
frame atual
       ↓
/title <seletor> actionbar <texto>
       ↓
esperar os ticks configurados
       ↓
próximo frame
       ↓
repetir ou parar
```

------------------------------------------------------------------------

# 🧩 Relação com o `hud_screen.json`

O Animation Player **não transforma a textura em uma imagem através do
Script API**.

A textura continua sendo responsabilidade do `hud_screen.json`.

Por exemplo:

``` json
{
    "type": "image",
    "texture": "textures/ui/411001",
    "visible": "($gde_actionbar_text = 'test_0001')"
}
```

O script envia:

``` text
/title @a actionbar test_0001
```

O texto altera a variável usada pelo HUD e a condição `visible`
correspondente pode mostrar a imagem.

Assim:

``` text
main.js
  │
  │ /title @a actionbar test_0001
  ▼
hud_screen.json
  │
  │ visible = test_0001
  ▼
textures/ui/411001
```

------------------------------------------------------------------------

# 📦 Estrutura recomendada

O sistema normalmente envolve um Resource Pack e um Behavior Pack.

## Resource Pack

``` text
resource_pack/
├── ui/
│   ├── hud_screen.json
│   └── _ui_defs.json
│
└── textures/
    └── ui/
        ├── 411001.png
        ├── 411002.png
        └── 411003.png
```

## Behavior Pack

``` text
behavior_pack/
├── manifest.json
└── scripts/
    └── main.js
```

------------------------------------------------------------------------

# 📄 `manifest.json`

O Animation Player disponibiliza um gerador de `manifest.json` para
acompanhar o script.

A dependência do Script API é configurada para:

``` text
@microsoft/server
2.10.0
```

Confira sempre a versão do Minecraft/Script API utilizada pelo seu
projeto antes de publicar o addon.

------------------------------------------------------------------------

# ⬇️ Scripts grandes

Se o JavaScript ficar muito grande para copiar pelo celular, use:

**⬇ Baixar script .js**

O navegador gera o arquivo:

``` text
main.js
```

Isso permite usar animações com muitos frames sem precisar copiar
milhares de linhas manualmente.

Também existe:

**📋 Copiar script**

para scripts menores.

------------------------------------------------------------------------

# 📊 Tamanho do script

A ferramenta mostra o tamanho aproximado do JavaScript gerado.

Isso ajuda a perceber quando é mais conveniente baixar o arquivo em vez
de copiar o conteúdo.

------------------------------------------------------------------------

# 👁️ Preview

O Animation Player possui uma área de preview para visualizar os frames
detectados.

Ela ajuda a conferir:

-   quantidade de frames;
-   texto ActionBar;
-   textura;
-   ordem dos frames;
-   dados encontrados no JSON.

------------------------------------------------------------------------

# 🔄 Fluxo completo

``` text
hud_screen.json
      ↓
Importar ou colar
      ↓
Analisar JSON
      ↓
Encontrar controles image
      ↓
Ler visible
      ↓
Extrair textos ActionBar
      ↓
Criar frames
      ↓
Definir seletor
      ↓
Definir ticks
      ↓
Escolher Loop / Once / Repetições
      ↓
Gerar main.js
      ↓
Copiar ou baixar
```

------------------------------------------------------------------------

# 🛠️ Exemplo

Suponha que o HUD tenha:

``` text
test_0001
test_0002
test_0003
```

Configure:

``` text
Seletor:
@a

Ticks por frame:
3

Modo:
Loop infinito
```

O conceito da animação será:

``` text
/title @a actionbar test_0001
   ↓
3 ticks
   ↓
/title @a actionbar test_0002
   ↓
3 ticks
   ↓
/title @a actionbar test_0003
   ↓
3 ticks
   ↓
voltar para test_0001
```

------------------------------------------------------------------------

# 🚀 Uso junto com o HUD Screen Image Builder

O fluxo recomendado entre as duas ferramentas é:

### 1. Criar as imagens

Use:

**HUD Screen Image Builder**

para importar e configurar as imagens.

### 2. Exportar o HUD

Gere:

``` text
hud_screen.json
```

e o `_ui_defs.json` correspondente ao seu projeto.

### 3. Abrir o Animation Player

Importe:

``` text
hud_screen.json
```

### 4. Detectar os frames

O Animation Player encontra as condições:

``` text
visible = texto ActionBar
```

### 5. Configurar a animação

Defina:

``` text
Seletor
Ticks
Modo
Repetições
Script Events
```

### 6. Gerar o script

Baixe:

``` text
main.js
manifest.json
```

### 7. Colocar no Behavior Pack

``` text
behavior_pack/
├── manifest.json
└── scripts/
    └── main.js
```

------------------------------------------------------------------------

# 📱 Compatibilidade

A ferramenta foi criada como uma aplicação HTML e pode ser utilizada em:

-   PC;
-   tablet;
-   celular.

A importação de arquivos grandes é especialmente útil em dispositivos
móveis, onde copiar e colar grandes arquivos JSON/JavaScript pode ser
inconveniente.

------------------------------------------------------------------------

# ⚠️ Observações importantes

O Animation Player depende do formato do `hud_screen.json`.

Ele procura especificamente controles de imagem com uma propriedade
`visible` contendo uma comparação com texto.

Por exemplo:

``` text
($gde_actionbar_text = 'test_0001')
```

Se um HUD usar outro sistema de visibilidade, os frames podem não ser
detectados automaticamente.

Também é importante testar o script gerado na versão do Bedrock e da
Script API usada pelo seu addon.

------------------------------------------------------------------------

# 📜 Licença

Adicione aqui a licença escolhida para o seu projeto GitHub.

Exemplo:

``` text
MIT License
```

------------------------------------------------------------------------

# 🔧 Ideias para futuras versões

Possíveis melhorias:

-   timeline visual;
-   duração individual por frame;
-   arrastar frames para reordenar;
-   preview automático;
-   importar spritesheets;
-   gerar Resource Pack + Behavior Pack;
-   controle individual de velocidade;
-   múltiplas animações no mesmo script;
-   eventos diferentes para cada animação;
-   transições;
-   ferramentas de depuração do `hud_screen.json`.
