# Redundant Print

## 🔍 Descrição do Problema
**Redundant Print** ocorre quando um método de teste contém instruções de impressão (print statements). Isso pode consumir recursos computacionais ou aumentar o tempo de execução se o desenvolvedor chamar um método de longa duração dentro da instrução de impressão. 

Embora `print` possa ser útil em alguns casos de depuração, o uso permanente e exagerado dessa prática pode dificultar a leitura e interpretação dos resultados dos testes, além de gerar saídas de console desordenadas.


---

## ⚠️ Sintomas e Impacto
- **Saída de Console Poluída**: Prints tornam a saída do console confusa e difícil de ler.
- **Má Prática**: Dependência de `print` em vez de asserts ou outras ferramentas automatizadas que fornecem feedback imediato e específico sobre o sucesso ou falha do teste.

---

## 🔑 Critérios de Identificação
Para identificar o **Redundant Print**, procure por:
- Testes que contêm vários `print` sem necessidade real.  

---

## ✅ Exemplo de Código

### Exemplo com Redundant Print

```dart
import 'package:test/test.dart';

void main() {
  test('Redundant Print Test', () {
    final cart = ShoppingCart();
    cart.add(Item(price: 10));
    cart.add(Item(price: 20));
  
    print("Total Price: ${cart.getTotalItems()}");
    print("Total Items: ${cart.getTotalItems()}");
  });
}

class ShoppingCart {
  final List<Item> items = [];

  void add(Item item) {
    items.add(item);
  }

  double getTotalPrice() {
    double total = 0;

    for (var item in items) {
      total = total + item.price;
    }
  
    return total;
  }

  int getTotalItems() {
    return items.length;
  }
}

class Item {
  final double price;

  Item({
    required this.price
  });
}
```

### Exemplo sem Redundant Print

```dart
import 'package:test/test.dart';

void main() {
  test('Calculate Total without Redundant Print', () {
    final cart = ShoppingCart();
    cart.add(Item(price: 10));
    cart.add(Item(price: 20));

    expect(cart.getTotalPrice(), equals(30), reason: "Total price should be 30 after adding items");
    expect(cart.getTotalItems(), equals(2), reason: "Total items should be 2 after adding items");
  });
}

class ShoppingCart {
  final List<Item> items = [];

  void add(Item item) {
    items.add(item);
  }

  double getTotalPrice() {
    double total = 0;

    for (var item in items) {
      total = total + item.price;
    }
  
    return total;
  }

  int getTotalItems() {
    return items.length;
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
Para resolver o **Redundant Print**:

- **Substituir `print` por Asserts**: Use asserts para verificar condições diretamente, em vez de printar valores.
- **Remover Prints Desnecessários**: Retire prints que não contribuem diretamente para a depuração ou verificação.
- **Utilizar Logging Controlado**: Em casos em que uma saída extra é necessária para depuração, considere implementar uma solução de log que possa ser habilitada ou desabilitada conforme necessário, evitando o excesso de prints nos testes.

---

## 🌟 Exceções e Casos Especiais
Em testes de desenvolvimento inicial ou casos de depuração complexa, prints temporários podem ser aceitáveis, mas devem ser removidos antes da finalização do teste.

---

## 🛠 Ferramentas de Detecção
- **Linters e Analisadores de Código**: Ferramentas como `dart analyze` e plugins de lint podem ser configurados para identificar e sinalizar o uso de `print` em arquivos de teste.
- **Plugins de Code Smell**: Ferramentas como SonarQube podem ajudar a monitorar o uso de práticas inadequadas, como o uso excessivo de prints em testes.

---

## 📝 Nota
Eliminar o **Redundant Print** dos testes ajuda a manter a saída limpa e focada nas informações relevantes, aumentando a clareza e a confiabilidade do feedback fornecido pelos testes. 

---

## 📚 Referências e Estudos Relacionados
- Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
- Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
- Van Deursen, A., et al. (2001). "Refactoring Test Code."

---
