# 📘 Calculadora com Testes Unitários (JUnit 5)

> **Objetivo:** implementar uma **Calculadora** e cobrir suas regras com **testes unitários**.
---

## 🎯 Objetivos de aprendizagem
- Aplicar o padrão **AAA (Arrange–Act–Assert)** em testes.
- Usar as principais **anotações** do JUnit 5 (`@Test`, `@BeforeEach`, `@DisplayName`, `@Nested`, `@ParameterizedTest`).
- Escrever **asserções** claras (`assertEquals`, `assertThrows`, `assertAll`, etc.).
- Tratar **exceções**.
- Organizar projeto **Maven** com estrutura padrão.

---

## ✅ Requisitos funcionais
Implemente a classe `Calculadora` com os métodos:
1. `int somar(int a, int b)`  
2. `int subtrair(int a, int b)`  
3. `int multiplicar(int a, int b)`  
4. `int dividir(int a, int b)` → **lançar** `IllegalArgumentException` se `b == 0`.  
5. `int potencia(int base, int exp)` → regras:  
   - `exp < 0` → **lançar** `IllegalArgumentException("Expoente deve ser >= 0")`  
   - `base == 0 && exp == 0` → **lançar** `IllegalArgumentException("Indeterminação 0^0")`  
6. `int media(java.util.List<Integer> valores)` → regras:  
   - Lista **não** pode ser nula nem vazia (lançar `IllegalArgumentException`)  
   - Média inteira (divisão inteira → **arredonda para baixo**)

---

## 🧱 Estrutura de pastas (Maven)
```
calculadora/
│── pom.xml
├── src/
│   ├── main/java/calc/Calculadora.java
│   └── test/java/calc/CalculadoraTest.java
```

---

## ⚙️ Configuração do Maven (JUnit 5)
Inclua no `pom.xml`:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>calc</groupId>
  <artifactId>calculadora</artifactId>
  <version>1.0.0</version>
  <properties>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
    <junit.jupiter.version>5.10.2</junit.jupiter.version>
  </properties>
  <dependencies>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <version>${junit.jupiter.version}</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>3.2.5</version>
        <configuration>
          <useModulePath>false</useModulePath>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

---

## 🧩 Passo a passo (roteiro de implementação)

### Passo 1 — Criar o projeto Maven
- Crie a estrutura de diretórios acima.
- Adicione o `pom.xml` com JUnit 5.

### Passo 2 — Esqueleto da classe de produção
Crie `src/main/java/calc/Calculadora.java` com a **assinatura** dos métodos:

```java
package calc;

import java.util.List;

public class Calculadora {

    public int somar(int a, int b) { return a + b; }
    public int subtrair(int a, int b) { return a - b; }
    public int multiplicar(int a, int b) { return a * b; }

    public int dividir(int a, int b) {
        if (b == 0) throw new IllegalArgumentException("Divisão por zero");
        return a / b;
    }

    public int potencia(int base, int exp) {
        if (exp < 0) throw new IllegalArgumentException("Expoente deve ser >= 0");
        if (base == 0 && exp == 0) throw new IllegalArgumentException("Indeterminação 0^0");
        int resultado = 1;
        for (int i = 0; i < exp; i++) resultado *= base;
        return resultado;
    }

    public int media(List<Integer> valores) {
        if (valores == null || valores.isEmpty())
            throw new IllegalArgumentException("A lista não pode ser vazia");
        int soma = 0;
        for (int v : valores) soma += v;
        return soma / valores.size(); 
    }
}
```

### Passo 3 — Primeiro teste (AAA)
Crie `src/test/java/calc/CalculadoraTest.java`:

```java
package calc;

import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class CalculadoraTest {

    @Test
    void somar_deveRetornar5_para2e3() {
        // Arrange
        Calculadora calc = new Calculadora();
        // Act
        int resultado = calc.somar(2, 3);
        // Assert
        assertEquals(5, resultado);
    }
}
```

Rode os testes:
```bash
mvn test
```

### Passo 4 — Cobrir operações básicas
Adicione testes para `subtrair` e `multiplicar`:

```java
@Test
void subtrair_deveRetornar2_para5e3() {
    Calculadora calc = new Calculadora();
    assertEquals(2, calc.subtrair(5, 3));
}

@Test
void multiplicar_deveRetornar15_para3e5() {
    Calculadora calc = new Calculadora();
    assertEquals(15, calc.multiplicar(3, 5));
}
```

### Passo 5 — Tratar erro (divisão por zero)
```java
@Test
void dividir_por_zero_deveLancarExcecao() {
    Calculadora calc = new Calculadora();
    IllegalArgumentException ex = assertThrows(
        IllegalArgumentException.class,
        () -> calc.dividir(10, 0)
    );
    assertTrue(ex.getMessage().contains("Divisão por zero"));
}
```

### Passo 6 — Fixtures com `@BeforeEach` e nomes com `@DisplayName`
```java
import org.junit.jupiter.api.*;

@DisplayName("Testes da Calculadora")
class CalculadoraTest {

    private Calculadora calc;

    @BeforeEach
    void setup() {
        calc = new Calculadora();
    }

    @Test @DisplayName("Somar: 2 + 3 = 5")
    void somar_basico() {
        assertEquals(5, calc.somar(2, 3));
    }
}
```

### Passo 7 — Cobrir `potencia` e `media`
```java
@Test
@DisplayName("Potência — casos básicos e erro")
void potencia_casos() {
    assertAll(
        () -> assertEquals(1,  calc.potencia(5, 0)),
        () -> assertEquals(25, calc.potencia(5, 2)),
        () -> assertEquals(0,  calc.potencia(0, 5))
    );
    assertAll(
        () -> assertThrows(IllegalArgumentException.class, () -> calc.potencia(2, -1)),
        () -> assertThrows(IllegalArgumentException.class, () -> calc.potencia(0, 0))
    );
}

@Test
@DisplayName("Média — lista válida e arredondamento para baixo")
void media_casos() {
    assertAll(
        () -> assertEquals(5,  calc.media(java.util.List.of(5))),
        () -> assertEquals(3,  calc.media(java.util.List.of(2, 3, 4))),
        () -> assertEquals(-2, calc.media(java.util.List.of(-1, -2, -3))),
        () -> assertEquals(3,  calc.media(java.util.List.of(2, 3, 5))) // 10/3 -> 3
    );
    assertAll(
        () -> assertThrows(IllegalArgumentException.class, () -> calc.media(java.util.List.of())),
        () -> assertThrows(IllegalArgumentException.class, () -> calc.media(null))
    );
}
```

### Passo 8 — Organização com `@Nested` e `assertAll`
```java
@Nested
@DisplayName("Operações de multiplicação")
class Multiplicacao {
    @Test
    void varios_casos() {
        assertAll(
            () -> assertEquals(0,  calc.multiplicar(0, 10)),
            () -> assertEquals(20, calc.multiplicar(4, 5)),
            () -> assertEquals(-6, calc.multiplicar(-2, 3))
        );
    }
}
```

### Passo 9 — (Opcional) Teste parametrizado
```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

@ParameterizedTest(name = "{0} + {1} = {2}")
@CsvSource({ "1,1,2", "2,3,5", "-1,1,0" })
void somar_parametrizado(int a, int b, int esperado) {
    assertEquals(esperado, calc.somar(a, b));
}
```

---