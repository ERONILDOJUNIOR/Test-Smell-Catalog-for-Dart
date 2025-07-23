# Conditional Test Logic

## 🔍 Descrição do Problema

**Conditional Test Logic** ocorre quando métodos de teste em Dart contêm estruturas condicionais (como `if`, `switch`, `for`, `while`) que podem alterar o comportamento do teste. Esse uso de lógica condicional reduz a previsibilidade do teste, dificultando a identificação de falhas e podendo ocultar defeitos no código de produção, já que alguns caminhos do teste podem não ser executados.

-----

## ⚠️ Sintomas e Impacto

  * **Inconsistência nos Resultados**: Como o teste depende de condições, pode produzir resultados diferentes em execuções distintas.
  * **Dificuldade de Manutenção**: A lógica condicional dificulta a interpretação do teste e aumenta a complexidade, tornando-o mais difícil de manter.
  * **Cobertura de Testes Comprometida**: Alguns trechos de código de produção podem não ser testados se as condições não forem atendidas.

-----

## 🔑 Critérios de Identificação

Para identificar o **Conditional Test Logic**, verifique:

  * Métodos de teste que contêm estruturas de controle de fluxo, como `if`, `switch`, `for`, `while`, etc.

-----

## ✅ Exemplo de Código

### Exemplo com Conditional Test Logic

```dart
import 'package:flutter_test/flutter_test.dart';

class ShoppingCart {
  final double totalAmount;
  double discount = 0;

  ShoppingCart(this.totalAmount);

  void applyDiscount() {
    if (totalAmount < 0) {
      throw ArgumentError('Amount cannot be negative');
    }
    discount = totalAmount > 100 ? 0.1 : 0;
  }
}

void main() {
  test('Calcula o desconto com lógica condicional', () {
    final cart = ShoppingCart(50);
    cart.applyDiscount();
    
    // Lógica condicional dentro do teste, tornando-o menos previsível
    if (cart.totalAmount > 100) {
      expect(cart.discount, 0.1); // Só é executado se a condição for verdadeira
    } else {
      expect(cart.discount, 0); // Pode passar sem verificar todas as possibilidades
    }
  });
}
```

### Exemplo sem Conditional Test Logic

```dart
import 'package:flutter_test/flutter_test.dart';

void main() {
  test('Calcula desconto para valor acima de 100', () {
    final cart = ShoppingCart(150); // Cenário específico
    cart.applyDiscount();
    expect(cart.discount, 0.1, reason: "Desconto deve ser 10% para valores acima de 100");
  });

  test('Calcula desconto para valor igual ou abaixo de 100', () {
    final cart = ShoppingCart(50); // Outro cenário específico
    cart.applyDiscount();
    expect(cart.discount, 0, reason: "Desconto deve ser 0 para valores iguais ou abaixo de 100");
  });

  // A classe ShoppingCart precisa estar definida ou importada
  // para que os testes funcionem. Para este exemplo, a colocamos aqui:
  // class ShoppingCart {
  //   final double totalAmount;
  //   double discount = 0;
  //
  //   ShoppingCart(this.totalAmount);
  //
  //   void applyDiscount() {
  //     if (totalAmount < 0) {
  //       throw ArgumentError('Amount cannot be negative');
  //     }
  //     discount = totalAmount > 100 ? 0.1 : 0;
  //   }
  // }
}

// Em um projeto Dart real, a classe ShoppingCart estaria em seu próprio arquivo
// e seria importada onde necessário. Para que os exemplos de código sejam autocontidos,
// a classe ShoppingCart é incluída aqui.
class ShoppingCart {
  final double totalAmount;
  double discount = 0;

  ShoppingCart(this.totalAmount);

  void applyDiscount() {
    if (totalAmount < 0) {
      throw ArgumentError('Amount cannot be negative');
    }
    discount = totalAmount > 100 ? 0.1 : 0;
  }
}
```

-----

## 🚀 Correções Sugeridas

Para resolver o **Conditional Test Logic**:

  * **Remova Condicionais dos Testes**: Divida os cenários condicionais em testes separados, cada um cobrindo um caso específico. Isso garante que cada `test` foca em uma única condição ou caminho de execução.
  * **Mantenha Testes Simples e Lineares**: Testes devem ser diretos, garantindo que cada execução passe pelo mesmo fluxo e validações. O objetivo é que o teste seja previsível e fácil de entender.
  * **Use Mocking para Contextos Específicos**: Para dependências externas ou contextos complexos, configure o teste com valores específicos usando `mockito` ou outras ferramentas de mock, evitando depender de condições internas que variem.

-----

## 🌟 Exceções e Casos Especiais

Para testes de lógica complexa onde múltiplas condições são inevitáveis no código de produção, **não no teste**, considere utilizar técnicas de **parametrização de testes** (se a estrutura de teste suportar, como alguns frameworks em outras linguagens, embora menos comum diretamente em `package:test` ou `flutter_test`) e **mocks** para controlar o fluxo e reduzir a complexidade dos testes. O foco deve ser sempre isolar as condições no código testado, não no teste em si.

-----

## 🛠 Ferramentas de Detecção

  * **Linters e Analisadores de Código**: Ferramentas como `dart analyze` (com regras de lint adicionais como as do pacote `lints` ou `pedantic`) podem ser configuradas para detectar estruturas condicionais (`if`, `for`, `while`, `switch`) dentro dos métodos de teste.
  * **Plugins de Test Smells**: Ferramentas de análise de código estática como **SonarQube** podem ser adaptadas ou configuradas com regras personalizadas para identificar o uso de estruturas de controle em testes Dart.

-----

## 📝 Nota

**Conditional Test Logic** é uma prática comum, mas que reduz a confiabilidade dos testes, pois qualquer alteração na condição interna pode alterar a cobertura ou o comportamento do teste, tornando-o menos eficaz. Evitar essa prática leva a testes mais robustos, fáceis de depurar e manter.

-----

## 📚 Referências e Estudos Relacionados

  * Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
  * Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
  * Van Deursen, A., et al. (2001). "Refactoring Test Code."
