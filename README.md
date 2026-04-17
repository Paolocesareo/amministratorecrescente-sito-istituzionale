# Amministratore Crescente™ — sito istituzionale

Sito statico Jekyll del brand **Amministratore Crescente™** di Paolo Cesareo, ospitato su GitHub Pages con dominio `amministratorecrescente.net`.

## Struttura

```
.
├── _config.yml              Dati globali e contatti (modifica qui nome, P.IVA, email, WhatsApp)
├── _includes/
│   ├── header.html          MENU principale (una volta sola, riflesso su tutto il sito)
│   ├── footer.html          FOOTER principale
│   └── cta-masterclass.html Box "Candidati" riutilizzabile
├── _layouts/
│   ├── default.html         Impalcatura HTML (head + header + main + footer)
│   ├── page.html            Pagine istituzionali (Chi sono, Metodo, ecc.)
│   └── post.html            Layout articoli blog
├── _posts/                  Articoli blog in Markdown (nome file: AAAA-MM-GG-slug.md)
├── assets/
│   ├── css/style.css        Tutto il CSS del sito (palette, tipografia, layout)
│   ├── js/main.js           Piccolo JS per menu mobile
│   └── img/                 Logo, foto, copertine
├── blog/
│   └── index.html           Elenco articoli
├── index.html               Homepage
├── chi-sono.html
├── metodo.html
├── masterclass.html
├── risorse-gratuite.html
├── contatti.html
├── privacy.html
├── cookie-policy.html
├── note-legali.html
├── 404.html
├── CNAME                    Dominio custom (amministratorecrescente.net)
├── robots.txt
├── sitemap.xml
├── Gemfile                  Dipendenze per test in locale (non serve per GitHub Pages)
└── .gitignore
```

## Cose che modifichi più spesso

- **Cambiare una voce di menu** → `_includes/header.html`
- **Cambiare footer (numero telefono, indirizzo)** → `_config.yml` (sezione `contact` o `brand`)
- **Cambiare colori/font** → `assets/css/style.css` (blocco `:root` in alto, variabili CSS)
- **Pubblicare un nuovo articolo** → crea file in `_posts/AAAA-MM-GG-slug.md`
- **Cambiare link Kartra (candidatura Masterclass, lead magnet)** → `_config.yml` (sezione `kartra`)

## Deploy su GitHub Pages

1. Crea il repository su GitHub: **Paolocesareo/amministratorecrescente-sito-istituzionale**
2. Push del contenuto di questa cartella nella branch `main`
3. Settings → Pages → "Deploy from a branch" → Branch `main`, folder `/ (root)` → Save
4. Aggiungi il dominio custom in Settings → Pages → Custom domain → `amministratorecrescente.net`
5. Spunta **Enforce HTTPS** (appare dopo pochi minuti, quando Let's Encrypt ha emesso il certificato)

## Migrazione DNS (Register.it → GitHub Pages)

Sul pannello di **Register.it** → Gestione DNS del dominio `amministratorecrescente.net`:

**Sostituisci i record attuali (SiteGround) con:**

| Tipo  | Nome | Valore                  | TTL  |
|-------|------|-------------------------|------|
| A     | @    | 185.199.108.153         | 3600 |
| A     | @    | 185.199.109.153         | 3600 |
| A     | @    | 185.199.110.153         | 3600 |
| A     | @    | 185.199.111.153         | 3600 |
| CNAME | www  | paolocesareo.github.io. | 3600 |

> Sostituisci `paolocesareo.github.io.` con il tuo vero username GitHub se diverso (nota il punto finale in alcuni pannelli).

Downtime atteso: 5–30 minuti (propagazione DNS). Meglio farlo la sera.

## Kartra: integrazioni da completare

In `_config.yml` sostituisci i placeholder:

- `kartra.masterclass_candidacy`: URL del funnel di candidatura alla Masterclass
- `kartra.lead_magnet`: URL della pagina di download del lead magnet
- `kartra.newsletter_embed`: snippet del form iscrizione newsletter (va incollato in `risorse-gratuite.html`)

Il form dei **Contatti** (`contatti.html`) usa un `action=""` placeholder: incolla l'endpoint Kartra o, in alternativa, un form Formspree gratuito.

## Test in locale (opzionale, per dev)

```bash
bundle install
bundle exec jekyll serve
```

Apri `http://localhost:4000`. Non serve per pubblicare: GitHub Pages compila da solo.

## Stato attuale

Il sito è **a scheletro**: struttura tecnica completa, stili pronti, pagine create. I **contenuti** sono segnati con marcatori `[[TODO]]` o bozze da rifinire con Paolo. I contenuti finali andranno integrati dal backup Wix (`SITO ISTITUZIONALE AC/File del sito wix/AC punto NET/`).

## Checklist pre-lancio

- [ ] Compilare tutti i `[[TODO]]` nelle pagine
- [ ] Caricare logo definitivo in `assets/img/favicon.png` e `assets/img/og-default.jpg`
- [ ] Scegliere il destino del blog (ripubblicare i vecchi articoli o partire da zero)
- [ ] Integrare form Kartra (newsletter + candidatura)
- [ ] Redigere privacy policy, cookie policy, note legali (consiglio: iubenda)
- [ ] Attivare Google Analytics 4 in `_layouts/default.html` (oppure Plausible)
- [ ] Testare da mobile e tablet
- [ ] Migrazione DNS Register.it → GitHub Pages
- [ ] Verifica SSL (attendere Let's Encrypt ~15 min)
- [ ] Inviare sitemap su Google Search Console
