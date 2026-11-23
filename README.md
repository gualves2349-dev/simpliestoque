# SimpliEstoque v2 — MVP multiestabelecimento

## Teste imediato (sem Firebase)
Abra `index.html` no navegador e clique **Entrar na demonstração**. Os dados ficam no `localStorage` deste navegador. Para produção, não use o modo demonstração.

## Configuração Firebase
1. Crie um projeto em https://console.firebase.google.com/.
2. Em **Authentication > Sign-in method**, habilite **E-mail/senha**.
3. Em **Firestore Database**, crie um banco de dados (modo produção) na região desejada.
4. Em **Firestore > Regras**, cole o conteúdo completo de `firestore.rules` e publique.
5. Em **Configurações do projeto > Seus apps**, registre um aplicativo Web e copie `apiKey`, `authDomain`, `projectId`, `appId` para `firebase-config.js`.
6. Publique os arquivos `index.html`, `app.js`, `firebase-config.js` e `manifest.webmanifest` no GitHub Pages ou outro serviço HTTPS. No GitHub, configure **Settings > Pages > Deploy from branch**.
7. Em **Authentication > Settings > Authorized domains**, inclua o domínio do seu site se necessário.
8. Crie uma conta, depois crie o estabelecimento. Para funcionários: eles criam conta, selecionam **Entrar em equipe**, informam o código do estabelecimento; o administrador aprova em **Equipe**. O funcionário clica **Atualizar acesso**.

## Funcionalidades
- Estabelecimentos isolados, login por e-mail/senha, administrador e operador.
- Produtos, categorias, unidades, mínimo, quantidade, último fornecedor/preço.
- Entrada, saída, compra com fornecedor e preço, ajuste (somente admin).
- Movimentações atômicas via transação Firestore, evitando saldo negativo e conflitos entre dispositivos.
- Lista de compras, estimativa financeira pelo último preço, histórico (últimos 200), exportação CSV e JSON.
- Layout responsivo, manifesto PWA (instalação varia conforme navegador).

## Limites conhecidos / antes de comercializar
- Não há cobrança automática, assinatura, recuperação de senha dentro da interface, importação de backup, gestão de múltiplos estabelecimentos por usuário ou inventário por lote/validade.
- O valor financeiro usa **último preço × quantidade**, não custo médio contábil.
- O histórico carrega as 200 movimentações mais recentes, e o JSON exporta somente esse histórico carregado.
- O Firestore registra os movimentos e os saldos na mesma transação, mas as regras são uma camada inicial: para uso comercial mais exigente, mova movimentações para uma Cloud Function, validando atomicamente cada delta e aplicando rate limiting. As regras atuais permitem que operadores autorizados alterem diretamente campos de saldo por clientes modificados.
- Ao remover um funcionário, a entrada de associação no perfil permanece; a validação de membro impede o acesso, e o sistema ignora a associação obsoleta.
- Configuração Firebase requer acesso à internet; dependências JavaScript são carregadas por CDN.
- Faça testes de permissões, concorrência, acessibilidade, recuperação de conta e política de backup antes de disponibilizar a clientes pagantes.

## Novidades v2.1 — sincronização e avisos
- A sincronização entre dispositivos já usa listeners `onSnapshot` do Firestore. O indicador 🟢 confirma snapshot vindo do servidor, e 🟡 indica cache, escrita pendente ou conexão em andamento.
- Pop-ups de movimentações feitas por **outros usuários** e alertas de produtos que acabam de cruzar o estoque mínimo. A primeira carga não dispara avisos antigos.
- Botão **Ativar avisos** pede permissão para notificações do navegador. Estas funcionam somente enquanto a página estiver aberta; **push em segundo plano não está implementado** (requer service worker, Firebase Cloud Messaging, infraestrutura de envio e HTTPS).
- Botão **Criar estoque fictício** cadastra quatro produtos reais no Firestore da empresa, para testar em dois aparelhos. Use em estabelecimento de testes.
- Para testar, publique em HTTPS, configure Firebase e regras, crie o estabelecimento, abra a mesma conta em dois navegadores ou aprove outro operador; retire uma unidade no tablet 1 e confira o tablet 2.
- **Atenção**: antes de vender, endurecer regras Firestore / usar backend de confiança para validar movimentações. O estado sincronizado não elimina essa vulnerabilidade existente na v2.

## Correção de configuração — 23/11/2026
O arquivo `firebase-config.js` já está preenchido com o projeto `simpliestoque` e expõe `window.SIMPLIESTOQUE_FIREBASE`, conforme esperado por `app.js`. Não substitua pelo snippet `import { initializeApp } from "firebase/app"` do console, que exige bundler.
Para testar, publique todos os arquivos na raiz do GitHub Pages, faça recarga forçada (Ctrl+F5), habilite Authentication > E-mail/senha e adicione o domínio GitHub Pages em Authentication > Settings > Authorized domains. Publique as regras de `firestore.rules` no console do Firestore.
