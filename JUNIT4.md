# 1. JUnit4
### 1.1 Descrição do sistema
JUnit é um *framework* desenvolvido em Java por Erich Gamma e Kent Beck. O objetivo do JUnit é facilitar a execução de testes de unidade provendo uma maneira fácil de escrever código de testes.

Testes de unidade ou unitários, se concentram em validar a menor unidade testável do projeto de *software*. Essa unidade pode ser método, classe ou objeto. Vamos imaginar, por exemplo, que tenhamos que criar um teste em Java, sem a utilização de um *framework*, para um método que soma dois números inteiros ( somar(int a, int b) ).  Provavelmente, vamos ter algo semelhante a isso:
```
public class CalculadoraTest {
	public void testeSomar() throws FalhouException{
	    int a = 2, b = 5;
	    int resultado = somar(a, b);
	    
	    if(resultado != 7){
	        throw new FalhouException("Falhou!");
	    }
	}
}
```
O JUnit utiliza uma sintaxe que facilita a escrita das verificações dos testes. O mesmo teste feito anteriormente utilizando os recursos do JUnit, ficaria assim:

```
public class CalculadoraTest extends TestCase {
	public void testSomar(){
	    int a = 2, b = 5;
	    int resultado = somar(a, b);
	    
	    assertEquals("O teste falhou.", 7, resultado);
	}
}
```
 
JUnit4 é uma versão do *framework* que contém diversas melhorias se comparada com a versão anterior. As melhorias estão relacionadas diretamente com o uso de anotações, recurso presente no Java 5. Com este recurso, foi possível facilitar ainda mais a escrita e legibilidade dos códigos de testes. Essas melhorias serão descritas neste capítulo, bem como algumas características do código fonte do *framework*, possibilitando o entendimento conceitual e arquitetural do sistema a fim de despertar novos colaboradores para o mesmo.

### 1.2 Principais Features
A principal vantagem do JUnit4 é o uso de anotações para simplificar o desenvolvimento dos testes. 
#### 1.2.1 @Test
Antes dessa versão, todos os métodos de teste deveriam começar com a palavra "test" e eram encontrados através de reflexão. Na versão 4, basta adicionar a anotação @Test antes do método para indicar que aquele método é um método de teste. Utilizando o exemplo anterior:
```
@Test
public void somar(){
    int a = 2, b = 5;
    int resultado = somar(a, b);
    
    assertEquals("O teste falhou.", 7, resultado);
}
```
Dessa forma, é mais fácil de identificar os métodos de teste e possibilitar, por exemplo, que o método de teste tenha o mesmo nome que o método que está sendo testado. 
#### 1.2.2 @SuiteClasses
Além da anotação em métodos, as anotações existentes em classes também contribuíram para a legibilidade das mesmas. Um exemplo dessa melhoria foi aplicado nas suites de teste. Uma suite de teste é uma classe que contém várias classes de testes. Na versão anterior, uma suite era construída da seguinte forma:
```
public class ClassSuiteTests {

    public static void main(String[] args) {
        junit.textui.TestRunner.run(suite());
    }

    public static Test suite() { 
        TestSuite suite = new TestSuite("Tests");
        suite.addTestSuite(Class1Test.class);
        suite.addTestSuite(Class2Test.class);
        suite.addTestSuite(Class3Test.class);
        return suite;
    }
  }
```
Na versão 4, a mesma suite é escrita assim:
```
@RunWith(Suite.class)
@SuiteClasses({
        Class1Test.class,
        Class2Test.class,
        Class3Test.class,
})
public class ClassSuiteTests {
}
```
# 2. Equipe de Desenvolvimento
A equipe de desenvolvimento é composta por membros e contribuidores. Os membros são aqueles que possuem acesso direto ao código fonte do projeto, enquanto que os contribuidores melhoram o projeto através de submissões de *patches* e sugestões para os membros. 

 - Membros

- David Saff - david@saff.net
- Kevin Cooney - kcooney@google.com
- Stefan Birkner - mail@stefan-birkner.de
- Marc Philipp - mail@marcphilipp.de

 - Contribuidores
 
- stefanbirkner - https://github.com/stefanbirkner
- dsaff - https://github.com/dsaff
- Tibor17 - https://github.com/Tibor17
- marcphilipp - https://github.com/marcphilipp
- kcooney - https://github.com/kcooney
- pimterry - https://github.com/pimterry

# 3. Releases
### 3.1 Categorias
Na release 4.4, foi incluído um novo recurso: Categories. Categories é um tipo de suite que permite executar ou não as classes ou métodos que estiverem anotados com determinada categoria. Com este recurso, tornou-se possível organizar os testes em grupos. Exemplo:
```
    public static class SomeUITests {
        @Category(UserAvailable.class)
        @Test
        public void askUserToPressAKey() { }
        
        @Test
        public void simulatePressingKey() { }
    }
        
    @Category(InternetConnected.class)
    public static class InternetTests {
        @Test
        public void pingServer() { }
```
Para executar uma determinada categoria, basta utilizar a anotação @IncludeCategory e para não executar uma determinada categoria, utilizar a anotação @ExcludeCategory.

```
    @RunWith(Categories.class)
    @IncludeCategory(UserAvailable.class)
    @ExcludeCategory(InternetTests .class)
    @SuiteClasses({
	    SomeUITests.class,  
	    InternetTests.class
    })
    public class UserTestsSuite() {}  
```

### 3.2 AssertThat
Na release 4.4, JUnit incluiu pela primeira vez uma biblioteca de terceiros. Notou-se que utilizar Matchers da biblioteca Hamcrest para validar os resultados dos testes era vantajoso, pois a sintaxe é mais legível e quando o teste falha, a mensagem de erro consegue ser mais específica, facilitando entender o porquê da falha ocorrer. 
Exemplo da sintaxe usando um Matcher:
``` 
	assertThat(x, is(3));
    assertThat(x, is(not(4)));
    assertThat(myList, hasItem("3"));
```
Exemplo de mensagem de erro quando o teste falha, sem matcher:
```
assertTrue(responseString.contains("color") || responseString.contains("colour"));
// ==> failure message: 
// java.lang.AssertionError:
```
Exemplo de mensagem de erro quando o teste falha, usando matcher:
```
assertThat(responseString, anyOf(containsString("color"), containsString("colour")));
// ==> failure message:
// java.lang.AssertionError: 
// Expected: (a string containing "color" or a string containing "colour")
//      got: "Please choose a font"
```

Devido a essas e outras vantagens da Hamcrest, JUnit incorporou, nesta *release*, a biblioteca e criou um novo método assertThat que recebe como parâmetro o valor a ser validado e um Matcher.
```
assertThat([value], [matcher statement]);
```
### 3.3 Testes em paralelo
Na release 4.6, JUnit incluiu um método para permitir a execução de dois ou mais métodos de forma concorrente. Isto está implementado em ParallelComputer.java. Exemplo:

```
@Test public void testsRunInParallel() {
	long start= System.currentTimeMillis();
	Result result= JUnitCore.runClasses(ParallelComputer.methods(),
			Example.class);
	assertTrue(result.wasSuccessful());
	long end= System.currentTimeMillis();
	assertThat(end - start, betweenInclusive(1000, 1500));
}
```
### 3.4 Rules
Rules permitem executar um comportamento antes ou depois de cada método de teste. Esse comportamento é escrito em um classe que implementa a interface TestRule.java e pode ser reutilizado em outros testes, não necessitando escrever novamente este comportamento quando precisar. Essa *feature* foi incluída na release 4.7. JUnit já disponibiliza algumas Rules prontas que podem ser  encontradas no pacote org.junit.rules. São elas:

 - DisableOnDebug.java
 - ErrorCollector.java
 - ExpectedException.java
 - ExternalResource.java
 - RuleChain.java
 - Stopwatch.java
 - TemporaryFolder.java
 - TestName.java
 - TestWatcher.java
 - Timeout.java
 - Verifier.java

Para criar uma nova Rule, é necessário implementar a interface TestRule.java. A utilização de uma Rule é bem simples, basta adicionar a anotação @Rule na classe que usará a Rule:
```
public class NameRuleTest {
  @Rule
  public final TestName name = new TestName();
  
  @Test
  public void testA() {
    assertEquals("testA", name.getMethodName());
  }
}
```
### 3.5 Testes parametrizados
Na release 4.11, foi incluído no framework os testes parametrizados o que permite ao desenvolvedor executar um teste várias vezes, porém com parâmetros diferentes. Um recurso muito poderoso, que evita a duplicação de testes. Para utilizar, é necessário anotar a classe com @RunWith (Parameterized.class) e um método anotado com @Parameters que retorna uma coleção contendo os parâmetros.



# 4. Tecnologias
### 4.1 Hamcrest
O JUnit4 conta com as técnicas do Hamcrest para verificar os resultados dos testes de forma mais concisa. O Hamcrest é uma biblioteca que permite que o desenvolvedor crie verificações customizadas além de detalhar melhor o erro quando um teste falha. 

### 4.2 Maven
O controle de dependências do sistema JUnit4 é feito utilizando o Maven. O Maven é uma ferramenta de automação de compilação. 

# 5. Arquitetura
### 5.1 Padrões de Projeto
O JUnit utiliza em sua implementação, alguns padrões de projeto, conforme pode ser visto na Figura 1. 

![Figura 1. Padrões de projeto encontrados no JUnit](http://junit.sourceforge.net/doc/cookstour/Image6.gif)

Figura 1. Padrões de projeto encontrados no JUnit (Fonte: http://junit.sourceforge.net/doc/cookstour)

Posteriormente serão apresentados alguns destes padrões, explicando em quais classes do JUnit eles se encontram.

#### 5.1.1 Command
A classe TestCase é a classe que contém o método run. Esta classe possui o papel de encapsular um pedido de teste como um objeto (Padrão Command).

#### 5.1.2 Template Method
Além disso, esta classe possui os métodos abstratos setUp(), runTest() e tearDown(). Estes métodos são responsáveis, respectivamente, por criar o contexto, conter o código a ser executado e as validações e limpar o contexto. É usado aqui, o padrão de projeto Template Method. Este padrão permite com que as subclasses definam o comportamento de partes de código (abstratas), implementando estas partes de acordo com a necessidade de cada subclasse. As classes concretas serão as classes que o usuário do framework irá criar para testar seu código.

#### 5.1.3 Collecting Parameter
O método run recebe por parâmetro um objeto TestResult. Essa classe coleciona informações de resultados de execuções. Algumas destas informações são: quantidade de testes executados, quantidade de falhas e quantidade de erros. Essa estratégia de se passar o objeto como parâmetro que irá colecionar informações está descrita no padrão Collecting Parameter. Esse padrão é uma solução para agrupar o resultado de vários métodos. Existe também um outro método run sem parâmetros que já cria o próprio TestResult (padrão Factory Method).

#### 5.1.4 Composite
Usando o padrão Composite foi possível disponibilizar para os desenvolvedores recursividade de suites (suites de suites de teste). O Composite é um padrão que permite que os métodos de uma classe sejam aplicáveis também ao conjunto de classes. As classes que participam deste padrão são:

 - Componente: Implementa o componente padrão
 - Folha: Herda os métodos do Componente
 - Composite: Mantém a coleção (adiciona novos filhos) e define o comportamento para aqueles componentes que possuam filhos.
 - Cliente: Manipula objetos do conjunto através do Componente.

No caso do JUnit:

 - Componente: classe Test.java
 - Folha: classe TestCase.java
 - Composite: classe TestSuite.java
 - Cliente: classes que contém a *annotation* @Test

### 5.2 Estilo de Codificação
Estilo de Codificação ou Coding Style é um conjunto de orientações usadas para codificar um sistema com o objetivo de organizar e facilitar o entendimento do código durante a existência do projeto.

O projeto JUnit utiliza o guia de estilo para projetos *open-source* criado pelo Google (Chttps://github.com/google/styleguide).

As orientações para colaborar com o projeto JUnit seguindo o estilo de codificação, podem ser encontradas no arquivo CODING_STYLE.txt para os arquivos dentro dos pacotes org.junit.* e no arquivo LEGACY_CODING_STYLE.txt para os arquivos dentro dos pacotes junit.*. Ambos os arquivos se encontram na raiz do projeto. 

### 5.3 Diagrama de Pacotes
 
O código fonte do Junit está organizado em duas estruturas de pacotes. A estrutura abaixo de junit.* contém o código que existia nas versões anteriores do Junit. No diagrama de pacotes mostrado a seguir é possível ver a organização dos pacotes dentro desta estrutura:
 
![Pacotes abaixo de junit.*](https://lh3.googleusercontent.com/-YcePvfhJ_40/WSHi7RxtgkI/AAAAAAAAElQ/bozTp_pdCmELYSMBwg4P1xRfuNr2AvfRQCLcB/s0/packageJunit.png "packageJunit.png")

Figura 2. Diagrama de pacotes abaixo de junit.*.

Já a estrutura abaixo de org.junit.* contém o código existente na versão 4 do JUnit:
 ![Pacotes abaixo de org.*](https://lh3.googleusercontent.com/-pRfYfo2Ukj0/WSHi0QFgRCI/AAAAAAAAElI/Z8YJjqdZdt4LOC99Qh5BNuQtpmxj81zJgCLcB/s0/packageOrg.png "packageOrg.png")
 
 Figura 3. Diagrama de pacotes abaixo de org.junit.*.

### 5.4 Testes Unitários
Os testes unitários são feitos usando o próprio JUnit. Por isso, ao criar um novo teste, é necessário utilizar a sintaxe do JUnit, conforme descrito na seção 1.

Os testes unitários estão todos concentrados em Suites de Testes. Cada pacote possui uma suite de teste chamada AllTests.java. Em códigos feitos na versão 3.x do JUnit, cada suite AllTests.java (pacote junit.\*) chama, diretamente, os testes a serem executados. Para a verão 4, as suites AllTests.java (pacote org.junit.\*) chama outras suites, recurso adicionado nesta versão e que foi descrito na seção 5.1.4 deste capítulo.

# 6. Referências
http://junit.org/junit4/
http://www.devmedia.com.br/junit-implementando-testes-unitarios-em-java-parte-i/1432
https://www.javaskool.com/index.php/65-blog/javaskool-category/testing/junit/524-junit4-11chap1
http://www.devmedia.com.br/criando-testes-de-unidades-com-o-junit-4-usando-anotacoes/4798
http://blog.caelum.com.br/melhorando-a-legibilidade-dos-seus-testes-com-o-hamcrest/
https://pt.wikipedia.org/wiki/Apache_Maven
http://www.dsc.ufcg.edu.br/~jacques/cursos/map/html/frame/junit.htm
http://siep.ifpe.edu.br/anderson/blog/?page_id=976
