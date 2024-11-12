# Magic Number Test

## 🔍 Descrição do Problema
O **Magic Number Test** ocorre quando números específicos são usados diretamente nos testes sem uma explicação clara do seu significado. Esses "números mágicos" tornam o teste mais difícil de ler e entender, pois é necessário inferir o propósito de cada valor diretamente do contexto. Esse problema reduz a clareza e a manutenção dos testes, tornando-os menos robustos e mais propensos a erros.

---

## ⚠️ Sintomas e Impacto
- **Dificuldade de Entendimento**: Outros desenvolvedores têm dificuldade para entender o propósito dos valores específicos usados nos testes.
- **Alta Manutenção**: Se os valores específicos mudarem, os testes podem precisar de atualizações em múltiplos locais, aumentando a chance de erros.
- **Redução de Flexibilidade**: Valores "mágicos" podem dificultar a adaptação do código a mudanças nos requisitos, pois o número exato precisa ser localizado e ajustado no código.

---

## 🔑 Critérios de Identificação
Para identificar o **Magic Number Test**, observe:
- Testes que utilizam números diretamente, sem variáveis ou constantes nomeadas que expliquem seu significado.
- Valores numéricos arbitrários usados repetidamente em múltiplos locais de um teste sem explicação.

### Detecção Automática
Ferramentas de análise estática podem sinalizar valores numéricos que aparecem sem explicação ou documentação, especialmente se usados de forma repetida.

---

## ✅ Exemplo de Código

### Exemplo com Magic Number Test

```dart
import 'package:test/test.dart';

void main() {
  test('Calculate Discount with Magic Numbers', () {
    final discount = calculateDiscount(150);  // 150 é um número mágico
    expect(discount, equals(20));  // 20 é um número mágico
  });
}

int calculateDiscount(int basePrice) {
  return basePrice * 0.1;  // Calcula o desconto com um valor fixo
}

```

### Exemplo sem Magic Number Test

```dart
import 'package:test/test.dart';

void main() {
  test('Calculate Discount without Magic Numbers', () {
    const int basePrice = 150;  // Definição clara do preço base
    const int expectedDiscount = 20;  // Definição clara do desconto esperado
    
    final discount = calculateDiscount(basePrice);
    expect(discount, equals(expectedDiscount), "Discount should be 20 for base price of 150");
  });
}

int calculateDiscount(int basePrice) {
  const double discountRate = 0.1;  // Taxa de desconto bem definida
  return (basePrice * discountRate).toInt();  // Aplica o desconto baseado na taxa
}

```

---

## 🚀 Correções Sugeridas
Para resolver o problema de **Magic Number Test**:

- **Use Constantes Nomeadas**: Defina os valores em constantes com nomes descritivos para indicar o propósito de cada número.
- **Documente o Propósito dos Valores**: Inclua um comentário ou mensagem que explique por que valores específicos são esperados no teste.
- **Refatore Valores Repetidos**: Em casos de valores repetidos em múltiplos testes, considere usar uma constante global ou definir valores em uma configuração de teste.

---

## 🌟 Exceções e Casos Especiais
Em alguns testes triviais, onde o número é autoexplicativo e não representa um valor especial (por exemplo, `0` para inicialização), o uso direto pode ser aceitável. No entanto, qualquer valor com significado específico deve ser nomeado.

---

## 🛠 Ferramentas de Detecção
- **Linters Configuráveis**: Ferramentas como `dart analyze` podem ser ajustadas para detectar valores numéricos não documentados.
- **Ferramentas de Análise de Código**: Ferramentas como **SonarQube** ajudam a identificar e sinalizar o uso de números mágicos no código.

---

## 📚 Referências e Estudos Relacionados
- Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
- Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
- Martin, R. C. (2008). *Clean Code: A Handbook of Agile Software Craftsmanship*

---

## 📝 Nota
A remoção de números mágicos em testes ajuda a tornar o código mais legível e fácil de manter, especialmente em sistemas que exigem alta confiabilidade.
