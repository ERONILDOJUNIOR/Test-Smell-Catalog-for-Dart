# Exception Handling


## 🔍 Descrição do Problema

**Exception Handling** como um *test smell* ocorre quando um método de teste em Dart depende de uma exceção ser lançada pelo código de produção, mas o teste falha em usar os recursos apropriados do framework de testes (`package:test` ou `flutter_test`) para **verificar explicitamente** essa exceção. Isso pode se manifestar pela falta de captura e verificação da exceção, ou pelo uso incorreto de blocos `try-catch` dentro do teste de forma que mascare a falha ou a torne menos legível.

Testes que não verificam corretamente as exceções podem levar a lacunas significativas na cobertura de testes e comprometem a confiança na robustez do sistema, pois comportamentos de erro esperados não estão sendo confirmados.

-----

## ⚠️ Sintomas e Impacto

  * **Cobertura Insuficiente**: A falta de tratamento adequado para exceções resulta em uma cobertura de teste incompleta, onde cenários de falha críticos não são verificados.
  * **Comportamento Inesperado Não Detectado**: Se uma exceção for ignorada ou não for verificada corretamente, comportamentos inesperados podem não ser detectados durante os testes, levando a falhas em produção.
  * **Dificuldade de Depuração**: Testes mal estruturados que não capturam ou verificam exceções de forma idiomática podem dificultar a identificação da causa raiz de falhas em sistemas complexos, pois a exceção pode "estourar" o teste de forma genérica, sem um contexto claro.
  * **Testes Frágeis**: A dependência de `try-catch` genéricos pode fazer com que o teste passe mesmo se uma exceção diferente da esperada for lançada.

-----

## 🔑 Critérios de Identificação

Para identificar o **Exception Handling** como um *test smell*, procure por:

  * Métodos de teste onde uma operação que sabidamente pode lançar uma exceção é executada sem um `expect(() => ..., throwsA(...))` ou similar.
  * Um método de teste que contém um bloco `try-catch` que não utiliza os *matchers* de exceção do framework de testes (ex: `throwsA`, `throwsFormatException`, `isA<MyCustomException>`).
  * Testes que apenas esperam que a exceção pare a execução do teste, sem verificar o *tipo* ou a *mensagem* da exceção.

-----

## ✅ Exemplo de Código

### Exemplo com Exception Handling (Test Smell)

Neste exemplo, o teste `Teste de Divisão por Zero, exemplo 03` executa uma divisão por zero, o que naturalmente lançará uma exceção de tempo de execução (`_CastError` ou `IntegerDivisionByZeroException` em Dart puro, dependendo do contexto). No entanto, o teste não tem uma expectativa explícita para essa exceção. Ele espera um resultado numérico (`expect(result, 0)`), que nunca será alcançado, e o teste falhará com um erro não tratado, em vez de passar confirmando a exceção.

```dart
import 'package:flutter_test/flutter_test.dart';

// Função de produção que pode lançar uma exceção (divisão por zero)
double divide(int a, int b) {
  // Em Dart, a divisão por zero com inteiros lançaria IntegerDivisionByZeroException
  // e com double resulta em Infinity ou NaN. Para este exemplo, vamos simular
  // um cenário que lançaria uma exceção clara para a intenção do smell.
  if (b == 0) {
    throw ArgumentError('Não é possível dividir por zero.');
  }
  return a / b;
}

void main() {
  // Testes que validam o comportamento esperado para entradas válidas
  test('Deve dividir dois números positivos corretamente', () {
    var result = divide(10, 2);
    expect(result, 5.0, reason: "10 dividido por 2 deve ser 5.0");
  });

  test('Deve dividir um número positivo por um negativo corretamente', () {
    var result = divide(10, -1);
    expect(result, -10.0, reason: "10 dividido por -1 deve ser -10.0");
  });

  test('Teste de Divisão por Zero (com smell)', () {
    // Ação que se espera que lance uma exceção
    var result = divide(10, 0); // Isso Lançará uma ArgumentError
    
    // Expectativa incorreta: o teste espera um valor, não a exceção.
    // O teste irá falhar devido à exceção não tratada, não porque a expectativa foi explicitamente violada.
    expect(result, 0.0, reason: "Esperava-se 0.0, mas uma exceção será lançada antes.");
  });
}
```

### Exemplo sem Exception Handling (Correção Idiomática em Dart)

Neste exemplo, o teste `Deve lançar ArgumentError ao dividir por zero` utiliza o *matcher* `throwsA(isA<ArgumentError>())` do `package:test` para verificar explicitamente que o código lança o tipo de exceção esperado quando a divisão por zero ocorre. Este é o modo preferencial em Dart.

```dart
import 'package:flutter_test/flutter_test.dart';

// Função de produção que pode lançar uma exceção (divisão por zero)
double divide(int a, int b) {
  if (b == 0) {
    throw ArgumentError('Não é possível dividir por zero.'); // Lança ArgumentError
  }
  return a / b;
}

void main() {
  // Testes que validam o comportamento esperado para entradas válidas
  test('Deve dividir dois números positivos corretamente', () {
    var result = divide(10, 2);
    expect(result, 5.0, reason: "10 dividido por 2 deve ser 5.0");
  });

  test('Deve dividir um número positivo por um negativo corretamente', () {
    var result = divide(10, -1);
    expect(result, -10.0, reason: "10 dividido por -1 deve ser -10.0");
  });

  test('Deve lançar ArgumentError ao dividir por zero', () {
    // Arrange & Act: O expect com throwsA espera uma função anônima que executa a ação.
    expect(
      () => divide(10, 0), // O código que esperamos que lance a exceção
      throwsA(isA<ArgumentError>()), // O matcher que verifica o tipo da exceção
      reason: "Deve lançar um ArgumentError quando o divisor é zero",
    );
  });

  // Exemplo adicional: verificando a mensagem da exceção
  test('Deve lançar ArgumentError com mensagem específica ao dividir por zero', () {
    expect(
      () => divide(10, 0),
      throwsA(
        isA<ArgumentError>()
          .having((e) => e.message, 'message', contains('Não é possível dividir por zero.')),
      ),
      reason: "A mensagem do erro deve indicar a impossibilidade de divisão por zero",
    );
  });
}
```

-----

## 🚀 Correções Sugeridas

Para resolver o **Exception Handling** como um *test smell*:

  * **Use os Matchers de Exceção do Framework**: Em Dart, utilize `expect(() => suaFuncaoQueLancaExcecao(), throwsA(isA<TipoDaExcecaoEsperada>()))`. Isso verifica que a função de fato lança uma exceção e, opcionalmente, que é do tipo esperado.
  * **Verifique o Tipo e/ou a Mensagem da Exceção**: Vá além de apenas verificar se uma exceção é lançada. Use `isA<Tipo>()` para verificar o tipo da exceção e, se necessário, `having((e) => e.message, 'message', contains('mensagem esperada'))` para verificar o conteúdo da mensagem da exceção. Isso garante que a exceção correta está sendo lançada pelo motivo certo.
  * **Evite Blocos `try-catch` no Teste para Validação**: Salvo raras exceções (como testar um comportamento de recuperação após o catch), não use `try-catch` para validar que uma exceção foi lançada. Isso mascara a intenção do teste e é menos legível do que os *matchers* de exceção do framework.

-----

## 🌟 Exceções e Casos Especiais

Em alguns casos muito específicos, um bloco `try-catch` dentro de um teste pode ser justificado, por exemplo:

  * Para testar um cenário onde o código *cliente* (dentro do teste) precisa **reagir** a uma exceção lançada pelo SUT, e você está testando essa reação.
  * Em testes de integração complexos, onde você simula uma falha em um serviço e quer verificar o fluxo de dados através de várias camadas após essa falha, não apenas se a exceção foi lançada.

No entanto, para a maioria dos testes unitários focados em validar que uma exceção é lançada sob certas condições, o uso de `expect` com `throwsA` é a abordagem correta e preferencial.

-----

## 🛠 Ferramentas de Detecção

  * **Linter Configurável**: Ferramentas como `dart analyze` (com as regras de lint do `package:lints` ou `flutter_lints`) podem ser configuradas para sinalizar o uso de `try-catch` em blocos de teste ou a ausência de `expect(..., throwsA(...))` em chamadas que sabidamente podem lançar exceções.
  * **Cobertura de Testes**: Ferramentas de cobertura de teste (`dart test --coverage`) podem ajudar a identificar cenários de falha (caminhos de código que lançam exceções) que não estão sendo exercitados por testes que verificam explicitamente essas exceções.
  * **Revisão de Código (Code Review)**: A revisão por pares é eficaz para identificar testes que esperam uma exceção, mas não a verificam de forma idiomática ou explícita.

-----

## 📝 Nota

O **Exception Handling** como um *test smell* pode comprometer seriamente a robustez de um sistema. Ignorar ou verificar incorretamente as exceções durante os testes significa que seu sistema pode falhar de maneiras inesperadas em produção. Garantir que as exceções sejam corretamente lançadas e verificadas durante os testes, utilizando as ferramentas apropriadas do framework, ajuda a prevenir falhas em produção e aumenta a confiança na qualidade do seu código Dart.
