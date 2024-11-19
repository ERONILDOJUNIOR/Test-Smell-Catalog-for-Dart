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

class CustomWidget extends StatelessWidget {
  final String title;
  final int count;

  const CustomWidget({required this.title, required this.count, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(title: Text(title)),
        body: Center(
          child: Text('Count: $count'),
        ),
      ),
    );
  }
}

void main() {
  testWidgets('Test with repeated widget setup - Case 1', (WidgetTester tester) async {
    await tester.pumpWidget(const CustomWidget(title: 'Test Title 1', count: 10));

    expect(find.text('Test Title 1'), findsOneWidget);
    expect(find.text('Count: 10'), findsOneWidget);
  });

  testWidgets('Test with repeated widget setup - Case 2', (WidgetTester tester) async {
    await tester.pumpWidget(const CustomWidget(title: 'Test Title 2', count: 20));

    expect(find.text('Test Title 2'), findsOneWidget);
    expect(find.text('Count: 20'), findsOneWidget);
  });
}
```

### Exemplo sem Widget Setup Smell

Arquivo: `widget_setup_smell_without_smell.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';

class CustomWidget extends StatelessWidget {
  final String title;
  final int count;

  const CustomWidget({required this.title, required this.count, Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(title: Text(title)),
        body: Center(
          child: Text('Count: $count'),
        ),
      ),
    );
  }
}

void main() {
  Future<void> pumpCustomWidget(WidgetTester tester, String title, int count) async {
    await tester.pumpWidget(CustomWidget(title: title, count: count));
  }

  testWidgets('Test using helper method - Case 1', (WidgetTester tester) async {
    await pumpCustomWidget(tester, 'Test Title 1', 10);

    expect(find.text('Test Title 1'), findsOneWidget);
    expect(find.text('Count: 10'), findsOneWidget);
  });

  testWidgets('Test using helper method - Case 2', (WidgetTester tester) async {
    await pumpCustomWidget(tester, 'Test Title 2', 20);

    expect(find.text('Test Title 2'), findsOneWidget);
    expect(find.text('Count: 20'), findsOneWidget);
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
