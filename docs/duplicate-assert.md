# Duplicate Assert

## 🔍 Descrição do Problema

**Duplicate Assert** ocorre quando um teste em Dart contém múltiplas **expectativas** (`expect`) que validam a mesma condição ou o mesmo comportamento. Isso pode indicar um código de teste redundante, onde a verificação de uma única condição é repetida, aumentando a complexidade do teste sem trazer benefícios adicionais.

O **Duplicate Assert** compromete a clareza do teste e pode dificultar a manutenção, uma vez que a alteração de uma das expectativas pode não ser refletida na outra, resultando em inconsistências e uma falsa sensação de segurança.

-----

## ⚠️ Sintomas e Impacto

  * **Redundância no Código**: O teste fica mais longo e difícil de entender, sem adicionar valor real, pois valida as mesmas condições várias vezes.
  * **Manutenção Dificultada**: Modificações em uma das condições podem ser negligenciadas ou esquecidas em outras expectativas duplicadas, levando a testes que passam, mas não detectam falhas reais, ou a testes que falham por inconsistência interna.
  * **Poluição no Código**: Expectativas duplicadas adicionam ruído ao código de teste, dificultando a leitura e a compreensão do que está sendo realmente validado.

-----

## 🔑 Critérios de Identificação

Para identificar o **Duplicate Assert**, procure por:

  * Testes que contenham múltiplas expectativas (`expect`) que verificam a mesma variável, propriedade, ou condição de forma idêntica e redundante.
  * Condições ou comportamentos que são testados mais de uma vez dentro do mesmo método de teste, sem um propósito claro para a repetição (como verificar a imutabilidade após uma operação, que seria um cenário válido).

-----

## ✅ Exemplo de Código

### Exemplo com Duplicate Assert

```dart
import 'package:flutter_test/flutter_test.dart';

class User {
  final String name;

  User({
    required this.name
  });
}

void main() {
  test('Teste de Nome de Usuário com Expectativas Duplicadas', () {
    var user = User(name: "John");

    expect(user.name, equals("John")); // Primeira verificação
    expect(user.name, equals("John")); // Expectativa redundante, verifica a mesma coisa
    // Uma falha na primeira já invalidaria o teste, a segunda não agrega valor
  });
}
```

### Exemplo sem Duplicate Assert

```dart
import 'package:flutter_test/flutter_test.dart';

class User {
  final String name;

  User({
    required this.name
  });
}

void main() {
  test('Teste de Nome de Usuário Único', () {
    var user = User(name: "John");

    expect(user.name, equals("John"), reason: "O nome do usuário deve ser 'John'");
    // Uma única expectativa clara e com uma boa razão é suficiente.
  });
}
```

-----

## 🚀 Correções Sugeridas

Para resolver o **Duplicate Assert**:

  * **Remova Expectativas Redundantes**: Verifique se o teste já valida corretamente a condição e remova qualquer `expect` duplicado. Uma única expectativa para uma única condição ou comportamento é geralmente suficiente.
  * **Centralize a Verificação**: Ao invés de repetir a mesma verificação, realize-a uma única vez de forma clara e compreensível. Se houver diferentes aspectos de um mesmo dado a serem verificados (ex: `list.length` e `list[0]`), eles devem ser expectativas distintas, não duplicatas.
  * **Refatore o Código de Teste**: Se você se sentir tentado a duplicar expectativas porque a lógica de validação é complexa ou o estado é modificado, considere refatorar o código sob teste ou o próprio teste. Isso pode envolver dividir o teste em múltiplos testes menores (se houver diferentes comportamentos a serem testados), ou usar métodos auxiliares para preparar o estado ou encapsular lógicas de verificação mais complexas.

-----

## 🌟 Exceções e Casos Especiais

Em alguns casos muito específicos, a validação de uma condição similar em diferentes pontos do teste **pode ser justificada se houver uma mudança de estado intermediária**. Por exemplo:

  * Validar um estado antes e depois de uma operação para garantir que o estado esperado foi alterado e o estado inicial não foi afetado.
  * Em testes de fluxo (integration tests), onde o mesmo dado pode ser verificado em diferentes etapas para garantir sua persistência ou transformação correta.

No entanto, a duplicação pura, onde a mesma expectativa é feita no mesmo ponto sem uma alteração intermediária no objeto de teste ou no seu estado, sem uma razão lógica clara, deve ser evitada.

-----

## 🛠 Ferramentas de Detecção

  * **Analisadores Estáticos de Código (Linters)**: Ferramentas como `dart analyze` podem ser configuradas para identificar padrões de código que sugerem expectativas duplicadas.
  * **Plugins de Test Smells**: Ferramentas de análise de código estática como **SonarQube** (com suas regras para Dart) podem identificar repetições de expectativas e sugerir melhorias.

-----

## 📚 Referências e Estudos Relacionados

  * Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
  * Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
  * Van Deursen, A., et al. (2001). "Refactoring Test Code."

-----

## 📝 Nota

O **Duplicate Assert** pode ser encontrado frequentemente em testes mal estruturados ou escritos apressadamente. É um problema que pode ser facilmente resolvido, garantindo que os testes sejam mais claros, concisos e eficientes. Este guia ajuda a refatorar testes redundantes, melhorando a qualidade do código de testes em Dart.
