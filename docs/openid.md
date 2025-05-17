# Integração OpenID Connect com Keycloak

## Visão Geral

Este documento descreve como a autenticação centralizada está estruturada na arquitetura do projeto **Miniverse**, utilizando o protocolo **OpenID Connect** com o **Keycloak** como provedor de identidade (IdP).

OpenID Connect permite que aplicações façam autenticação de usuários com segurança e de forma padronizada. Com essa abordagem, os usuários têm uma única identidade para acessar múltiplos sistemas da plataforma Miniverse, facilitando a gestão e melhorando a segurança.

### Como funciona na arquitetura

A arquitetura de autenticação do Miniverse é composta por:

- **Keycloak**: servidor de identidade central que gerencia usuários, autenticação, autorização e emissão de tokens.
- **Aplicações clientes**: sistemas como WordPress, frontend React, admin dashboards, etc., que se conectam ao Keycloak via OpenID Connect.
- **Protocolo OIDC**: fornece endpoints para login, obtenção de tokens, verificação de identidade e acesso a informações do usuário (profile, email, etc.).

### Configuração do Keycloak

O Keycloak será disponibilizado via container Docker, com um **Realm principal** (ex: `miniverse`) já pré-configurado a partir de arquivos JSON de importação.

Esses arquivos conterão:
- O Realm
- Clientes já cadastrados (ex: `wordpress`)
- Roles e mapeamentos de atributos
- Usuários administrativos e permissões básicas

Isso garante que, ao iniciar o ambiente, o Keycloak estará pronto para autenticar as aplicações do ecossistema Miniverse sem necessidade de configurações manuais.

---

## Integração com o WordPress

A primeira aplicação cliente documentada é o **WordPress**, utilizando o plugin [OpenID Connect Generic Client](https://wordpress.org/plugins/daggerhart-openid-connect-generic/).

### Pré-requisitos

- Keycloak ativo e acessível no endereço `https://auth.miniverse.dev`
- Realm `miniverse` configurado
- Client `wordpress` com `Access Type: confidential`
- Redirecionamentos e web origins configurados com `https://cms.miniverse.dev`

### Configuração no WordPress

Instale e ative o plugin **OpenID Connect Generic Client**.

#### Campos a serem preenchidos no plugin:

| Campo                         | Valor (produção)                                   | Valor (desenvolvimento/local com Docker)              |
|------------------------------|----------------------------------------------------|--------------------------------------------------------|
| Login Endpoint URL           | `https://auth.miniverse.dev/realms/miniverse/protocol/openid-connect/auth` | `http://keycloak:8080/realms/miniverse/protocol/openid-connect/auth` |
| Userinfo Endpoint URL        | `https://auth.miniverse.dev/realms/miniverse/protocol/openid-connect/userinfo` | `http://keycloak:8080/realms/miniverse/protocol/openid-connect/userinfo` |
| Token Validation Endpoint URL| `https://auth.miniverse.dev/realms/miniverse/protocol/openid-connect/token` | `http://keycloak:8080/realms/miniverse/protocol/openid-connect/token` |
| Client ID                    | `wordpress`                                        | `wordpress`                                            |
| Client Secret                | (copiado do Keycloak)                              | (copiado do Keycloak)                                  |
| Scope                        | `openid profile email`                             | `openid profile email`                                 |
| Login Type                   | `Button` ou `Auto`                                 | `Button`                                               |

> **Importante**:  
> Em desenvolvimento local, como WordPress e Keycloak estão em containers separados, o WordPress **não poderá acessar `https://auth.miniverse.dev` diretamente**.  
> Use o nome do serviço Docker (`keycloak:8080`) nos campos de endpoint, e garanta que ambos os containers estejam na mesma `network`.

Se necessário, utilize a diretiva `extra_hosts` no Docker Compose para simular o DNS do ambiente de produção. Exemplo:

```yaml
extra_hosts:
  - "auth.miniverse.dev:172.20.0.10"  # IP do container keycloak
```

### Testando
1. Acesse a tela de login do WordPress.
2. Clique em Login with OpenID Connect.
3. Você será redirecionado ao Keycloak.
4. Após autenticação, será redirecionado de volta e autenticado no WordPress.


### Próximas Integrações

Outras aplicações que também utilizarão OpenID Connect e terão documentação própria:

- Frontend React SPA
- Dashboard Admin (React ou Django)
- APIs protegidas com JWT
- Serviços internos com autenticação via token

Cada integração seguirá a mesma lógica de cliente OIDC, com configurações adaptadas ao ambiente (produção vs. desenvolvimento).