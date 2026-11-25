# Bookmark Manager

**UFSM · Sistemas de Informação · 2026/1** — trabalho em grupo, Profª Rosecleia

Gerenciador de favoritos (bookmarks) na web, com login e dados salvos por usuário no Firebase.

**Grupo:** Diego Kelme Santos da Silva, Gabriel Araujo Sangoi e Pedro Augusto Klein

## Tecnologias

- HTML, CSS e JavaScript puros
- Firebase Authentication (e-mail e senha) e Cloud Firestore

## Estrutura

| Arquivo | Papel |
|---|---|
| `login.html`, `login.css`, `login.js` | Tela de login |
| `index.html`, `style.css`, `app.js` | Aplicação: lista, cadastro e edição de bookmarks |
| `firebase-config.js`, `firebase-init.js` | Configuração e inicialização do Firebase |

## Como executar

Não há dependências para instalar: abra o `index.html` num navegador e faça login.

Ao clicar em **Sair** e voltar para a tela de login, atualize a página (F5) para a sessão reiniciar corretamente.
