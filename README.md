# Relatório Mensal – Uademis Adolescentes

Site com login pelo Gmail, ligado a um Google Sheets.

| Endereço | Quem usa | O que vê |
|---|---|---|
| `https://SEU-USUARIO.github.io/SEU-REPO/` | Líderes de setor | Envia relatórios e vê **só os próprios envios** |
| `https://SEU-USUARIO.github.io/SEU-REPO/admin/` | `adms.uademis@gmail.com` | **Todos** os relatórios, resumo, pendências e impressão |

## Arquivos

```
index.html          → página de envio (vai para o GitHub)
admin/index.html    → painel do admin (vai para o GitHub, dentro da pasta admin)
apps-script/Code.gs → script da planilha (vai no Apps Script do Google)
```

Cada página é um arquivo único: o visual, o código e o logo já estão dentro dele.

---

## 1. Criar o login do Google (uma vez)

Faça tudo logado como **adms.uademis@gmail.com**.

1. Acesse **https://console.cloud.google.com** e crie um projeto (ex.: *Uademis Relatórios*).
2. Menu **APIs e serviços → Tela de consentimento OAuth** (ou *Google Auth Platform*):
   - Nome do app: *Relatório Uademis* · E-mail de suporte: o seu
   - Público / Tipo de usuário: **Externo**
   - Depois de criar, em **Público**, clique em **Publicar aplicativo** (status *Em produção*).
     Sem isso, só os e-mails cadastrados como "usuários de teste" conseguem entrar.
     Como o site só pede nome e e-mail, não precisa de verificação do Google.
3. Menu **APIs e serviços → Credenciais → Criar credenciais → ID do cliente OAuth**:
   - Tipo: **Aplicativo da Web**
   - **Origens JavaScript autorizadas**: `https://SEU-USUARIO.github.io` (sem barra no fim e sem o nome do repositório)
   - Salve e copie o **ID do cliente** (termina em `.apps.googleusercontent.com`).

## 2. Preparar a planilha

1. Crie uma planilha no Google Drive (ex.: *Relatórios Uademis*), também com a conta admin.
2. **Extensões → Apps Script**, apague o código de exemplo e cole todo o `apps-script/Code.gs`.
3. No topo do código, preencha:
   ```js
   const CLIENT_ID = 'xxxxxxxx.apps.googleusercontent.com'; // o ID do passo 1
   const ADMINS = ['adms.uademis@gmail.com'];                 // pode colocar mais de um
   const PERMITIDOS = [];                                     // vazio = qualquer Gmail pode enviar
   ```
4. Salve, escolha a função **setup** e clique em **Executar**. Autorize
   (em "app não verificado": *Avançado → Acessar*). As abas **Relatorios** e **Atividades** serão criadas.
5. **Implantar → Nova implantação → App da Web**
   - Executar como: **Eu**
   - Quem pode acessar: **Qualquer pessoa**
6. Copie a **URL do app da Web** (termina em `/exec`).

## 3. Ligar o site

No topo de **`index.html`** e de **`admin/index.html`**, preencha os mesmos dois valores:

```js
const SCRIPT_URL = 'https://script.google.com/macros/s/..../exec';
const GOOGLE_CLIENT_ID = 'xxxxxxxx.apps.googleusercontent.com';
```

## 4. Publicar no GitHub Pages

1. No repositório, envie o `index.html` na raiz e crie a pasta `admin` com o `admin/index.html` dentro
   (em *Add file → Upload files*, arraste a pasta `admin` inteira).
2. **Settings → Pages** → *Deploy from a branch* → **main** / **(root)** → **Save**.
3. Em 1–2 minutos estará no ar. Mande o link principal para os líderes e use `/admin/` para o painel.

---

## Como funciona

- Cada pessoa entra com o próprio Gmail. O relatório é gravado com o **e-mail de quem enviou**.
- Na página de envio:
  - **Novo relatório:** formulário com atividades ilimitadas, totais automáticos e rascunho salvo no aparelho.
  - **Meus envios:** mostra só os relatórios daquele e-mail, cada um pode ser aberto e impresso.
  - O admin vê também o botão **Painel admin**.
- Em `/admin/` (só para os e-mails em `ADMINS`):
  - Resumo geral e filtros por ano, mês, setor, além de busca por líder, e-mail ou atividade.
  - Gráfico mês a mês, tipos de atividade e tabela por setor.
  - **Pendências do mês:** quais setores já enviaram e quais faltam.
  - Cada relatório abre completo, com impressão/PDF no layout do formulário.
- Quem não é admin e abre `/admin/` vê "Acesso restrito". A planilha confere o login no servidor,
  então ninguém consegue ver relatórios de outros só mexendo no site.
- O login do Google dura cerca de 1 hora. Se expirar no meio do preenchimento, é só entrar de novo:
  o rascunho continua salvo.

## Observações

- **Alterou o `Code.gs`** (ex.: adicionou um admin)? *Implantar → Gerenciar implantações → lápis →
  Versão: Nova versão → Implantar*. A URL continua a mesma.
- **Restringir quem envia:** coloque os e-mails dos líderes em `PERMITIDOS`. Quem não estiver na lista
  consegue entrar, mas vê um aviso e não consegue enviar.
- **Planilha da versão anterior:** pode reaproveitar. Ao rodar `setup` de novo, as colunas
  `Email_Envio` e `Nome_Conta` são acrescentadas. Relatórios antigos (sem e-mail) aparecem só para o admin.
- **"Erro 400: origin_mismatch" no login:** a origem do passo 1.3 está diferente do endereço do site.
  Use exatamente `https://SEU-USUARIO.github.io`.
- Testar abrindo o arquivo direto no computador (`file://`) não funciona com o login do Google.
  Teste pelo endereço do GitHub Pages.
