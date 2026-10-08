# tw-mail

Template-uri de email pentru brandul Top-Win. Construite **email-safe**: tabele + stiluri inline, comentarii condiționale MSO + fallback VML pentru Outlook desktop.

Preview live: https://design-mkt-1.github.io/tw-mail/

## Conținut

| Fișier | Descriere |
|---|---|
| `welcome-email.html` | Welcome email — logo, banner hero, intro, 2 carduri bonus (Sports + Casino), 2 grile de jocuri (Popular, Top Games), Telegram / termeni / 18+, unsubscribe |
| `images/` | Toate asset-urile, exportate din Figma la 2x (retina): 16 fișiere, ~330 KB în total |
| `index.html` | Pagina de index servită de GitHub Pages |

Sursa design: Figma, frame `Welcome Email` (node `20:339`, fișier `s2CqwGqe0O0FcALhBNlTRe`).

## Cum e construit

- **Lățime fixă 600px**, tabele, stiluri inline. Nu există media queries, intenționat: clientul de mail / browserul mobil scalează tot emailul în jos, nu rearanjează nimic. Nu adăugați breakpoint-uri.
- Fonturi: Outfit / Space Mono / Inter prin `@import` Google Fonts, cu fallback Segoe UI / Arial / Courier New unde clientul blochează `@import` (Gmail, Outlook).
- Cardurile de bonus: fundalul (glow-uri + grafic) e o imagine (`card-sports-bg.jpg`, `card-casino-bg.jpg`), aplatizată din straturile Figma (blend mode `color-dodge` nu există în email). Textul și butoanele sunt text HTML real. Fundalul e declarat de 3 ori: atributul `background=`, `background-image` CSS și `<v:fill>` VML (Outlook desktop).
- Pozele jocurilor și bannerul sunt JPG, restul PNG. Textul `alt` al jocurilor e dedus din imagine, de verificat.

## Zonele editabile per campanie

În `welcome-email.html`, fiecare zonă care se schimbă e marcată cu comentarii (în engleză):

```
<!-- ═══════════ EDIT: PREHEADER ═══════════ -->
<!-- ═══════════ EDIT: HERO BANNER ═══════════ -->
<!-- ═══════════ EDIT: HEADLINE + INTRO ═══════════ -->
<!-- ═══════════ EDIT: SPORTS OFFER ═══════════ -->
<!-- ═══════════ EDIT: CASINO OFFER ═══════════ -->
<!-- ═══════════ EDIT: POPULAR GAMES ROW ═══════════ -->
<!-- ═══════════ EDIT: TOP GAMES ROW ═══════════ -->
```

Textele ofertelor (`375%`, `UP TO £900`, butoanele) sunt text HTML real, se editează direct.

## Înainte de un send real

1. **Imagini pe URL absolut.** Un client de email nu poate citi căi relative. Cele 16 asset-uri sunt referite de 20 de ori (14× `src`, 2× `background=`, 2× `background-image:url(...)`, 2× `<v:fill src=...>`), deci înlocuiește prefixul `images/` peste tot, nu doar în `src=`:

   ```bash
   sed 's|images/|https://cdn.yourdomain.com/topwin/|g' \
     welcome-email.html > welcome-email.send.html
   ```

   Cu Pages activ se poate folosi temporar `https://design-mkt-1.github.io/tw-mail/images/`, dar pentru producție recomandat un CDN propriu (Pages nu are SLA pentru trafic de campanie). Verificare: `grep -c 'images/' welcome-email.send.html` trebuie să dea 0.

2. **Linkuri reale.** Toate `href` sunt placeholder — de înlocuit: `https://example.com` (12×), `https://example.com/terms`, `https://example.com/unsubscribe`, `https://t.me/example`.

3. **Text real.** Paragraful de intro este încă lorem ipsum, exact ca în Figma.

4. **Test de randare.** Litmus / Email on Acid / Mailtrap. Local se verifică doar în browser; Outlook desktop randează diferit (colțuri drepte în loc de `border-radius`, fără fonturi Google, fără umbre).
