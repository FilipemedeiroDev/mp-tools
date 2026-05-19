# Design Tokens — [DS] MultiDS

---

## Variáveis específicas — pedirdelivery

O pedirdelivery usa temas por restaurante. A cor primária **não é hardcoded** — ela é injetada via CSS variable do Ionic:

| Uso | Variável | Observação |
|---|---|---|
| Cor primária (brand) | `var(--ion-color-main)` | Definida dinamicamente pelo tema do restaurante |
| Texto sobre a cor primária | `var(--ion-color-main-contrast)` | Garante legibilidade no tema atual |

**Regra:** Sempre que o Figma mostrar a cor laranja do DS (`#FF6501` / `Color/orange/500`), use `var(--ion-color-main)` no código — nunca o hex direto. O restaurante pode ter outra cor primária configurada.

---

Fonte: https://www.figma.com/design/ZR4ynyOJvCwZBCXtuhjqG8/-DS--MultiDS?node-id=6277-6

---

## Tipografia

**Font family:** `Inter` (aplicada via `.new-design-font` no bloco novo)

### Font Size (`Font/size/*`)
| Token | px | rem |
|---|---|---|
| `Font/size/xs` | `10px` | `0.625rem` |
| `Font/size/sm` | `12px` | `0.75rem` |
| `Font/size/md` | `14px` | `0.875rem` |
| `Font/size/lg` | `16px` | `1rem` |
| `Font/size/xl` | `18px` | `1.125rem` |
| `Font/size/2xl` | `20px` | `1.25rem` |
| `Font/size/3xl` | `24px` | `1.5rem` |
| `Font/size/4xl` | `30px` | `1.875rem` |
| `Font/size/5xl` | `36px` | `2.25rem` |
| `Font/size/6xl` | `48px` | `3rem` |
| `Font/size/7xl` | `64px` | `4rem` |

### Font Weight (`Font/weight/*`)
| Token | Valor |
|---|---|
| `Font/weight/regular` | `400` |
| `Font/weight/medium` | `500` |
| `Font/weight/semibold` | `600` |
| `Font/weight/bold` | `700` |

### Line Height (`Font/line-height/*`)
| Token | Multiplicador | Uso |
|---|---|---|
| `Font/line-height/tight` | `×1.2` | Headings compactos |
| `Font/line-height/normal` | `×1.5` | Texto corrido |
| `Font/line-height/relaxed` | `×1.75` | Espaçamento mais aberto |

---

## Espaçamento (`Space/*`)

Base: grid de 4px. Token `Space/N` = `N × 4px`.

| Token | Valor |
|---|---|
| `Space/0` | `0px` |
| `Space/1` | `4px` |
| `Space/2` | `8px` |
| `Space/3` | `12px` |
| `Space/4` | `16px` |
| `Space/5` | `20px` |
| `Space/6` | `24px` |
| `Space/8` | `32px` |
| `Space/10` | `40px` |
| `Space/12` | `48px` |
| `Space/16` | `64px` |
| `Space/20` | `80px` |

---

## Border Radius (`Radius/*`)

| Token | Valor |
|---|---|
| `Radius/none` | `0px` |
| `Radius/xs` | `2px` |
| `Radius/sm` | `4px` |
| `Radius/md` | `8px` |
| `Radius/lg` | `12px` |
| `Radius/xl` | `16px` |
| `Radius/full` | `999px` |

---

## Elevação / Sombra (`Elevation/*`)

| Token | Valor CSS | Uso |
|---|---|---|
| `Elevation/0` | `none` | Flat |
| `Elevation/1` | `0 1px 2px rgba(0,0,0,.08)` | Sutil |
| `Elevation/2` | `0 2px 4px rgba(0,0,0,.10)` | Leve |
| `Elevation/3` | `0 4px 12px rgba(0,0,0,.12)` | Médio |
| `Elevation/4` | `0 8px 24px rgba(0,0,0,.16)` | Alto (modais, dropdowns) |

---

## Paleta de Cores — Foundation Tokens

### Orange — Brand primário
| Token | Hex |
|---|---|
| `Color/orange/100` | `#FFF3E6` |
| `Color/orange/200` | `#FFD9BF` |
| `Color/orange/300` | `#FFB380` |
| `Color/orange/400` | `#FF8C42` |
| `Color/orange/500` | `#FF6501` |
| `Color/orange/600` | `#E65A00` |
| `Color/orange/700` | `#FF4C00` |
| `Color/orange/800` | `#B33600` |
| `Color/orange/900` | `#7A2400` |

### Green — Brand secundário (teal)
| Token | Hex |
|---|---|
| `Color/brand-green/100` | `#E0F2F1` |
| `Color/brand-green/200` | `#B2DFDB` |
| `Color/brand-green/300` | `#80CBC4` |
| `Color/brand-green/400` | `#4DB6AC` |
| `Color/brand-green/500` | `#009688` |
| `Color/brand-green/600` | `#00897B` |
| `Color/brand-green/700` | `#00766A` |
| `Color/brand-green/800` | `#005F56` |
| `Color/brand-green/900` | `#003D39` |

### Neutral
| Token | Hex |
|---|---|
| `Color/neutral/100` | `#FFFFFF` |
| `Color/neutral/200` | `#F6F6F6` |
| `Color/neutral/300` | `#F1F1F1` |
| `Color/neutral/400` | `#DEE1E4` |
| `Color/neutral/500` | `#BDC7CF` |
| `Color/neutral/600` | `#8F9FAC` |
| `Color/neutral/700` | `#7D8E9B` |
| `Color/neutral/800` | `#556470` |
| `Color/neutral/850` | `#2A3849` |
| `Color/neutral/900` | `#222932` |
| `Color/neutral/950` | `#171D24` |

### Red — Danger
| Token | Hex |
|---|---|
| `Color/red/100` | `#FDECEC` |
| `Color/red/200` | `#F9D5D5` |
| `Color/red/300` | `#F4AFAF` |
| `Color/red/400` | `#ED7A7A` |
| `Color/red/500` | `#D52334` |
| `Color/red/600` | `#C73E3E` |
| `Color/red/700` | `#CE0D20` |
| `Color/red/800` | `#7F1D1D` |
| `Color/red/900` | `#4A0D0D` |

### Yellow — Warning
| Token | Hex |
|---|---|
| `Color/yellow/100` | `#FFF8E1` |
| `Color/yellow/200` | `#FFECB3` |
| `Color/yellow/300` | `#FFE082` |
| `Color/yellow/400` | `#FFD54F` |
| `Color/yellow/500` | `#FFC107` |
| `Color/yellow/600` | `#E0A800` |
| `Color/yellow/700` | `#B28704` |
| `Color/yellow/800` | `#7A5C00` |
| `Color/yellow/900` | `#4E3B00` |

### Green — Success
| Token | Hex |
|---|---|
| `Color/green/100` | `#E6F4EA` |
| `Color/green/200` | `#CDE9D5` |
| `Color/green/300` | `#A6D8B8` |
| `Color/green/400` | `#74C69D` |
| `Color/green/500` | `#52B788` |
| `Color/green/600` | `#40916C` |
| `Color/green/700` | `#2D6A4F` |
| `Color/green/800` | `#1B4332` |
| `Color/green/900` | `#081C15` |

---

## Tokens Semânticos

### Fundo (Background)
| Token | Light | Dark |
|---|---|---|
| `background` | `#FFFFFF` | `#222932` |
| `background-hover` | `#F1F1F1` | `#2A3849` |

### Texto
| Token | Light | Dark | Uso |
|---|---|---|---|
| `text-primary` | `#222932` | `#FFFFFF` | Títulos e texto principal |
| `text-secondary` | `#7D8E9B` | `#8F9FAC` | Texto de suporte |
| `text-disabled` | `#BDC7CF` | `#F6F6F6` | Desabilitado |
| `text-warning` | `#B28704` | `#FFC107` | Alertas |

### Borda
| Token | Light | Dark |
|---|---|---|
| `border` | `#BDC7CF` | `#7D8E9B` |
| `border-hover` | `#8F9FAC` | `#BDC7CF` |

### Ícones
| Token | Light | Dark |
|---|---|---|
| `icon` | `#556470` | `#8F9FAC` |

### Interação / Feedback
| Token | Light | Dark | Uso |
|---|---|---|---|
| `primary` | `#FF6501` | `#FF4C00` | Ações primárias / CTAs |
| `primary-hover` | `#FF4C00` | `#FF6501` | Hover primário |
| `danger` | `#D52334` | `#CE0D20` | Erro / destrutivo |
| `success` | `#52B788` | `#52B788` | Status positivo |
| `warning` | `#FFC107` | `#E0A800` | Alerta moderado |
