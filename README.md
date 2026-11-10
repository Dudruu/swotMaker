# SWOT Studio — Matriz SWOT (FOFA) Interativa

Aplicação web minimalista e responsiva em arquivo único (`index.html`) para criação, edição, persistência local e exportação de **Matrizes SWOT (FOFA)**.

## Visão Geral

O **SWOT Studio** organiza o planejamento estratégico em um grid 2×2 com identificação visual sutil por quadrante, edição direta nos tópicos, suporte automático a tema claro/escuro e múltiplos formatos de exportação prontos para documentação (Notion/Obsidian), apresentações visuais ou backup estruturado.

---

## Funcionalidades

### 1. Cabeçalho do Projeto
- **Título editável inline:** Clique no título no topo da página para renomear a análise (padrão: *"Planejamento Estratégico 2027"*). As alterações são salvas automaticamente.
- **Data automática:** Exibe a data da última atualização formatada em `pt-BR`.
- **Contadores:** Mostra o total de tópicos no cabeçalho e a contagem individual no badge de cada quadrante.

### 2. Matriz SWOT 2×2
Divisão semântica nos quatro quadrantes clássicos:
- **Forças (`S` — Strengths):** Fatores internos positivos (destaque em verde esmeralda).
- **Fraquezas (`W` — Weaknesses):** Fatores internos negativos (destaque em âmbar/laranja).
- **Oportunidades (`O` — Opportunities):** Fatores externos positivos (destaque em azul oceano).
- **Ameaças (`T` — Threats):** Fatores externos negativos (destaque em rosa/vermelho).

### 3. Gestão Ágil de Tópicos
- **Adição rápida:** Digite no campo inferior do quadrante e pressione `Enter` ou clique no botão `+`.
- **Edição inline:** Clique sobre o texto de qualquer tópico existente para editá-lo diretamente. Pressione `Enter` (ou clique fora) para salvar, ou `Esc` para cancelar. Esvaziar o texto e confirmar remove o item.
- **Remoção individual:** Passe o cursor (ou navegue por teclado) sobre um card para revelar o botão discreto `×` de exclusão.
- **Limpar Tudo:** Botão no cabeçalho que abre um modal nativo (`<dialog>`) de confirmação antes de esvaziar todos os quadrantes.

### 4. Persistência e Sincronização
- **Salvamento automático:** Todo o estado é gravado continuamente no `localStorage` sob a chave `swot_studio_state_v1`.
- **Sincronização entre abas:** Alterações de dados ou tema em outra aba do navegador são refletidas automaticamente via evento `storage`.

### 5. Exportação e Backup
Disponível no menu **Exportar**:
- **Markdown (`.md`):** Documento limpo com títulos e listas, ideal para colar no Notion ou Obsidian.
- **Texto Estruturado (`.txt`):** Versão em texto plano numerado e delimitado.
- **Imagem em Alta Resolução (`.png`):** Renderizada via `<canvas>` em escala 2× (Retina), respeitando o tema atual (claro/escuro) e omitindo inputs e botões de ação.
- **Documento PDF (`.pdf`):** Aciona a impressão limpa (`window.print()`) com folha de estilos `@media print` que oculta cabeçalho de ações, inputs e rodapé.
- **Backup JSON (`.json`):** Exporta o estado completo da matriz para arquivamento ou compartilhamento.
- **Importar (`.json`):** Botão **Importar** no cabeçalho permite restaurar um backup `.json` previamente exportado.

### 6. Tema Claro e Escuro (Dark Mode)
- Segue a preferência do sistema operacional (`prefers-color-scheme`) por padrão via `<meta name="color-scheme" content="light dark">` e função CSS `light-dark()`.
- Botão no canto superior direito permite alternar manualmente entre claro e escuro, persistindo a preferência no `localStorage` (`color-scheme`) apenas quando diverge do sistema.

---

## Como Executar

Não há etapa de compilação ou instalação de pacotes obrigatória.

### Opção 1: Abrir diretamente no navegador
1. Abra o arquivo `index.html` com um duplo clique; ou
2. No PowerShell, dentro da pasta do projeto:
   ```powershell
   start index.html
   ```

### Opção 2: Servidor local (recomendado)
Servir via `http://localhost` mantém o `localStorage` isolado em uma origem consistente:

- **Node.js:**
  ```powershell
  npx serve .
  ```
- **Python:**
  ```powershell
  python -m http.server 8080
  ```
  Acesse `http://localhost:8080`.
- **VS Code:** Clique com o botão direito em `index.html` e escolha **Open with Live Server**.

> **Requisito:** Conexão com a internet na carga inicial para obter o Tailwind CSS e as fontes (`Inter` e `JetBrains Mono`) via CDN.

---

## Arquitetura e Estrutura Técnica

```text
swotMaker/
├── index.html   # Aplicação completa (HTML semântico, CSS com tokens light/dark e JS)
└── README.md    # Documentação do projeto
```

### Modelo de Dados (`localStorage`)
Chave: `swot_studio_state_v1`

```json
{
  "title": "Planejamento Estratégico 2027",
  "updatedAt": "2026-11-10T20:14:00.000Z",
  "quadrants": {
    "strengths": [{ "id": "s-1", "text": "..." }],
    "weaknesses": [{ "id": "w-1", "text": "..." }],
    "opportunities": [{ "id": "o-1", "text": "..." }],
    "threats": [{ "id": "t-1", "text": "..." }]
  }
}
```

### Tecnologias e APIs Utilizadas
- **HTML5 Semântico:** Landmarks (`<header>`, `<main>`, `<footer>`), `<dialog>` modal nativo com `closedby="any"` (e fallback para navegadores sem suporte), e atributo `popover="auto"` no menu de exportação (com carregamento condicional de `@oddbird/popover-polyfill` via CDN quando necessário).
- **Tailwind CSS (CDN) + CSS Custom Properties:** Layout responsivo em grid (`grid-cols-1 md:grid-cols-2`), tokens de tema usando `light-dark()` com fallback `@media (prefers-color-scheme: dark)` e estilos de impressão dedicados.
- **JavaScript Vanilla (ES6+):** Gerenciamento de estado imutável na renderização, geração de arquivos via `Blob` + `URL.createObjectURL`, leitura de arquivos via `FileReader` e renderização vetorial de PNG em `<canvas>`.

---

## Atalhos de Teclado

| Contexto | Tecla | Ação |
| :--- | :--- | :--- |
| Campo de novo tópico | `Enter` | Adiciona o tópico ao quadrante |
| Edição inline de tópico | `Enter` | Salva a alteração |
| Edição inline de tópico | `Esc` | Cancela a edição sem alterar o texto |
| Modal de confirmação / Menu Exportar | `Esc` | Fecha o modal ou o popover |
