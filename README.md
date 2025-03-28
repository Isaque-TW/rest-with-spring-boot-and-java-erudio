### Nesta sessão, vamos mergulhar em uma série de tópicos que irão expandir nosso conhecimentos sobre o desenvolvimento de aplicações com o Spring Boot.

## 📌 Tópicos abordados

### 🔹 Notação `@DataJpaTest`
A notação `@DataJpaTest` é muito útil para testar o comportamento de repositórios JPA. Ela configura um ambiente mínimo necessário para testar a camada de persistência, desativando componentes desnecessários, como controllers e serviços.

### 🔹 Uso do Banco de Dados em Memória (H2)
Aprenderemos como reestruturar nossa aplicação para suportar um banco de dados em memória usando o **H2**. Isso permitirá executar testes mais rápidos e independentes do banco de dados real.

### 🔹 Testando operações CRUD
Exploraremos como testar as principais operações de **Create, Read, Update e Delete (CRUD)** de forma automatizada. Isso garante a consistência dos dados e a funcionalidade correta das operações.

### 🔹 Testando consultas personalizadas
Aprenderemos a testar a operação de busca de pessoas por e-mail usando o recurso de **Query Methods** do Spring Data JPA. Também veremos como definir consultas personalizadas e validar os resultados corretamente.

Cada tópico será acompanhado de **exemplos práticos** e **discussões teóricas** para facilitar a compreensão.

---

# 📌 Testando Repositórios no Spring Boot

## 📖 Introdução

Nessa aula, iremos aprender os **conceitos por trás dos testes de repositórios**.

## 🏗 Arquitetura da Aplicação

Abaixo, temos uma arquitetura típica de uma aplicação **Spring Boot MVC**, onde diferentes camadas se comunicam:
![img.png](img.png) 

- **Controller Layer** → Responsável por receber requisições HTTP e interagir com a camada de serviços.
- **Service Layer** → Contém a lógica de negócio da aplicação.
- **Repository Layer** → Gerencia a persistência dos dados, interagindo com o banco de dados.
- **Database** → Armazena as informações e é acessado pela camada de repositórios.

📌 Essa estrutura permite **separação de responsabilidades** e facilita a manutenção do código.

---

# Testando Repositórios com @DataJpaTest no Spring Boot

## Introdução
Ao testar a camada de repositório, não testamos a **Service Layer** nem a **Controller Layer**. Nosso objetivo é isolar os testes da camada **Repository** sem precisar de um banco de dados real.

Para isso, utilizamos a anotação `@DataJpaTest`, que sobe um banco de dados em memória, o **H2**, para testar nossos repositórios.

## Como funciona o @DataJpaTest?
O `@DataJpaTest` configura automaticamente um banco de dados em memória para testes, sem a necessidade de um banco real.

Diferente de outras abordagens, aqui **não utilizamos o Mockito**, pois não há necessidade de mockar a camada de repositório. O próprio `@DataJpaTest` se encarrega de configurar o ambiente adequado.

## Vantagens do @DataJpaTest
- **Focado na camada de persistência**: O Spring Boot fornece essa anotação para testar os componentes da camada de persistência ou repositório.
- **Carregamento leve e rápido**: Apenas os beans necessários para testes são carregados, como as entidades anotadas com `@Entity` e os repositórios anotados com `@Repository`. Outros beans, como `@Component`, `@Service` e `@Controller`, **não são carregados**, tornando os testes mais eficientes.
- **Execução transacional**: Os testes anotados com `@DataJpaTest` são **transacionais por padrão**, garantindo que qualquer alteração feita no banco em memória seja revertida ao final do teste. Isso evita que um teste afete o resultado de outro, mantendo o ciclo de vida esperado do JUnit.

## Estrutura do Teste
Ao utilizar `@DataJpaTest`, o Spring Boot:
1. Identifica as classes anotadas com `@Entity`.
2. Configura os repositórios do **Spring Data JPA**.
3. Cria um banco de dados H2 em memória.
4. Executa os testes de maneira isolada, garantindo a integridade do ambiente.

## Considerações Finais
Apesar de ser uma abordagem eficaz para testes unitários de repositórios, o uso de um banco de dados em memória pode ter **desvantagens** em relação a bancos reais. Esse ponto será abordado mais adiante.

Por enquanto, essa é a abordagem que utilizaremos para testar a camada de repositório no **Spring Boot** utilizando **Spring Data JPA**.
________________________________________________________________________________________________________________________

# 📌 Testando Serviços com Mockito no Spring Boot

## Introdução
Agora que aprendemos a testar nossos repositórios, vamos focar nos testes da camada de **serviços**.

Diferente dos repositórios, onde utilizamos a anotação `@DataJpaTest` para criar um banco de dados em memória e **não usamos o Mockito**, nos testes de serviços **utilizaremos o Mockito**.

## Anotações Utilizadas
Para isso, utilizamos duas anotações do Mockito que já conhecemos:

- **`@Mock`**: Cria um mock (objeto simulado) de uma classe ou interface.
- **`@InjectMocks`**: Injeta os mocks criados em uma instância da classe que está sendo testada.

Quando queremos injetar um objeto mockado em outro objeto mockado, usamos `@InjectMocks`. Essa anotação cria uma instância real da classe e injeta os mocks configurados com `@Mock`.

## Estrutura do Teste
Nos testes da camada de serviço, precisamos:
1. Criar um **mock do repositório**.
2. Injetar esse mock na **classe de serviço** utilizando `@InjectMocks`.
3. Adicionar a extensão **MockitoExtension** para gerenciar os mocks corretamente.

Dessa forma, conseguimos testar a lógica da camada de serviço sem depender de um banco de dados real.

## Diferença entre Testes de Repositório e Serviço
- **Repositório (`@DataJpaTest`)**: Utiliza um banco de dados em memória (H2) e **não usa o Mockito**.
- **Serviço (Mockito)**: Não precisa de um banco de dados, pois usamos mocks para simular dependências.

## Considerações Finais
O uso do Mockito nos permite testar a lógica de negócios sem precisar carregar todo o contexto da aplicação. Essa abordagem torna os testes mais rápidos e eficientes, garantindo que a camada de serviço funcione corretamente antes de integrá-la com outras partes do sistema.
-------------------------------------------------------------------------------------------------------------------------------------------------
_________________________________________________________________________________________________________________________________________

# 🧪 Testando Controllers no Spring Boot

## 📌 Visão Geral
Durante essas aulas, vamos começar com uma visão geral dos testes de controllers e entender a importância de testar essa camada da nossa aplicação.

Em seguida, vamos comparar duas anotações utilizadas para testes de controllers no Spring Boot.

## 🔍 Comparação: `@WebMvcTest` vs `@SpringBootTest`
O **Spring Boot** fornece a anotação `@WebMvcTest` para testar controllers **Spring MVC**.  
Além disso, os testes baseados em `@WebMvcTest` são **mais rápidos**, pois carregam apenas o **controller especificado** e suas dependências, sem carregar a aplicação inteira.

- O **Spring Boot** instancia apenas a **camada web**, em vez de todo o **Application Context**.
- Em uma aplicação com vários controllers, você pode definir a instanciação de apenas um deles usando, por exemplo:
  ```java
  @WebMvcTest(PersonController.class)

## Testando as Operações
Nos testes, colocaremos em prática o que aprendemos testando as operações mais comuns em nossos controladores.

### 🛠️ Operação de Criação (Create)
- Garantir que os dados sejam enviados corretamente.

### 🔍 Operação de Busca (Find All)
- Verificar se os resultados são retornados conforme esperado.

### 🔎 Operação de Busca por ID (Find by ID)
- Testar cenários positivos e negativos (registros existentes e inexistentes).

### ✏️ Operação de Atualização (Update)
- Testar em cenários positivos e negativos.

### 🗑️ Operação de Exclusão (Delete)
- Garantir que os recursos sejam removidos adequadamente.
- 
### **JSONPath Library**
Utilizaremos a **JSONPath Library**, que é uma **DSL para leitura de documentos JSON**, para fazer as **asserções** em nossos testes.

- JSONPath é uma ferramenta semelhante ao **XPath**, usada para XML, mas voltada para **JSON**.
- No **JSONPath**, o objeto raiz sempre é referenciado com um **`$` (cifrão)**, independentemente de ser um objeto ou um array.

### 🔎 Exemplo de Uso do JSONPath
Se tivermos o seguinte JSON:
```json
{
  "firstName": "Leandro",
  "lastName": "Dohler"
}
```
Podemos acessar as propriedades usando JSONPath:
```{
$.firstName   // Retorna "Leandro"
$.lastName    // Retorna "Dohler"
}
```
Se quisermos acessar o segundo elemento de um array, usaríamos:
````
$.items[1]   // Retorna o segundo item do array "items"
````

## 🔧 Ferramentas Utilizadas

### `@WebMvcTest`
A anotação `@WebMvcTest` permite testar **controllers Spring MVC** sem carregar toda a aplicação.  
Ela nos ajuda a focar exclusivamente na camada de **controller** e suas interações.

### **Mockito**
Usaremos o **Mockito** para criar objetos simulados (**mocks**) e testar os controllers isoladamente.

### **JSONPath Library**
Utilizaremos a **JSONPath Library**, que é uma **DSL para leitura de documentos JSON**, para fazer as **asserções** em nossos testes.  
_______________________________________________________________________________________________________________________

## Testes de Integração com Spring Boot

Nesta seção, iremos explorar os testes de integração utilizando o Spring Boot.

Durante essa jornada, vamos aprender sobre diferentes ferramentas e técnicas para garantir a qualidade e o bom funcionamento dos nossos endpoints.

### Visão Geral

Iniciaremos com uma visão geral dos testes de integração, compreendendo sua importância no desenvolvimento de aplicações.

Como o nome sugere, os testes de integração têm foco na integração de diferentes camadas da aplicação. Isso também significa que não há uso de mocks. Basicamente, escrevemos testes de integração para validar funcionalidades que podem envolver interação com múltiplos componentes.

### Annotation `@SpringBootTest`

Vamos explorar a annotation `@SpringBootTest`, uma poderosa notação fornecida pelo Spring Boot para facilitar a criação e execução de testes de integração.

Essa anotação inicializa um servidor embarcado, cria um web environment e possibilita que os métodos anotados com `@Test` executem testes de integração.

Por padrão, `@SpringBootTest` não inicia um servidor. Para definir como os testes serão executados, precisamos adicionar o atributo `webEnvironment`. As principais opções disponíveis são:

- **MOCK (Padrão)**: Carrega um `WebServerApplicationContext` e fornece um web environment mockado.
- **RANDOM_PORT**: Carrega um `WebServerApplicationContext` e fornece um web environment real. O servidor embarcado é iniciado e exposto em uma porta aleatória. Essa opção deve ser usada para testes de integração.
- **DEFINED_PORT**: Carrega um `WebServerApplicationContext` e fornece um web environment real em uma porta predefinida.
- **NONE**: Carrega um `ApplicationContext` usando o `SpringApplication`, mas não fornece nenhum web environment.

### Documentação com Swagger / OpenAPI

Conheceremos o Swagger (OpenAPI) e veremos como utilizá-lo para documentar e testar nossos endpoints de forma eficiente e automatizada. Também abordaremos a configuração básica do Swagger em nosso projeto, permitindo que ele gere a documentação automaticamente.

Para isso, utilizaremos a dependência `SpringDoc OpenAPI Starter WebMVC UI`, que facilita a criação e exibição da documentação interativa.

### Ferramentas Essenciais para Testes de Integração

- **MVN REPOSITORY**: Site para buscar dependências do `pom.xml`, incluindo bibliotecas essenciais para os testes de integração.
- **TestContainers**: Exploraremos como o TestContainers pode nos ajudar a preparar a infraestrutura necessária para executar nossos testes, garantindo um ambiente isolado e controlado.
- **Validação do Swagger**: Verificaremos a geração da documentação do Swagger e sua integração com o TestContainers e Azure, garantindo que os endpoints estejam funcionando conforme esperado.

### Configuração de Beans no Spring

No Spring, um **Bean** é um objeto que é instanciado, montado e gerenciado pelo container do Spring. O container do Spring busca informações em XML, anotações ou código Java sobre como os beans devem ser instanciados, configurados e montados, além de como eles se relacionam com outros beans. Esse processo é conhecido como **injeção de dependências**.

Se você cria uma classe que depende de um bean, só precisa se preocupar com o que sua classe necessita, sem se preocupar com as dependências dela.

Existem diferentes formas de criar beans no Spring:
- Anotando classes com `@Component`, `@Service` ou `@Configuration` para que sejam gerenciadas pelo Spring.
- Usando a anotação `@Bean` em um método para tornar a instância retornada um objeto gerenciado pelo Spring, seja de uma classe própria ou de terceiros.

Essas classes que, do ponto de vista do Spring, são os beans, representam as regras de funcionamento da sua aplicação.

### Configuração do OpenAPI no Projeto

Para configurar a documentação da API com OpenAPI, utilizamos uma classe de configuração:

```java
import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Info;
import io.swagger.v3.oas.models.info.License;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class OpenAPIConfig {

    @Bean // BEAN É UM OBJETO QUE É INSTANCIADO, MONTADO E GERENCIADO PELO CONTAINER DO SPRING.
    public OpenAPI customOpenAPI() {
        return new OpenAPI()
            .info(new Info()
                .title("Hello Swagger OpenAPI")
                .version("v1")
                .description("Some description about your API.")
                .termsOfService("http://pub.erudio.com.br/meus-cursos")
                .license(new License()
                    .name("Apache 2.0")
                    .url("http://pub.erudio.com.br/meus-cursos")
                )
            );
    }
}
```

Essa classe usa `@Configuration` para definir que contém configurações do Spring e `@Bean` para disponibilizar a instância de `OpenAPI` como um bean gerenciado pelo Spring.

### Testes de Repositórios e Banco de Dados

Removemos o banco de dados H2 para criar testes de integração mais realistas, garantindo que a aplicação seja testada em um ambiente próximo ao de produção.

Convertendo nossos testes de repositórios em testes de integração, interagindo diretamente com o banco de dados e validando o comportamento do código em um ambiente mais próximo do mundo real.

### Testando Endpoints do `PersonController`

Prepararemos a infraestrutura de testes para os endpoints de `PersonController`, validando as seguintes operações:

- **Create**
- **Update**
- **FindById**
- **FindAll**
- **Delete**
______________________________________________________________________________________________________________________

### Resolvendo Problema com `Jakarta Bean Validation`

Após a criação da classe de testes e adição das dependências, ao iniciar a aplicação, encontramos um erro. O Jakarta Bean Validation não conseguiu encontrar um provider para o Hibernate Validator.

Para resolver esse problema, basta adicionar a dependência `Spring Boot Starter Validation` no `pom.xml`. Embora possamos utilizar diretamente o Hibernate Validator, é mais interessante usar a versão integrada ao Spring Boot, pois futuras atualizações do framework já incluirão quaisquer mudanças necessárias automaticamente.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

Após adicionar essa dependência e reiniciar a aplicação, o erro será resolvido e o Swagger estará acessível no navegador.

### Validação do Swagger

Após iniciar a aplicação com sucesso, podemos abrir o navegador e acessar `http://localhost:8080/swagger-ui/index.html` para visualizar a documentação do Swagger. As propriedades configuradas serão refletidas na interface do Swagger.

Além disso, podemos testar nossa API diretamente pelo Swagger, verificando os endpoints e as respostas retornadas.

### Criando Testes de Integração

Nos próximos passos, iremos criar um teste de integração para garantir que o Swagger foi gerado corretamente e que os endpoints estão funcionando conforme esperado.
_______________________________________________________________________________________________________________________

# 🛠️ Testes de Integração com Spring Boot

## 📌 Spring Doc Open API
O **Spring Doc Open API** é um recurso valioso para documentar e testar APIs.  
Se quiser entender melhor as propriedades, tags e detalhes da documentação, vale a pena conferir:  
🔗 [Spring Doc Open API](https://springdoc.org/)

## 📦 Test Containers
O **Test Containers** é uma biblioteca Java que permite subir instâncias do Docker durante o ciclo de testes.  
Ele fornece containers leves e descartáveis para:

- Bancos de dados
- Mensageria
- Cache
- Outros serviços que possam rodar em containers

### 🔹 Benefícios
✅ Ambientes de teste mais próximos ao de produção  
✅ Automatização da criação e destruição dos containers  
✅ Testes realistas com o mesmo banco da aplicação

### 🔍 **Por que Test Containers e não H2?**
Ao utilizar o banco de dados em memória **H2** nos testes, você pode enfrentar diferenças de sintaxe e comportamento em relação ao banco de produção. Isso ocorre porque o H2 não reflete fielmente bancos como MySQL ou PostgreSQL, podendo gerar falsos positivos nos testes.

Com o **Test Containers**, os testes utilizam um banco de dados real rodando dentro de um container. Isso garante que:  
✔️ O mesmo banco de produção seja usado nos testes  
✔️ Os scripts SQL sejam executados exatamente como no ambiente real  
✔️ O banco seja criado, inicializado com **Flyway** ou **Liquibase**, testado e destruído automaticamente

🔗 [Site Oficial do Test Containers](https://testcontainers.com/)

## 🔍 REST Assured
O **REST Assured** facilita a criação de testes automatizados para APIs REST.  
Ele suporta validações para **JSON, XML e YAML**, incluindo:

- Status codes
- Headers
- Corpo da resposta

🔗 [Site Oficial do REST Assured](https://rest-assured.io/)  
🔗 [Maven Repository - REST Assured](https://mvnrepository.com/artifact/io.rest-assured/rest-assured)

## ⚙️ Adicionando Dependências
Para utilizar essas ferramentas, adicione as dependências no `pom.xml` do projeto.

### **Test Containers**
```xml
<dependency>
  <groupId>org.testcontainers</groupId>
  <artifactId>testcontainers</artifactId>
  <version>${testcontainers.version}</version>
</dependency>

### **Test Containers**
```xml
<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>testcontainers</artifactId>
    <version>${testcontainers.version}</version>
</dependency>
```

### REST Assured
````
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>${restassured.version}</version>
</dependency>
````
______________________________________________________________________________________________________________________

## Configuração de TestContainers para Testes de Integração no Spring Boot

### 1. Configuração do `application-test.yaml`

Crie o arquivo `src/test/resources/application-test.yaml` e configure:

```yaml
server:
  port: 8888  # Porta para evitar conflito com a aplicação principal

spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/testdb
    username: test
    password: test
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: false  # Defina como true para depuração
```

### 2. Criando a Classe de Configuração `TestConfigs`

Crie a classe `TestConfigs` no pacote `config`:

```java
package config;

public class TestConfigs {
    public static final int SERVER_PORT = 8888;
    public static final String CONTENT_TYPE_JSON = "application/json";
}
```

### 3. Criando a Classe Base `AbstractIntegrationTest`

Crie a classe `AbstractIntegrationTest` no pacote `br.com.studio.integrationtest.testcontainers`:

```java
package br.com.studio.integrationtest.testcontainers;

import org.springframework.boot.test.context.TestConfiguration;
import org.springframework.context.ApplicationContextInitializer;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.core.env.ConfigurableEnvironment;
import org.springframework.core.env.MapPropertySource;
import org.testcontainers.containers.MySQLContainer;

import java.util.Map;

@TestConfiguration
public class AbstractIntegrationTest {

    static class Initializer implements ApplicationContextInitializer<ConfigurableApplicationContext> {
        private static final MySQLContainer<?> mysql = new MySQLContainer<>("mysql:8.0.28");

        static {
            mysql.start();
        }

        @Override
        public void initialize(ConfigurableApplicationContext context) {
            ConfigurableEnvironment environment = context.getEnvironment();
            Map<String, Object> properties = Map.of(
                "spring.datasource.url", mysql.getJdbcUrl(),
                "spring.datasource.username", mysql.getUsername(),
                "spring.datasource.password", mysql.getPassword()
            );
            environment.getPropertySources().addFirst(new MapPropertySource("testcontainers", properties));
        }
    }
}
```

### 4. Explicação
- O `application-test.yaml` configura um banco de dados externo para testes.
- `TestConfigs` define constantes globais para os testes.
- `AbstractIntegrationTest`:
  - Inicia um container MySQL dinamicamente com TestContainers.
  - Configura o contexto do Spring para utilizar as credenciais geradas pelo TestContainers.

Agora, qualquer teste de integração pode estender `AbstractIntegrationTest` para utilizar essa infraestrutura!


# Testes de Integração com Swagger no Spring Boot

Aqui, vamos configurar e executar um teste de integração para garantir que a interface do Swagger UI está sendo carregada corretamente dentro do nosso projeto Spring Boot. Além disso, explicaremos as dependências necessárias e como executar os testes corretamente.

## 📌 Dependências Necessárias

Antes de rodar os testes, é fundamental ter as seguintes dependências no seu `pom.xml`:

```xml
<dependencies>
    <!-- Spring Boot Test -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>

    <!-- RestAssured para testes de API -->
    <dependency>
        <groupId>io.rest-assured</groupId>
        <artifactId>rest-assured</artifactId>
        <scope>test</scope>
    </dependency>

    <!-- TestContainers para testes de integração -->
    <dependency>
        <groupId>org.testcontainers</groupId>
        <artifactId>testcontainers</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### Explicação das Dependências
- **Spring Boot Test**: Proporciona suporte para a execução de testes unitários e de integração no Spring Boot.
- **RestAssured**: Uma biblioteca que simplifica a realização de testes de API, permitindo chamadas HTTP de forma fluida.
- **TestContainers**: Permite a criação de containers para bancos de dados e outras dependências, garantindo um ambiente isolado para testes de integração.

## 🎯 Configuração do Teste de Integração

Criamos uma classe de teste para validar se a interface do Swagger está sendo corretamente carregada. A classe estende `AbstractIntegrationTest` e usa o `SpringBootTest` com um ambiente de porta definida:

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.DEFINED_PORT)
class SwaggerIntegrationTest extends AbstractIntegrationTest {

    @Test
    @DisplayName("JUnit test for Should Display Swagger UI Page")
    void testShouldDisplaySwaggerUiPage() {
        var content = given()
            .basePath("/swagger-ui/index.html")
            .port(TestConfigs.SERVER_PORT)
            .when()
                .get()
            .then()
                .statusCode(200)
            .extract()
                .body()
                    .asString();
        assertTrue(content.contains("Swagger UI"));
    }
}
```

### 🔹 Explicação do Teste
- **`@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.DEFINED_PORT)`**: Configura o teste para rodar em uma porta fixa definida no `application.yml`.
- **Estende `AbstractIntegrationTest`**: Garante que a infraestrutura do banco de dados e outras dependências estejam configuradas corretamente.
- **Uso do RestAssured**: A API `RestAssured` facilita a execução de chamadas HTTP para validar o carregamento do Swagger UI.
- **Validação do Status Code `200`**: Confirma que a página do Swagger foi carregada com sucesso.
- **Validação do Conteúdo**: O teste verifica se a resposta contém "Swagger UI", garantindo que a interface está acessível.

## 🚀 Executando os Testes

### 🔹 Rodando um único teste
Para executar apenas o teste de Swagger, utilize o seguinte comando:

```sh
mvn test -Dtest=SwaggerIntegrationTest
```

### 🔹 Rodando todos os testes do projeto
Para executar todos os testes:

```sh
mvn test
```

### 🔹 Executando os testes na IDE
Se estiver usando IntelliJ IDEA ou Eclipse:
1. Navegue até a classe `SwaggerIntegrationTest`.
2. Clique com o botão direito e selecione **Run 'SwaggerIntegrationTest'**.
3. Para executar todos os testes, vá até `src/test/java` e selecione **Run 'All Tests'**.

## 🛠️ Possíveis Erros e Soluções

### ❌ `Failed to load ApplicationContext`
Esse erro pode ocorrer se o Docker não estiver rodando. Certifique-se de iniciar o Docker antes de rodar os testes:

```sh
docker start
```

### ❌ `Failed to create DataSource`
Isso pode indicar que o banco de dados não está inicializado corretamente. Verifique a configuração do `TestContainers` e se a conexão com o banco está funcionando.

---


