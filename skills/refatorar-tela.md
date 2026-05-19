---
name: refatorar-tela
description: Refatora uma página do pedirdelivery para o novo layout. Guia de estratégia — separação de layouts via flag, sem Ionic no bloco novo, HTML/CSS puro com BEM, com componentização obrigatória.
---

Refatore a página **$ARGUMENTS** seguindo a estratégia de refatoração do pedirdelivery.

## 1 — Preparação

Antes de escrever qualquer linha:

1. Leia os arquivos atuais da página (`.ts`, `.html`, `.scss`)
2. Leia o arquivo de design tokens do DS MultiDS em `~/.claude/plugins/marketplaces/mp-tools/skills/design-tokens.md` — ele contém a paleta de cores, tipografia, espaçamento, radius e elevação oficiais. Use esses valores como base; se o Figma do ticket especificar valores diferentes, o Figma prevalece.
3. Se um link do Figma for fornecido, acesse-o para extrair os valores específicos do componente — mas sempre confronte com os tokens do DS antes de hardcodar um valor arbitrário
4. Leia uma página já refatorada com a mesma flag como referência de padrão:
   - `new_home_pedir_delivery` → `src/app/pages/cashback/cashback.page.*` e `src/app/pages/cashback/components/`
   - `new_flow_checkout_pedir_delivery` → `src/app/pages/shopping-cart/shopping-cart.page.*`
   - `new_complements_pedir_delivery` → `src/app/pages/menu/components/extra/extra.page.*`

---

## 2 — Flags disponíveis

| Propriedade TS                   | Módulo                              |
|----------------------------------|-------------------------------------|
| `isNewHomePedirDelivery`         | `modules?.new_home_pedir_delivery`  |
| `isNewFlowCheckoutPedirDelivery` | `modules?.new_flow_checkout_pedir_delivery` |
| `isNewComplementsPedirDelivery`  | `modules?.new_complements_pedir_delivery`   |

Declaração padrão:
```typescript
public isNewHomePedirDelivery = !!this.restaurantService.restaurant?.info?.modules?.new_home_pedir_delivery;
```

---

## 3 — Estratégia de separação (HTML)

O novo e o antigo layout vivem em dois `ng-container` separados. **O bloco legado nunca é tocado.**

```html
<!-- Novo layout: HTML/CSS puro, sem nenhum componente Ionic -->
<ng-container *ngIf="isNewHomePedirDelivery">
  <div class="nome-page">
    ...
  </div>
</ng-container>

<!-- Layout legado: mantido exatamente como estava -->
<ng-container *ngIf="!isNewHomePedirDelivery">
  <ion-header>...</ion-header>
  <ion-content>...</ion-content>
</ng-container>
```

**Regras do bloco novo:**
- Zero componentes Ionic (`ion-button`, `ion-icon`, `ion-header`, `ion-content`, `ion-toolbar`, etc.)
- Ícones como SVG inline — nunca `ion-icon` nem `<img>` de asset
- **Ícones — fluxo obrigatório:**
  1. Busque primeiro nos assets locais do projeto (`src/assets/`, `src/assets/icons/`, `src/assets/svg/`)
  2. Se encontrar, use via `<img src="assets/...">` ou SVG inline copiando o conteúdo do arquivo
  3. Se não existir localmente, **crie o SVG inline** com o mesmo visual do Figma, seguindo a estrutura dos SVGs já existentes no projeto (mesmo padrão de `viewBox`, `stroke`, `fill`, etc.)
  4. Nunca use ícones de bibliotecas externas nem `ion-icon`
- Nomenclatura CSS em BEM: `nome-page__elemento--modificador`
- Sem `ion-content` — o div raiz controla altura e scroll
- **`new-design-font` obrigatório no div raiz** — aplica `font-family: 'Inter'` em todo o novo bloco (o legado usa Roboto via `* { font-family: 'Roboto' }` global):

```html
<div class="nome-page" [class.new-design-font]="isNewFlagPedirDelivery">
```

> Definido em `src/global.scss`: `.new-design-font * { font-family: 'Inter', sans-serif !important; }`

**Botão fechar — padrão do projeto:**
```html
<button type="button" class="nome-page__close-btn" (click)="back()" aria-label="Fechar">
  <svg xmlns="http://www.w3.org/2000/svg" width="13" height="13" viewBox="0 0 13 13" fill="none" aria-hidden="true">
    <path d="M1 1L12 12M12 1L1 12" stroke="#656F77" stroke-width="1.8" stroke-linecap="round"/>
  </svg>
</button>
```

---

## 4 — Componentização (obrigatório)

Toda seção visual com complexidade própria deve virar componente. Os componentes ficam diretamente dentro do diretório da página, sem subpasta `components/`:

```
src/app/pages/nome-page/
├── nome-secao/
│   ├── nome-secao.component.ts
│   ├── nome-secao.component.html
│   └── nome-secao.component.scss
├── outro-componente/
│   ├── ...
├── nome-page.module.ts   ← declarar todos os componentes aqui
├── nome-page.page.ts     ← só lógica de dados, filtros e navegação
├── nome-page.page.html   ← usa os componentes via selector
└── nome-page.page.scss   ← layout estrutural apenas
```

**Regras de componentização:**
- Cada componente recebe dados via `@Input()` e emite eventos via `@Output()`
- O componente **nunca** chama serviços diretamente — quem chama é a page
- Todo componente deve ter `:host { display: block; width: 100%; flex-shrink: 0; }` no SCSS para se comportar corretamente em layouts flex
- Itens de lista repetidos (`*ngFor`) são sempre extraídos para componente (ex: `cashback-transaction-item`)
- Overlays/modais do novo layout viram componente próprio — sem Ionic

**Quando mover para `shared/`:**
Antes de criar o componente dentro da pasta da página, verifique se ele pode ser reaproveitado em outras páginas. Se sim, crie em `src/app/shared/components/nome-componente/` e importe via `SharedModule` (ou o módulo shared equivalente do projeto). Exemplos de candidatos a shared:
- Filtros em abas (tab filters genéricos)
- Overlays/modais de FAQ ou informação
- Cards de saldo ou resumo
- Itens de lista com ícone + texto + valor
- Spinners e estados de loading
- Empty states

---

## 5 — SCSS

Quando o novo layout não usa `ion-content`, o `:host` é obrigatório na page:

```scss
:host {
  display: flex;
  flex-direction: column;
  height: 100%;
  overflow: hidden;
}

.nome-page {
  display: flex;
  flex-direction: column;
  width: 100%;
  height: 100%;
  background: #fff;
}
```

**Padrão flex para estrutura de página:**
- Seções fixas (header, footer): `flex-shrink: 0`
- Área de conteúdo scrollável: `flex: 1 0 0` + `min-height: 1px` + `overflow-y: auto`
- Scroll deve ser aplicado **apenas na área de lista/conteúdo**, nunca no wrapper inteiro da page
- Scrollbar sutil: `width: 3px`, track transparente, thumb `#dee1e4`
- Texto que cresce na linha: `flex: 1 0 0` + `min-width: 1px`
- Todos os `<p>` e `<h*>`: `margin: 0`
- Filhos de largura total: `width: 100%`
- Em telas pequenas (`max-height: 680px`) reduza padding e tamanhos com media query ou `clamp()` para não comprimir a área de scroll

Os valores de padding, gap, cores, border-radius, tamanhos de fonte e sombras vêm **sempre do Figma** — não hardcode valores de outras telas nem aproxime visualmente.

**Fidelidade obrigatória ao Figma:**
- Meça e use os valores exatos do nó: `padding`, `gap`, `width`, `height`, `font-size`, `font-weight`, `line-height`, `border-radius`, `color`
- Quando o Figma usar um token do DS (ex: `Space/4` = 16px), use o valor em px correspondente — consulte `~/.claude/plugins/marketplaces/mp-tools/skills/design-tokens.md`
- Não arredonde nem "normalize" valores do Figma sem confirmar com o designer
- Se um elemento tem tamanho fixo no Figma (ex: ícone 24×24), use exatamente esse tamanho — não use `em` ou valores relativos a não ser que o Figma os especifique

---

## 6 — Checklist

- [ ] Tokens do DS lidos de `~/.claude/plugins/marketplaces/mp-tools/skills/design-tokens.md` — cores, tipografia e espaçamento alinhados com o DS
- [ ] Figma consultado — valores exatos de padding, gap, font-size, font-weight, border-radius e cores extraídos do nó correto
- [ ] Nenhum valor CSS aproximado ou inventado — tudo rastreável ao Figma ou ao DS
- [ ] Cor primária usando `var(--ion-color-main)` — nunca o hex do laranja diretamente
- [ ] Ícones buscados nos assets locais primeiro; se ausentes, criados como SVG inline seguindo estrutura do projeto
- [ ] Bloco legado intacto — zero alterações no `*ngIf="!isNew*"`
- [ ] Nenhum componente Ionic dentro do bloco novo
- [ ] `:host` presente quando não há `ion-content` no novo layout
- [ ] `margin: 0` em todos os `<p>` e `<h*>` do novo layout
- [ ] `width: 100%` e `flex-shrink: 0` nas seções que precisam de largura total
- [ ] Seções com complexidade própria extraídas para componentes no diretório da página
- [ ] Componentes potencialmente reaproveitáveis avaliados para `shared/`
- [ ] Todo componente tem `:host { display: block; width: 100%; flex-shrink: 0 }`
- [ ] Scroll aplicado apenas na área da lista, com scrollbar sutil
- [ ] Componentes declarados no módulo da página
- [ ] `[class.new-design-font]="isNew*"` no div raiz do novo bloco
- [ ] Erros TypeScript pré-existentes corrigidos
