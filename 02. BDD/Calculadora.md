# 🧪 Projeto Calculadora com Cucumber em Java

Este projeto demonstra como utilizar **Java**, **JUnit 5** e **Cucumber** para automatizar **testes de aceitação (BDD)**, aplicando cenários baseados em comportamento para operações matemáticas de uma calculadora.

---

## ✅ Pré-requisitos

Certifique-se de ter instalado:

| Ferramenta | Versão recomendada |
|-----------|-------------------|
| Java | **21** |
| Apache NetBeans | **18 ou superior** |
| Maven | **3.9+** |
| Cucumber for Java | (Incluído via dependências Maven) |

Verifique a versão do Java:
```bash
java -version
```

## 📁 Estrutura do Projeto
```css
projeto-cucumber
├── pom.xml
└── src
    ├── main
    │   └── java
    │       └── calculadora
    │           └── Calculadora.java
    └── test
        ├── java
        │   └── steps
        │       ├── CalculadoraSteps.java
        │       └── RunnerTest.java
        └── resources
            └── features
                └── calculadora.feature
```                


## 📝 Feature (calculadora.feature)

```gherkin
#language: pt

Funcionalidade: os usuários veem os resultados de acordo com a operação desejada

  Esquema do Cenário: mostrar o resultado quando executar uma operação na calculadora
    Dado que "Maria" esteja na calculadora
    Quando pressionar o <numero1>
    E pressionar o <numero2>
    E escolher a "<operacao>"
    Então o sistema devera mostrar o <resultado>

    Exemplos:
      | numero1 | numero2 | operacao      | resultado |
      | 4       | 2       | soma          | 6         |
      | 4       | 2       | subtração     | 2         |
      | 4       | 2       | multiplicação | 8         |
      | 4       | 2       | divisão       | 2         |
```
## 🔧 Implementação da Classe Calculadora.java
```java
package calculadora;

public class Calculadora {
    public int calcular(int numero1, int numero2, String operacao) {
        return switch (operacao) {
            case "soma" -> numero1 + numero2;
            case "subtração" -> numero1 - numero2;
            case "multiplicação" -> numero1 * numero2;
            case "divisão" -> numero1 / numero2;
            default -> throw new IllegalArgumentException("Operação inválida: " + operacao);
        };
    }
}
```

## 🧩 Steps Definitions (CalculadoraSteps.java)

```java
package steps;

import calculadora.Calculadora;
import io.cucumber.java.pt.*;
import static org.junit.jupiter.api.Assertions.assertEquals;

public class CalculadoraSteps {

    private int numero1;
    private int numero2;
    private String operacao;
    private int resultado;
    private Calculadora calculadora;

    @Dado("que {string} esteja na calculadora")
    public void que_esteja_na_calculadora(String usuario) {
        calculadora = new Calculadora();
    }

    @Quando("pressionar o {int}")
    public void pressionar_o_numero1(int num) {
        if (numero1 == 0) {
            numero1 = num;
        } else {
            numero2 = num;
        }
    }

    @E("escolher a {string}")
    public void escolher_a_operacao(String op) {
        this.operacao = op;
    }

    @Então("o sistema devera mostrar o {int}")
    public void o_sistema_devera_mostrar_o_resultado(int esperado) {
        resultado = calculadora.calcular(numero1, numero2, operacao);
        assertEquals(esperado, resultado);
    }
}
```

## 🧭 Runner (JUnit 5) — RunnerTest.java

```java
package steps;

import org.junit.platform.suite.api.ConfigurationParameter;
import org.junit.platform.suite.api.SelectClasspathResource;
import org.junit.platform.suite.api.Suite;

import static io.cucumber.junit.platform.engine.Constants.PLUGIN_PROPERTY_NAME;

@Suite
@SelectClasspathResource("features")
@ConfigurationParameter(key = PLUGIN_PROPERTY_NAME, value = "pretty")
public class RunnerTest {
}
```
## ⚙️ Dependências (pom.xml atualizado)

```xml
<dependencies>
    <dependency>
        <groupId>io.cucumber</groupId>
        <artifactId>cucumber-java</artifactId>
        <version>7.20.1</version>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>io.cucumber</groupId>
        <artifactId>cucumber-junit-platform-engine</artifactId>
        <version>7.20.1</version>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.11.3</version>
        <scope>test</scope>
    </dependency>

    <dependency>
    <groupId>org.junit.platform</groupId>
    <artifactId>junit-platform-suite</artifactId>
    <version>1.11.3</version>
    <scope>test</scope>
</dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>3.2.5</version>
        </plugin>
    </plugins>
</build>
```
## ▶️ Como executar os testes

No terminal, dentro do projeto:
```bash
mvn test
```
Ou via NetBeans:

Clique com o botão direito no projeto → Test