# 🧪 Sistema de Reserva de Salas de Reunião

Este projeto apresenta um exemplo completo de **BDD (Behavior-Driven Development)** com **Java + Cucumber**, implementando um sistema de **reserva de salas de reunião**.

O objetivo é demonstrar:

- escrita de cenários em **Gherkin**
- implementação dos **Steps Definitions**
- aplicação de regras de negócio em Java.

## 🧩 Contexto do problema

Uma empresa possui diversas salas de reunião disponíveis para seus colaboradores. Cada sala possui:

- nome
- capacidade máxima
- localização
- status de disponibilidade

Os colaboradores podem solicitar reservas informando:

- nome do responsável
- sala desejada
- data
- horário de início
- horário de fim
- quantidade de participantes

O sistema deve validar se a reserva pode ou não ser realizada com base em regras de negócio.

## ✅ Regras de negócio

O sistema deve atender às seguintes regras:

- Uma sala só pode ser reservada se estiver disponível.
- Não pode haver conflito de horário para a mesma sala na mesma data.
- A quantidade de participantes não pode exceder a capacidade da sala.
- O horário de início deve ser anterior ao horário de fim.
- Reservas só podem ser feitas para datas válidas.
- Ao reservar com sucesso, o sistema deve registrar a reserva.
- Caso a reserva seja inválida, o sistema deve informar o motivo da recusa.

---

# 📁 Estrutura do projeto

```text
reserva-salas-bdd/
├── pom.xml
├── src/
│   ├── main/
│   │   └── java/
│   │        └── reservas/
│   │            ├── Sala.java
│   │            ├── Reserva.java
│   │            ├── ResultadoReserva.java
│   │            └── SistemaReservas.java
│   └── test/
│       ├── java/
│   │       └── reservas/
│   │             ├── runner/
│   │             │   └── RunnerTest.java
│   │             └── steps/
│   │                 └── ReservaSteps.java
│       └── resources/
│           └── features/
│               └── reserva_salas.feature
```
---

## 📝 Exemplos de cenários que devem ser contemplados

```gherkin
#language: pt

Funcionalidade: Reserva de salas de reunião
  Como colaborador da empresa
  Quero reservar salas de reunião
  Para organizar reuniões sem conflitos de horário

  Cenário: Realizar reserva com sucesso
    Dado que existe uma sala chamada "Sala Azul" com capacidade para 10 pessoas
    E que a sala está disponível
    Quando o usuário solicitar uma reserva para a sala "Sala Azul" no dia "20/12/2026" das "14:00" às "15:00" para 8 participantes
    Então a reserva deve ser realizada com sucesso
    E a mensagem exibida deve ser "Reserva confirmada com sucesso"

  Cenário: Tentar reservar sala com participantes acima da capacidade
    Dado que existe uma sala chamada "Sala Verde" com capacidade para 5 pessoas
    E que a sala está disponível
    Quando o usuário solicitar uma reserva para a sala "Sala Verde" no dia "20/12/2026" das "10:00" às "11:00" para 8 participantes
    Então a reserva não deve ser realizada
    E a mensagem exibida deve ser "Quantidade de participantes excede a capacidade da sala"

  Cenário: Tentar reservar uma sala em horário já ocupado
    Dado que existe uma sala chamada "Sala Sol" com capacidade para 12 pessoas
    E que a sala está disponível
    E que já existe uma reserva para a sala "Sala Sol" no dia "20/12/2026" das "09:00" às "10:00"
    Quando o usuário solicitar uma reserva para a sala "Sala Sol" no dia "20/12/2026" das "09:30" às "10:30" para 6 participantes
    Então a reserva não deve ser realizada
    E a mensagem exibida deve ser "Já existe uma reserva para esse horário"

  Cenário: Tentar reservar com horário inicial maior que o final
    Dado que existe uma sala chamada "Sala Executiva" com capacidade para 15 pessoas
    E que a sala está disponível
    Quando o usuário solicitar uma reserva para a sala "Sala Executiva" no dia "20/12/2026" das "16:00" às "15:00" para 10 participantes
    Então a reserva não deve ser realizada
    E a mensagem exibida deve ser "Horário inválido"

  Cenário: Tentar reservar sala indisponível
    Dado que existe uma sala chamada "Sala Ouro" com capacidade para 20 pessoas
    E que a sala "Sala Ouro" está indisponível
    Quando o usuário solicitar uma reserva para a sala "Sala Ouro" no dia "20/12/2026" das "08:00" às "09:00" para 10 participantes
    Então a reserva não deve ser realizada
    E a mensagem exibida deve ser "Sala indisponível"
    ```
---

## ⚙️ Configuração `pom.xml`

> Neste exemplo, a versão do Java está centralizada na propriedade `java.version`.  
> Para usar outra versão, basta alterar apenas esta linha.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>reserva</groupId>
    <artifactId>reserva-salas-bdd</artifactId>
    <version>1.0-SNAPSHOT</version>
    <name>reserva-salas-bdd</name>

    <properties>
        <!-- Altere aqui para 11, 17, 21 etc. -->
        <java.version>17</java.version>

        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>${java.version}</maven.compiler.source>
        <maven.compiler.target>${java.version}</maven.compiler.target>

        <cucumber.version>7.20.1</cucumber.version>
        <junit.jupiter.version>5.11.3</junit.jupiter.version>
        <junit.platform.version>1.11.3</junit.platform.version>
        <maven.surefire.plugin.version>3.2.5</maven.surefire.plugin.version>
    </properties>

    <dependencies>
        <!-- Cucumber Java -->
        <dependency>
            <groupId>io.cucumber</groupId>
            <artifactId>cucumber-java</artifactId>
            <version>${cucumber.version}</version>
            <scope>test</scope>
        </dependency>

        <!-- Integração Cucumber + JUnit Platform -->
        <dependency>
            <groupId>io.cucumber</groupId>
            <artifactId>cucumber-junit-platform-engine</artifactId>
            <version>${cucumber.version}</version>
            <scope>test</scope>
        </dependency>

        <!-- JUnit 5 -->
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>${junit.jupiter.version}</version>
            <scope>test</scope>
        </dependency>

        <!-- Suite JUnit Platform -->
        <dependency>
            <groupId>org.junit.platform</groupId>
            <artifactId>junit-platform-suite</artifactId>
            <version>${junit.platform.version}</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!-- Compilador -->
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                </configuration>
            </plugin>

            <!-- Execução dos testes -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>${maven.surefire.plugin.version}</version>
            </plugin>
        </plugins>
    </build>

</project>
```
## 🧱 Classes

### Classe `Sala.java`

```java
package br.com.exemplo.reservas;

public class Sala {
    private String nome;
    private int capacidade;
    private String localizacao;
    private boolean disponivel;

    public Sala(String nome, int capacidade, String localizacao, boolean disponivel) {
        this.nome = nome;
        this.capacidade = capacidade;
        this.localizacao = localizacao;
        this.disponivel = disponivel;
    }

    public String getNome() {
        return nome;
    }

    public int getCapacidade() {
        return capacidade;
    }

    public String getLocalizacao() {
        return localizacao;
    }

    public boolean isDisponivel() {
        return disponivel;
    }

    public void setDisponivel(boolean disponivel) {
        this.disponivel = disponivel;
    }
}
```

---

### Classe `Reserva.java`

```java
package br.com.exemplo.reservas;

import java.time.LocalDate;
import java.time.LocalTime;

public class Reserva {
    private String responsavel;
    private Sala sala;
    private LocalDate data;
    private LocalTime horarioInicio;
    private LocalTime horarioFim;
    private int quantidadeParticipantes;

    public Reserva(String responsavel, Sala sala, LocalDate data,
                   LocalTime horarioInicio, LocalTime horarioFim,
                   int quantidadeParticipantes) {
        this.responsavel = responsavel;
        this.sala = sala;
        this.data = data;
        this.horarioInicio = horarioInicio;
        this.horarioFim = horarioFim;
        this.quantidadeParticipantes = quantidadeParticipantes;
    }

    public String getResponsavel() {
        return responsavel;
    }

    public Sala getSala() {
        return sala;
    }

    public LocalDate getData() {
        return data;
    }

    public LocalTime getHorarioInicio() {
        return horarioInicio;
    }

    public LocalTime getHorarioFim() {
        return horarioFim;
    }

    public int getQuantidadeParticipantes() {
        return quantidadeParticipantes;
    }

    public boolean conflitaCom(Reserva outra) {
        boolean mesmaSala = this.sala.getNome().equalsIgnoreCase(outra.getSala().getNome());
        boolean mesmaData = this.data.equals(outra.getData());

        if (!mesmaSala || !mesmaData) {
            return false;
        }

        return this.horarioInicio.isBefore(outra.getHorarioFim)
                && this.horarioFim.isAfter(outra.getHorarioInicio);
    }
}
```

---

### Classe `ResultadoReserva.java`

```java
package br.com.exemplo.reservas;

public class ResultadoReserva {
    private boolean sucesso;
    private String mensagem;

    public ResultadoReserva(boolean sucesso, String mensagem) {
        this.sucesso = sucesso;
        this.mensagem = mensagem;
    }

    public boolean isSucesso() {
        return sucesso;
    }

    public String getMensagem() {
        return mensagem;
    }
}
```

---

### Classe `SistemaReservas.java`

```java
package br.com.exemplo.reservas;

import java.time.LocalDate;
import java.util.ArrayList;
import java.util.List;

public class SistemaReservas {
    private List<Sala> salas;
    private List<Reserva> reservas;

    public SistemaReservas() {
        this.salas = new ArrayList<>();
        this.reservas = new ArrayList<>();
    }

    public void cadastrarSala(Sala sala) {
        salas.add(sala);
    }

    public Sala buscarSalaPorNome(String nome) {
        for (Sala sala : salas) {
            if (sala.getNome().equalsIgnoreCase(nome)) {
                return sala;
            }
        }
        return null;
    }

    public void adicionarReservaExistente(Reserva reserva) {
        reservas.add(reserva);
    }

    public ResultadoReserva reservar(Reserva novaReserva) {
        if (novaReserva.getSala() == null) {
            return new ResultadoReserva(false, "Sala não encontrada");
        }

        if (!novaReserva.getSala().isDisponivel()) {
            return new ResultadoReserva(false, "Sala indisponível");
        }

        if (novaReserva.getQuantidadeParticipantes() > novaReserva.getSala().getCapacidade()) {
            return new ResultadoReserva(false, "Quantidade de participantes excede a capacidade da sala");
        }

        if (!novaReserva.getHorarioInicio().isBefore(novaReserva.getHorarioFim())) {
            return new ResultadoReserva(false, "Horário inválido");
        }

        if (novaReserva.getData().isBefore(LocalDate.now())) {
            return new ResultadoReserva(false, "Data inválida");
        }

        for (Reserva reservaExistente : reservas) {
            if (novaReserva.conflitaCom(reservaExistente)) {
                return new ResultadoReserva(false, "Já existe uma reserva para esse horário");
            }
        }

        reservas.add(novaReserva);
        return new ResultadoReserva(true, "Reserva confirmada com sucesso");
    }

    public List<Reserva> getReservas() {
        return reservas;
    }
}
```

---

## 🧪 Runner 

### `RunnerTest.java`

```java
package br.com.exemplo.reservas.runner;

import static io.cucumber.junit.platform.engine.Constants.GLUE_PROPERTY_NAME;
import static io.cucumber.junit.platform.engine.Constants.PLUGIN_PROPERTY_NAME;

import org.junit.platform.suite.api.ConfigurationParameter;
import org.junit.platform.suite.api.IncludeEngines;
import org.junit.platform.suite.api.SelectClasspathResource;
import org.junit.platform.suite.api.Suite;

@Suite
@IncludeEngines("cucumber")
@SelectClasspathResource("features")
@ConfigurationParameter(key = GLUE_PROPERTY_NAME, value = "br.com.exemplo.reservas.steps")
@ConfigurationParameter(key = PLUGIN_PROPERTY_NAME, value = "pretty")
public class RunnerTest {
}
```

---

## 🧪 Steps 

### Step `ReservaSteps.java`

```java
package br.com.exemplo.reservas.steps;

import br.com.exemplo.reservas.Reserva;
import br.com.exemplo.reservas.ResultadoReserva;
import br.com.exemplo.reservas.Sala;
import br.com.exemplo.reservas.SistemaReservas;
import io.cucumber.java.pt.Dado;
import io.cucumber.java.pt.Quando;
import io.cucumber.java.pt.Então;

import java.time.LocalDate;
import java.time.LocalTime;
import java.time.format.DateTimeFormatter;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertTrue;

public class ReservaSteps {

    private SistemaReservas sistema;
    private ResultadoReserva resultado;

    private final DateTimeFormatter formatterData = DateTimeFormatter.ofPattern("dd/MM/yyyy");
    private final DateTimeFormatter formatterHora = DateTimeFormatter.ofPattern("HH:mm");

    public ReservaSteps() {
        sistema = new SistemaReservas();
    }

    @Dado("que existe uma sala chamada {string} com capacidade para {int} pessoas")
    public void que_existe_uma_sala_chamada_com_capacidade_para_pessoas(String nomeSala, Integer capacidade) {
        Sala sala = new Sala(nomeSala, capacidade, "2º andar", true);
        sistema.cadastrarSala(sala);
    }

    @Dado("que a sala está disponível")
    public void que_a_sala_está_disponível() {
        // A sala já foi cadastrada como disponível
    }

    @Dado("que a sala {string} está indisponível")
    public void que_a_sala_esta_indisponivel(String nomeSala) {
        Sala sala = sistema.buscarSalaPorNome(nomeSala);
        if (sala != null) {
            sala.setDisponivel(false);
        }
    }

    @Dado("que já existe uma reserva para a sala {string} no dia {string} das {string} às {string}")
    public void que_ja_existe_uma_reserva_para_a_sala_no_dia_das_as(
            String nomeSala, String data, String horaInicio, String horaFim) {

        Sala sala = sistema.buscarSalaPorNome(nomeSala);

        Reserva reserva = new Reserva(
                "Reserva anterior",
                sala,
                LocalDate.parse(data, formatterData),
                LocalTime.parse(horaInicio, formatterHora),
                LocalTime.parse(horaFim, formatterHora),
                1
        );

        sistema.adicionarReservaExistente(reserva);
    }

    @Quando("o usuário solicitar uma reserva para a sala {string} no dia {string} das {string} às {string} para {int} participantes")
    public void o_usuario_solicitar_uma_reserva_para_a_sala_no_dia_das_as_para_participantes(
            String nomeSala, String data, String horaInicio, String horaFim, Integer participantes) {

        Sala sala = sistema.buscarSalaPorNome(nomeSala);

        Reserva novaReserva = new Reserva(
                "Rafaela",
                sala,
                LocalDate.parse(data, formatterData),
                LocalTime.parse(horaInicio, formatterHora),
                LocalTime.parse(horaFim, formatterHora),
                participantes
        );

        resultado = sistema.reservar(novaReserva);
    }

    @Então("a reserva deve ser realizada com sucesso")
    public void a_reserva_deve_ser_realizada_com_sucesso() {
        assertTrue(resultado.isSucesso());
    }

    @Então("a reserva não deve ser realizada")
    public void a_reserva_nao_deve_ser_realizada() {
        assertFalse(resultado.isSucesso());
    }

    @Então("a mensagem exibida deve ser {string}")
    public void a_mensagem_exibida_deve_ser(String mensagemEsperada) {
        assertEquals(mensagemEsperada, resultado.getMensagem());
    }
}
```