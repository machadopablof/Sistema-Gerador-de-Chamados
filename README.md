# Sistema Gerador de Chamados

Ferramenta web para geração de chamados de suporte (Fiocruz), com dois modos:

- **Reset de Senha**
- **Criação de Usuário**

## Funcionalidades

- Extração automática de e-mail institucional, e-mail pessoal e nome a partir do texto do chamado colado.
- Geração de senha aleatória.
- Prévia do texto do chamado pronta para copiar.
- Histórico de chamados gerados, salvo localmente no navegador (`localStorage`).
- Tema claro/escuro, com preferência salva no navegador.
- Envio automático de cada chamado copiado para um único arquivo `.txt` de histórico numa pasta do Google Drive, com sincronização desse histórico entre dispositivos.

## Como usar

Basta abrir o arquivo `index.html` em um navegador — não há dependências externas nem processo de build.

> **Importante:** a integração com o Google Drive (abaixo) só funciona quando o arquivo é servido via `http://` ou `https://` (ex.: GitHub Pages, ou `python3 -m http.server` localmente). Abrir o arquivo direto do disco (`file://`) não funciona, pois o Google não permite login OAuth nessa origem.

## Configurando o envio automático para o Google Drive

O app usa OAuth do Google (client-side, via [Google Identity Services](https://developers.google.com/identity/oauth2/web/guides/overview)) para acrescentar cada chamado copiado a um único arquivo de histórico (`historico_chamados.txt`) na pasta do Drive configurada. Para ativar:

1. Acesse o [Google Cloud Console](https://console.cloud.google.com/) e crie um projeto (ou use um existente).
2. Em **APIs e serviços → Biblioteca**, habilite a **Google Drive API**.
3. Em **APIs e serviços → Tela de consentimento OAuth**, configure um app do tipo "Externo" (ou "Interno" se for Workspace), preenchendo nome do app e e-mail de suporte. Se o app ficar em modo de teste, adicione o e-mail da sua conta Google como usuário de teste.
4. Em **APIs e serviços → Credenciais → Criar credenciais → ID do cliente OAuth**, escolha o tipo **Aplicativo da Web** e cadastre em **Origens JavaScript autorizadas** o(s) endereço(s) onde o site vai rodar (ex.: `https://seuusuario.github.io` ou `http://localhost:8000`).
5. Copie o **Client ID** gerado (algo como `123456-abc.apps.googleusercontent.com`) e cole no lugar de `SEU_CLIENT_ID_AQUI.apps.googleusercontent.com` na constante `GOOGLE_CLIENT_ID`, no início do `<script>` em `index.html`.
6. Abra o site, clique em **"Conectar Google Drive"** na barra lateral e autorize sua conta Google.

A partir daí, todo chamado copiado é acrescentado automaticamente ao arquivo `historico_chamados.txt` dentro da pasta configurada em `GOOGLE_DRIVE_FOLDER_ID` (já configurada para [esta pasta](https://drive.google.com/drive/folders/1WrUxMv-tASmF0etoj8HLoIO4nKvTr4OD)). Chamados gerados antes de conectar, ou que falharem no envio, mostram um aviso e podem ser reenviados manualmente pelo botão dentro do modal "Detalhes do Log". Ao conectar (ou clicar em "Sincronizar histórico"), o app também lê esse mesmo arquivo e traz para a barra lateral os chamados salvos a partir de outros dispositivos.

A permissão solicitada (`drive.file`) só dá ao app acesso aos arquivos que ele mesmo cria — não ao restante do seu Drive.
