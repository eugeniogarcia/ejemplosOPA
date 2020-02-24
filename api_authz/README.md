# HTTP API Authorization with OPA

This directory helps provide fine-grained, policy-based control over who
can run which RESTful APIs.

A tutorial is available at [HTTP API Authorization](http://www.openpolicyagent.org/docs/http-api-authorization)


## Contents

* A sample web application that asks OPA for authorization before executing an API call (`docker/`)
* A default policy that allows `/finance/salary/<user>` for `<user>` and for `<user>`'s manager (`docker/policy`)
    * There are two policies given. The first is `api_authz.rego`, which is the default policy. The second is
      `api_authz_token.rego`, which allows you to perform the same task, but by communicating information relevant
      to the policy via JSON Web Tokens. The tokens to use for the second policy can be found in the `tokens`
      directory. Files with the `jwt` extension are the tokens themselves, and files with the `txt` extension
      are their respective decoded tokens for reference.

## Setup

```sh
docker build . -t egsmartin/demo-restful-api:latest
```

```sh
docker-compose up
```

```sh
curl --location --request GET 'http://localhost:5000/finance/salary/charlie' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--header 'Authorization: Basic YmV0dHk6dmVyYTE1MTE=' \
--data-raw ''
```

```
Success: user betty is authorized
```

```sh
curl --location --request GET 'http://localhost:5000/finance/salary/luis' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--header 'Authorization: Basic YmV0dHk6dmVyYTE1MTE=' \
--data-raw ''
```

```
Error: user betty is not authorized to GET url /finance/salary/luis
```

```sh
docker-compose -f .\docker-compose-token.yaml up
```

```sh
curl --location --request GET 'http://localhost:5000/finance/salary/betty?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiYmV0dHkiLCJhenAiOiJiZXR0eSIsInN1Ym9yZGluYXRlcyI6WyJjaGFybGllIl0sImhyIjpmYWxzZX0.TGCS6pTzjrs3nmALSOS7yiLO9Bh9fxzDXEDiq1LIYtE' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--header 'Authorization: Basic YmV0dHk6dmVyYTE1MTE=' \
--data-raw ''
```