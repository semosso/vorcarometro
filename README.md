# Vorcarômetro — pasta de deploy

Três arquivos, sem build, sem backend:

- `index.html` — a página inteira (HTML/CSS/JS).
- `data.json` — a base de pessoas/instituições, fontes e valores por estado. É o único arquivo que você vai editar pra adicionar gente nova.
- `og-image.png` (1200×630) — a capa que aparece quando o link é compartilhado no WhatsApp/Twitter/etc. Já vem pronta, só precisa ficar na mesma pasta que `index.html`.

## Antes de publicar

1. ~~Troque o email de sugestões~~ — já configurado (`vorcarometro@gmail.com`, na constante `SUGESTOES_EMAIL` perto do topo do `<script>`).
2. Domínio: por enquanto aponta pro GitHub Pages (`https://semosso.github.io/vorcarometro/`), nas tags `og:image`/`og:url`/`twitter:image` do `<head>`. O rodapé da imagem de compartilhar (dentro de `drawShareCard`) usa só o nome "Vorcarômetro", sem domínio. Se comprar `vorcaromet.ro` e configurar o DNS, troque as URLs dessas três tags e adicione um arquivo `CNAME` — o `og:image` precisa continuar sendo uma URL absoluta pra funcionar em preview de link.
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

## Contador de visitas (GoatCounter)

O `index.html` já vem com o script do [GoatCounter](https://www.goatcounter.com/) no `<head>`, apontando pro código `vorcarometro` (`https://vorcarometro.goatcounter.com/count`). Pra ativar de verdade:

1. Crie uma conta grátis em https://www.goatcounter.com/signup.
2. No cadastro, escolha o código do site — se `vorcarometro` já estiver em uso por outra pessoa, escolha outro e troque o valor de `data-goatcounter` na tag `<script data-goatcounter="...">` perto do topo do `<head>` pra bater com o código escolhido.
3. Publique o `index.html` normalmente (nenhum outro passo de deploy muda). Em alguns minutos as visitas já aparecem no painel em `https://SEU-CODIGO.goatcounter.com`.

O GoatCounter conta page views e visitantes únicos sem cookies e sem guardar IP em texto puro (fica hasheado e é descartado depois de um tempo), então não precisa de banner de consentimento pra isso. Ele ignora automaticamente hits de quem tem bloqueador de anúncios ou "Do Not Track" ativado — é normal o número ficar um pouco abaixo do tráfego real.

## Links diretos (deep link)

A página lê e escreve o estado na URL, então dá pra compartilhar um link direto pra:

- Uma entidade específica: `?p=<id>` (ex.: `?p=daniel-vorcaro`).
- Um termo de busca: `?q=<termo>`.
- Um estado no mapa: `#estado=<UF>` (ex.: `#estado=SP`).

Isso também alimenta o botão "Compartilhar" de cada resultado/estado, que já monta o link certo automaticamente — não precisa configurar nada extra.
