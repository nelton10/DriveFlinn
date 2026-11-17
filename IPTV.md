# IPTV no DriveFlin — primeira integração M3U

Esta branch adiciona uma **área IPTV separada**, sem modificar as bibliotecas do Google Drive.

## Uso

1. Implante a branch na sua Cloudflare **de teste** (mantenha o ambiente principal sem alterações até validar).
2. Faça login como administrador e abra `https://SEU-WORKER/iptv`.
3. Informe um nome e cole sua lista M3U ou informe uma URL HTTP(S) de lista M3U.
4. Clique em **Importar lista** e depois em **Abrir canais de TV**.
5. Acesse `/api/iptv/channels` com um token Jellyfin autenticado para obter um catálogo JSON.
6. `/api/iptv/play/{id}` redireciona para o link original do canal.

## O que funciona nesta fase

- Importar até 1.500 canais por lista (até 2 MB) pelo conteúdo colado ou URL pública.
- Salvar listas e canais no Cloudflare D1; manter bibliotecas do Google Drive intactas.
- Listar canais por categoria; abrir player HTML5; apagar listas.
- Restringir administração ao login de administrador já existente; exigir autenticação nas APIs.

## Limitações e segurança

- **Não é ainda uma integração Live TV nativa ao menu Jellyfin**: o catálogo e player IPTV ficam em `/iptv`.
- **Xtream Codes, EPG/XMLTV, sincronização automática de listas, séries/VOD IPTV e integração no app de TV** ainda não foram implementados.
- O Worker **não faz transcodificação**; HLS, MPEG-TS e codecs dependem do navegador/aparelho.
- O redirecionamento de reprodução revela a URL original ao player. Se ela contiver usuário, senha ou token, isso será visível ao cliente. Não compartilhe acesso com usuários que não devem conhecer suas credenciais.
- Streams HTTP podem sofrer bloqueio de conteúdo misto em páginas HTTPS, e streams HLS podem exigir CORS ou player especializado.
- O importador rejeita muitos hosts privados e respostas redirecionadas, mas isso não substitui uma política de rede/allowlist e validação de DNS para uso com administradores não confiáveis.
- Não utilize playlists sem autorização de acesso/distribuição.
- Antes de produção, faça backup do D1, valide custos e limites do plano Cloudflare e revise a política de acesso.

## APIs

`GET /api/iptv/channels`: JSON (sem links de origem).
`GET /api/iptv/play/:id`: 302 para a URL de stream.
`GET /iptv`: painel administrativo protegido.
`POST /iptv/import`: importação (formulário).
`POST /iptv/delete`: remoção (formulário).
`GET /iptv/watch`: lista HTML.
`GET /iptv/player/:id`: player HTML5.

Nenhuma migração manual: o módulo cria as duas tabelas `IptvSources` e `IptvChannels` quando acessado. O banco D1 precisa estar configurado como `DB`.
