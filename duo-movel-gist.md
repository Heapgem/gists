<img src="https://duotel.com.br/wp-content/uploads/2023/11/LOGOTIPOS_page-0001-scaled.jpg" alt="Texto Alternativo" width="200"/>




# Duo Móvel DOC
Documentação exemplo para integração do app com API Duo Móvel

Essa documentação é somente um exemplo seguindo boas praticas e padrões de integração
para comunicação entre um app e a API, não pode ser usada de base para a versão final e
pode ser alterada ao longo do desenvolvimento.

### Primeiros passos da API
{ URL_BASE } - É a URL padrão da API, Ex: `http://master.duotel.com.br:3000/api`

Usaremos na legenda *{ URL_BASE }* para se referir ao link raiz da API nos próximos tópicos.

Atenção aos metodos usados para cada tipo de requisição bem como os status correspondentes, 200, 201, 401, 500 etc...

Assuma o padrão dos status **http** para os correspondentes, serão usados para validação da chamada pelo app caso a API esteja indisponível ou retorne erro. 

### Criação dos endpoints para comunicação.

### Endpoint de login
### 🔶 POST - { URL_BASE }/login

| PARÂMETRO    | OBRIGATORIEDADE | TIPO   |
|--------------|-----------------|--------|
| phone_number | Obrigatório     | String |
| password     | Obrigatório     | String |

Precisamos decidir se esses parametros vão ser encriptados ou não numa primeira versão,
pois o campo de senha é um dado sensivel.

### Exemplo de chamada:
```shell

  curl --location --request POST '{ URL_BASE }/login?phone_number=86998054531&password=wkedasokdja%C3%A7asda'


```
### Exemplo de resposta sucesso:

### 🟢 STATUS: 200
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZS‑I6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c",
  "session_active": "true",
  "user": {
    "name": "Marcelo Henrique",
    "phone_number": "(86) 99805-4678",
    "email": "marcelo@mail.com",
    "document": "6789876976998",
    "avatar": "https://s3.aws/profile-photo.jpg",
    "current_plan": "Duo Caju",
    "accept_terms": "true",
  },
  "error": false
}

````


### Exemplo de resposta erro:
### 🔶 STATUS: 401
```json
{
"error": true,
"msg": "Unauthorized"
}
```

### Endpoint de logon
### 🟢 GET - { URL_BASE }/logon

| HEADER         | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
| Authorization  | Obrigatorio     | TOKEN  |
| Content-Type   | Obrigatorio     | String |

| PARÂMETRO    | OBRIGATORIEDADE | TIPO   |
|--------------|-----------------|--------|
|     null     |       null      |  null  |

Usado para verificar se o usuario ainda está com a sessão ativa(logado) no sistema, mesmo depois de sair do aplicativo,
ou o token de acesso estiver expirado, nesse caso será enviado um novo token de acesso e a sessão do usuario permanecerá ativa.

### Exemplo de chamada:
```shell

  curl --location --request GET '{ URL_BASE }/logon


```

### Exemplo de resposta sucesso:

### 🟢 STATUS: 200
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZS‑I6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c",
  "session_active": "true",
  "user": {
    "name": "Marcelo Henrique",
    "phone_number": "(86) 99805-4678",
    "email": "marcelo@mail.com",
    "document": "6789876976998",
    "avatar": "https://s3.aws/profile-photo.jpg",
    "current_plan": "Duo Caju",
    "accept_terms": "true",
  },
  "error": false
}
````

### Endpoint de logout
### 🔶 POST - { URL_BASE }/logout

| HEADER         | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
| Authorization  | Obrigatorio     | TOKEN  |
| Content-Type   | Obrigatorio     | String |

| PARÂMETRO    | OBRIGATORIEDADE | TIPO   |
|--------------|-----------------|--------|
|     null     |       null      |  null  |

Usado para deslogar o usuario da conta, e setar o campo de sessão como false.

### Exemplo de chamada:
```shell

  curl --location --request POST '{ URL_BASE }/logout


```
### Exemplo de resposta sucesso:

### 🟢 STATUS: 200
```json
{
  "session_active": "false",
  "error": false
}

````

## Endpoint de cadastro
### 🔶 POST - { URL_BASE }/sign_in

| PARAMETRO      | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
| name           | Obrigatorio     | String |
| document       | Obrigatorio     | String |
| email          | Obrigatorio     | String |
| phone_number   | Obrigatorio     | String |

Precisamos decidir qual vai ser o fluxo após o usuario enviar os dados para cadastro, vamos
ficar aguardando algum retorno da API ou liberamos o usuario enquanto o processo está em
analise.

### Exemplo de chamada:
```shell

  curl --location --request POST '{ URL_BASE }/sign_in?name=Marcelo%20Henrique&document=679807698769&email=marcelo%40mail.com&phone_number=86998054531'


```
### Exemplo de resposta sucesso:
### 🟢 STATUS: 200

```json
{
"error": false,
"msg": "Processo em analise"
}
```
### Exemplo resposta erro:

Caso o usuário caia em alguma validação e por tanto não seja possível cadastrá-lo.

### 🔶 STATUS: 422

Caso o sistema apresente qualquer outro erro interno.

### 🔴 STATUS: 500
```json
{
"error": true,
"msg": "Não foi possivel realizar o cadastro"
}
```
## Endpoint de Consumo de dados
Todas as requisições em que o usuario está logado, será preciso passar o token de acesso no
HEADER da requisição, para fazer a autorização do recurso.

### Exemplo de chamada com o token de acesso:

### 🟢 GET - { URL_BASE }/duo/data_usage

| HEADER         | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
| Authorization  | Obrigatorio     | TOKEN  |
| Content-Type   | Obrigatorio     | String |

| PARAMETRO      | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
|  null |   null   |  null |
|  null  |    null  | null |


### Exemplo de chamada:
```shell
curl --location --request POST '{ URL_BASE }/data_usage' \
--header 'Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c' \
--header 'Content-Type: application/json'
```

### Exemplo de resposta:
```json
{
  {
    "current_plan": {
      "name": "Duo Caju",
      "package_size": "10GB",
      "package_used": "5.6GB",
      "expire_date": "01/09/2077",
  },
    "extra_packages": {
      "package_size": "5GB",
      "package_used": "1GB",
    }
  }
}
```
## Endpoint de faturas
### 🟢 GET - { URL_BASE }/plan_bills
| HEADER         | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
| Authorization  | Obrigatorio     | TOKEN  |
| Content-Type   | Obrigatorio     | String |

| PARAMETRO | OBRIGATORIEDADE | TIPO                              |
|-----------|-----------------|-----------------------------------|
| month     | OPCIONAL        | String                            |
| status    | OPCIONAL        | String (Pago, Pendente, Não gerado)|

### Exemplo de chamada:
```shell
curl --location '{ URL_BASE }/plan_bills?month=06&status=PG' \
--header 'Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c' \
--header 'Content-Type: application/json'
```
### Exemplo de resposta:

```json
{
  "bills": [
    {
        "month": "05",
        "status": "PG",
        "value": 59.90,
        "due_date": "21/06/2077",
        "payment_date": "21/06/2077",
        "payment_method": "credit_card",
        "payment_code": "xxxxxxx.xxxxxxxxxx.xxxxx.xxxxxxxxxxx"
    },
    {
        "month": "05",
        "status": "PE",
        "value": 59.90,
        "due_date": "21/06/2077",
        "payment_date": "",
        "payment_method": "",
        "payment_code": "xxxxxxx.xxxxxxxxxx.xxxxx.xxxxxxxxxxx"
    },
  ]
}
```


## Endpoint de boletos
### 🟢 GET - { URL_BASE }/bills/billet
| HEADER         | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
| Authorization  | Obrigatorio     | TOKEN  |
| Content-Type   | Obrigatorio     | String |

| PARAMETRO | OBRIGATORIEDADE | TIPO                              |
|-----------|-----------------|-----------------------------------|
| month     | OPCIONAL        | String                            |

### Exemplo de chamada:
```shell
curl --location '{ URL_BASE }/plan_bills?month=06&status=PG' \
--header 'Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c' \
--header 'Content-Type: application/json'
```
### Exemplo de resposta:

```json
{
    "boletos": [
        {
            "pdf": "https://data.cel.cash/landingpage41599/boleto/34792834",
            "bankLine": "3847293874234230",
            "bankNumber": 6598096,
            "barCode": "900905867590687565",
            "bankEmissor": "itau",
            "bankAgency": "0000",
            "bankAccount": "000000",
            "value": "R$ 30,00",
            "due_date": "19/01/2024",
            "status": "payedBoleto"
        }
  ]
}
```

## Endpoint de pix
### 🟢 GET - { URL_BASE }/bills/pix
| HEADER         | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
| Authorization  | Obrigatorio     | TOKEN  |
| Content-Type   | Obrigatorio     | String |

| PARAMETRO | OBRIGATORIEDADE | TIPO                              |
|-----------|-----------------|-----------------------------------|
| month     | OPCIONAL        | String                            |

### Exemplo de chamada:
```shell
curl --location '{ URL_BASE }/plan_bills?month=06&status=PG' \
--header 'Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c' \
--header 'Content-Type: application/json'
```
### Exemplo de resposta:

```json
{
    "pix": [
        {
            "reference": "987DFS80SDNFFGKJHKJHGKJH",
            "qrCode": "09eddflsdkf nsdlfksjdçf",
            "image": "https://data.cel.cash/landingpage41599/pix/vklxhjgcvkjxhcv",
            "page": "https://cel.cash/q/38521/sdfsdf",
            "value": "R$ 30,00",
            "due_date": "19/01/2024",
            "status": "payedBoleto"
        }
  ]
}
```

## Endpoint Pacotes adicionais
### 🟢 GET - { URL_BASE }/extra_packages
| HEADER         | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
| Authorization  | Obrigatório     | TOKEN  |
| Content-Type   | Obrigatório     | String |

| PARAMETRO      | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
|  null |   null   |  null |
|  null  |    null  | null |

### Exemplo de chamada:
```shell
curl --location 'https://duo-movel.com/api/v1/extra_packages' \
--header 'Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c' \
--header 'Content-Type: application/json'
```


### Exemplo de resposta:
```json
{
  "extra_packages": [
    {
      "name": "Pacote 20GB PLUS",
      "package_size": "20GB",
      "due_date": "30 dias"
    },
    {
      "name": "Pacote 20GB PLUS",
      "package_size": "20GB",
      "due_date": "30 dias"
    },
  ]
}
```

## Endpoint de troca de plano (Não existe na V0)
### 🟢 GET - { URL_BASE }/available_plans

| HEADER         | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
| Authorization  | Obrigatório     | TOKEN  |
| Content-Type   | Obrigatório     | String |

| PARAMETRO      | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
|  null |   null   |  null |
|  null  |    null  | null |

### Exemplo de chamada:
```shell
curl --location '{ URL_BASE }/available_plans' \
--header 'Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c' \
--header 'Content-Type: application/json'
```

### Exemplo de resposa:

```json
{
  "plans": [
    {
      "name": "Duo Caju",
      "package_size": "10GB",
      "package_used": "5.6GB",
      "expire_date": "01/09/2077",
      "benefits": {
        "calls": "unlimited",
        "whatsapp": true,
        "waze": true
      }
    },
    {
      "name": "Duo Plus",
      "package_size": "10GB",
      "package_used": "5.6GB",
      "expire_date": "01/09/2077",
      "benefits": {
        "calls": "unlimited",
        "whatsapp": true,
        "waze": true
      }
    }
  ]
}

```

## Endpoint de perfil
### 🟢 GET - { URL_BASE }/user_profile
| HEADER         | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
| Authorization  | Obrigatório     | TOKEN  |
| Content-Type   | Obrigatório     | String |

| PARAMETRO      | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
|  null |   null   |  null |
|  null  |    null  | null |

### Exemplo de chamada:
```shell
curl --location '{ URL_BASE }/user_profile' \
--header 'Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c' \
--header 'Content-Type: application/json
```
### Exemplo de resposta:

```json
{
  "profile": {
    "name": "Marcelo Henrique",
    "email": "marcelo@mail.com",
    "document": "7978698769",
    "birthday": "21/02/1999",
    "phone_number": "(86) 98908709",
    "avatar": "http://s3.amazonaws.com/profile.jpg",
    "address": {
      "street": "Rua das Flores",
      "number": "123",
      "neighborhood": "Centro",
      "complement": "APT 03, BLC 02",
      "city": "São Paulo",
      "state": "SP",
      "country": "Brasil",
      "zip_code": "12345-678"
    }
  }
}
```
## Endpoint Editar perfil
### 🔶 POST  - { URL_BASE }/user_profile

| HEADER         | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
| Authorization  | Obrigatório     | TOKEN  |
| Content-Type   | Obrigatório     | String |

| PARAMETRO                    | OBRIGATORIEDADE | TIPO   |
|------------------------------|-----------------|--------|
| email                        | Opcional        | String |
| address[zip_code]            | Opcional        | String |
| address[street]              | Opcional        | String |
| address[number]              | Opcional        | String |
| address[complement]          | Opcional        | String |
| address[neighborhood]        | Opcional        | String |
| address[state]               | Opcional        | String |
| address[city]                | Opcional        | String |


### Exemplo de chamada:
```shell
curl --location '{ URL_BASE }/user_profile' \
--header 'Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c' \
--header 'Content-Type: application/json' \
--data-raw '{
    "email": "marcelo@mail.com",
    "address": {
        "street": "Rua das Flores",
        "zip_code": "12345-678"
    }
}'
```

### Exemplo de resposta:

```json
{
"error": false,
"msg": "Atualizado com sucesso"
}
```

## Endpoint Editar avatar
### 🔶 POST  - { URL_BASE }/user_profile/avatar

| HEADER         | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
| Authorization  | Obrigatório     | TOKEN  |
| Content-Type   | Obrigatório     | String |

| PARAMETRO                    | OBRIGATORIEDADE | TIPO   |
|------------------------------|-----------------|--------|
| avatar                       | Required        | File   |


### Exemplo de chamada:
```shell
curl --location '{ URL_BASE }/user_profile/avatar' \
--header 'Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c' \
--header 'Content-Type: application/json' \
--form 'avatar=@"/C:/tests/app/maroto/michael-dam-mEZ3PoFGs_k-unsplash.jpg"'
```

### Exemplo de resposta:

```json
{
"error": false,
"msg": "Atualizado com sucesso"
}
```

## Endpoint termos de uso
### 🟢 GET - { URL_BASE }/user_terms

| PARAMETRO      | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
|  null |   null   |  null |
|  null |   null   |  null |

### Exemplo de chamada:

```shell
curl --location 'https://duo-movel.com/api/v1/use_terms'
```

### Exemplo de resposta:

```json
{
  "use_terms": "Aceitação dos Termos Ao utilizar o aplicativo Plano de Internet..."
}

```

## Endpoint notificações
### 🟢 GET - { URL_BASE }/notifications

| HEADER         | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
| Authorization  | Obrigatório     | TOKEN  |
| Content-Type   | Obrigatório     | String |


| PARAMETRO      | OBRIGATORIEDADE | TIPO   |
|----------------|-----------------|--------|
|  null  |   null   |  null |
|  null  |   null   |  null |

### Exemplo de chamada:

```shell
curl --location 'https://duo-movel.com/api/notifications'
```

### Exemplo de resposta:
>> types
>> alert - avisos, alertas que tem ação do usuario.
>> message - mensagens do sistema ou do suporte que não tem ação do usuario.
>> info - informações que não tem ação do usuario.

```json
{ 
  "notifications":
    [
      {
        "title": "Consumo",
        "subtitle":
            "Você consumiu 80% do seu pacote, deseja adicionar mais internet?",
        "icon": "Icons.warning",
        "type": "alert",
        "action": "change_package",
        "date": "2024-07-13 22:00:39",
        "read": false
      },
      {
        "title": "Fatura vencida",
        "subtitle": "Sua vatura venceu, pague agora e fique livre de juros",
        "icon": "Icons.warning",
        "type": "alert",
        "action": "pay",
        "date": "2024-07-12 13:21:39",
        "read": true
      },
      {
        "title": "Fatura em atraso",
        "subtitle": "Sua fatura no valor de R$59,90 está em atraso.",
        "icon": "Icons.warning",
        "type": "alert",
        "action": "pay",
        "date": "2024-07-13 20:00:02",
        "read": true
      },
      {
        "title": "Fatura paga",
        "subtitle":
            "Identificamos o pagamento da sua fatura no valor de R$59,90.",
        "icon": "Icons.warning",
        "type": "message",
        "action": "",
        "date": "2024-07-09 09:00:32",
        "read": false
      },
      {
        "title": "Fatura disponivel",
        "subtitle": "Sua nova fatura de R$59,90 já está disponivel.",
        "icon": "Icons.warning",
        "type": "alert",
        "action": "pay",
        "date": "2024-07-09 12:32:12",
        "read": false
      },
      {
        "title": "Mensagem",
        "subtitle": "Você recebeu uma nova mensagem do suporte.",
        "icon": "Icons.warning",
        "type": "message",
        "action": "",
        "date": "2024-07-08 21:40:12",
        "read": false
      },
      {
        "title": "Bloqueio",
        "subtitle":
            "Seus serviço será bloqueado em 13/07/2024 devio ao atraso no pagamento.",
        "icon": "Icons.warning",
        "type": "alert",
        "action": "pay",
        "date": "2024-07-07 08:21:45",
        "read": true
      }
    ]
}

```

#### A documentação tem como propósito final auxiliar na construção da API. É importante que a equipe de desenvolvimento responsável pela API documente detalhadamente o desenvolvimento dos endpoints, incluindo os testes e a utilização da API construída. 

## Fluxo de construção App Duo - Version One

<img src="https://duotel.com.br/wp-content/uploads/2024/06/Fluxo-App-One.png"/>

<img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRDUSX8uoRSEL-ok9Y_DNoQYQvQOtfSh2Uq5g&s" alt="Texto Alternativo" width="200"/>

Desenvolvido pela Heapgem
