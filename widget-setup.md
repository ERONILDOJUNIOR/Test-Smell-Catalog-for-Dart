### Widget Setup Smell

#### Descrição do Problema  
O "Widget Setup Smell" ocorre quando a configuração de widgets complexos é repetida ou realizada de forma inadequada nos testes, impactando o desempenho, a clareza e a manutenibilidade. Esse smell é comum no contexto de Flutter, onde widgets são a base da construção de interfaces de usuário e frequentemente precisam ser configurados com dados específicos para os testes.

#### Sintomas e Impacto  
- **Repetição desnecessária**: Configurações idênticas ou muito semelhantes para widgets em múltiplos testes.  
- **Desempenho reduzido**: Configurações pesadas de widgets podem aumentar o tempo de execução dos testes.  
- **Legibilidade comprometida**: Difícil entender o que está sendo testado devido à configuração extensa.  
- **Manutenção dificultada**: Alterações no widget ou na configuração exigem mudanças em vários locais.  

---

#### Critérios de Identificação  
- A configuração de widgets está duplicada em vários testes.  
- Testes incluem configurações longas que poderiam ser simplificadas ou centralizadas.  
- Não há uso de métodos auxiliares, `setUp` ou factories para abstrair a configuração de widgets.  

---

#### Exemplo com Widget Setup Smell  
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

---

#### Exemplo sem Widget Setup Smell  
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

#### Correções Sugeridas  
- **Criar métodos auxiliares**: Encapsule configurações repetitivas em métodos reutilizáveis.  
- **Utilizar `setUp` ou factories**: Centralize configurações comuns para uso em múltiplos testes.  
- **Reduzir complexidade**: Simplifique os parâmetros e a lógica de configuração de widgets.  

---

#### Exceções e Casos Especiais  
- Configurações únicas ou altamente específicas para um único teste não são consideradas smells.  
- Testes que validam diretamente as configurações de widgets podem justificar duplicações mínimas.  

---

#### Ferramentas de Detecção  
- Revisão manual do código é necessária para identificar configurações repetitivas ou excessivas.  
- Ferramentas de lint podem sugerir a redução de duplicações.  

---

#### Referências e Estudos Relacionados  
- Documentação oficial do [Flutter Test](https://docs.flutter.dev/cookbook/testing/widget/introduction)  
- Artigos sobre design de testes e redução de duplicação em Flutter  

---

#### Nota  
Evitar o "Widget Setup Smell" melhora a legibilidade e facilita a manutenção dos testes, além de reduzir o tempo de execução, especialmente em projetos grandes.
