# Bolão da Copa

Aplicação web mobile-first para criar salas de bolão, compartilhar por link ou QR Code, registrar palpites sem login e acompanhar rankings.

## Tecnologias

- PHP 8+
- MySQL 8+
- PDO com prepared statements
- HTML5, CSS3 e JavaScript puro
- QRCode.js e html2canvas carregados por CDN

## Estrutura

```text
BolaoCopa/
  index.php
  criar-sala.php
  sala.php
  ranking.php
  ranking-geral.php
  admin.php
  admin-jogos.php
  admin-resultados.php
  admin-salas.php
  admin-sala.php
  app/
  assets/
  database/
```

## Instalação

1. Use PHP 8+ com a extensão `pdo_mysql`.
2. Crie o banco importando `database/schema.sql`.
3. Opcionalmente, importe `database/seed.sql` para adicionar jogos de exemplo.
4. Ajuste os valores em `app/config.php` ou use variáveis de ambiente:

```text
BOLAO_DB_HOST
BOLAO_DB_NAME
BOLAO_DB_USER
BOLAO_DB_PASS
BOLAO_BASE_URL
BOLAO_ADMIN_PASSWORD
```

5. Aponte o servidor web para a pasta do projeto.

Exemplo com o servidor embutido do PHP:

```bash
php -S localhost:8000
```

Nesse caso, configure `BOLAO_BASE_URL=http://localhost:8000`.

## Uso

### Criar e compartilhar uma sala

Abra `criar-sala.php`, informe criador e nome da sala. A tela seguinte exibe:

- link público;
- QR Code;
- card vertical;
- botão para baixar PNG;
- compartilhamento no WhatsApp;
- link administrativo privado.

O QR Code e a imagem usam QRCode.js e html2canvas pelos CDNs do jsDelivr. Para funcionar sem internet, baixe essas bibliotecas, salve-as em `assets/js/` e substitua os scripts CDN em `criar-sala.php` e `admin-sala.php`.

### Participar

O participante abre o link público, informa apenas o nome e os placares. O token do participante fica associado à sessão e ao `localStorage` do navegador.

Nomes não podem se repetir dentro da mesma sala. Os placares aceitam inteiros de 0 a 30.

### Bloqueio dos palpites

Cada palpite fecha exatamente um minuto antes da partida. Um jogo marcado para 16:00 aceita alterações somente antes de 15:59.

O bloqueio é validado no PHP e também representado visualmente na interface. O fuso utilizado é `America/Sao_Paulo`.

### Administração geral

Abra `admin.php` e entre com a senha definida em `ADMIN_PASSWORD` ou `BOLAO_ADMIN_PASSWORD`.

O painel permite:

- cadastrar e editar jogos;
- excluir jogos sem palpites;
- informar placares e status;
- recalcular pontuações;
- ver salas, participantes e palpites;
- iniciar sincronização da API quando configurada.

Troque a senha padrão antes de publicar o sistema.

### Administração privada da sala

O link privado gerado na criação contém um token seguro produzido com `random_bytes()`. Ele permite:

- editar mensagem e prêmio;
- ver participantes e palpites;
- gerar e baixar novamente o card com QR Code;
- compartilhar a sala no WhatsApp.

## Pontuação

- placar exato: 5 pontos;
- vencedor ou empate correto: 3 pontos;
- gols corretos de uma das seleções: 1 ponto extra;
- nenhum acerto: 0 pontos.

Um palpite exato recebe 5 pontos no total, sem bônus adicional.

Os rankings ordenam por pontos, placares exatos, quantidade de palpites e nome.

## API de futebol

O modo manual funciona sem qualquer API.

As opções ficam em `app/config.php`:

```php
define('API_FOOTBALL_ENABLED', false);
define('API_FOOTBALL_KEY', '');
define('API_FOOTBALL_BASE_URL', '');
```

`app/ApiFootballService.php` centraliza a integração e possui controle de intervalo de sincronização. Como cada provedor usa campos e autenticação diferentes, implemente o request e o mapeamento nesse arquivo depois de escolher a API.

## Segurança

- consultas com PDO e prepared statements;
- escaping de HTML;
- tokens criptograficamente seguros;
- proteção CSRF nos formulários;
- validação de placares no servidor;
- validação do limite de horário no servidor;
- senha administrativa configurável;
- chaves estrangeiras, índices e restrições no MySQL.

Em produção, use HTTPS, senha forte, variáveis de ambiente e permissões de arquivo adequadas.

## Melhorias futuras

- armazenar bibliotecas JavaScript localmente;
- adaptar o serviço a um provedor real de futebol;
- adicionar paginação para instalações com muitas salas;
- registrar auditoria administrativa;
- adicionar tarefas agendadas para sincronização automática;
- transformar a aplicação em PWA.
