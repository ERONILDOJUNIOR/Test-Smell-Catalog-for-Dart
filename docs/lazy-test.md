# Lazy Test

## 🔍 Descrição do Problema
O **Lazy Test** ocorre quando multiplos métodos de teste invocam o mesmo método de produção, tornando o código de teste difícil de manter, já que responsabilidades relacionadas a um método de produção são testadas em diferentes métodos de teste. **Lazy Test** fornecem uma verificação mínima e tendem a não capturar erros sutis, o que prejudica a confiabilidade e a robustez da suíte de testes.

Em geral, esses testes verificam apenas os casos mais básicos e negligenciam cenários de borda e possíveis exceções, deixando vulnerabilidades não testadas.

---

## ⚠️ Sintomas e Impacto
- **Cobertura Incompleta**: O teste cobre apenas um escopo mínimo da funcionalidade, deixando falhas potenciais não detectadas.
- **Baixa Confiabilidade**: Testes superficiais geram uma falsa sensação de segurança, pois o código pode ter comportamentos inesperados que não são validados.
- **Manutenção Deficiente**: Testes incompletos dificultam a detecção de erros quando o código é atualizado, aumentando a probabilidade de introduzir bugs.

---

## 🔑 Critérios de Identificação
Para identificar o **Lazy Test**, observe:
- Teste que chamam o mesmo método de produção muitas vezes.
- Testes que verificam apenas os casos "felizes" ou básicos, sem considerar cenários de erro ou exceção.
- Testes com apenas uma ou duas afirmações mínimas que não refletem o comportamento real esperado do sistema.

---

## ✅ Exemplo de Código

### Exemplo com Lazy Test

```dart
import 'package:flutter_test/flutter_test.dart';


void main() {
  test('Lazy Test - Adicionar Item ao Carrinho', () {
    final cart = ShoppingCart();
    cart.add(Item(price: 10));
    
    expect(cart.getTotalItems(), 1);  // Testa apenas uma coisa: o total de itens
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

  bool isValid() {
    return (getTotalItems() > 0);
  }

  bool isEmpty() {
    return items.isEmpty;
  } 
}

class Item {
  final double price;

  Item({
    required this.price
  });
}

```

### Exemplo sem Lazy Test

```dart
import 'package:flutter_test/flutter_test.dart';

void main() {
  test('Teste - getTotalITems', () {
    final cart = ShoppingCart();
    cart.add(Item(price: 10));
    
    // Verifica o total de itens
    expect(cart.getTotalItems(), 1);
  });

  test('Teste - getTotalPrice', () {
    final cart = ShoppingCart();
    cart.add(Item(price: 10));
    
    // Verifica o preço total
    expect(cart.getTotalPrice(), 10);
  });

  test('Teste - isValid', () {
    final cart = ShoppingCart();
    cart.add(Item(price: 10));
    
    // Verifica a validade do carrinho
    expect(cart.isValid(), isTrue);
  });

  test('Teste - isEmpty', () {
    final cart = ShoppingCart();
    cart.add(Item(price: 10));
    
    // Cenário de borda: verifica se o carrinho não está vazio
    expect(cart.isEmpty(), isFalse);
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

  bool isValid() {
    return getTotalItems() > 0;
  }

  bool isEmpty() {
    return items.isEmpty;
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
Para resolver o problema de **Lazy Test**:

- **Adicione Cenários de Borda e Exceção**: Verifique o comportamento do sistema em condições extremas e para diferentes valores de entrada.
- **Divida o Teste em Menores**: Refatore o teste em métodos menores, com um único foco e com a verificação menos condições por teste.
- **Reduza a Quantidade de Métodos**: Verifique se há necessidade de chamar o método de produção tantas vezes e remova quando for fútil. 

---

## 🌟 Exceções e Casos Especiais
Em funções triviais ou puras, um teste simples pode ser aceitável. No entanto, em funcionalidades com lógica de negócios, testes completos são essenciais para garantir cobertura robusta.

---

## 🛠 Ferramentas de Detecção
- **Linters Configuráveis**: Ferramentas como `dart analyze` podem ser ajustadas para sinalizar testes com poucas afirmações.
- **Ferramentas de Análise de Cobertura**: Ferramentas como **SonarQube** e **Coveralls** podem auxiliar na identificação de áreas com cobertura de teste insuficiente.

---

## 📝 Nota
Lazy Tests devem ser evitados, especialmente em componentes críticos ou complexos do sistema. Eles oferecem uma cobertura superficial e podem mascarar problemas profundos no código.

---

## 📚 Referências e Estudos Relacionados
- Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
- Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
- Martin, R. C. (2008). *Clean Code: A Handbook of Agile Software Craftsmanship*
