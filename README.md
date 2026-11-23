# Talão digital de pedidos da Cefas

App web que substitui o talão de papel. Funciona no celular e no computador, e pode ser instalado como aplicativo.

- Preenche o pedido nos mesmos campos do talão: responsável, endereço, telefone, quantidade, discriminação, valor unitário e total.
- Gera o PDF no layout do talão, com numeração própria, QR Code PIX já com o valor e o campo "Recebido por" (com assinatura na tela).
- Envia para o cliente pelo WhatsApp (mensagem com resumo e PIX copia e cola), pelo compartilhar do celular (PDF ou imagem) ou por e-mail (Gmail, Outlook/Hotmail ou app padrão).
- Guarda o histórico (a "via amarela"): busca, filtros (em andamento, atrasados, a receber, entregues), situação do pedido e pagamentos.
- Funciona sem internet depois de aberto uma vez. Backup em arquivo e exportação para planilha (.csv).
- Backup automático na nuvem pelo Firebase (opcional): cada pedido salvo vai para o Cloud Firestore. Com o mesmo login, outro aparelho recebe os mesmos pedidos.

## Arquivos

| Arquivo | Para que serve |
|---|---|
| `index.html` | O app inteiro (telas, PDF, QR Code). Abre sozinho no navegador. |
| `manifest.webmanifest`, `icon-*.png` | Nome e ícone quando instalado no celular/computador. |
| `sw.js` | Faz o app abrir sem internet. |
| `firestore.rules` | Regras de segurança para colar no Firestore (backup na nuvem). |

## Como publicar (grátis)

O app precisa estar num endereço **https** para o compartilhar do celular e a instalação funcionarem. Qualquer hospedagem de site estático serve. Pelo GitHub Pages:

1. Entre no GitHub e crie um repositório novo (por exemplo `pedidos-cefas`). Em conta gratuita, o repositório precisa ser público: só o código fica público, os pedidos nunca vão para lá.
2. Clique em **Add file > Upload files**, arraste todos os arquivos desta pasta e confirme em **Commit changes**.
3. Vá em **Settings > Pages**, em *Build and deployment* escolha **Deploy from a branch**, branch **main**, pasta **/ (root)**, e salve.
4. Em um ou dois minutos o endereço aparece na mesma tela, no formato `https://SEU-USUARIO.github.io/pedidos-cefas/`.

Para atualizar o app depois, é só subir o `index.html` novo. Quem já usa recebe a versão nova na próxima vez que abrir com internet.

## Como instalar no aparelho

- **Android (Chrome):** abra o endereço, toque no menu ⋮ e em "Instalar app" (ou "Adicionar à tela inicial").
- **iPhone (Safari):** botão Compartilhar e "Adicionar à Tela de Início". No iPhone isso é obrigatório para uso diário: aberto só pelo Safari, o sistema pode apagar os dados do site depois de alguns dias sem uso.
- **Computador (Chrome ou Edge):** ícone de instalar no canto da barra de endereço.

## Primeiro uso

1. Abra **Ajustes** e confira os dados que vieram da foto do talão: endereço, telefone, e-mail e principalmente a **chave PIX e o tipo da chave** (está como celular).
2. Use o QR de teste de R$ 0,01 que aparece em Ajustes: leia no app do banco e confira se aparece o nome certo de quem recebe. Não precisa pagar.
3. Coloque em **Próximo número de pedido** o próximo número do talão de papel, para a numeração continuar.
4. Se tiver o logo em imagem, carregue em Ajustes. Sem logo, o pedido usa as iniciais.

## Onde ficam os dados

Os pedidos ficam guardados **no próprio aparelho**, dentro do navegador, e o app funciona mesmo sem internet.

- **Sem o backup na nuvem:** cada aparelho tem a sua lista, e um aparelho perdido leva os pedidos junto. Use **Ajustes > Fazer backup** com frequência e guarde o arquivo no Drive ou no e-mail.
- **Com o backup na nuvem (Firebase):** cada pedido salvo também vai para o Firestore, em segundos. Sem internet, as alterações ficam na fila e sobem quando a conexão volta. Um aparelho novo recebe tudo ao entrar com o login.

## Backup na nuvem com Firebase

Leva uns 10 minutos e é feito uma vez só, no console do Firebase, com a conta Google da empresa. O mesmo passo a passo aparece no app, em **Ajustes > Backup na nuvem**.

1. Entre em [console.firebase.google.com](https://console.firebase.google.com) e crie um projeto (por exemplo `pedidos-cefas`). O Google Analytics pode ficar desligado.
2. Abra **Firestore Database** e crie o banco no **modo de produção**, no local **southamerica-east1 (São Paulo)**.
3. Na aba **Regras** do Firestore, apague o texto que estiver lá, cole o conteúdo de `firestore.rules` (ou use o botão "Copiar regras" no app) e clique em **Publicar**.
4. Abra **Authentication**, clique em Começar e ative o login por **E-mail/senha**.
5. Ainda em Authentication, na aba **Usuários**, adicione o usuário de quem vai usar o app (e-mail e senha).
6. Copie o **UID** desse usuário. No Firestore, crie a coleção **`membros`** com um documento cujo **ID é esse UID** e um campo `nome` (texto). Só quem está em `membros` vê os pedidos: alguém que crie uma conta sozinho não acessa nada.
7. Em **Configurações do projeto > Seus apps**, adicione um app **Web** (ícone `</>`) e copie o bloco `firebaseConfig`.
8. No app publicado, em **Ajustes > Backup na nuvem**, cole o bloco, digite o e-mail e a senha do passo 5 e toque em **Entrar**. O app manda todos os pedidos que já existem.

**Outro aparelho ou outra pessoa:** em Ajustes, toque em "Link para outro aparelho" e abra o link no outro aparelho (ele leva só a configuração, não a senha). Cada pessoa pode ter o próprio usuário; crie no passo 5 e libere o UID dela no passo 6.

**Custo:** o plano gratuito do Firebase (Spark) inclui 1 GiB guardado, 50 mil leituras e 20 mil gravações por dia no Firestore. Cada pedido salvo é uma gravação, então o volume de uma fábrica fica bem dentro do grátis.

**Como o app resolve conflitos:** se o mesmo pedido for alterado em dois aparelhos, vale a alteração mais recente. Se dois aparelhos sem internet derem o mesmo número a pedidos diferentes, o que subir por último ganha o próximo número livre, e o app avisa. Pedido excluído fica marcado como excluído na nuvem, nunca é apagado de lá.

**Segurança extra (opcional):** no Google Cloud Console, em APIs e serviços > Credenciais, restrinja a chave do navegador ao endereço do GitHub Pages (`https://SEU-USUARIO.github.io/*`). A `apiKey` do Firebase não é senha (ela fica visível em qualquer app web); quem protege os dados são as regras e a coleção `membros`.

## O que este app não faz

- **Não emite nota fiscal.** O documento é um pedido/recibo e sai marcado como "Documento sem valor fiscal". Nota fiscal continua no emissor oficial.
- **Não envia sozinho.** Ele abre o WhatsApp ou o e-mail do aparelho com tudo pronto, e a pessoa toca em enviar. Envio 100% automático exige servidor e a API paga do WhatsApp Business, com aprovação da Meta.
- **Não guarda nada na nuvem sem o Firebase configurado.** Sem o passo a passo acima, os pedidos ficam só no aparelho.
