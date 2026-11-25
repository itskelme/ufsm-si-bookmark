# Bookmark Manager

> Um gerenciador de favoritos (bookmarks) que roda no navegador, com login por e-mail e senha e links salvos por usuário no Firebase, sincronizados em tempo real.

**Contexto:** UFSM · Sistemas de Informação · trabalho em grupo · Profª Rosecleia · 2026/1

## Sobre

A aplicação guarda links organizados por categoria. Cada pessoa cria uma conta, salva seus bookmarks com título, descrição e categoria e encontra tudo depois por busca, filtro e ordenação. Os dados ficam no Cloud Firestore, separados por usuário, e qualquer alteração aparece na hora em todas as abas e dispositivos abertos.

Como trabalho acadêmico, o objetivo é praticar HTML, CSS e JavaScript sem framework (manipulação do DOM, eventos, formulários com validação, modais) e integrar um front-end a um serviço de autenticação e banco de dados na nuvem.

## Funcionalidades

- **Conta:** login e cadastro com e-mail e senha (Firebase Authentication), botão para mostrar a senha e mensagens de erro em português (e-mail inválido, senha fraca, e-mail já cadastrado, muitas tentativas etc.). A sessão continua aberta ao recarregar a página.
- **Bookmarks:** criar, editar e excluir (com confirmação). Cada bookmark tem URL (obrigatória, com `https://`), título (obrigatório), descrição opcional e categoria.
- **Título automático:** ao sair do campo de URL com o título vazio, ele é preenchido com o domínio (por exemplo `Github.com`).
- **Categorias:** a conta começa com `Geral`. Dá para criar categorias (até 30 caracteres, sem repetir) e excluí-las. Ao excluir, os bookmarks da categoria vão para `Geral`, que não pode ser excluída.
- **Barra lateral** com o total de bookmarks por categoria. Clicar em uma categoria filtra a lista.
- **Busca** instantânea no título, na URL e na descrição, sem diferenciar maiúsculas de minúsculas.
- **Ordenação** por mais recentes, mais antigos ou A-Z.
- **Visualização** em grade (cartões) ou em lista.
- **Favicon** de cada site, buscado pelo serviço de favicons do Google. Se ele não carregar, aparece a inicial do título.
- **Sincronização em tempo real** com `onSnapshot` do Firestore.
- **Atalhos:** Ctrl+K (ou Cmd+K) vai para a busca, Ctrl+N (ou Cmd+N) abre um novo bookmark e Esc fecha os modais.
- Avisos rápidos (toasts) de sucesso e erro e tela de carregamento enquanto conecta.

## Tecnologias

- HTML5, CSS3 e JavaScript puro, sem framework nem etapa de build
- [Firebase](https://firebase.google.com/) Web SDK 9.23.0 (*compat*), carregado por CDN:
  - Firebase Authentication (e-mail e senha)
  - Cloud Firestore
- Serviço de favicons do Google (`https://www.google.com/s2/favicons`)

Modelo de dados no Firestore:

```
users/{uid}/bookmarks/{id}   { title, url, description, category, createdAt, updatedAt }
users/{uid}/settings/data    { categories: [ "Geral", ... ] }
```

## Estrutura do projeto

| Caminho | Descrição |
|---|---|
| `index.html` | **A aplicação completa em um arquivo:** tela de login, aplicação, CSS e JavaScript embutidos, mais a configuração do Firebase (`FIREBASE_CONFIG`) |
| `login.html`, `login.css`, `login.js` | Tela de login separada, parte da versão modular (veja abaixo) |
| `app.js`, `style.css` | Lógica e estilo da aplicação na versão modular, com o SDK modular do Firebase v10 |
| `firebase-init.js` | Inicializa Auth e Firestore para a versão modular |
| `firebase-config.js` | Configuração do Firebase para a versão modular |
| `LICENSE` | Licença CC BY-NC 4.0 |

**Sobre a versão modular:** os arquivos fora do `index.html` são uma divisão do projeto em módulos ES que **não está terminada e não funciona como está**:

- `firebase-config.js` importa `firebase/app` pelo nome do pacote, o que só funciona com um bundler, e não exporta o `FIREBASE_CONFIG` que `firebase-init.js` e `app.js` importam;
- nenhuma página carrega `app.js` ou `style.css`;
- `app.js` procura o elemento `#user-email`, mas o `index.html` usa `#user-name`.

Use o `index.html`.

## Como rodar

### Pré-requisitos

- Um navegador atual (Chrome, Firefox, Edge ou Safari)
- Conexão com a internet (o SDK do Firebase vem por CDN e os dados ficam na nuvem)
- Opcional: Python 3 ou Node.js para servir os arquivos localmente

### Passo a passo

1. Clone o repositório:

   ```bash
   git clone https://github.com/itskelme/ufsm-si-bookmark.git
   cd ufsm-si-bookmark
   ```

2. Abra o `index.html` no navegador (clique duas vezes ou arraste para a janela). Se o navegador bloquear algo ao abrir como arquivo, sirva a pasta por HTTP:

   ```bash
   python -m http.server 8000      # ou: npx serve .
   ```

   e acesse <http://localhost:8000/index.html>.

3. Na aba **Criar conta**, cadastre um e-mail e uma senha de pelo menos 6 caracteres. Depois é só entrar.

O `index.html` aponta para o projeto Firebase do grupo (`trabalho-rose`). Se esse projeto for desativado, ou se você quiser usar o seu, siga a próxima seção.

### Usando o seu próprio projeto Firebase

1. Crie um projeto no [Console do Firebase](https://console.firebase.google.com/) e adicione um app Web (ícone `</>`).
2. Em **Authentication > Sign-in method**, ative **E-mail/senha**.
3. Em **Firestore Database**, crie o banco.
4. Em **Firestore > Regras**, restrinja o acesso para que cada usuário só leia e grave os próprios dados:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /users/{uid}/{document=**} {
         allow read, write: if request.auth != null && request.auth.uid == uid;
       }
     }
   }
   ```

5. No `index.html`, troque o objeto `FIREBASE_CONFIG` (no começo do `<script>` principal, perto do fim do arquivo) pelo `firebaseConfig` do seu app.
6. Se for publicar em um domínio que não seja `localhost`, adicione esse domínio em **Authentication > Settings > Authorized domains**.

## Como usar

1. Entre com sua conta.
2. Clique em **+ Novo Bookmark** (ou Ctrl+N), cole a URL, ajuste o título, escolha a categoria e salve.
3. Use a barra lateral para filtrar por categoria e **Gerenciar Categorias** para criar ou excluir categorias.
4. Pesquise pela barra de busca (Ctrl+K), mude a ordem no seletor e alterne entre grade e lista pelos botões ao lado.
5. Passe o mouse sobre um cartão (ou uma linha, na lista) para ver os botões de editar e excluir.
6. Clique em **Sair** para encerrar a sessão.

**Depois de clicar em Sair, recarregue a página (F5) antes de entrar de novo.** O botão **Entrar** fica desabilitado depois de um login bem-sucedido e só volta a funcionar ao recarregar.

## Observações

Pontos encontrados ao revisar o código para este README:

- O botão de login desabilitado depois do logout, explicado acima, acontece porque o código só reabilita o botão quando o login dá erro.
- O `index.html` ainda tem um objeto `Auth` antigo, baseado em `localStorage`, que não é mais usado e chama uma função `hashPwd` que não existe. É código morto que sobrou da versão anterior ao Firebase.
- A validação de URL aceita qualquer URL válida para `new URL()`, inclusive `javascript:...`, e os nomes de categoria entram na barra lateral sem escape de HTML. Como cada usuário só vê os próprios dados, o risco fica restrito à própria conta, mas vale validar o protocolo (`http`/`https`) e escapar os nomes.
- A chave `apiKey` do Firebase aparece no código. Isso é normal em apps web Firebase (a chave identifica o projeto, não dá acesso). A proteção dos dados depende das regras do Firestore.

## Referências

- [Documentação do Firebase para Web](https://firebase.google.com/docs/web/setup)
- [Firebase Authentication: e-mail e senha](https://firebase.google.com/docs/auth/web/password-auth)
- [Cloud Firestore: dados em tempo real (`onSnapshot`)](https://firebase.google.com/docs/firestore/query-data/listen)
- [Regras de segurança do Firestore](https://firebase.google.com/docs/firestore/security/get-started)
- [MDN Web Docs](https://developer.mozilla.org/pt-BR/) (HTML, CSS, JavaScript e a API `URL`)

## Autores

Grupo:

- Diego Kelme Santos da Silva ([@itskelme](https://github.com/itskelme))
- Gabriel Araujo Sangoi
- Pedro Augusto Klein

Docente: Profª Rosecleia

## Licença

Este projeto está sob a licença [CC BY-NC 4.0](LICENSE): uso pessoal e modificação permitidos com atribuição; uso comercial proibido. Material de terceiros (enunciados, código fornecido pelo professor) pertence aos respectivos autores.
