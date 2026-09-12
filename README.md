# Gerenciador de Encerramento

App web (PWA) de página única para técnicos preencherem os dados de um
atendimento (instalação, reativação, serviço, transferência, troca de
roteador, upgrade, retrabalho, equipamento recolhido, teste de velocidade,
pendência fibra etc.) e gerarem um relatório em texto pronto para colar no
WhatsApp. Funciona 100% no navegador (sem backend), salva o histórico dos
últimos 50 relatórios em `localStorage` e pode ser "instalado" no celular
como um app (ícone na tela inicial, funciona offline).

## Como funciona

1. Escolha a **categoria** do atendimento (Instalação, Reativação, Serviço,
   Transferência, Troca de Roteador, Upgrade, Retrabalho de Instalação,
   Retrabalho de Pendência, Equipamento Recolhido, Teste de Velocidade ou
   Pendência Fibra) e, quando aplicável, o **subtipo** (PF, PJ, B2B,
   Condomínio, ou a versão do repetidor).
2. O formulário se adapta automaticamente: alguns tipos mostram Caixa/
   Porta/Potência, outros não; alguns pedem Faturamento, outros não; e o
   campo "Serviço Realizado" já vem preenchido automaticamente quando o
   serviço é sempre o mesmo (ex.: "Instalação Fibra").
3. Marque os **produtos utilizados** — para cabos (Cabo de Rede / Cabo
   Drop) o app pede o valor inicial e final do medidor e calcula o total
   gasto automaticamente.
4. Adicione os **patrimônios utilizados** (escolhendo da lista de
   equipamentos) e informe o MAC de cada um. Para Troca de Roteador,
   Upgrade e Equipamento Recolhido também é possível registrar os
   **patrimônios recolhidos** (equipamento antigo + MAC); para Teste de
   Velocidade, os **patrimônios trocados**.
5. Escolha o **faturamento** (Não / Sim, com valor e parcelamento em até
   3x) quando aplicável.
6. A **Classificação** é calculada automaticamente: fica **Andamento**
   enquanto algum campo relevante do tipo escolhido ainda não foi
   preenchido, e muda para **Encerramento** assim que tudo estiver
   completo.
7. Toque em **Gerar relatório** para ver o texto final, pronto para copiar
   e colar no WhatsApp.

## Estrutura do projeto

```
gerenciador-encerramento/
├── index.html                # App inteiro (HTML + CSS + JS)
├── manifest.json              # Metadados do PWA (nome, ícones, cores)
├── sw.js                      # Service worker (cache offline)
├── icons/
│   ├── icon-192.png
│   ├── icon-512.png
│   ├── icon-maskable-512.png
│   └── apple-touch-icon.png
└── .github/workflows/deploy.yml   # Publica automaticamente no GitHub Pages
```

## 1. Criar o repositório no GitHub

```bash
# opção A: pelo site
# 1. Acesse https://github.com/new
# 2. Nome: gerenciador-encerramento (ou o que preferir)
# 3. Deixe "Public", NÃO marque "Add a README" (já temos um)
# 4. Clique em "Create repository"

# opção B: pelo terminal, com GitHub CLI já autenticado (gh auth login)
gh repo create gerenciador-encerramento --public --source=. --remote=origin
```

## 2. Subir os arquivos

Dentro da pasta `gerenciador-encerramento` (a mesma onde está este README):

```bash
git init
git add .
git commit -m "Primeira versão do gerenciador de encerramento"
git branch -M main
git remote add origin https://github.com/SEU-USUARIO/gerenciador-encerramento.git
git push -u origin main
```

> Troque `SEU-USUARIO` pelo seu usuário do GitHub. Se você usou a opção B
> do passo 1 (`gh repo create`), o `git remote add` já foi feito
> automaticamente — basta rodar `git push -u origin main`.

## 3. Publicar online (GitHub Pages)

O repositório já vem com um workflow (`.github/workflows/deploy.yml`) que
publica o site automaticamente a cada `push` na branch `main`. Só falta
ativar o GitHub Pages uma vez:

1. No GitHub, abra o repositório → **Settings** → **Pages**.
2. Em **Build and deployment → Source**, selecione **GitHub Actions**.
3. Pronto. Volte na aba **Actions** e acompanhe o deploy (leva ~1 minuto).
4. Quando terminar, o link do site aparece em **Settings → Pages**, algo
   como:

```
https://SEU-USUARIO.github.io/gerenciador-encerramento/
```

A partir daí, todo `git push` na branch `main` atualiza o site
automaticamente.

### Alternativa sem GitHub Actions (mais simples, manual)

Se preferir não usar o workflow:

1. **Settings → Pages → Source**: escolha **Deploy from a branch**.
2. Branch: `main`, pasta `/ (root)` → **Save**.
3. O GitHub publica em 1–2 minutos no mesmo link acima.

Nesse caso você pode até apagar o arquivo `.github/workflows/deploy.yml`,
ele não é necessário.

## 4. Instalar como app no celular

Depois de publicado:

- **Android (Chrome)**: abra o link → menu (⋮) → "Adicionar à tela inicial".
- **iPhone (Safari)**: abra o link → botão de compartilhar → "Adicionar à
  Tela de Início".

O ícone, nome e cores já estão configurados no `manifest.json`, e o
`sw.js` permite que o app abra mesmo sem internet (usando a última versão
salva em cache). A captura de coordenadas por GPS só funciona em conexão
segura (HTTPS) — o GitHub Pages já atende esse requisito.

## Observações técnicas

- Os dados (histórico e nome do técnico) ficam salvos apenas no navegador
  do aparelho (`localStorage`). Trocar de navegador/celular não migra o
  histórico — é só um app local, sem servidor nem login.
- Não há build step: é HTML/CSS/JS puro, então qualquer hospedagem de
  arquivos estáticos funciona (GitHub Pages, Netlify, Vercel, Cloudflare
  Pages etc.).
- Sempre que editar o `index.html`, se quiser forçar os usuários a
  receberem a nova versão imediatamente (e não a versão em cache), aumente
  o número em `CACHE_NAME` no início do `sw.js` (ex.:
  `gerenciador-encerramento-v2`).
- Para adicionar/remover itens das listas de **Produtos** ou
  **Patrimônios**, edite os arrays `PRODUTOS_LIST` e `PATRIMONIOS_LIST` no
  topo do `<script>` em `index.html`. Para marcar um produto como "cabo"
  (medido em metros, com início/fim), adicione o nome dele também em
  `CABO_PRODUTOS`.
