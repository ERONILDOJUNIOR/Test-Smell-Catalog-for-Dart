# Redundant Assertion

## 🔍 Descrição do Problema
**Redundant Assertion** ocorre quando um método de teste contém assertions cujos resultados são sempre verdadeiros ou sempre falsos. Um teste deve retornar um resultado binário indicando se o resultado pretendido está correto ou não, e não deve retornar a mesma saída independentemente da entrada. Além disso, afirmações redundantes podem reduzir a clareza e o desempenho dos testes, pois introduzem verificações desnecessárias.

---

## ⚠️ Sintomas e Impacto
- **Desempenho Degradado**: Afirmações duplicadas podem aumentar o tempo de execução dos testes, especialmente em grandes conjuntos de testes.
- **Manutenção Dificultada**: Testes com lógica redundante são mais difíceis de atualizar, uma vez que qualquer alteração precisa ser feita em múltiplas afirmações semelhantes.

---

## 🔑 Critérios de Identificação
Para identificar o **Redundant Assertion**, procure por:
- Teste contém assertions cujos resultados são sempre verdadeiros ou sempre falsos
- Afirmações que verificam a mesma condição várias vezes dentro do mesmo teste.

---

## ✅ Exemplo de Código

### Exemplo com Redundant Assertion

```dart
import 'package:test/test.dart';

void main() {
  final cart = ShoppingCart();
  cart.add(Item(price: 10));
  cart.add(Item(price: 20));

  test('Redundant Assertion Test 01', () {
    expect(cart.getTotalPrice(), equals(30));
    expect(cart.getTotalPrice(), equals(30));  // Repetição desnecessária
  });

  test('Redundant Assertion Test 02', () {
    expect(cart.getTotalItems(), equals(2));
    expect(cart.getTotalItems(), equals(2));  // Repetição desnecessária
  });
}

class ShoppingCart {
  final List<Item> items = [];

  void add(Item item) {
    items.add(item);
  }

  int getTotalItems() {
    return items.length;
  }

  double getTotalPrice() {
    double total = 0;

    for (var item in items) {
      total += item.price; 
    }

    return total;
  }
}

class Item {
  final double price;

  Item({
    required this.price
  });
}

```

### Exemplo sem Redundant Assertion

```dart
import 'package:test/test.dart';

void main() {
  final cart = ShoppingCart();
  cart.add(Item(price: 10));
  cart.add(Item(price: 20));

  test('Redundant Assertion Test 01', () {
    expect(cart.getTotalPrice(), equals(30));
  });

  test('Redundant Assertion Test 02', () {
    expect(cart.getTotalItems(), equals(2));
  });
}

class ShoppingCart {
  final List<Item> items = [];

  void add(Item item) {
    items.add(item);
  }

  int getTotalItems() {
    return items.length;
  }

  double getTotalPrice() {
    double total = 0;

    for (var item in items) {
      total += item.price; 
    }

    return total;
  }
}

class Item {
  final double price;

  Item({
    required this.price
  });
}

```

---

## 🚀 Correções Sugeridas
Para resolver o **Redundant Assertion**:

- **Remover Afirmações Duplicadas**: Exclua asserts redundantes que verificam a mesma condição.
- **Consolidar Verificações**: Combine as condições de verificação em uma única afirmação quando apropriado.

---

## 🌟 Exceções e Casos Especiais
Em alguns cenários de teste com lógica complexa, afirmações semelhantes podem ser necessárias para verificar estados em diferentes etapas do processo. No entanto, essas exceções devem ser cuidadosamente documentadas.

---

## 🛠 Ferramentas de Detecção
- **Linters e Ferramentas de Análise de Código**: Ferramentas como `dart analyze` e SonarQube podem ser configuradas para identificar afirmações duplicadas.
- **Code Smell Detectors**: Ferramentas especializadas em detectar code smells em testes, como SonarQube, podem ser usadas para detectar redundâncias.

---

## 📝 Nota
O **Redundant Assertion** pode ser especialmente problemático em grandes conjuntos de testes. Remover afirmações duplicadas ajuda a manter o código de teste limpo e eficiente.

---

## 📚 Referências e Estudos Relacionados
- Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
- Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
- Van Deursen, A., et al. (2001). "Refactoring Test Code."

