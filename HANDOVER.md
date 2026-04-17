# HANDOVER — Sito Istituzionale Amministratore Crescente

**Ultimo aggiornamento:** 2026-04-17, 16:00 CET
**Scritto da:** Claude (sessione 283763d8)
**Destinato a:** il prossimo assistente che riprende il progetto

---

## Cos'è questo progetto

Sito istituzionale del brand **AMMINISTRATORE CRESCENTE™** di Paolo Cesareo. Dominio: `amministratorecrescente.net`. Brand di coaching/formazione **per** amministratori di condominio (non è un sito di amministrazione condominiale).

Paolo è:
- Amministratore di condominio (abilitato dal 1998) + avvocato
- Coach/formatore per altri amministratori
- Autore del Metodo V3 e della Regola dei 15 Minuti
- Fondatore APACI (2010)

Società intestataria del sito: **C.S.F. Lab Srls** — P.IVA 09026981218 — Viale Augusto 132, Napoli.

Prodotto principale: **Masterclass Amministratore Crescente™** — 8 settimane, max 10 partecipanti, gestita su **Kartra** (il sito Jekyll è vetrina + SEO e rimanda a Kartra per conversione).

Lo Studio legale Cesareo (`avvocatocesareo@studiocesareo.it` / 3394625823) è identità separata, NON compare su questo sito.

---

## Stato attuale (2026-04-17)

### Fatto
- Repo GitHub creato: `Paolocesareo/amministratorecrescente-sito-istituzionale` (pubblico)
- Sito Jekyll 3.10 base con layouts, includes, pagine principali, blog, 1 post esempio
- GitHub Pages attivo, build `success`
- CNAME custom domain: `amministratorecrescente.net`
- DNS spostati da Register.it a SiteGround (nameserver `ns1.siteground.net` / `ns2.siteground.net`)
- CDN SiteGround disattivata sul dominio (bloccava i record A)
- Record DNS finale su SiteGround:
  - 4 record A su `amministratorecrescente.net.` → `185.199.108-111.153`
  - CNAME su `www` → `paolocesareo.github.io.`
  - MX/TXT email lasciati intatti
- Sito già raggiungibile in HTTP via dominio custom (testato in incognito)

### In corso
- Certificato HTTPS Let's Encrypt: GitHub lo emette in automatico entro ~1h dalla propagazione DNS (iniziata ore 15:50 circa). Al momento l'API `/repos/.../pages` restituisce ancora `https_certificate: None`. Quando sarà `state: approved`, abilitare **Enforce HTTPS** dalle Settings → Pages.

### Da fare (prossimi passi)
1. **Contenuti reali nelle pagine.** Oggi molte pagine hanno placeholder `[[TODO: ...]]` con ipotesi di testo ricavate dal backup Wix. Paolo deve rivedere e approvare pagina per pagina (index, chi-sono, masterclass, metodo, contatti, risorse-gratuite). Il ton of voice del backup Wix è enfatico-motivazionale ("self-made man", "acceleratore"); da decidere se mantenere o ammorbidire.
2. **Link Kartra reali.** In `_config.yml` ci sono placeholder `#kartra-url-*`. Paolo deve fornire gli URL veri (candidatura Masterclass, lead magnet, newsletter embed).
3. **Form contatti.** Default proposto: embed Kartra. Alternativa Formspree/Getform.
4. **Blog.** C'è 1 post esempio (`_posts/2026-04-17-esempio-come-scrivere-un-articolo.md`). Da sostituire con post veri, magari migrando alcuni post dal backup Wix.
5. **Assets reali.** `assets/img/` contiene placeholder. Servono: logo AC definitivo, foto profilo Paolo, og-default.jpg per social preview, cover placeholder blog.
6. **Google Analytics / Search Console.** Nel `_layouts/default.html` c'è un blocco GA4 commentato con `G-XXXXXXX` da sostituire. Aggiungere anche verifica Search Console (tag meta o file TXT DNS).

---

## Architettura tecnica

### Stack
- **Hosting:** GitHub Pages (branch `main`, root `/`)
- **Generator:** Jekyll 3.10 (builder legacy di GitHub Pages)
- **Theme:** nessuno (`theme: null` nel `_config.yml`, layouts/include fatti in casa)
- **Plugins abilitati:** `jekyll-seo-tag`, `jekyll-sitemap`, `jekyll-feed`
- **DNS:** SiteGround (nameserver), record A sul dominio nudo + CNAME www
- **Funnel/vendita:** Kartra (separato, il sito linka)

### Perché NON WordPress / SiteGround
Paolo ha 50 servizi sullo stesso piano SiteGround, nessun motivo di disdire l'hosting. Ma il sito WordPress è stato abbandonato — pesante, difficile da manutenere, contenuti disallineati. GitHub Pages è gratis, versionato, veloce, gestibile da Claude via Git.

### Struttura repo
```
/
├── _config.yml              ← config globale (title, brand, contatti, kartra)
├── _layouts/
│   ├── default.html         ← wrapper HTML (head + header + footer inlineati)
│   ├── page.html            ← pagine statiche (eredita default)
│   └── post.html            ← blog post (eredita default, con CTA inlineata)
├── _includes/               ← NOTA: gli include NON sono più referenziati dai layout
│                              (vedi sezione "Bug risolto" sotto). Restano come riferimento.
├── _posts/                  ← blog posts (1 solo esempio)
├── assets/
│   ├── css/style.css
│   ├── js/main.js
│   └── img/
├── blog/index.html          ← pagina lista post
├── *.html                   ← pagine statiche root (index, chi-sono, masterclass, ecc.)
├── CNAME                    ← contiene "amministratorecrescente.net"
├── Gemfile                  ← github-pages + plugins
└── sitemap.xml, robots.txt, 404.html
```

---

## TRAPPOLE TECNICHE — leggere prima di modificare

### Bug Jekyll 3.10 su GitHub Pages: `realpath_prefixed_with? stack level too deep`

**Sintomo:** build GitHub Pages fallisce con `Liquid Exception: stack level too deep in /_layouts/post.html` (o un'altra pagina) con traceback su `include.rb:191 realpath_prefixed_with?`. Nel pannello Actions vedi il workflow "pages build and deployment" in failure.

**Causa:** bug noto di Jekyll 3.10 + Ruby 3+ sul builder legacy. Ogni volta che un layout fa `{% include foo.html %}` di un file da `_includes/`, il resolve del path ricorsa all'infinito. Non riguarda solo Paolo — è sul repo github/jekyll issue #9128.

**Workaround applicato (commit `7f44523` / `2c1876e`):**
- Inlineati i contenuti di `_includes/header.html`, `_includes/footer.html`, `_includes/cta-masterclass.html` direttamente nei layout e nelle pagine che ne avevano bisogno.
- Tutti i `{% include %}` residui nei **commenti HTML** (es. `<!-- {% include cta-masterclass.html %} -->`) sono stati wrappati in `{% raw %}...{% endraw %}`, altrimenti Liquid li parsava comunque e ripartiva il loop.
- La cartella `_includes/` è rimasta nel repo come riferimento ma **NON è più referenziata** da nessun layout.

**NON reintrodurre `{% include %}` nei layout** senza aver prima switched al build via GitHub Actions con Jekyll 4. Per farlo serve:
- un PAT con scope `workflow` (quello attuale ha solo `repo`)
- un file `.github/workflows/pages.yml` con `actions/configure-pages` + `actions/jekyll-build-pages`
- impostare `build_type: workflow` via API GitHub Pages

### Permalink su Jekyll 3
`permalink: /:slug/` a livello globale non funziona per le pagine statiche in Jekyll 3 (solo per i post). Per questo ogni pagina ha il suo `permalink: /nome/` nel front-matter. Se aggiungi una pagina nuova, ricordati il permalink esplicito.

### DNS — CDN bloccante
Sia Register.it (prima) che SiteGround (dopo) hanno una CDN che, quando attiva, BLOCCA la modifica dei record A (li mostra come "Record CDN" non editabili). Per spostare il dominio a GitHub Pages è stato necessario disattivare la CDN SiteGround (Site Tools → Speed → CDN).

### Resolver DNS nella sandbox Linux
La sandbox di lavoro ha DNS locale che cache-hitta vecchi IP a lungo. Per testare il sito via dominio custom, usare sempre `curl --resolve dominio:443:IP_GITHUB` oppure `dig @8.8.8.8`. Test con `curl https://amministratorecrescente.net/` senza resolve può restituire il vecchio WordPress ore dopo che è stato spostato.

### DNS — record MX/TXT email
Non toccare mai MX, SPF, DKIM, DMARC, TXT di verifica. La migrazione è solo sito — email `info@amministratorecrescente.it` continua a passare da dove passava prima. Se qualcuno ti chiede di "pulire la zona DNS" verifica cosa stai rimuovendo.

---

## Contatti e identità da NON sbagliare

Sul sito compaiono questi contatti del brand AC:
- Email: `info@amministratorecrescente.it` (NON `.com` — la vecchia email era sbagliata)
- Tel fisso: `06 56 54 70 13`
- WhatsApp: `+39 349 00 79 677` → `https://wa.me/393490079677`
- Indirizzo: Viale Augusto, 132 — 80125 Napoli
- Società: C.S.F. Lab Srls, P.IVA 09026981218

**NON mischiare con lo Studio legale Cesareo** (`avvocatocesareo@studiocesareo.it`, 3394625823). Sono due identità separate, e Paolo tiene molto a tenerle distinte.

---

## Credenziali / token

Le credenziali sono nel PROFILE.md di Paolo su GitHub (repo `Paolocesareo/Paolo`, file `PROFILE.md`). Lì ci sono GitHub PAT e Notion token.

**Attenzione:** il PAT scritto nelle preferenze utente di Paolo quando ho iniziato questa sessione era scaduto (401 Bad credentials). Mi ha dato uno nuovo in chat. Se hai problemi 401, chiedigli di generare un nuovo token. Quando serve pushare workflow o settings Actions, serve lo scope `workflow` oltre a `repo`.

---

## Come riprendere

1. Leggi `PROFILE.md` su GitHub (`https://raw.githubusercontent.com/Paolocesareo/Paolo/master/PROFILE.md`) — contiene il metodo di lavoro di Paolo e i comandi rapidi (`ip`, `as`, `pl`, `op`).
2. Il progetto corrente nel PROFILE è **CCII-OCC** (gestione ADER), ma questo progetto "Sito Istituzionale AC" è un side-track attivo. Paolo al momento usa il workspace locale `C:\Users\Golook pc\Documents\CoWork\Sito Istituzionale\SITO ISTITUZIONALE AC` con Cowork mode.
3. Per riprendere il lavoro sul sito, `git clone` questo repo, modifica, `git push`. GitHub Pages rebuilda entro 1-2 minuti.
4. Se Paolo dice "op", lancia la procedura di sync a 4 sistemi: `.md` GitHub + pagina Notion + `.docx` Drive + `.md` progetto Claude.

---

## Note di metodo di Paolo (valide sempre)

- Paolo è veloce e diretto. Niente spiegazioni lunghe se non le chiede. Se ti chiede "perché" è perché sta imparando, non perché dubita — dagli una spiegazione tecnica pulita.
- Dice "ip NOME" per aprire un progetto, "pl IDEA" per parcheggiare, "op" per sync output.
- Preferisce decisioni nette e opzioni chiare. Le domande che fa meritano risposte strutturate, non bullet infiniti.
- Non inventare "progetti attivi" — il progetto attivo lo decide lui nel PROFILE.

Buon lavoro.
