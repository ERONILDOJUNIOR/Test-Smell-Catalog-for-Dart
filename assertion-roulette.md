# Assertion Roulette

## 🔍 Descrição do Problema
**Assertion Roulette** ocorre quando um método de teste contém múltiplas afirmações (`asserts`) sem uma mensagem descritiva ou contexto adequado. Isso torna difícil identificar qual assert falhou e por quê, prejudicando a legibilidade e a manutenção do teste.

Em outras palavras, o **Assertion Roulette** ocorre quando o teste "joga a roleta" com o desenvolvedor, deixando-o adivinhar qual afirmação falhou.

---

## ⚠️ Sintomas e Impacto
- **Dificuldade de Depuração**: Quando uma afirmação falha, o motivo exato não fica claro, especialmente se várias afirmações estão presentes.
- **Redução da Manutenção**: Esse problema pode dificultar o trabalho de outros desenvolvedores ao tentar entender o teste, aumentando o custo de manutenção.
- **Baixa Legibilidade**: O código se torna confuso, dificultando a identificação das condições testadas.

---

## 🔑 Critérios de Identificação
Para identificar o **Assertion Roulette**, procure por:
- Métodos de teste que contenham múltiplas afirmações (`assert`) sem mensagens descritivas.
- Testes em que a falha não indica claramente qual condição específica foi violada.

### Detecção Automática
Ferramentas de análise estática e linters podem ser configuradas para verificar métodos de teste com múltiplas afirmações sem mensagens de contexto. 

---

## ✅ Exemplo de Código

### Exemplo com Assertion Roulette

```dart
void testCalculateTotalPrice() {
  final cart = ShoppingCart();
  cart.add(Item(price: 10));
  cart.add(Item(price: 20));

  assert(cart.totalPrice == 30);
  assert(cart.totalItems == 2);
  assert(cart.isValid);  // Falha possível sem explicação clara
}
```
### Exemplo sem Assertion Roulette

```dart
void testCalculateTotalPrice() {
  final cart = ShoppingCart();
  cart.add(Item(price: 10));
  cart.add(Item(price: 20));

  assert(cart.totalPrice == 30, "Total price should be 30 after adding two items");
  assert(cart.totalItems == 2, "Total items should be 2 after adding two items");
  assert(cart.isValid, "Cart should be valid after adding valid items");
}
```

