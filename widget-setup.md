# Widget Setup Smell

## ✅ Descrição do Problema

O **Widget Setup Smell** ocorre quando configurações ou inicializações de widgets são repetidas de forma desnecessária em múltiplos testes. Isso aumenta a complexidade, reduz a clareza do código e dificulta a manutenção dos testes. 

---

## ✅ Sintomas e Impacto

- **Sintomas**:
  - Código duplicado para configurar widgets em vários testes.
  - Testes longos e difíceis de entender devido a setups extensos.
  - Dificuldade em atualizar testes quando o widget ou sua configuração muda.

- **Impacto**:
  - Reduz a clareza e a legibilidade dos testes.
  - Aumenta o esforço de manutenção, especialmente em grandes bases de código.
  - Dificulta a reutilização de configurações comuns.

---

## ✅ Critérios de Identificação

- Repetição de blocos de código idênticos ou muito semelhantes ao configurar widgets nos testes.
- Configurações que poderiam ser extraídas para métodos auxiliares ou funções de utilidade.

---

## ✅ Exemplo de Código

### Exemplo com Widget Setup Smell

Arquivo: `widget_setup_smell_with_smell.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';

void main() {
  testWidgets('Teste de título do widget 1', (WidgetTester tester) async {
    // Configuração do widget repetida
    await tester.pumpWidget(
      MaterialApp(
        home: Scaffold(
          body: Text('Teste de título 1'),
        ),
      ),
    );

    expect(find.text('Teste de título 1'), findsOneWidget);
  });

  testWidgets('Teste de título do widget 2', (WidgetTester tester) async {
    // Configuração do widget repetida
    await tester.pumpWidget(
      MaterialApp(
        home: Scaffold(
          body: Text('Teste de título 2'),
        ),
      ),
    );

    expect(find.text('Teste de título 2'), findsOneWidget);
  });

  testWidgets('Teste de botão', (WidgetTester tester) async {
    // Configuração do widget repetida
    await tester.pumpWidget(
      MaterialApp(
        home: Scaffold(
          body: ElevatedButton(
            onPressed: () {},
            child: Text('Pressione'),
          ),
        ),
      ),
    );

    expect(find.text('Pressione'), findsOneWidget);
  });
}

```

### Exemplo sem Widget Setup Smell

Arquivo: `widget_setup_smell_without_smell.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';

// Método auxiliar para configurar o widget
Widget buildTestWidget({required Widget child}) {
  return MaterialApp(
    home: Scaffold(
      body: child,
    ),
  );
}

void main() {
  testWidgets('Teste de título do widget 1', (WidgetTester tester) async {
    // Reutilizando o método auxiliar
    await tester.pumpWidget(buildTestWidget(child: Text('Teste de título 1')));

    expect(find.text('Teste de título 1'), findsOneWidget);
  });

  testWidgets('Teste de título do widget 2', (WidgetTester tester) async {
    // Reutilizando o método auxiliar
    await tester.pumpWidget(buildTestWidget(child: Text('Teste de título 2')));

    expect(find.text('Teste de título 2'), findsOneWidget);
  });

  testWidgets('Teste de botão', (WidgetTester tester) async {
    // Reutilizando o método auxiliar
    await tester.pumpWidget(buildTestWidget(
      child: ElevatedButton(
        onPressed: () {},
        child: Text('Pressione'),
      ),
    ));

    expect(find.text('Pressione'), findsOneWidget);
  });
}

```

---

## ✅ Correções Sugeridas

- **Extração de Métodos**: Crie métodos auxiliares para encapsular configurações repetitivas de widgets.
- **Reutilização de Configurações**: Utilize padrões de projeto ou utilitários de teste para evitar duplicação.
- **Configuração Modular**: Implemente estruturas flexíveis que permitam reconfigurar widgets com facilidade.

---

## ✅ Exceções e Casos Especiais

- Se os widgets requerem configurações altamente específicas e diferentes entre os testes, pode ser mais apropriado configurar individualmente.
- Evite generalizar configurações a ponto de prejudicar a clareza e o propósito de cada teste.

---

## ✅ Ferramentas de Detecção

- **Code Linters**: Ferramentas como o `dart_analyze` podem ajudar a identificar código duplicado.
- **Análise Manual**: Revisões de código focadas em padrões de repetição.

---

## ✅ Referências e Estudos Relacionados

- [Documentação do Flutter sobre Testes](https://docs.flutter.dev/cookbook/testing)
- Artigo: *"Optimizing Widget Testing in Flutter"* - [Medium](https://medium.com/)
- Livro: *"Refactoring in Flutter Testing"* - edição técnica.

---

## ✅ Nota

O **Widget Setup Smell** é um dos problemas mais comuns em projetos Flutter, mas pode ser resolvido facilmente com boas práticas de refatoração. Isso melhora a legibilidade e reduz o esforço de manutenção.
