# Unknown Test

## 🔍 Descrição do Problema
**Unknown Test** isso ocorre quando um método de teste não contém asserções. Como resultado, o JUnit exibe o método de teste como aprovado, a menos que as instruções lancem uma exceção no método de teste. Esse tipo de teste cria uma falsa ilusão de bom funcionamento.

---

## ⚠️ Sintomas e Impacto
- **Inconsistência nos Resultados**: Como o teste depende de condições, pode produzir resultados diferentes em execuções distintas.
- **Poluição no Código**: Testes vazios adicionam ruído ao código de teste, dificultando a leitura e a compreensão do que está sendo realmente validado.

---

## 🔑 Critérios de Identificação
Para identificar o **Unknown Test**, procure por:
- Testes que não contem assertions.
- Testes que não possuem um nome ou descrição clara sobre o que estão testando.
- Testes cujos detalhes (como dados de entrada e saída) não deixam claro qual comportamento está sendo validado.

---

## ✅ Exemplo de Código

### Exemplo com Unknown Test

```dart
import 'package:flutter_test/flutter_test.dart';

void main() {
  test("Calculate Discount Test", () {
    var cart = ShoppingCart();
    cart.addItem(Item(price: 100));
    // ignore: unused_local_variable
    var discount = cart.calculateDiscount();
  
  });
} 

class ShoppingCart {
  List<Item> items = [];

  void addItem(Item item) {
    items.add(item);
  }

  double calculateDiscount() {
    double total = 0;

    for (var item in items) {
      total = total + item.price;
    }

    return (total * 0.1); // Desconto de 10%
  }
}

class Item {
  int price;

  Item({
    required this.price
  });
}

```

### Exemplo sem Unknown Test

```dart
import 'package:flutter_test/flutter_test.dart';

void main() {
  test("Calculate Discount Test", () {
    var cart = ShoppingCart();
    cart.addItem(Item(price: 100));
    var discount = cart.calculateDiscount();
    
    expect(discount, 10, reason: "Expected discount to be 10% of the total price");
  });
} 

class ShoppingCart {
  List<Item> items = [];

  void addItem(Item item) {
    items.add(item);
  }

  double calculateDiscount() {
    double total = 0;

    for (var item in items) {
      total = total + item.price;
    }

    return (total * 0.1); // Desconto de 10%
  }
}

class Item {
  int price;

  Item({
    required this.price
  });
}
```

---

## 🚀 Correções Sugeridas
Para resolver o **Unknown Test**:

- **Remova Testes Vazios**: Se um teste vazio não for mais necessário, remova-o para reduzir a poluição no código de testes.
- **Revise Periodicamente os Testes**: Durante a manutenção ou refatoração, revise os testes para garantir que todos estão fazendo algo útil.

---

## 🛠 Ferramentas de Detecção
- **Linters**: Ferramentas como `dart analyze` podem ser configuradas para detectar testes com nomes ou comportamentos imprecisos.
- **Analisadores de Teste**: Ferramentas como SonarQube podem ser configuradas para detectar testes sem descrições claras.

---

## 📝 Nota
Testes imprecisos podem aumentar significativamente o custo de manutenção do código e dificultar a compreensão do comportamento do sistema. Garantir que os testes sejam claros, focados e bem descritos ajuda a manter a qualidade e a confiabilidade do código.

---

## 📚 Referências e Estudos Relacionados
- Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
- Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
- Van Deursen, A., et al. (2001). "Refactoring Test Code."

---
