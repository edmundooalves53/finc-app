# Finc — pronto pra publicar

Está tudo construído: o app (`index.html`), o ícone, o `manifest.json` e o
`service-worker.js` que permitem instalar como app de verdade — igual ao SIT.

Falta só uma parte que **só você consegue fazer**, porque envolve criar uma
conta/projeto seu no Firebase (não tenho como fazer isso por você). São só
cliques, leva uns 10 minutos. Depois disso, é publicar e usar.

---

## Passo 1 — Criar o projeto no Firebase (grátis)

1. Acesse **console.firebase.google.com** e entre com sua conta Google.
2. Clique em **"Adicionar projeto"**, dê o nome que quiser (ex: `finc-app`) e
   siga os passos padrão (pode desativar o Google Analytics, não precisa dele).
3. Dentro do projeto, clique no ícone **"</>"** (Web) pra criar um app Web.
   Dê um nome (ex: `finc-web`) e clique em registrar.
4. O Firebase vai te mostrar um bloco de código com `firebaseConfig = { ... }`.
   **Copie esse bloco inteiro.**

## Passo 2 — Colar a configuração no arquivo

1. Abra o arquivo `index.html` num editor de texto.
2. Procure por este trecho, perto do topo do `<script>`:

```js
const firebaseConfig = {
  apiKey: "COLE_AQUI_SUA_API_KEY",
  authDomain: "COLE_AQUI.firebaseapp.com",
  projectId: "COLE_AQUI_SEU_PROJECT_ID",
  storageBucket: "COLE_AQUI.appspot.com",
  messagingSenderId: "COLE_AQUI",
  appId: "COLE_AQUI"
};
```

3. Substitua pelos valores reais que o Firebase te deu no Passo 1.

## Passo 3 — Ativar o login com Google

1. No console do Firebase, vá em **Build → Authentication → Sign-in method**.
2. Clique em **Google** na lista de provedores e ative (toggle).
3. Escolha um e-mail de suporte (pode ser o seu) e salve.

## Passo 4 — Ativar o banco de dados (Firestore)

1. No console do Firebase, vá em **Build → Firestore Database**.
2. Clique em **"Criar banco de dados"**.
3. Escolha **"Iniciar em modo de produção"** e selecione uma região (qualquer
   uma do Brasil, ex: `southamerica-east1`).
4. Depois de criado, vá na aba **"Regras"** e substitua pelo texto abaixo —
   isso garante que cada pessoa só acessa os próprios dados:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

5. Clique em **"Publicar"**.

## Passo 5 — Publicar (igual ao SIT, pelo GitHub Pages)

1. Crie um repositório novo no GitHub (ex: `finc-app`).
2. Suba os arquivos desta pasta inteira (`index.html`, `manifest.json`,
   `service-worker.js`, e a pasta `icons/`) pra esse repositório.
3. Vá em **Settings → Pages** do repositório, escolha a branch `main` e
   salve. O GitHub te dá um link tipo `https://seuusuario.github.io/finc-app/`.

## Passo 6 — Autorizar esse domínio no Firebase

1. Volte no Firebase: **Authentication → Settings → Authorized domains**.
2. Clique em **"Adicionar domínio"** e cole o link do Passo 5
   (ex: `seuusuario.github.io`).
   Sem isso, o "Entrar com Google" não funciona no link publicado.

## Passo 7 — Instalar no celular

1. Abra o link do Passo 5 no navegador do celular.
2. Toque em "Entrar com Google" e escolha sua conta.
3. O navegador vai oferecer **"Adicionar à tela de início"** — toque nisso.
   Pronto, o Finc vira um ícone normal, abre em tela cheia, como qualquer app.

---

## O que já está pronto e funcionando

- Login com Google (`signIn()` / `signOutUser()`)
- Todos os dados salvos no Firestore, na conta de quem logou — troca de
  aparelho ou reinstalação não perde nada, porque nunca dependeu do aparelho
- Toda a estrutura: Home, Contas (credor → conta → parcela), Calendário,
  Resumo, Perfil, Configurações
- Exportar / Importar backup (dentro de Configurações)
- Animação de abertura (o morph do "FINC")
- Ícone do app já gerado (`icons/icon-192.png` e `icons/icon-512.png`)

## O que ainda não está incluído (combinado como próxima fase)

- **Notificações push** (3 dias antes / 1 dia antes / no dia / 1 dia depois) —
  isso exige configurar o Firebase Cloud Messaging e uma Cloud Function
  agendada, que é um pacote de trabalho à parte.

## Se algo der errado

O erro mais comum é esquecer o **Passo 6** (autorizar o domínio) — se o botão
"Entrar com Google" não fizer nada ou der erro, comece checando isso.
