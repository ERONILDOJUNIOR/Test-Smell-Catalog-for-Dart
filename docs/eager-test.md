# Eager Test

## 🔍 Descrição do Problema

O **Eager Test** ocorre quando um método de teste em Dart tenta verificar **muitos comportamentos ou condições diferentes de uma só vez**, ou invoca diversos métodos do código de produção de forma excessiva. Em vez de testar um comportamento específico de maneira focada e isolada, o **Eager Test** acumula múltiplas expectativas (`expect`) e chamadas, o que pode resultar em:

  * **Testes Complicados de Entender**: Dificultando seu uso como documentação para o código.
  * **Aumento da Dependência**: Tornando os testes mais acoplados entre si e ao código de produção.
  * **Manutenção Complexa**: Qualquer mudança no código testado pode quebrar múltiplas expectativas em um único teste.

Em essência, um **Eager Test** é escrito para verificar mais do que o necessário em um único cenário, resultando em testes excessivos e com foco disperso.

-----

## ⚠️ Sintomas e Impacto

  * **Testes Longos e Complexos**: O corpo do teste é extenso, abrangendo muitos comportamentos ou cenários, o que o torna difícil de ler e entender rapidamente.
  * **Identificação Difícil de Falhas**: Quando uma falha ocorre, é árduo identificar qual das várias expectativas falhou e qual o comportamento exato que causou a falha, pois o teste cobre múltiplas condições.
  * **Dificuldade de Manutenção**: Testes complexos e multifuncionais são mais difíceis de modificar ou refatorar. Alterações em uma parte do sistema podem inesperadamente quebrar um **Eager Test** devido às suas muitas verificações.
  * **Baixa Legibilidade como Documentação**: Testes deveriam servir como uma forma de documentação executável. Um **Eager Test** falha nisso por ser muito denso e testar "tudo de uma vez".

-----

## 🔑 Critérios de Identificação

Para identificar o **Eager Test**, procure por:

  * Métodos de teste que incluem **muitas chamadas a métodos do código de produção** que não são estritamente necessários para configurar ou atuar no comportamento principal do teste.
  * Métodos de teste que contêm **um grande número de expectativas (`expect`)** validando diferentes aspectos do comportamento ou do estado do System Under Test (SUT) após uma única ação.
  * Falta de clareza sobre o **comportamento específico** sendo testado, devido à mistura de várias verificações independentes.

-----

## ✅ Exemplo de Código

### Exemplo com Eager Test

Neste exemplo, o teste `Teste de Registro do Usuário` verifica várias propriedades do objeto `User` após uma única chamada a `user.register()`, misturando validações sobre nome, email, status de registro, validade de credenciais e data de registro.

```dart
import 'package:flutter_test/flutter_test.dart';

class User {
  final String name;
  final String email;
  bool isRegistered = false;
  bool hasValidEmail = true; // Simulado para o exemplo
  bool hasValidPassword = true; // Simulado para o exemplo
  DateTime? registrationDate;

  User({
    required this.name,
    required this.email
  });

  void register() {
    isRegistered = true;
    registrationDate = DateTime.now();
    // Em um cenário real, aqui haveria lógica de validação de email/senha,
    // persistência em banco de dados, etc.
  }
}

void main() {
  test('Teste de Registro do Usuário (Eager Test)', () {
    final user = User(name: "John", email: "john@example.com");
    
    // Ação principal do teste
    user.register();

    // Testa muitas condições ao mesmo tempo (sintoma de Eager Test)
    expect(user.name, equals("John"), reason: "O nome do usuário deve ser 'John'");
    expect(user.email, equals("john@example.com"), reason: "O email do usuário deve ser 'john@example.com'");
    expect(user.isRegistered, isTrue, reason: "O usuário deve estar registrado");
    expect(user.hasValidEmail, isTrue, reason: "O email deve ser válido");
    expect(user.hasValidPassword, isTrue, reason: "A senha deve ser válida");
    expect(user.registrationDate, isNotNull, reason: "A data de registro não deve ser nula");
  });
}
```

### Exemplo sem Eager Test

Neste exemplo, cada `test` foca em uma única expectativa ou em um grupo muito pequeno de expectativas relacionadas a um comportamento específico, tornando cada teste conciso e fácil de entender.

```dart
import 'package:flutter_test/flutter_test.dart';

class User {
  final String name;
  final String email;
  bool isRegistered = false;
  DateTime? registrationDate;

  User({
    required this.name,
    required this.email
  });

  void register() {
    isRegistered = true;
    registrationDate = DateTime.now();
  }

  // Métodos que seriam testados individualmente
  bool isValidEmailFormat() => email.contains('@');
  bool hasStrongPassword(String password) => password.length >= 8; // Simulado
}

void main() {
  group('Funcionalidades de Registro de Usuário', () {
    late User user; // Declaração para ser inicializada no setUp

    setUp(() {
      // Setup comum para os testes neste grupo
      user = User(name: "Jane Doe", email: "jane@example.com");
      user.register(); // Executa a ação principal uma vez para o grupo
    });

    test('Deve registrar o usuário e definir isRegistered como verdadeiro', () {
      expect(user.isRegistered, isTrue, reason: "isRegistered deve ser verdadeiro após o registro");
    });

    test('Deve definir a data de registro após o registro', () {
      expect(user.registrationDate, isNotNull, reason: "registrationDate não deve ser nulo após o registro");
      expect(user.registrationDate!.year, DateTime.now().year, reason: "O ano de registro deve ser o ano atual");
      // Pode-se adicionar mais verificações de data aqui, se relevante.
    });

    // Testes de validade de email e senha seriam feitos separadamente,
    // ou em outro grupo de testes focado em validações.
    test('Email deve ter formato válido', () {
      final emailUser = User(name: "Test", email: "test@domain.com");
      expect(emailUser.isValidEmailFormat(), isTrue, reason: "Email deve ser considerado válido");
    });

    test('Senha forte deve ser validada', () {
      final passwordUser = User(name: "Test", email: "test@test.com");
      expect(passwordUser.hasStrongPassword("MyStrongP@ssw0rd"), isTrue, reason: "Senha deve ser forte");
    });
  });
}
```

-----

## 🚀 Correções Sugeridas

Para resolver o **Eager Test**:

  * **Siga o Princípio "Um Teste, Um Comportamento"**: Refatore cada **Eager Test** em múltiplos testes menores, onde cada um foca na validação de **um único comportamento, uma única regra de negócio, ou um único aspecto do System Under Test (SUT)**.
  * **Concentre-se em um Resultado por Ação**: Após realizar a "ação" do teste (o "Act" do padrão AAA - Arrange, Act, Assert), concentre-se em verificar **um resultado específico** ou um conjunto muito coeso de resultados diretamente relacionados a essa ação.
  * **Utilize `setUp` e `tearDown` (com parcimônia)**: Se múltiplos testes precisam da mesma configuração inicial, use a função `setUp()` do `package:test` (ou `flutter_test`) para preparar o ambiente antes de cada teste. O `tearDown()` pode ser usado para limpar o estado após cada teste. No entanto, o `setUp` deve ser para setup de **estado comum**, não para realizar a "ação" principal do teste em si.
  * **Extraia Lógicas de Validação Complexas**: Se a validação de um único comportamento exigir muitas linhas de `expect`, considere criar um "matcher" personalizado (`Matcher` em `package:test`) ou um método auxiliar para encapsular essa lógica.

-----

## 🌟 Exceções e Casos Especiais

Em alguns contextos, testar múltiplos aspectos pode ser aceitável ou mesmo necessário:

  * **Testes de Componente/Widget (Flutter)**: Ao testar um `Widget` complexo no Flutter, pode ser razoável ter várias expectativas (`find.byType`, `find.text`, `expect(widget.property, ...)` ) dentro do mesmo teste se elas verificarem a **consistência de um único estado visual** após uma interação do usuário. O foco ainda é um "comportamento" (a renderização correta ou a reação à interação).
  * **Testes de Integração ou End-to-End**: Nesses tipos de teste, onde a complexidade inerente do fluxo de usuário é maior, pode ser justificável ter mais passos e mais expectativas para validar uma jornada completa. Mesmo assim, a clareza e a modularidade devem ser mantidas.

A chave é sempre perguntar: "Este teste está verificando uma única preocupação/comportamento principal, ou está tentando verificar muitas coisas que poderiam ser testadas de forma independente?"

-----

## 🛠 Ferramentas de Detecção

  * **Analisadores Estáticos de Código (Linters)**: Ferramentas como `dart analyze` (com pacotes de lint como `lints` ou `flutter_lints`) podem ser configuradas para alertar sobre métodos de teste excessivamente longos ou com um alto número de linhas, o que pode ser um indicativo de um **Eager Test**.
  * **Métricas de Complexidade Ciclomática**: Ferramentas de análise de código que calculam a complexidade ciclompática podem apontar para testes muito complexos.
  * **Refatoração Manual e Revisão de Código**: Embora ferramentas automatizadas possam ajudar, a melhor prática é a revisão regular do código de teste para garantir que ele seja focado, conciso e claro.

-----

## 📚 Referências e Estudos Relacionados

  * Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
  * Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
  * Van Deursen, A., et al. (2001). "Refactoring Test Code."

-----

## 📝 Nota

O **Eager Test** é um problema comum quando os testes não são bem projetados ou quando há pressa em criar testes abrangentes. Adotar o princípio de **"um teste, um comportamento"** (ou "um teste, uma assertiva") pode evitar esse problema e resultar em uma suíte de testes mais eficaz, fácil de manter e que serve como uma documentação clara e confiável do seu código Dart.
---

## 📚 Referências e Estudos Relacionados
- Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
- Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
- Van Deursen, A., et al. (2001). "Refactoring Test Code."
