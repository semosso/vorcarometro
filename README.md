# Vorcarômetro — pasta de deploy

Três arquivos, sem build, sem backend:

- `index.html` — a página inteira (HTML/CSS/JS).
- `data.json` — a base de pessoas/instituições, fontes e valores por estado. É o único arquivo que você vai editar pra adicionar gente nova.
- `og-image.png` (1200×630) — a capa que aparece quando o link é compartilhado no WhatsApp/Twitter/etc. Já vem pronta, só precisa ficar na mesma pasta que `index.html`.

## Antes de publicar

1. ~~Troque o email de sugestões~~ — já configurado (`vorcarometro@gmail.com`, na constante `SUGESTOES_EMAIL` perto do topo do `<script>`).
2. ~~Troque o domínio~~ — já configurado (`vorcaromet.ro`), nas tags `og:image`/`og:url`/`twitter:image` do `<head>` e no rodapé da imagem de compartilhar (dentro de `drawShareCard`). Se o domínio final mudar, procure por `vorcaromet.ro` em `index.html` e troque todas as ocorrências — o `og:image` precisa continuar sendo uma URL absoluta pra funcionar em preview de link.
3. Favicon já incluído (emoji 💸 embutido como `data:` URI no `<head>` — não precisa de arquivo separado). Se quiser um ícone próprio, troque o `<link rel="icon">` por um arquivo `favicon.svg`/`favicon.ico` na mesma pasta.

## Como publicar

Qualquer hospedagem de arquivo estático funciona — os dois arquivos só precisam ficar na mesma pasta, servidos por HTTP(S) (não abre certo com duplo-clique local, porque `data.json` é carregado via `fetch`, que exige http/https).

**Netlify (mais simples):** arraste a pasta `deploy/` inteira em https://app.netlify.com/drop — pronto, já tem link público. Domínio próprio depois é só configurar nas configurações do site.

**Vercel:** `vercel deploy` dentro da pasta (ou conecte um repositório Git com esses dois arquivos na raiz).

**GitHub Pages:** suba os dois arquivos pra um repositório, ative Pages nas configurações apontando pra branch/pasta raiz.

Qualquer outro host de arquivo estático (Cloudflare Pages, S3+CloudFront, etc.) também serve — não tem nada específico de uma plataforma.

## Como adicionar um nome novo depois

Abra `data.json` (é um JSON puro, dá pra editar em qualquer editor de texto):

1. Se a fonte ainda não está listada em `"src"`, adicione uma entrada: `"chave-curta": {"t": "Nome do veículo — título da matéria", "u": "https://..."}`.
2. Adicione um objeto em `"entities"` seguindo o formato dos existentes: `id` (slug único), `name`, `aliases` (variações de busca que não são substring do nome), `type` (`pessoa`/`instituicao`/`fundo`), `status.label` (curto: Preso, Investigado(a), Citado(a), Liquidação, Contraparte, Parte afetada — mantenha esse vocabulário) e `status.kind` (`critical`/`warning`/`affected`), `role`, `period`, `amounts` (array, pode ser vazio), `joke` (uma frase, mirando o esquema/sistema — `null` se o único fato relevante for algo grave demais pra piada, como no caso do Mourão), `related` (ids de outras entidades), `states` (siglas de UF, pode ser vazio), `summary` (sempre sério, hedged, com fonte), `src` (array de chaves de `"src"`).
3. Se quiser esse estado aparecendo colorido no mapa, adicione/edite uma entrada em `"stateInfo"` (chave = sigla do estado).

Não precisa mexer no `index.html` pra isso — só no `data.json`. O site relê o arquivo a cada carregamento de página (sem cache agressivo, mas o navegador do visitante pode cachear por um tempo — considere um parâmetro de versão na URL do fetch, tipo `data.json?v=2`, se quiser forçar atualização imediata pra quem já visitou).

## Links diretos (deep link)

A página lê e escreve o estado na URL, então dá pra compartilhar um link direto pra:

- Uma entidade específica: `?p=<id>` (ex.: `?p=daniel-vorcaro`).
- Um termo de busca: `?q=<termo>`.
- Um estado no mapa: `#estado=<UF>` (ex.: `#estado=SP`).

Isso também alimenta o botão "Compartilhar" de cada resultado/estado, que já monta o link certo automaticamente — não precisa configurar nada extra.
