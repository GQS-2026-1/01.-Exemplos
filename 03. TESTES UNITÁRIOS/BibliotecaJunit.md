## Biblioteca com Testes Unitários (JUnit 5)

No contexto de uma **biblioteca**, podemos ter regras como:
- Um livro deve estar disponível para ser emprestado.
- Um usuário não pode pegar mais de 3 livros ao mesmo tempo.
- A data de devolução não pode ser anterior à data de empréstimo.

---
## Estrutura Maven 
```bash
biblioteca/
│── pom.xml
│
└── src/
    ├── main/
    │   ├── java/
    │   │   └── biblioteca/
    │   │       ├── Livro.java
    │   │       ├── Usuario.java
    │   │       └── Emprestimo.java
    │   │
    │   └── resources/ # Arquivos de configuração (se houver)
    │
    └── test/
        ├── java/
        │   └── biblioteca/
        │       ├── LivroTest.java
        │       ├── UsuarioTest.java
        │       ├── EmprestimoTest.java
        │       └── BibliotecaTestSuite.java
        │
        └── resources/ # Arquivos de configuração (se houver)
```
## Código de Produção

**Pacote:** `biblioteca`

### `Livro.java`
```java
package biblioteca;

import java.util.Objects;

public class Livro {

    private final String titulo;
    private boolean disponivel = true;

    public Livro(String titulo) {
        if (titulo == null || titulo.isBlank()) {
            throw new IllegalArgumentException("Título do livro não pode ser vazio");
        }
        this.titulo = titulo;
    }

    public String getTitulo() { return titulo; }
    public boolean isDisponivel() { return disponivel; }

    public void marcarIndisponivel() {
        if (!disponivel) throw new IllegalStateException("Livro já está emprestado");
        this.disponivel = false;
    }

    public void marcarDisponivel() { this.disponivel = true; }

    @Override public String toString() {
        return "Livro{titulo='" + titulo + "', disponivel=" + disponivel + "}";
    }

    @Override public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Livro)) return false;
        Livro livro = (Livro) o;
        return titulo.equalsIgnoreCase(livro.titulo);
    }

    @Override public int hashCode() { return Objects.hash(titulo.toLowerCase()); }
}
```

### `Usuario.java`
```java
package biblioteca;

import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;
import java.util.Objects;

public class Usuario {

    public static final int LIMITE_EMPRESTIMOS = 3;

    private final String nome;
    private final List<Emprestimo> emprestimosAtivos = new ArrayList<>();

    public Usuario(String nome) {
        if (nome == null || nome.isBlank()) {
            throw new IllegalArgumentException("Nome do usuário é obrigatório");
        }
        this.nome = nome;
    }

    public String getNome() { return nome; }
    public List<Emprestimo> getEmprestimosAtivos() { return List.copyOf(emprestimosAtivos); }

    public Emprestimo adicionarEmprestimo(Livro livro) {
        Objects.requireNonNull(livro, "Livro é obrigatório");
        if (emprestimosAtivos.size() >= LIMITE_EMPRESTIMOS) {
            throw new IllegalStateException("Usuário já possui " + LIMITE_EMPRESTIMOS + " empréstimos ativos");
        }
        Emprestimo e = new Emprestimo(this, livro);
        emprestimosAtivos.add(e);
        return e;
    }

    public void devolver(Livro livro) {
        Objects.requireNonNull(livro, "Livro é obrigatório");
        boolean devolvido = false;
        Iterator<Emprestimo> it = emprestimosAtivos.iterator();
        while (it.hasNext()) {
            Emprestimo e = it.next();
            if (e.getLivro().equals(livro) && e.isAtivo()) {
                e.devolver();
                it.remove();
                devolvido = true;
                break;
            }
        }
        if (!devolvido) {
            throw new IllegalStateException("Nenhum empréstimo ativo encontrado para este livro");
        }
    }
}
```

### `Emprestimo.java`
```java
package biblioteca;

import java.time.LocalDate;
import java.util.Objects;

public class Emprestimo {

    private final Usuario usuario;
    private final Livro livro;
    private final LocalDate dataEmprestimo;
    private LocalDate dataDevolucao;
    private boolean ativo = true;

    public Emprestimo(Usuario usuario, Livro livro) {
        this(usuario, livro, LocalDate.now());
    }

    public Emprestimo(Usuario usuario, Livro livro, LocalDate dataEmprestimo) {
        if (usuario == null || livro == null) {
            throw new IllegalArgumentException("Usuário e livro são obrigatórios");
        }
        if (!livro.isDisponivel()) {
            throw new IllegalStateException("Livro indisponível para empréstimo");
        }
        this.usuario = usuario;
        this.livro = livro;
        this.dataEmprestimo = Objects.requireNonNull(dataEmprestimo, "Data de empréstimo é obrigatória");
        livro.marcarIndisponivel();
    }

    public void devolver() {
        if (!ativo) return; 
        this.ativo = false;
        this.dataDevolucao = LocalDate.now();
        livro.marcarDisponivel();
    }

    public boolean isAtivo() { return ativo; }
    public Usuario getUsuario() { return usuario; }
    public Livro getLivro() { return livro; }
    public LocalDate getDataEmprestimo() { return dataEmprestimo; }
    public LocalDate getDataDevolucao() { return dataDevolucao; }
}
```

## Configuração do Maven (JUnit 5 + Surefire)

**`pom.xml`:**
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>biblioteca</groupId>
    <artifactId>biblioteca</artifactId>
    <version>1.0.0</version>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <junit.jupiter.version>5.10.2</junit.jupiter.version>
        <junit.platform.version>1.10.2</junit.platform.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>${junit.jupiter.version}</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.junit.platform</groupId>
            <artifactId>junit-platform-suite</artifactId>
            <version>${junit.platform.version}</version>
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

## Código de Teste

### `LivroTest.java`
```java
package biblioteca;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;

import static org.junit.jupiter.api.Assertions.*;

class LivroTest {

    @Test
    @DisplayName("Deve criar livro disponível com título válido")
    void criarLivroValido() {
        Livro l = new Livro("Clean Code");
        assertAll(
            () -> assertEquals("Clean Code", l.getTitulo()),
            () -> assertTrue(l.isDisponivel())
        );
    }

    @ParameterizedTest
    @ValueSource(strings = {"", " ", "   "})
    @DisplayName("Deve rejeitar títulos vazios ou em branco")
    void tituloInvalido(String titulo) {
        assertThrows(IllegalArgumentException.class, () -> new Livro(titulo));
    }

    @Test
    @DisplayName("Não deve permitir marcar indisponível duas vezes")
    void marcarIndisponivelDuasVezes() {
        Livro l = new Livro("Refactoring");
        l.marcarIndisponivel();
        assertThrows(IllegalStateException.class, () -> l.marcarIndisponivel());
    }
}
```

### `EmprestimoTest.java`
```java
package biblioteca;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

class EmprestimoTest {

    private Usuario usuario;
    private Livro livro;

    @BeforeEach
    void setup() {
        usuario = new Usuario("Rafaela");
        livro = new Livro("Domain-Driven Design");
    }

    @Test
    @DisplayName("Ao criar empréstimo, livro fica indisponível")
    void emprestimoMarcaLivroIndisponivel() {
        Emprestimo e = new Emprestimo(usuario, livro);
        assertAll(
            () -> assertSame(usuario, e.getUsuario()),
            () -> assertSame(livro, e.getLivro()),
            () -> assertFalse(livro.isDisponivel()),
            () -> assertTrue(e.isAtivo())
        );
    }

    @Test
    @DisplayName("Devolver empréstimo torna livro disponível")
    void devolverEmprestimo() {
        Emprestimo e = new Emprestimo(usuario, livro);
        e.devolver();
        assertAll(
            () -> assertFalse(e.isAtivo()),
            () -> assertNotNull(e.getDataDevolucao()),
            () -> assertTrue(livro.isDisponivel())
        );
    }

    @Test
    @DisplayName("Não deve emprestar livro já indisponível")
    void naoEmprestaLivroIndisponivel() {
        new Emprestimo(usuario, livro);
        assertThrows(IllegalStateException.class, () -> new Emprestimo(new Usuario("Maria"), livro));
    }
}
```

### `UsuarioTest.java`
```java
package biblioteca;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

class UsuarioTest {

    private Usuario usuario;

    @BeforeEach
    void setup() {
        usuario = new Usuario("João");
    }

    @Test
    @DisplayName("Usuário começa sem empréstimos ativos")
    void semEmprestimosAtivosInicialmente() {
        assertTrue(usuario.getEmprestimosAtivos().isEmpty());
    }

    @Nested
    class LimiteEmprestimos {

        @Test
        @DisplayName("Pode emprestar até o limite")
        void podeEmprestarAteOLimite() {
            usuario.adicionarEmprestimo(new Livro("L1"));
            usuario.adicionarEmprestimo(new Livro("L2"));
            usuario.adicionarEmprestimo(new Livro("L3"));
            assertEquals(Usuario.LIMITE_EMPRESTIMOS, usuario.getEmprestimosAtivos().size());
        }

        @Test
        @DisplayName("Ultrapassar limite deve lançar exceção")
        void ultrapassarLimiteLancaExcecao() {
            usuario.adicionarEmprestimo(new Livro("L1"));
            usuario.adicionarEmprestimo(new Livro("L2"));
            usuario.adicionarEmprestimo(new Livro("L3"));
            assertThrows(IllegalStateException.class, () -> usuario.adicionarEmprestimo(new Livro("L4")));
        }
    }

    @Nested
    class Devolucao {

        @Test
        @DisplayName("Devolver remove empréstimo da lista ativa")
        void devolverRemoveDaListaAtiva() {
            Livro l = new Livro("Clean Architecture");
            usuario.adicionarEmprestimo(l);
            usuario.devolver(l);
            assertTrue(usuario.getEmprestimosAtivos().isEmpty());
            assertTrue(l.isDisponivel());
        }

        @Test
        @DisplayName("Devolver livro não emprestado deve falhar")
        void devolverLivroNaoEmprestadoFalha() {
            Livro l = new Livro("Effective Java");
            assertThrows(IllegalStateException.class, () -> usuario.devolver(l));
        }
    }
}
```
## Test Suite
Test Suite em JUnit 5, reune várias classes de teste (ex.: LivroTest, UsuarioTest, EmprestimoTest) em uma única execução organizada.
```java
package biblioteca;

import org.junit.platform.suite.api.SelectPackages;
import org.junit.platform.suite.api.Suite;

@Suite
@SelectPackages("biblioteca")
public class BibliotecaTestSuite {
}
```

