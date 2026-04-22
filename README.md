# DW2 Preset Editor - Editor Visual para Presets do Drunken Wrestlers 2

[![HTML5](https://img.shields.io/badge/HTML5-E34F26?logo=html5&logoColor=white)](https://developer.mozilla.org/en-US/docs/Web/HTML)
[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=black)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![React](https://img.shields.io/badge/React-61DAFB?logo=react&logoColor=black)](https://reactjs.org/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Status](https://img.shields.io/badge/status-active-success)]()

**Editor web completo para criação, edição e conversão de presets de customização do Drunken Wrestlers 2 (.dw2p) com suporte a sistema de cores por arena, tipos de pele e banco de dados de itens.**

## 📋 Índice

- [Visão Geral](#visão-geral)
- [Características Principais](#características-principais)
- [Formato do Preset DW2](#formato-do-preset-dw2)
- [Sistema de Cores por Arena](#sistema-de-cores-por-arena)
- [Tipos de Pele](#tipos-de-pele)
- [Sistema de Itens](#sistema-de-itens)
- [Interface do Editor](#interface-do-editor)
- [Uso e Funcionalidades](#uso-e-funcionalidades)
- [Conversão entre Arenas](#conversão-entre-arenas)
- [Exportação e Importação](#exportação-e-importação)
- [Integração com o Jogo](#integração-com-o-jogo)
- [Arquitetura Técnica](#arquitetura-técnica)
- [Desenvolvimento](#desenvolvimento)
- [Banco de Dados de Itens](#banco-de-dados-de-itens)
- [Troubleshooting](#troubleshooting)
- [FAQ](#faq)
- [Contribuição](#contribuição)
- [Licença](#licença)
- [Contato](#contato)

## 🎯 Visão Geral

O **DW2 Preset Editor** é uma ferramenta web desenvolvida para a comunidade do Drunken Wrestlers 2 que permite criar, editar, visualizar e converter presets de customização do personagem sem necessidade de abrir o jogo. Baseado na engenharia reversa do código do jogo (Steam App ID 667530), o editor oferece controle preciso sobre todos os aspectos da customização.

### Casos de Uso Principais

- **Criação de Presets Personalizados**: Design visual de personagens com cores e itens específicos
- **Conversão entre Arenas**: Adaptação automática de cores para diferentes iluminações (Streets, Cage, etc.)
- **Análise de Presets Existentes**: Reverse engineering de presets compartilhados pela comunidade
- **Otimização Visual**: Ajuste fino de cores para melhor aparência em cada arena
- **Backup e Organização**: Gerenciamento de biblioteca de presets personalizados
- **Compartilhamento na Comunidade**: Geração de códigos .dw2p para compartilhar com outros jogadores

## ✨ Características Principais

### 🎨 **Editor de Cores Avançado**
- Controle individual para Skin, Primary, Secondary e Blood colors
- Sistema de sync entre cores para harmonização visual
- Visualização em tempo real das alterações
- Suporte a valores extremos (Infinity, -Infinity) para efeitos especiais

### 🏟️ **Sistema de Cores por Arena**
- Conversão automática entre Streets e Cage
- Preservação de efeitos visuais específicos de cada arena
- Algoritmos de mapeamento baseados na engenharia reversa do jogo
- Detecção automática do tipo de arena baseado nos valores de cor

### 👤 **Tipos de Pele Completo**
- Suporte a 3 tipos de pele: Normal, Bot e Shiny
- Efeitos visuais específicos para cada tipo (metálico, glossy)
- Valores hardcoded do jogo para fidelidade máxima
- Preview dos efeitos de cada tipo

### 👕 **Banco de Dados de Itens**
- Lista de itens extraída dos prefabs reais do jogo
- Categorização por tipo (Custom RGB, General RGB)
- Sistema de equip/unequip com visualização
- IDs numéricos reais compatíveis com o jogo

### 🔄 **Conversão Inteligente**
- Preservação de efeitos especiais durante conversão
- Detecção automática de arena de origem
- Algoritmos de mapeamento precisos baseados no código do jogo
- Preview side-by-side das cores convertidas

### 📁 **Exportação/Importação**
- Geração de código .dw2p no formato exato do jogo
- Download automático com nome personalizado
- Importação de presets existentes via copy-paste
- Clipboard integration para fácil compartilhamento

## 📝 Formato do Preset DW2

### Estrutura do Código .dw2p

```
{tipo}[gEnd]{peleR,G,B,A}[gEnd]{primR,G,B,A}[gEnd]{secR,G,B,A}[gEnd]{sanguR,G,B,A}[divEnd]{id1}[itemEnd]{id2}[itemEnd]...
```

### Componentes do Preset

1. **Tipo de Personagem**: String que define o tipo/gênero
2. **Cores RGBA**: 4 conjuntos de valores float (R,G,B,A)
   - Pele (Skin)
   - Primária (Primary) 
   - Secundária (Secondary)
   - Sangue (Blood)
3. **Itens Equipados**: Lista de IDs numéricos separados por `[itemEnd]`

### Exemplo Concreto

```
Male[gEnd]0.787,0.667,0.529,1[gEnd]0.2,0.4,0.9,1[gEnd]0.1,0.1,0.1,1[gEnd]0.909,0,0,1[divEnd]905[itemEnd]2655[itemEnd]16[itemEnd]112[itemEnd]35[itemEnd]63[itemEnd]1855[itemEnd]
```

### Valores Especiais de Tipo

| Tipo | String | Efeito no Jogo |
|------|--------|----------------|
| Normal | Qualquer string normal | Pele normal com glossiness 0.732 |
| Bot | "This is clearly a bot" | Pele metálica (Metallic=1, Glossiness=0.6) |
| Shiny | "Shiny boi" | Pele metálica brilhante (Metallic=1, Glossiness=0.6 + extra) |

## 🏟️ Sistema de Cores por Arena

### Arenas Suportadas

| Arena | Categoria | Características | Workshop ID |
|-------|-----------|----------------|-------------|
| **Streets** | `streets` | Day/Night cycle, multiplicador 0.5 | 2800496603 |
| **Cage** | `cage` | Iluminação HDR extrema (1e37+) | Built-in (ID=1) |
| **Basketball** | `streets` | Usa sistema Streets | 1716364813 |
| **Pro League** | `streets` | Usa sistema Streets | 3519460640 |

### Algoritmos de Conversão

#### Cage → Streets
```javascript
function cageChannelToStreets(v) {
  if (v > 1e10) return Infinity;
  if (v < -1e10) return -Infinity;
  return v * 0.5;  // Fator de conversão baseado na engenharia reversa
}
```

#### Streets → Cage  
```javascript
function streetsChannelToCage(v) {
  if (v === Infinity) return 1e37;
  if (v === -Infinity) return -1e37;
  return v * 2.0;  // Fator inverso
}
```

### Detecção Automática de Arena

```javascript
function detectArenaFromColor(a) {
  if (a > 1e10) return "cage";
  return "streets";
}
```

## 👤 Tipos de Pele

### Configurações Técnicas

| Tipo | `_Glossiness` | `_Metallic` | Efeito Visual |
|------|---------------|-------------|---------------|
| **Normal** | 0.732f | 0f | Pele humana normal |
| **Bot** | 0.6f | 1f | Superfície totalmente metálica |
| **Shiny** | 0.6f | 1f | Metálico + brilho extra (glossy) |

### Strings no Preset

- **Normal**: Qualquer string (ex: "Male", "Female", "MyCharacter")
- **Bot**: Exatamente `"This is clearly a bot"`
- **Shiny**: Exatamente `"Shiny boi"`

### Considerações de Exportação

```javascript
// Shiny tem nome de arquivo fixo
if (skinType === "shiny") {
  fileName = "Shiny boi.dw2p";  // Nome exato requerido pelo jogo
}
```

## 👕 Sistema de Itens

### Categorias de Itens

| Categoria | Ícone | Descrição | Exemplos |
|-----------|-------|-----------|----------|
| **Custom RGB** | 🟣 | Itens com suporte a RGB completo | Roupas com texturas customizáveis |
| **General RGB** | 🔵 | Paleta geral - também aceitam RGB | Acessórios, equipamentos básicos |

### Estrutura do Banco de Dados

```javascript
const ITEMS = {
  custom: {
    name: "Custom RGB",
    icon: "🟣",
    desc: "Silhueta roxa — RGB completo",
    items: [
      { id: "905", name: "Item 905" },
      { id: "2655", name: "Item 2655" },
      // ... mais itens
    ]
  },
  general: {
    name: "General RGB", 
    icon: "🔵",
    desc: "Paleta geral — também aceitam RGB",
    items: [
      { id: "16", name: "Item 16" },
      { id: "112", name: "Item 112" },
      // ... mais itens
    ]
  }
};
```

### IDs de Itens Confirmados

O editor inclui IDs extraídos dos prefabs reais do Unity:

```
905, 2655, 16, 112, 35, 63, 1855, ...
```

**Nota**: Apenas itens com suporte confirmado a RGB estão incluídos.

## 🎛️ Interface do Editor

### Layout Principal

```
┌─────────────────────────────────────────────────────┐
│  DW2 PRESET EDITOR                          [v1.0]  │
├─────────────────────────────────────────────────────┤
│  [🎨 Colors]  [👕 Items]  [🔄 Convert]  [📁 Preset]  │
├─────────────────────────────────────────────────────┤
│                                                      │
│  Skin Type: [Normal] [Bot] [Shiny]                   │
│                                                      │
│  ┌──────────────────────────────────────────────┐   │
│  │  Color Slots                                │   │
│  │  [Skin] [Primary] [Secondary] [Blood]      │   │
│  └──────────────────────────────────────────────┘   │
│                                                      │
│  ┌──────────────────────────────────────────────┐   │
│  │  Color Controls                             │   │
│  │  R: ████████████ [0.787]                    │   │
│  │  G: ████████████ [0.667]                    │   │
│  │  B: ████████████ [0.529]                    │   │
│  │  A: ████████████ [1.000]                    │   │
│  └──────────────────────────────────────────────┘   │
│                                                      │
│  Sync: [Skin ↔ Primary] [Skin ↔ Secondary]          │
│                                                      │
└─────────────────────────────────────────────────────┘
```

### Abas Principais

1. **🎨 Colors** - Controle de cores e tipos de pele
2. **👕 Items** - Seleção e gerenciamento de itens equipados
3. **🔄 Convert** - Conversão entre sistemas de cores de arena
4. **📁 Preset** - Exportação e importação de presets

### Componentes de Controle

#### Selector de Tipo de Pele
```jsx
<button onClick={() => setSkinType("normal")}>
  Normal
</button>
<button onClick={() => setSkinType("bot")}>
  Bot
</button>
<button onClick={() => setSkinType("shiny")}>
  Shiny
</button>
```

#### Controles de Cor RGBA
```jsx
<div className="color-control">
  <div className="channel-label" style={{color: "#ff4444"}}>R</div>
  <input 
    type="range" 
    min="0" 
    max="2" 
    step="0.001"
    value={color[0]}
    onChange={(e) => updateChannel(0, parseFloat(e.target.value))}
  />
  <input 
    type="number"
    value={color[0].toFixed(3)}
    onChange={(e) => updateChannel(0, parseFloat(e.target.value))}
  />
</div>
```

#### Sistema de Sync
```javascript
const [syncPair, setSyncPair] = useState("skin-primary");
const [syncActive, setSyncActive] = useState(true);

// Atualização sincronizada
const setSkinColorSynced = (v) => {
  setSkinColor(v);
  if (syncActive && syncPair === "skin-primary") setPrimary([...v]);
  if (syncActive && syncPair === "skin-secondary") setSecondary([...v]);
};
```

## 🚀 Uso e Funcionalidades

### Fluxo de Criação de Preset

1. **Selecionar Tipo de Pele**
   ```javascript
   // Escolha entre Normal, Bot ou Shiny
   setSkinType("normal"); // ou "bot", "shiny"
   ```

2. **Ajustar Cores**
   ```javascript
   // Valores RGBA padrão
   setSkinColor([0.787, 0.667, 0.529, 1]);    // Pele
   setPrimary([0.2, 0.4, 0.9, 1]);           // Primária
   setSecondary([0.1, 0.1, 0.1, 1]);         // Secundária  
   setBlood([0.909, 0, 0, 1]);               // Sangue
   ```

3. **Selecionar Itens**
   ```javascript
   // IDs dos itens a equipar
   setItems(["905", "2655", "16", "112", "35", "63", "1855"]);
   ```

4. **Exportar Preset**
   ```javascript
   // Gera string .dw2p
   const preset = genP({
     st: skinTypeValue,
     sk: skinColor,
     pr: primary,
     se: secondary,
     bl: blood,
     it: items
   });
   ```

### Conversão entre Arenas

1. **Selecionar Arena de Origem**
   ```javascript
   setFromMap("streets"); // ou "cage"
   ```

2. **Selecionar Arena de Destino**
   ```javascript
   setToMap("cage"); // ou "streets"
   ```

3. **Aplicar Conversão**
   ```javascript
   const convertedColors = convertPreset(presetString, fromMapId, toMapId);
   ```

4. **Visualizar Diferenças**
   ```javascript
   // Mostra valores antes/depois
   console.log("Original:", originalColors);
   console.log("Convertido:", convertedColors);
   ```

### Importação de Preset Existente

```javascript
// Cole string .dw2p no editor
const presetString = "Male[gEnd]0.787,0.667,0.529,1[gEnd]...";

// Parse automático
const parsed = parseP(presetString);
// {
//   st: "Male",
//   sk: [0.787, 0.667, 0.529, 1],
//   pr: [0.2, 0.4, 0.9, 1],
//   se: [0.1, 0.1, 0.1, 1],
//   bl: [0.909, 0, 0, 1],
//   it: ["905", "2655", "16", ...]
// }
```

## 🔄 Conversão entre Arenas

### Algoritmo de Conversão Completo

```javascript
function convertPreset(presetStr, fromMapId, toMapId) {
  // Parse do preset
  const p = parseP(presetStr);
  
  // Determinar categorias
  const fromCat = fromMapId === "cage" ? "cage" : "streets";
  const toCat = toMapId === "cage" ? "cage" : "streets";
  
  // Converter cores
  const converted = {
    ...p,
    sk: convertColor(p.sk, fromCat, toCat),
    pr: convertColor(p.pr, fromCat, toCat),
    se: convertColor(p.se, fromCat, toCat),
    bl: convertColor(p.bl, fromCat, toCat)
  };
  
  // Gerar novo preset
  return genP(converted);
}

function convertColor(c, fromCat, toCat) {
  if (fromCat === toCat) return [...c];
  
  if (fromCat === "streets" && toCat === "cage") {
    return [
      streetsChannelToCage(c[0]),
      streetsChannelToCage(c[1]),
      streetsChannelToCage(c[2]),
      c[3]
    ];
  }
  
  if (fromCat === "cage" && toCat === "streets") {
    return [
      cageChannelToStreets(c[0]),
      cageChannelToStreets(c[1]),
      cageChannelToStreets(c[2]),
      c[3]
    ];
  }
  
  return [...c];
}
```

### Preservação de Efeitos Especiais

```javascript
// Valores extremos são preservados
function streetsChannelToCage(v) {
  if (v === Infinity) return 1e37;      // Mantém efeito especial
  if (v === -Infinity) return -1e37;    // Mantém efeito especial
  return v * 2.0;                       // Conversão normal
}
```

### Visualização de Diferenças

```jsx
<div className="conversion-diff">
  <div className="detail-row">
    <span>Prim IN</span>
    <span style={{color: "#f59e0b"}}>
      [{pIn.pr.map(fmt).join(", ")}]
    </span>
  </div>
  <div className="detail-row">
    <span>Prim OUT</span>
    <span style={{color: "#10b981"}}>
      [{pOut.pr.map(fmt).join(", ")}]
    </span>
  </div>
</div>
```

## 📁 Exportação e Importação

### Geração do Código .dw2p

```javascript
function genP(s) {
  const fmtC = (c) => c.map((v) => 
    v === Infinity ? "Infinity" : 
    v === -Infinity ? "-Infinity" : 
    String(v)
  ).join(",");
  
  return s.st + "[gEnd]" + 
         fmtC(s.sk) + "[gEnd]" + 
         fmtC(s.pr) + "[gEnd]" + 
         fmtC(s.se) + "[gEnd]" + 
         fmtC(s.bl) + "[divEnd]" + 
         s.it.join("[itemEnd]") + 
         (s.it.length ? "[itemEnd]" : "");
}
```

### Download do Arquivo

```javascript
function downloadPreset(preset, fileName) {
  // Shiny tem nome fixo
  if (skinType === "shiny") {
    fileName = "Shiny boi.dw2p";
  }
  
  const blob = new Blob([preset], { type: "text/plain" });
  const a = document.createElement("a");
  a.href = URL.createObjectURL(blob);
  a.download = fileName + ".dw2p";
  a.click();
}
```

### Importação via Clipboard

```javascript
// Textarea para colar preset
<textarea 
  value={input}
  onChange={(e) => setInput(e.target.value)}
  placeholder="Paste the full .dw2p preset string..."
  rows={3}
/>

// Botão de parse
<button onClick={() => {
  try {
    const parsed = parseP(input);
    // Aplicar valores ao editor
    setSkinType(detectSkinType(parsed.st));
    setSkinColor(parsed.sk);
    setPrimary(parsed.pr);
    setSecondary(parsed.se);
    setBlood(parsed.bl);
    setItems(parsed.it);
  } catch (e) {
    alert("Invalid preset format");
  }
}}>
  Import Preset
</button>
```

## 🎮 Integração com o Jogo

### Localização dos Arquivos .dw2p

```
C:\Program Files (x86)\Steam\steamapps\common\Drunken Wrestlers 2\Files\presets\
```

### Processo de Uso no Jogo

1. **Criar/Editar Preset** no editor web
2. **Exportar** como arquivo `.dw2p`
3. **Salvar** na pasta `Files/presets/` do jogo
4. **No jogo**: Acessar Customization → Load Preset
5. **Selecionar** o arquivo exportado

### Nomes de Arquivo Especiais

- **Presets normais**: Qualquer nome (ex: `my_character.dw2p`)
- **Presets Shiny**: Deve ser exatamente `Shiny boi.dw2p`

### Compatibilidade com Sistema Online

- Presets podem ser carregados em partidas online
- Itens são filtrados pelo inventário Steam do jogador
- Cores são aplicadas em todas as arenas (com ajuste automático)

## 🏗️ Arquitetura Técnica

### Stack Tecnológica

- **Frontend**: React (via CDN) com hooks
- **Estilização**: CSS-in-JS com estilos inline
- **Estado**: useState, useMemo, useContext
- **Build**: Single HTML file (no build step necessário)

### Estrutura do Código

```javascript
// Componente principal
function App() {
  // Estados principais
  const [skinType, setSkinType] = useState("normal");
  const [skinColor, setSkinColor] = useState([0.787, 0.667, 0.529, 1]);
  const [primary, setPrimary] = useState([0, 0, 0, 1]);
  const [secondary, setSecondary] = useState([0, 0, 0, 1]);
  const [blood, setBlood] = useState([0.909, 0, 0, 1]);
  const [items, setItems] = useState(["905", "2655", "16", "112", "35", "63", "1855"]);
  
  // Memoized preset generation
  const preset = useMemo(() => genP({
    st: SKIN_TYPES[skinType].tv,
    sk: skinColor,
    pr: primary,
    se: secondary,
    bl: blood,
    it: items
  }), [skinType, skinColor, primary, secondary, blood, items]);
  
  // Renderização
  return (
    <div className="app">
      <Header />
      <Tabs>
        <ColorsTab {...props} />
        <ItemsTab {...props} />
        <ConvertTab {...props} />
        <PresetTab preset={preset} />
      </Tabs>
      <Footer />
    </div>
  );
}
```

### Parser do Preset

```javascript
function parseP(s) {
  const divs = s.split("[divEnd]");
  if (divs.length < 2) return null;
  
  const general = divs[0];
  const itemsPart = divs[1];
  
  const parts = general.split("[gEnd]");
  const parseFloats = (x) => x.split(",").map(v => 
    v.trim() === "Infinity" ? Infinity : 
    v.trim() === "-Infinity" ? -Infinity : 
    parseFloat(v)
  );
  
  return {
    st: parts[0] || "",
    sk: parts.length > 1 ? parseFloats(parts[1]) : [0,0,0,1],
    pr: parts.length > 2 ? parseFloats(parts[2]) : [0,0,0,1],
    se: parts.length > 3 ? parseFloats(parts[3]) : [0,0,0,1],
    bl: parts.length > 4 ? parseFloats(parts[4]) : [0.909,0,0,1],
    it: itemsPart ? itemsPart.split("[itemEnd]")
      .map(x => x.trim())
      .filter(x => x && !isNaN(x)) : []
  };
}
```

## 🛠️ Desenvolvimento

### Setup Local

```bash
# O projeto é um único arquivo HTML
# Basta abrir index.html no navegador

# Para desenvolvimento:
# 1. Clone o repositório
git clone https://github.com/JBRYAN333/preset-editor.git

# 2. Abra index.html localmente
# 3. Use live server para hot reload
```

### Estrutura de Projeto

```
preset-editor/
├── index.html              # Aplicação completa (189KB)
├── Backup/                 # Backups de versões anteriores
├── squarecloud.app         # Configuração para deploy
└── README.md              # Documentação
```

### Adicionando Novos Itens

```javascript
// Adicionar ao banco de dados
ITEMS.custom.items.push({
  id: "9999",
  name: "Novo Item Personalizado"
});

// O sistema automaticamente mostrará no seletor
```

### Estendendo Funcionalidades

**Novo Tipo de Pele**:
```javascript
SKIN_TYPES.newType = {
  tv: "CustomTypeString",
  label: "Novo Tipo",
  desc: "Descrição do novo tipo de pele"
};
```

**Novo Sistema de Arena**:
```javascript
function newArenaConversion(v, fromCat, toCat) {
  if (fromCat === "streets" && toCat === "newArena") {
    return v * conversionFactor;
  }
  // ... outras conversões
}
```

## 💾 Banco de Dados de Itens

### Fonte dos Dados

Os itens são extraídos dos prefabs do Unity do Drunken Wrestlers 2 através de:

1. **Engenharia Reversa** do código C# descompilado
2. **Análise dos arquivos** `.prefab` e `.asset`
3. **Identificação** de IDs numéricos reais usados pelo jogo
4. **Filtragem** para incluir apenas itens com suporte a RGB

### Estrutura de Dados

```javascript
// ITEMS é um objeto com categorias
const ITEMS = {
  custom: {
    name: "Custom RGB",
    icon: "🟣",
    desc: "Silhueta roxa — RGB completo",
    items: [
      { id: "905", name: "Item 905" },
      { id: "2655", name: "Item 2655" },
      { id: "16", name: "Item 16" },
      { id: "112", name: "Item 112" },
      { id: "35", name: "Item 35" },
      { id: "63", name: "Item 63" },
      { id: "1855", name: "Item 1855" }
    ]
  },
  general: {
    name: "General RGB",
    icon: "🔵",
    desc: "Paleta geral — também aceitam RGB",
    items: [
      // ... mais itens
    ]
  }
};
```

### Atualização do Banco de Dados

Para atualizar a lista de itens:

1. Extrair novos IDs dos prefabs do Unity
2. Adicionar ao objeto `ITEMS`
3. Testar no editor
4. Confirmar suporte a RGB no jogo

## 🐛 Troubleshooting

### Problemas Comuns

#### 1. Preset não carrega no jogo
**Causa**: Formato incorreto ou nome de arquivo errado
**Solução**:
- Verificar formato exato do código .dw2p
- Para Shiny: nome deve ser exatamente `Shiny boi.dw2p`
- Verificar se há caracteres inválidos

#### 2. Cores aparecem diferentes no jogo
**Causa**: Sistema de iluminação diferente por arena
**Solução**:
- Usar conversor de arena no editor
- Criar preset específico para cada arena
- Ajustar valores para compensação de iluminação

#### 3. Itens não aparecem equipados
**Causa**: IDs incorretos ou jogador não possui o item
**Solução**:
- Verificar IDs no banco de dados do editor
- Confirmar que jogador possui os itens no inventário Steam
- Testar com itens básicos primeiro

#### 4. Editor não parseia preset
**Causa**: String malformada ou encoding incorreto
**Solução**:
- Copiar string exata do arquivo .dw2p
- Verificar caracteres especiais
- Usar importação via textarea do editor

### Debug no Console

```javascript
// Habilitar logging detalhado
const DEBUG = true;

if (DEBUG) {
  console.log("Parsed preset:", parsed);
  console.log("Generated preset:", preset);
  console.log("Conversion:", convertPreset(preset, "streets", "cage"));
}
```

## ❓ FAQ

### Q: Posso usar valores negativos nas cores?
**R**: Sim, o jogo aceita valores negativos e produz efeitos especiais interessantes.

### Q: Como saber se meu preset é para Cage ou Streets?
**R**: O editor detecta automaticamente baseado nos valores de cor. Valores > 1e10 indicam Cage.

### Q: Posso converter presets múltiplas vezes?
**R**: Sim, mas cada conversão pode introduzir pequenas perdas de precisão.

### Q: O editor funciona offline?
**R**: Sim, é um arquivo HTML único que funciona completamente offline.

### Q: Como adicionar novos itens ao banco de dados?
**R**: Edite o objeto `ITEMS` no código e adicione novos objetos `{id: "...", name: "..."}`.

### Q: O editor é compatível com mobile?
**R**: Sim, a interface é responsiva e funciona em dispositivos móveis.

### Q: Posso salvar meus presets no editor?
**R**: Não diretamente, mas você pode exportar como arquivo .dw2p e gerenciar localmente.

## 🤝 Contribuição

Contribuições são bem-vindas! Áreas que precisam de ajuda:

1. **Expansão do banco de itens** com IDs confirmados
2. **Melhorias na UI/UX** do editor
3. **Novos algoritmos de conversão** para outras arenas
4. **Documentação** mais detalhada
5. **Testes** de compatibilidade com o jogo

### Processo de Contribuição

1. Fork o repositório
2. Crie uma branch para sua feature
3. Teste extensivamente com o jogo
4. Submit pull request com descrição detalhada

## 📄 Licença

Este projeto está licenciado sob a Licença MIT - veja o arquivo [LICENSE](LICENSE) para detalhes.

## 📞 Contato

- **Autor**: JBRYAN333
- **GitHub**: [https://github.com/JBRYAN333](https://github.com/JBRYAN333)
- **Email**: sufocoprojeto@gmail.com
- **Discord**: Comunidade do Drunken Wrestlers 2

## 🙏 Agradecimentos

- Comunidade do Drunken Wrestlers 2 pelo feedback e testing
- Desenvolvedores do jogo pela base técnica que tornou este projeto possível
- Todos que contribuíram com engenharia reversa e documentação
- Usuários que reportaram bugs e sugeriram melhorias

---

**⭐ Se este projeto foi útil para você, considere dar uma estrela no GitHub! ⭐**

*Editor desenvolvido para a comunidade do Drunken Wrestlers 2 - Por jogadores, para jogadores.*