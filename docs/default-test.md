# Default Test
## 🔍 Descrição do Problema

O **Default Test** ocorre quando um teste é escrito sem um propósito claro ou sem ser devidamente configurado, utilizando **valores padrão ou genéricos** (como `null`, `0`, strings vazias, ou valores iniciais padrão da linguagem/framework) que não validam condições ou funcionalidades reais do sistema. Esse tipo de teste não garante que o sistema funcione corretamente e, frequentemente, pode passar mesmo em situações onde falhas reais podem ocorrer.

Em outras palavras, um **Default Test** é um teste sem valor real, muitas vezes criado apenas para "preencher" o conjunto de testes ou para testar o comportamento padrão de construtores, sem verificar realmente o comportamento funcional esperado em um cenário de uso.

-----

## ⚠️ Sintomas e Impacto

  * **Cobertura de Testes Enganosa**: Testes com valores padrão não garantem a cobertura real de cenários de uso do sistema e podem criar uma falsa sensação de segurança.
  * **Falta de Garantia de Funcionalidade**: O teste não valida cenários de negócio importantes ou casos de borda, o que pode levar a falhas no sistema durante a produção, mesmo com testes "passando".
  * **Manutenção Difícil**: Testes com valores padrão podem ser difíceis de entender e manter, pois não refletem o comportamento esperado do sistema de forma clara em situações reais.

-----

## 🔑 Critérios de Identificação

Para identificar o **Default Test**, procure por:

  * Testes que utilizam **valores padrão ou genéricos** (por exemplo, `null`, `0`, `""` - string vazia, ou objetos inicializados sem propriedades significativas).
  * Testes que não validam efetivamente uma condição de negócio específica ou não cobrem um cenário real relevante.
  * Testes cujas expectativas (`expect`) apenas confirmam os valores padrão de um objeto ou as saídas triviais de uma função com entradas padrão.

### Detecção Automática

Ferramentas de análise estática podem ser configuradas para identificar quando testes estão utilizando dados excessivamente genéricos ou quando as expectativas são idênticas aos valores padrão de inicialização de um objeto, sugerindo que o teste pode não ser representativo de um caso real.

-----

## ✅ Exemplo de Código

### Exemplo com Default Test

```dart
import 'package:flutter_test/flutter_test.dart';

class User {
  String name;
  int age;

  // Construtor com valores padrão, comum em DTOs ou modelos simples.
  User({this.name = "", this.age = 0}); 
}

void main() {
  test('Verifica valores padrão do objeto User', () {
    final user = User(); // Cria um usuário sem fornecer valores específicos
    expect(user.name, "", reason: "O nome padrão deve ser uma string vazia");
    expect(user.age, 0, reason: "A idade padrão deve ser 0");
    // Este teste não valida nenhum COMPORTAMENTO real do sistema com um usuário.
    // Apenas confirma a inicialização padrão do construtor.
  });
}
```

### Exemplo sem Default Test

```dart
import 'package:flutter_test/flutter_test.dart';

class User {
  String name;
  int age;

  // Construtor que exige valores para nome e idade.
  User({required this.name, required this.age});

  // Exemplo de um método com lógica de negócio que merece ser testado
  bool isAdult() {
    return age >= 18;
  }
}

void main() {
  group('User Model Tests', () { // Agrupando testes relacionados para melhor organização
    test('Criação de usuário com valores específicos', () {
      final user = User(name: "John Doe", age: 30);
      expect(user.name, "John Doe", reason: "O nome deve ser 'John Doe'");
      expect(user.age, 30, reason: "A idade deve ser 30");
    });

    test('Usuário com 18 anos deve ser considerado adulto', () {
      final user = User(name: "Teenager", age: 18);
      expect(user.isAdult(), isTrue, reason: "Um usuário com 18 anos deve ser adulto");
    });

    test('Usuário com menos de 18 anos NÃO deve ser considerado adulto', () {
      final user = User(name: "Kid", age: 17);
      expect(user.isAdult(), isFalse, reason: "Um usuário com menos de 18 anos NÃO deve ser adulto");
    });

    test('Criação de usuário com nome vazio é permitida, mas valida outros comportamentos', () {
      final user = User(name: "", age: 5);
      expect(user.name, "", reason: "Nome vazio deve ser aceito");
      expect(user.isAdult(), isFalse, reason: "Usuário com 5 anos não é adulto");
    });
  });
}
```

-----

## 🚀 Correções Sugeridas

Para resolver o **Default Test**:

  * **Utilize Dados de Teste Reais e Representativos**: Em vez de usar valores padrão, forneça dados que representem cenários de uso reais ou casos de borda importantes para a sua aplicação.
  * **Crie Cenários de Teste Específicos**: Cada teste deve focar em um comportamento ou condição específica do sistema. Se há uma lógica de negócio associada a um valor padrão, teste essa lógica (e não apenas o valor em si). Por exemplo, se `age = 0` significa um usuário não registrado, teste o comportamento do sistema para "usuário não registrado".
  * **Evite Testes Meramente de Inicialização**: Se um teste apenas valida que um campo possui um valor padrão após a construção do objeto, e não há uma lógica de negócio associada a esse valor padrão que precise ser validada, esse teste pode ser redundante. Foco em testar **comportamentos**, não apenas estados iniciais triviais.
  * **Teste Casos Positivos e Negativos**: Garanta que os testes cubram tanto o comportamento esperado quanto os cenários de falha ou exceção.

-----

## 🌟 Exceções e Casos Especiais

Em alguns contextos, testar o estado inicial de um objeto pode ser aceitável, por exemplo:

  * Quando o valor padrão tem um significado funcional claro (ex: um `status` inicial de um processo).
  * Em testes de contrato de uma API ou interface, onde você precisa garantir que certos campos existem e têm um tipo inicial.
  * Quando o padrão é um valor `null` e a ausência de um valor é um cenário de negócio a ser validado.

No entanto, mesmo nesses casos, o teste deve ir além da simples verificação do valor e validar **o comportamento** que deriva desse estado padrão.

-----

## 🛠 Ferramentas de Detecção

  * **Analisadores Estáticos de Código (Linters)**: Ferramentas como `dart analyze` (especialmente com configurações de lint mais rigorosas, como as dos pacotes `lints` ou `pedantic`) podem ajudar a identificar padrões de código que levam a testes default, como a ausência de parâmetros nomeados em construtores onde se esperaria dados significativos.
  * **Plugins de Test Smells**: Ferramentas de análise de código como **SonarQube** (com suas regras para Dart) podem ser configuradas para identificar testes com baixo valor de asserção, ou que apenas validam a inicialização padrão de objetos sem lógica adicional, o que pode indicar um Default Test.

-----

## 📚 Referências e Estudos Relacionados

  * Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
  * Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
  * Van Deursen, A., et al. (2001). "Refactoring Test Code."

-----

## 📝 Nota

O **Default Test** é um problema que pode ser encontrado em qualquer projeto de software, especialmente quando os testes são escritos apressadamente ou sem um entendimento profundo do comportamento que precisa ser validado. Este guia é essencial para garantir que seus testes realmente validem o comportamento do sistema, fornecendo uma cobertura eficaz e uma base sólida para a qualidade do software.
