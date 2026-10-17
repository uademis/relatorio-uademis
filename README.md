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
   const ADMINS_PRINCIPAIS = ['adms.uademis@gmail.com'];      // admin fixo (os demais são cadastrados no painel)
   const PERMITIDOS = [];                                     // vazio = qualquer Gmail pode enviar
   ```
4. Salve, escolha a função **setup** e clique em **Executar**. Autorize
   (em "app não verificado": *Avançado → Acessar*). São criadas as abas **Relatorios**, **Atividades**, **Regioes** (já com Baixada Santista, Litoral Sul, Litoral Norte, Interior e Vale do Ribeira), **Setores** e **Admins**.
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
3. Em 1–2 minutos estará no ar.
4. **Antes de mandar o link para os líderes**, entre em `/admin/` → **Regiões e setores** e cadastre os setores de cada região.
   Sem nenhum setor cadastrado, os líderes não conseguem enviar.

---

## Como funciona

- As duas páginas têm um **menu lateral** (no celular, abre pelo botão ☰) com a navegação,
  a conta conectada e o botão **Sair**. Sem login, o menu mostra o botão *Entrar com Google*.
- Cada pessoa entra com o próprio Gmail. O relatório é gravado com o **e-mail de quem enviou**.
- Página de envio (líderes):
  - **Novo relatório:** o líder escolhe a **região** e depois o **setor** (só os cadastrados pelo admin);
    atividades ilimitadas, **valor total da contribuição mensal (R$)**, totais automáticos
    e rascunho salvo no aparelho.
  - **Meus envios:** só os relatórios daquele e-mail; cada um pode ser aberto, impresso ou excluído.
  - Para administradores, o menu mostra também **Painel do administrador**.
- `/admin/` (administrador principal + os cadastrados em *Administradores*):
  - **Painel:** filtros por ano, mês, **região** e **um ou vários setores** (caixinhas de marcar),
    busca por líder, e-mail ou atividade; resumo, **resumo por região**,
    total de contribuições, gráfico mês a mês, tipos de atividade, tabela por setor
    (com a contribuição de cada um) e **pendências do mês**
    (quais setores cadastrados já enviaram e quais faltam). Cada relatório abre completo, com impressão/PDF.
  - **Imprimir resumo consolidado:** gera um PDF com os totais do filtro atual, tabela por região,
    tabela por setor e, se houver mês escolhido, quem enviou e quem está pendente.
  - **Regiões e setores:** cadastrar setores dentro de uma região, **mudar o setor de região**, remover setor,
    criar novas regiões e remover regiões vazias. Remover um setor só o tira da lista dos líderes; os
    relatórios já enviados continuam. Ao mudar um setor de região, os relatórios antigos dele passam a
    contar na nova região.
  - **Administradores:** adicionar ou remover Gmails com acesso ao painel. Vale na hora, sem mexer no código.
    O administrador principal (`ADMINS_PRINCIPAIS` no `Code.gs`) não pode ser removido pelo painel,
    e ninguém consegue remover a si mesmo.
  - **Relatório Mensal de Atividades:** volta para a página de envio.
- **Excluir:** dentro de cada relatório aberto há o botão *Excluir*. Quem enviou pode excluir os próprios
  relatórios; o admin pode excluir qualquer um. O relatório sai do site e dos totais, mas fica guardado nas abas
  **Lixeira_Relatorios** e **Lixeira_Atividades** da planilha, com a data e quem excluiu. Para recuperar,
  copie a linha de volta para a aba original (sem as duas últimas colunas).
- Quem não é admin e abre `/admin/` vê "Acesso restrito". A planilha confere o login no servidor,
  então ninguém consegue ver relatórios de outros só mexendo no site.
- O login do Google dura cerca de 1 hora. Se expirar no meio do preenchimento, é só entrar de novo:
  o rascunho continua salvo.

## Observações

- **Alterou o `Code.gs`**? *Implantar → Gerenciar implantações → lápis →
  Versão: Nova versão → Implantar*. A URL continua a mesma.
- **Restringir quem envia:** coloque os e-mails dos líderes em `PERMITIDOS`. Quem não estiver na lista
  consegue entrar, mas vê um aviso e não consegue enviar.
- **Setores criados antes das regiões** aparecem no grupo *Sem região*: escolha a região de cada um.
- **Planilha da versão anterior:** pode reaproveitar. Cole o `Code.gs` novo, preencha o `CLIENT_ID` de novo
  (agora a lista de admin se chama `ADMINS_PRINCIPAIS`) e rode `setup` outra vez: ele cria as abas que faltam
  sem apagar nada. A coluna `Contribuicao_Mensal` é criada sozinha no primeiro envio.
  Relatórios antigos (sem e-mail) aparecem só para o admin, e os sem contribuição contam como R$ 0,00.
- **"Erro 400: origin_mismatch" no login:** a origem do passo 1.3 está diferente do endereço do site.
  Use exatamente `https://SEU-USUARIO.github.io`.
- Testar abrindo o arquivo direto no computador (`file://`) não funciona com o login do Google.
  Teste pelo endereço do GitHub Pages.
