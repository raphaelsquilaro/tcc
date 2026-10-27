# Controle de Patrimônio

Aplicação web para cadastro e acompanhamento de bens patrimoniais, ambientes, usuários, tags RFID, leitores e movimentações. O projeto também inclui um dashboard e um assistente conversacional integrado à API do Google Gemini.

## Tecnologias

- **Java 21**
- **Spring Boot 4.1.1**
- **Spring MVC** e **Thymeleaf** para a interface web
- **Spring Security** para autenticação e proteção das rotas
- **Spring Data JPA / Hibernate** para persistência
- **MySQL** como banco de dados
- **Bean Validation** para validação de dados
- **Maven Wrapper** para build e execução
- **HTML, CSS e JavaScript** para o front-end
- **Google Gemini API** para o assistente patrimonial

## Funcionalidades identificadas

- Autenticação de usuários.
- Dashboard com informações resumidas.
- Cadastro, consulta, edição e exclusão de produtos patrimoniais.
- Gestão de ambientes, usuários, tags RFID e leitores.
- Registro e consulta de movimentações.
- Recebimento de leituras RFID.
- Assistente patrimonial com Gemini, com consultas limitadas a intenções pré-definidas.

> Os recursos disponíveis e as permissões efetivas dependem das regras implementadas no código e da configuração da aplicação.

## Organização do código

O código principal está em `src/main/java/sp/senai/org/controlepatrimonio/`.

| Diretório | Responsabilidade |
|---|---|
| `config/` | Segurança, CORS, senha, inicialização de dados e Gemini |
| `controller/web/` | Controladores das páginas web |
| `controller/api/` | Endpoints REST |
| `dto/request/` | Objetos de entrada das requisições |
| `dto/response/` | Objetos de saída das respostas |
| `model/entity/` | Entidades persistidas |
| `model/enums/` | Enumerações do domínio |
| `repository/` | Acesso a dados com Spring Data JPA |
| `service/` | Regras de negócio e integrações |
| `exception/` | Tratamento de exceções |

A interface fica em `src/main/resources/templates/`, `src/main/resources/static/css/` e `src/main/resources/static/js/`. As configurações ficam em `src/main/resources/application.properties`.

## Requisitos

- JDK 21.
- MySQL compatível com o driver incluído.
- Uma chave da API Gemini apenas se for utilizar o assistente.

## Configuração local

### 1. Preparar o banco de dados

A configuração padrão aponta para `localhost:3306`, banco `teste-patrimonionew`, com criação automática habilitada na URL JDBC. Configure um usuário MySQL com permissões adequadas.

### 2. Configurar variáveis de ambiente

Exemplo no **PowerShell**:

```powershell
$env:SPRING_DATASOURCE_URL = "jdbc:mysql://localhost:3306/teste-patrimonionew?createDatabaseIfNotExist=true"
$env:SPRING_DATASOURCE_USERNAME = "SEU_USUARIO"
$env:SPRING_DATASOURCE_PASSWORD = "SUA_SENHA"
$env:ADMIN_NOME = "Administrador"
$env:ADMIN_EMAIL = "admin@exemplo.com"
$env:ADMIN_SENHA = "DefinaUmaSenhaForte"
$env:GEMINI_API_KEY = "SUA_CHAVE_GEMINI"
```

Exemplo no **Linux/macOS**:

```bash
export SPRING_DATASOURCE_URL='jdbc:mysql://localhost:3306/teste-patrimonionew?createDatabaseIfNotExist=true'
export SPRING_DATASOURCE_USERNAME='SEU_USUARIO'
export SPRING_DATASOURCE_PASSWORD='SUA_SENHA'
export ADMIN_NOME='Administrador'
export ADMIN_EMAIL='admin@exemplo.com'
export ADMIN_SENHA='DefinaUmaSenhaForte'
export GEMINI_API_KEY='SUA_CHAVE_GEMINI'
```

Também é possível ajustar `SERVER_ADDRESS`, `SERVER_PORT`, `GEMINI_MODEL` e `GEMINI_TIMEOUT`. Confira os valores esperados em `application.properties`.

**Alerta de segurança:** o `application.properties` presente no pacote contém uma chave Gemini e credenciais padrão diretamente no arquivo. Considere esses valores comprometidos: revogue/rotacione a chave Gemini, troque as senhas e remova segredos versionados. Use variáveis de ambiente ou um gerenciador de segredos; nunca publique chaves no Git nem no navegador. Este README não reproduz os valores secretos.

### 3. Executar em desenvolvimento

Na raiz do projeto:

**Windows (PowerShell):**
```powershell
.\mvnw.cmd spring-boot:run
```

**Linux/macOS:**
```bash
chmod +x ./mvnw
./mvnw spring-boot:run
```

A porta padrão configurada é `9093`. Após iniciar, acesse `http://localhost:9093`.

### 4. Gerar o pacote

```bash
./mvnw clean package
```

No Windows, execute `mvnw.cmd clean package`. O `pom.xml` configura empacotamento WAR; confira o artefato gerado em `target/`.

## API REST

Prefixos identificados nos controladores:

| Recurso | Prefixo |
|---|---|
| Autenticação | `/api/auth` |
| Ambientes | `/api/ambientes` |
| Leitores | `/api/leitores` |
| Movimentações | `/api/movimentacoes` |
| Produtos | `/api/produtos` |
| RFID | `/api/rfid` |
| Tags | `/api/tags` |
| Usuários | `/api/usuarios` |
| Assistente | `/api/assistente` |

O endpoint do assistente documentado no projeto é `POST /api/assistente/chat`. As operações disponíveis variam por controlador. Consulte os controladores e a configuração do Spring Security para payloads, respostas, autenticação e proteção CSRF.

## Assistente patrimonial (Gemini)

O assistente requer `GEMINI_API_KEY`. A documentação interna informa limite de 1.500 caracteres por pergunta, até oito mensagens de histórico e limitação de requisições por usuário. Sem chave configurada, o serviço pode responder com indisponibilidade. Consulte `HELP.md` e as classes de configuração/serviço do Gemini para os detalhes atuais.

Evite enviar informações pessoais, credenciais ou dados sensíveis para serviços externos. Revise privacidade e custos da API antes de habilitar o recurso em produção.

## Segurança e produção

- Troque todas as credenciais padrão antes de disponibilizar a aplicação.
- Rotacione a chave Gemini encontrada no arquivo de configuração.
- Revise `SecurityConfig`, CORS, CSRF e autorização por perfil antes de expor o sistema publicamente.
- `spring.jpa.hibernate.ddl-auto=update` é conveniente em desenvolvimento; em produção, prefira migrações controladas.
- Desative logs SQL detalhados em produção se não forem necessários.
- Configure backups, monitoramento, HTTPS e gestão segura de segredos.
- Teste autenticação e operações de alteração/exclusão antes da implantação.

## Testes

```bash
./mvnw test
```

No Windows, use `mvnw.cmd test`.

## Documentação adicional

- `HELP.md`: notas de configuração e detalhes do assistente.
- `pom.xml`: dependências, versão Java e configuração Maven.
- `src/main/resources/application.properties`: configuração da aplicação; não compartilhe valores secretos.

## Status

Documentação inicial elaborada com base na estrutura e nos arquivos de configuração do pacote analisado. Confirme ambiente, credenciais, endpoints e regras de negócio antes de usar este documento como procedimento oficial de implantação.
