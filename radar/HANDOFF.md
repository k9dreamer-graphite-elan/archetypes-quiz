# 📡 Handoff — Quiz de Arquétipos · RADAR Inconformados
## Guia de instalação no Netlify — inconformados.club

> Documento de handoff preparado por K9Dreamer em 2026-08-07.
> Público-alvo: quem vai instalar o quiz no Netlify sob o domínio **https://inconformados.club/**.

---

## 1. O que é este pacote

O quiz "Qual é o seu arquétipo de trabalho?" (edição RADAR Inconformados) é um site estático de **um único arquivo** — um `index.html` autocontido (HTML + CSS + JavaScript embutidos, ~500 KB). Não precisa de build, backend, banco de dados nem variável de ambiente. Qualquer hospedagem estática serve; este guia cobre o Netlify.

---

## 2. Arquivos-fonte (links)

| Arquivo | Onde está |
|---|---|
| **✅ RECOMENDADO — `index.html` corrigido** | https://raw.githubusercontent.com/k9dreamer-graphite-elan/archetypes-quiz/main/radar/index.html |
| Versão corrigida rodando ao vivo (para conferir) | https://k9dreamer-graphite-elan.github.io/archetypes-quiz/radar/ |
| Repositório com histórico dos patches | https://github.com/k9dreamer-graphite-elan/archetypes-quiz |
| ZIP original do quiz (Drive) — contém bugs, ver §3 | https://drive.google.com/file/d/1mtcIojMrd2BbgMQbdlIn8SG0Msie5QaG/view |
| Pacote de metadata/OpenGraph (Drive): `og-metadata.txt` + `og-radar-quiz.png` | https://drive.google.com/file/d/1u2IxXXVDuNtcB-JQNQx_nJ0EA3mF7LpE/view |

---

## 3. ⚠️ IMPORTANTE: use a versão corrigida, não o ZIP original

O `index.html` dentro do ZIP original do Drive tem **2 bugs conhecidos**, já corrigidos na versão do repositório:

1. **Página em branco** — o bundle JavaScript referencia um `React` global que nunca é definido; o app quebra com `ReferenceError: React is not defined` e a tela fica branca. *Correção aplicada:* uma linha no bundle expõe o React interno como `window.React`.
2. **Botão "download" não baixa a imagem** — o handler revogava a URL do blob imediatamente após o clique (condição de corrida que cancela o download em vários navegadores e em webviews como a do Telegram/Instagram). *Correção aplicada:* âncora anexada ao DOM, revogação adiada em 60 s e fallback que abre a imagem em nova aba.

**Ou seja: baixe o arquivo do link "RECOMENDADO" da tabela acima e use ele como seu `index.html`.** Se por algum motivo precisar partir do ZIP original, aplique os dois patches (o diff está no histórico do repositório, commits "Fix blank page" e "Fix download button" adaptados em "Add RADAR Inconformados version").

---

## 4. Instalação no Netlify

### Opção A — Drag & drop (mais simples, sem git)

1. Crie uma pasta local, por exemplo `radar-quiz/`.
2. Coloque dentro dela:
   - `index.html` (a versão corrigida — link na tabela do §2)
   - `og-radar-quiz.png` (do pacote de metadata — §5)
3. Edite o `<head>` do `index.html` com o bloco de metadata (§5) **antes** de subir.
4. Acesse https://app.netlify.com/ → **Add new site → Deploy manually**.
5. Arraste a pasta `radar-quiz/` inteira para a área de upload.
6. O site sobe em segundos numa URL `*.netlify.app`. Teste essa URL primeiro.

### Opção B — Via git (recomendado se houver atualizações futuras)

1. Crie um repositório com os mesmos 2 arquivos na raiz.
2. No Netlify: **Add new site → Import an existing project** → conecte o repositório.
3. Build command: *(vazio)* · Publish directory: `/` (raiz).
4. Cada `git push` publica automaticamente.

### Conectar o domínio inconformados.club

1. No painel do site: **Domain management → Add a domain** → digite `inconformados.club`.
2. Duas rotas de DNS (escolha uma):
   - **Netlify DNS (mais fácil):** aceite a sugestão de usar os nameservers do Netlify e troque os nameservers no registrador do domínio (Registro.br, GoDaddy etc.).
   - **DNS externo:** no seu provedor de DNS crie:
     - `A` @ → `75.2.60.5` (apex/raiz)
     - `CNAME` www → `<nome-do-site>.netlify.app`
3. Aguarde a propagação (minutos até ~24 h) e ative **HTTPS → Verify DNS → Provision certificate** (Let's Encrypt, automático e gratuito).
4. Em **Domain management**, defina `inconformados.club` como domínio primário (o Netlify redireciona `www` → apex automaticamente).

### Raiz ou /quiz?

O quiz pode morar na raiz (`https://inconformados.club/`) ou numa subpasta (`https://inconformados.club/quiz/`):

- **Raiz:** `index.html` e `og-radar-quiz.png` ficam na raiz da pasta publicada.
- **/quiz:** crie a subpasta `quiz/` com os dois arquivos dentro.

O bloco de metadata do §5 assume **`https://inconformados.club/quiz`** (como veio no arquivo `og-metadata.txt`). Se instalar na raiz, troque todas as URLs `https://inconformados.club/quiz` por `https://inconformados.club` e `…/quiz/og-radar-quiz.png` por `…/og-radar-quiz.png`.

---

## 5. Metadata e OpenGraph

O pacote de metadata (link no §2) traz dois arquivos:

- `og-metadata.txt` — o bloco de tags pronto para colar;
- `og-radar-quiz.png` — a imagem de preview 1200×630 (proporção 1.91:1, ideal para WhatsApp, LinkedIn, X e Slack sem cortes).

**Passo a passo:**

1. Suba `og-radar-quiz.png` no mesmo nível do `index.html` (ou ajuste o caminho em `og:image`).
2. Abra o `index.html` e localize as metas existentes no `<head>` (`<title>`, `description`, `og:title`, `og:description`, `og:type`). **Substitua-as** pelo bloco completo abaixo (é a versão final do `og-metadata.txt`):

```html
<!-- Primário -->
<title>Qual é o seu arquétipo de trabalho? · RADAR Inconformados</title>
<meta name="description" content="Seu cargo é um indicador atrasado. 10 cenários, 5 arquétipos, ~3 minutos — descubra o perfil que define como você trabalha e como usar IA a seu favor." />
<link rel="canonical" href="https://inconformados.club/quiz" />

<!-- OpenGraph (WhatsApp, LinkedIn, Facebook, Slack) -->
<meta property="og:type" content="website" />
<meta property="og:site_name" content="RADAR — Inconformados" />
<meta property="og:locale" content="pt_BR" />
<meta property="og:url" content="https://inconformados.club/quiz" />
<meta property="og:title" content="Qual é o seu arquétipo de trabalho?" />
<meta property="og:description" content="Seu cargo é um indicador atrasado. 10 cenários, 5 arquétipos, ~3 minutos — descubra o seu e como usar IA a seu favor. Um teste do RADAR." />
<meta property="og:image" content="https://inconformados.club/quiz/og-radar-quiz.png" />
<meta property="og:image:width" content="1200" />
<meta property="og:image:height" content="630" />
<meta property="og:image:alt" content="Qual é o seu arquétipo de trabalho? Teste do RADAR Inconformados — 10 cenários, 5 arquétipos." />

<!-- Twitter / X -->
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:title" content="Qual é o seu arquétipo de trabalho?" />
<meta name="twitter:description" content="10 cenários, 5 arquétipos, ~3 minutos. Descubra o seu — RADAR · inconformados.club" />
<meta name="twitter:image" content="https://inconformados.club/quiz/og-radar-quiz.png" />
```

> ⚠️ Lembre: se o quiz for instalado na **raiz** do domínio, ajuste as URLs conforme o §4 ("Raiz ou /quiz?").

**Dicas do pacote original:**

- O WhatsApp faz cache agressivo do preview: se trocar a imagem depois, **mude o nome do arquivo** (ex.: `og-radar-quiz-v2.png`) e atualize as tags.
- Valide antes de divulgar:
  - Facebook/WhatsApp: https://developers.facebook.com/tools/debug/
  - LinkedIn: https://www.linkedin.com/post-inspector/

---

## 6. Checklist final

- [ ] `index.html` é a **versão corrigida** (teste: a página carrega o quiz, não fica branca).
- [ ] Bloco de metadata colado no `<head>`, URLs ajustadas para raiz ou `/quiz`.
- [ ] `og-radar-quiz.png` acessível (abra a URL da imagem direto no navegador).
- [ ] Domínio `inconformados.club` apontado e com HTTPS ativo.
- [ ] Quiz completo de ponta a ponta: responder os 10 cenários → resultado com gráfico radar.
- [ ] Botões testados no resultado: **copiar imagem**, **baixar 1:1** e **copiar texto**.
- [ ] Preview validado no debugger do Facebook e no Post Inspector do LinkedIn.
- [ ] Teste real: mandar o link num chat do WhatsApp e conferir o card.

---

*Dúvidas ou problemas na instalação: falar com Diego, ou com K9Dreamer via @k9dreamer_btc.*
*Crédito do framework de arquétipos: Boris Cherny (@bcherny). Edição RADAR: Inconformados.*
