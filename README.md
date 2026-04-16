# Jardim Digital — Cristian Araujo

Meu jardim digital pessoal: notas, aprendizados e reflexoes publicados como site estatico.

Site: **https://araujocristian.github.io**

## Stack

- [Quartz v4](https://quartz.jzhao.xyz/) — static site generator para digital gardens
- [Obsidian](https://obsidian.md/) — editor de notas em markdown
- GitHub Pages — hospedagem (deploy automatico via GitHub Actions)

## Pre-requisitos

- Node.js >= 22
- npm >= 10.9.2

## Comandos

```bash
npm ci                          # Instalar dependencias
npx quartz build --serve        # Dev server local com hot reload
npx quartz build                # Build de producao (output em public/)
npm run check                   # Type check + format check
npm run format                  # Auto-format com Prettier
npm run test                    # Rodar testes
```

Para builds mais rapidos em dev, comente `Plugin.CustomOgImages()` em `quartz.config.ts`.

## Deploy

Push no branch **v4** dispara automaticamente o workflow `.github/workflows/deploy.yml` que faz build e deploy no GitHub Pages.

## Criando conteudo

1. Crie um arquivo `.md` em `content/` (ou use o template no Obsidian)
2. Adicione o frontmatter:
   ```yaml
   ---
   title: Titulo da nota
   draft: true
   tags:
     - tag1
   ---
   ```
3. Mude `draft: false` quando quiser publicar
4. Use `[[wikilinks]]` para links internos entre notas

## Estrutura do projeto

```
content/          # Notas em markdown (conteudo do vault)
templates/        # Templates do Obsidian (nao publicados)
quartz/           # Core do framework Quartz
quartz.config.ts  # Config principal (plugins, tema, analytics)
quartz.layout.ts  # Layout dos componentes nas paginas
public/           # Output gerado (gitignored no CI)
assets/           # Imagens e attachments
```

## Customizacoes do Quartz

Dois arquivos do framework foram customizados:

- **`quartz/components/Footer.tsx`** — links externos abrem em nova aba (`target="_blank"`)
- **`quartz/components/Search.tsx`** — opcao `showTitle` para esconder/mostrar titulo do botao de busca

## Atualizando o Quartz

O repositorio upstream esta configurado como remote:

```bash
git fetch upstream
git merge upstream/v4
```

Conflitos esperados nos 2 arquivos customizados acima — resolver mantendo as customizacoes.

Apos o merge:

```bash
npm ci                      # Reinstalar dependencias
npx quartz build --serve    # Validar que o build funciona
npm run check               # Verificar tipos
```

---

## Setup do Obsidian Vault

Documentacao completa para replicar este vault em outra maquina ou projeto.

### Configuracoes do vault

| Setting | Valor |
|---------|-------|
| Default location for new notes | `content/` |
| Attachment folder path | `assets/` |
| Templates folder | `templates/` |

### Core plugins habilitados

file-explorer, global-search, switcher, graph, backlink, canvas, outgoing-link, tag-pane, page-preview, daily-notes, templates, note-composer, command-palette, editor-status, bookmarks, outline, word-count, file-recovery, sync, bases

### Community plugins

| Plugin | ID | Funcao |
|--------|----|--------|
| Templater | `templater-obsidian` | Automacao de templates com scripting |
| Columns | `obsidian-columns` | Layout multi-coluna em markdown |
| Editing Toolbar | `editing-toolbar` | Toolbar de formatacao rica |
| Mermaid Tools | `mermaid-tools` | Toolbar visual para diagramas Mermaid |
| Table Editor | `table-editor-obsidian` | Edicao avancada de tabelas |

### Templates

| Arquivo | Uso |
|---------|-----|
| `templates/post.md` | Template padrao — prompt para nome, frontmatter com title/draft/tags |
| `templates/aulas.md` | Notas de aula — secoes "Resumo do Tema" e "Perguntas fixacao" |
| `templates/artigos.md` | Artigos (placeholder) |

### Automacoes do Templater

- **Folder template**: arquivos criados em `content/` automaticamente usam `templates/post.md`
- **Startup template**: `templates/post.md` carregado ao iniciar
- **Auto-rename**: o template prompt renomeia o arquivo via `tp.file.rename()`
- **Trigger on file creation**: ativado

### Integracao Obsidian x Quartz

| O que | Como funciona |
|-------|---------------|
| `.obsidian/` | No `.gitignore` — configs do vault nao vao pro git |
| `templates/` | Nos `ignorePatterns` do Quartz — nao e publicado |
| `private/` | No `.gitignore` e `ignorePatterns` — conteudo privado |
| Wikilinks | Resolvidos pelo plugin `ObsidianFlavoredMarkdown` (modo `shortest`) |
| Drafts | `draft: true` no frontmatter impede publicacao (plugin `RemoveDrafts`) |

### Checklist para replicar o vault

1. Instalar Obsidian e criar novo vault
2. Instalar community plugins: Templater, Columns, Editing Toolbar, Mermaid Tools, Table Editor
3. Em Settings > Files & Links: default folder `content/`, attachments `assets/`
4. Copiar pasta `templates/` com os 3 templates
5. Configurar Templater:
   - Templates folder: `templates`
   - Folder templates: `content/` -> `templates/post.md`
   - Trigger on file creation: ON
6. Clonar o repo e configurar `quartz.config.ts` (titulo, baseUrl, analytics)
7. `npm ci && npx quartz build --serve` para validar

---

Construido com [Quartz](https://quartz.jzhao.xyz/) por [jackyzha0](https://github.com/jackyzha0/quartz).
