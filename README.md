# miniverse boilerplate 🚀

Um universo (mínimo) de microserviços no seu Docker.
Ideal para quem quer brincar de arquiteto de sistemas sem escalar as montanhas até as nuvens.


**O que vem nesse pacotinho interestelar:**

- 🔐 Keycloak como centro de identidade intergaláctica (com OIDC já pronto pra FastAPI, WordPress e React Native)
- 🪣 MinIO pra simular um S3 e guardar arquivos perdidos no espaço
- 📬 Mailhog para capturar e-mails antes que escapem pela atmosfera
- 🧪 Exemplos de apps orbitando (FastAPI, WordPress, React Native)


**Serve para:**

- Testar integrações entre serviços em um ambiente controlado
- Prototipar rapidamente uma arquitetura de microserviços
- Fazer deploy local de sistemas complexos sem perder a sanidade


**Requisitos:**

- Docker
- Vontade de brincar de DevOps sem gastar com nuvem


✨ Bem-vindo ao miniverse, onde pequenos serviços fazem grandes coisas.


## 🔧 Tecnologias

- **Auth**: [Keycloak](https://www.keycloak.org/)
- **Storage**: [MinIO](https://min.io/) (simula S3)
- **E-mail**: [MailHog](https://github.com/mailhog/MailHog)
- **Apps de Exemplo**:
  - API: FastAPI
  - CMS: WordPress
  - Mobile/Web: React Native (com opção de versão web)


## ▶️ Como rodar

```bash
docker-compose up -d
```

**Acesse os serviços:**

| Serviço       | URL                                                         | Login Padrão                |
| ------------- | ----------------------------------------------------------- | --------------------------- |
| Keycloak      | [http://localhost:8080](http://localhost:8080)              | `admin` / `admin`           |
| MinIO Console | [http://localhost:9001](http://localhost:9001)              | `miniverse` / `supersecret` |
| MailHog       | [http://localhost:8025](http://localhost:8025)              | — (sem login)               |
| FastAPI API   | [http://localhost:8000/docs](http://localhost:8000/docs)    |                             |
| WordPress     | [http://localhost:8081](http://localhost:8081)              |                             |
| React Native  | [http://localhost:3000](http://localhost:3000) (versão web) |                             |

O login do Keycloak já vem configurado com um Realm e Clients para cada aplicação.

### 🌐 Roteamento local com Caddy (opcional)

Este boilerplate oferece uma configuração opcional com Caddy para centralizar o acesso a todos os serviços por meio de subdomínios e com suporte a HTTPS local. Essa configuração torna o ambiente mais próximo de um cenário real de produção, simplificando os testes e melhorando a organização durante o desenvolvimento.

Ao ativar o Caddy, você passa a contar com:

**🔁 Um entrypoint único com roteamento por subdomínio**

Caddy atua como um proxy reverso que escuta nas portas 80 e 443, e redireciona automaticamente as requisições para os serviços internos com base no subdomínio utilizado. Isso elimina a necessidade de lembrar diferentes portas ou IPs — você acessa tudo por domínios amigáveis como api.miniverse.dev ou cms.miniverse.dev.

**🔐 HTTPS local automático**

Caddy também gera certificados TLS automaticamente utilizando sua própria autoridade certificadora local (tls internal). Isso permite que todos os domínios do `miniverse.dev` sejam acessados via HTTPS mesmo em ambientes locais, simulando um ambiente seguro de produção.

**🛠️ Para usar o roteamento local**

Você precisará adicionar os domínios no seu arquivo de hosts local (`/etc/hosts` ou `C:\Windows\System32\drivers\etc\hosts`) apontando para `127.0.0.1`, por exemplo:

```
127.0.0.1 api.miniverse.dev
127.0.0.1 cms.miniverse.dev
127.0.0.1 auth.miniverse.dev
127.0.0.1 mail.miniverse.dev
127.0.0.1 storage.miniverse.dev
127.0.0.1 app.miniverse.dev
```

O Caddy vai gerar certificados com uma autoridade local (CA própria). Isso quer dizer que, fora do container, navegadores e outros clientes podem não confiar nesse certificado. Para resolver isso, você pode instalar o certificado raiz do Caddy no seu sistema.

Exemplo para o MacOS, para outros sistemas confira em [https://caddyserver.com/docs/running#local-https-with-docker](https://caddyserver.com/docs/running#local-https-with-docker):

```bash
docker compose cp \
    caddy:/data/caddy/pki/authorities/local/root.crt \
    /tmp/root.crt \
  && sudo security add-trusted-cert -d -r trustRoot \
    -k /Library/Keychains/System.keychain /tmp/root.crt
```

Com isso feito, ao iniciar o serviço do Caddy (junto com os demais via docker-compose), você poderá acessar cada aplicação pelo navegador usando URLs limpas, organizadas e seguras.

**🧭 Mapeamentos de domínio**

Abaixo estão os subdomínios utilizados e seus respectivos serviços:

| Subdomínio                                                        | Serviço                 | Descrição                        |
| ----------------------------------------------------------------- | ----------------------- | -------------------------------- |
| [https://api.miniverse.dev](https://api.miniverse.dev)            | FastAPI                 | API principal do miniverse       |
| [https://cms.miniverse.dev](https://cms.miniverse.dev)            | WordPress               | CMS de exemplo                   |
| [https://auth.miniverse.dev](https://auth.miniverse.dev)          | Keycloak                | Serviço de autenticação (OIDC)   |
| [https://mail.miniverse.dev](https://mail.miniverse.dev)          | MailHog                 | Visualização de e-mails enviados |
| [https://storage.miniverse.dev](https://storage.miniverse.dev)    | MinIO                   | Interface S3 de armazenamento    |
| [https://app.miniverse.dev](https://app.miniverse.dev)            | React Native Web (Expo) | Frontend mobile (versão web)     |

> 💡 Essa funcionalidade é opcional. Se preferir, você ainda pode acessar os serviços diretamente pelas portas expostas via Docker.


## 📁 Estrutura

```
.
├── docker-compose.yml
├── keycloak/
│   └── realm-export.json
├── minio/
│   └── (configurações opcionais)
├── mailhog/
├── api/
│   └── main.py (FastAPI)
├── wordpress/
├── app-mobile/
│   └── (React Native app)
├── README.md
└── docs/
```