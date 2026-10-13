# COE Digital ID — COE P1 Funcional

Painel institucional do efetivo do Comando de Operações Especiais (COE) da PMERJ.
Aplicativo web de arquivo único (`index.html`, JavaScript puro) sobre Firebase
Realtime Database + Firebase Authentication. Uso interno.

- **Site:** https://spaei-coe.github.io/coe-digital-id/ (espelho: https://spaei-digital-coe.web.app)
- **Painel da TV:** `#/tv` (apresentação automática, sem login) · **Admin:** tela de login na raiz
- **QR público:** `#/v/{token}` (nível de compartilhamento definido pelo administrador)

## Arquivos

| Arquivo | Função |
|---|---|
| `index.html` | O aplicativo inteiro (HTML + CSS + JS, brasão e efetivo-base embutidos) |
| `version.json` | Versão publicada — consultado pelo app a cada 5 min (a TV se recarrega sozinha) |
| `manifest.json`, `icons/` | Instalação como app (PWA) e ícones |
| `database.rules.json`, `REGRAS-DO-BANCO.md` | Regras do Realtime Database e o passo a passo para publicá-las (manual) |
| `firebase.json`, `.firebaserc` | Hosting (cabeçalhos de cache) e projeto `spaei-digital-coe` |
| `.github/workflows/` | Publicação automática a cada push em `main` (Firebase Hosting e GitHub Pages) |

## Como publicar uma versão

1. Em `index.html`, atualizar `VERSAO` (texto completo) e `VERSAO_ID` (ex.: `'v41'`).
2. Em `version.json`, colocar **o mesmo valor** de `VERSAO_ID`. Se divergirem, os
   administradores veem o aviso de "nova versão" sem que exista uma, e a TV tenta
   recarregar (no máximo 1 vez a cada 30 min por versão-alvo).
3. `git push` em `main` — os dois workflows publicam sozinhos.
4. Conferir a aba **Diagnóstico** do painel: a linha *Build* deve mostrar a versão nova.

## Segurança

- Nenhum segredo fica neste repositório; a `apiKey` do Firebase é pública por desenho
  (a proteção está nas regras do banco e no Authentication).
- Pendência de LGPD conhecida (ver `REGRAS-DO-BANCO.md`): dados sensíveis ainda estão
  no mesmo nó que a TV lê.
