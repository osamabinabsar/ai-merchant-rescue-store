# AI Merchant Suite storefront

Static, single-page storefront for the four product ZIPs. No JavaScript
frameworks, no build step, no analytics, no external dependencies.

## Files

- `index.html` - the storefront (HTML + CSS only).
- `products/*.zip` - the four product packages (Merchant Fee Audit,
  Chargeback Defense Kit, Bookkeeping Rescue, AI Merchant Suite bundle).
- `free-merchant-fee-audit-sample.md` - a real excerpt of audit output.
- `README.md` - this file.

## Open locally

Double-click `index.html`, or open it in any browser:

```text
C:\Users\Anagh\business\website\index.html
```

Because every link is relative (`products/...zip`, `free-...md`, `README.md`),
the page works without a server and nothing breaks when you move or rename the
folder.

## Deploy

### GitHub Pages

1. Create a new repository and push the contents of this `website/` folder to
   its root (or a `docs/` folder).
2. In the repository settings, enable Pages from the main branch / root (or
   `/docs`).
3. The published site will be `https://<user>.github.io/<repo>/`.
4. Keep the `products/` folder committed; the relative ZIP links will resolve
   against the deployed root.

### Netlify

1. Drag and drop the `website/` folder onto https://app.netlify.com/drop, or
   connect the repository and set the publish directory to `website`.
2. No build command is required. Netlify serves `index.html` as the index.
3. Relative links continue to work; ZIPs are served as static files.

## Before going live

1. Replace `hello@yourdomain.com` in `index.html` (contact section) with the
   real email or handle.
2. Update the "Last updated" line in the footer when content changes.
3. If you regenerate a product ZIP, copy it into `website/products/` so the
   storefront and source folder stay in sync.
4. Keep the free sample's numbers current: regenerate
   `free-merchant-fee-audit-sample.md` from a fresh `fee_audit.py` run rather
   than editing figures by hand.

## Analytics-free by design

There are no analytics scripts, pixels, or tracking cookies. If you want usage
stats, add them deliberately and disclose them in the privacy note on the page.

## Contact

Replace placeholder contact details before publishing. See the contact section
of `index.html`.
