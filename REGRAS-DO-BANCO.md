# Regras do Realtime Database — COE P1 FUNCIONAL

O arquivo `database.rules.json` está versionado neste repositório, mas **ainda
não é publicado automaticamente**. Publicar antes dos passos abaixo derruba o
painel da TV.

## Como as regras funcionam

| Quem | Como entra | Pode |
|---|---|---|
| TV (`#/tv`) e QR público (`#/v/{token}`) | login **anônimo** automático | apenas leitura |
| Administrador | Authentication (RG + senha) | leitura e escrita |
| Qualquer outro | — | nada |

A escrita é liberada apenas para UIDs presentes em `coep1/admins_uid`. Esse nó
**não é auto-gravável**: ninguém consegue se promover a administrador sozinho.
Quem cadastra o UID de um novo administrador é um administrador já autorizado,
pela tela **Administradores** do painel.

## Passos para ativar (nesta ordem)

1. **Console do Firebase → Authentication → Sign-in method → Anônimo → Ativar.**
   Sem isso a TV não consegue ler o banco depois que as regras entrarem.

2. **Cadastrar o primeiro administrador manualmente** (problema clássico do
   ovo e da galinha: o nó que autoriza escrita ainda está vazio).
   - Console → Authentication → Users → copie o UID do RG 82061.
   - Console → Realtime Database → Dados → crie:

     ```
     coep1 / admins_uid / <UID_DO_RG_82061>
         rg: "82061"
         ts: 0
     ```

3. **Publicar as regras**: Console → Realtime Database → Regras → colar o
   conteúdo de `database.rules.json` → Publicar.

4. **Conferir na aba 🩺 Diagnóstico do painel**: botão *Testar gravação no
   banco* deve responder “GRAVAÇÃO FUNCIONANDO”. Abrir a TV em outro aparelho e
   confirmar que os cards e as fotos aparecem.

Se algo falhar, o botão de teste mostra o código exato devolvido pelo banco.

## Publicação automática (opcional, depois de validado)

Para o GitHub Actions publicar as regras junto com o site, a conta de serviço
`FIREBASE_SERVICE_ACCOUNT_SPAEI_DIGITAL` precisa do papel **Firebase Realtime
Database Admin** no projeto, e o `firebase.json` precisa declarar:

```json
"database": { "rules": "database.rules.json" }
```

Enquanto esse papel não for concedido, mantenha a publicação manual do passo 3.

## Pendência de LGPD já identificada

`coep1/mil` guarda saúde, contatos e endereço no mesmo nó que a TV lê. Com
leitura autenticada, uma sessão anônima alcança dados sensíveis dos 142
policiais. A correção é separar esses campos em `coep1/mil_restrito/{rg}`,
legível apenas por administrador. Refatoração de fase 2, ainda não feita.
