# eCommerce Accessible Platform for You - Capi-vara Cart

Capi ou carinhosamente chamado de Capivara Cart é um e-commerce open source construido em svelte e python para ser usadocomo principal solução self-host.

Esse projeto ainda está em pré-alpha mas, pode ser usado.

## Gateway de pagamentos compactiveis
- Mercado pago
- Stripe
- Cielo (Wip)

## Feature
- Cupons
- Afiliados (Pode afiliar pessoas a produtos ou ao e-commerce)
- Co-produtor (Pode criar participação a um co-produtor)
- Pagamento em Cartão
- Pagamento em Pix
- Controle de estoque
- Controle de logística
- Calculo de frete via Correios


## Estrutura
- Front shopping
- Backend
- Admin

## Como iniciar localmente?

### Docker-compose
- WIP

## Como iniciar em produção?

### Docker-compose
- WIP

### Dokku

#### Requisitos
- Um bucket compáctivel com S2 recomendando o [Wasabi](https://wasabi.com/)
- Servidor com pelo menos 2Gb de Ram (vai depender da sua carga)
- Gateway de Pagamento no momento é compáctivel com Mercado Pago, Cielo e Stripe.
- Gateway de entregas no momento é compactivel com os Correios
- Servidor SMTP hoje é compactive com [Sendgrid](https://sendgrid.com/en-us)
- [Sentry](https://sentry.io) não é mandatório mas, é importando pra analisar erros.
#### Instalação

A instalação do Dokku pode ser feita através da documentação abaixo:
[Documentação do Dokku](https://dokku.com/docs/getting-started/installation/#1-install-dokku)

É necessário instalar o plugin do [rabbitmq](https://github.com/dokku/dokku-rabbitmq), [postgres](https://dokku.com/docs/deployment/application-deployment/) e [redis](https://github.com/dokku/dokku-redis) para isso é possível usar o comando abaixo:

```bash
# on 0.19.x+
sudo dokku plugin:install https://github.com/dokku/dokku-rabbitmq.git --name rabbitmq
sudo dokku plugin:install https://github.com/dokku/dokku-postgres.git
sudo dokku plugin:install https://github.com/dokku/dokku-redis.git --name redis
```

Recomendamos usar um servidor com pelo menos 2Gb de Ram, mas dependendo da quantidade de apps e a carga isso pode variar.

##### Criando os projetos no servidor

É necessário criar 3 projetos o frontend, admin e API que serão usados para a instalação.
```bash
dokku apps:create api-example
dokku apps:create front-example
dokku apps:create admin-example
```

Próximo passo é criar um banco de dados do postgres, redis e uma fila no rabbitmq e linka-lo a nossa API
```bash
# postgres
dokku postgres:create db-example
dokku postgres:link db-example api-example

# redis
dokku redis:create redis-example
dokku redis:link redis-example api-example

# rabbitmq
dokku rabbitmq:create rabbit-example
dokku rabbitmq:link rabbit-example api-example
```

##### deployment
Ir para o projeto local e adicionar o `IP` do Servidor e o `APP` como um remote do git. É importante que você adicione uma chave `SSH` ao `ssh-agent` do servidor.

```bash
ssh-add -k ~/<your private key>
```

Agora criamos o remote para cada projeto:

```bash
git remote add dokku dokku@100.100.100.100:admin-example
git remote add dokku dokku@100.100.100.100:front-example
git remote add dokku dokku@100.100.100.100:api-example
```

Agora precisamos colocar as variáveis de ambiente nos projetos.

```bash
#backend
dokku config:set api-example DYNACONF_ACCESS_TOKEN_EXPIRE_MINUTES=90 DYNACONF_ADMIN_URL=https://demo.admin.example.com DYNACONF_API_MAIL_URL=https://testapi.com/ DYNACONF_AWS_ACCESS_KEY_ID="xxxxxxx" DYNACONF_AWS_SECRET_ACCESS_KEY="xxxxxx" DYNACONF_BROKER_URL=amqp://example:xxxxxx@dokku-rabbitmq-staging:5672/example DYNACONF_BUCKET_NAME=cdn.example.com DYNACONF_COMPANY=Capivara DYNACONF_CORREIOSBR_API_SECRET="xxxxxxx" DYNACONF_CORREIOSBR_CEP_ORIGIN=1000000 DYNACONF_CORREIOSBR_PASS=usercorreios DYNACONF_CORREIOSBR_POSTAL_CART=000011111 DYNACONF_CORREIOSBR_USER="111111gr." DYNACONF_DATABASE_URI=postgresql+psycopg://postgres:xxxxx@dokku-postgres-example:5432/example DYNACONF_DATABASE_URL=postgresql+psycopg://postgres:xxxxx@dokku-postgres-example:5432/example DYNACONF_EMAIL_FROM=contact@jonatasoliveira.dev DYNACONF_ENDPOINT_UPLOAD_CLIENT=https://s3.us-east-2.wasabisys.com/ DYNACONF_ENDPOINT_UPLOAD_REGION=us-east-2 DYNACONF_ENVIRONMENT=production DYNACONF_FILE_UPLOAD_CLIENT=WASABI DYNACONF_FILE_UPLOAD_PATH=https://cdn.example.com/ DYNACONF_FRONTEND_URL=https://demo.capicart.com DYNACONF_FRONTEND_URLS=https://demo.capicart.com DYNACONF_GATEWAY_API=API_KEY DYNACONF_GATEWAY_CRYP=CRYP_KEY DYNACONF_MERCADO_PAGO_ACCESS_TOKEN=PROD-xxx-xxxx-xxx-xxxx DYNACONF_MERCADO_PAGO_PUBLIC_KEY=PROD-xxxxx-xxx-xx-xx-xxxx DYNACONF_MERCADO_PAGO_URL=https://api.mercadopago.com DYNACONF_PAYMENT_GATEWAY_URL=URL_GATEWAY DYNACONF_REDIS_DB=0 DYNACONF_REDIS_URL=redis://:xxxxx@dokku-redis-example:6379 DYNACONF_RESULT_BACKEND=rpc:// DYNACONF_SENDGRID_API_KEY="SG.xxx.xxxw-xxxxx" DYNACONF_SETRY_DSN=https://xxxx@o11111eee.ingest.sentry.io/ss12333 DYNACONF_STRIPE_API_KEY="pk_xxxxxxxl" DYNACONF_STRIPE_SECRET_KEY="sk_xxxxxxxxxx"

#frontend
dokku config:set front-example VITE_MERCADO_PAGO_PUBLIC_KEY="PROD-xxxxx-xxx-xxx-xxxx-xxxx" VITE_MERCADO_PAGO_ACCESS_TOKEN="PROD-xxxxx-xxxx-xxxx-xxxxxx" VITE_SERVER_BASE_URL=https://demo.api.capicart.com/docs WHATSAPP_NUMBER="+5511123456789" URL_LOGO="https://site.com/logo.svg" RECAPTCHA_KEY="xxxx" RECAPTCHA_SECRET_KEY="xxxxx" SENTRY_DSN="xxx" SENTRY_ENV="production" ALT_LOGO="logo" GTAG_ID="xxxxx"

#admin
dokku config:set admin-example SERVER_BASE_URL=https://api.api.com
dokku docker-options:add admin-gattorosa build '--build-arg PUBLIC_SERVER_BASE_URL'
```

#### O que faz cada variável?
[WIP]


#### Configuração pré deploy
Por hora a API não tem seed automático por conta disso vamos ter que adicionar manualmente as roles e configuração de parcelamento no banco de dados.


```sql
INSERT INTO public."role"
(role_id, "role", active)
VALUES(1, 'ADMIN', true);
INSERT INTO public."role"
(role_id, "role", active)
VALUES(2, 'USER', true);
INSERT INTO public."role"
(role_id, "role", active)
VALUES(3, 'AFFILIATE', true);

INSERT INTO public.credit_card_fee_config
(credit_card_fee_config_id, min_installment_with_fee, max_installments, fee, active_date)
VALUES(1, 4, 12, 0.12, '2023-10-17 11:14:22.822');
```

Para fazer o deploy em cada projeto usamos:

```bash
git push dokku main
```

Agora é necessário definir a url e ativar o SSL:


```bash
dokku domains:set api-example api.example.com
dokku domains:set admin-example admin.example.com
dokku domains:set front-example example.com
```

Agora par ativar o SSL após configurar o DNS é só usar os commandos abaixo:

```bash
# Instalar o plugin
sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.github

# Configura o -email para o dokku-letsencrypt
dokku letsencrypt:set --global email your-email@your.domain.com

# Habilitar nos sites
dokku letsencrypt:enable api-example
dokku letsencrypt:enable front-example
dokku letsencrypt:enable admin-example
```

Agora o projeto já pode ser executado.


Obs: Ainda é necessário depois de subir os projetos criar um usuário pelo front e ir manualmente e transforma-lo em admin.
