# Residual State Test

## ✅ Descrição do Problema

O **Residual State Test** ocorre quando os testes deixam estados residuais em componentes ou serviços testados, como widgets ou instâncias de gerenciamento de estado. Esse problema pode levar a falhas intermitentes, testes não confiáveis ou dependências inesperadas entre os casos de teste.

---

## ✅ Sintomas e Impacto

- **Sintomas**:
  - Testes que falham ou têm comportamento inconsistente dependendo da ordem de execução.
  - Mensagens de erro indicando que widgets ou serviços ainda estão ativos.
  - Acúmulo de listeners ou streams que não foram fechados corretamente.

- **Impacto**:
  - Aumenta a dificuldade de depuração.
  - Reduz a confiabilidade e a independência dos testes.
  - Pode causar vazamentos de memória devido a recursos não liberados.

---

## ✅ Critérios de Identificação

- Widgets ou serviços que possuem ciclos de vida não encerrados (ex.: `Stream`, `Future`, ou `Controller`).
- Não utilização de métodos como `dispose()` em testes de widgets.
- Depuração mostra acúmulo de listeners ou objetos não coletados.

---

## ✅ Exemplo de Código

### Exemplo com o Residual State Test

Arquivo: `residual_state_test_with_smell.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';

void main() {
  testWidgets('Test com residual state', (WidgetTester tester) async {
    // Configuração inicial do widget com controlador
    final controller = TextEditingController();
    await tester.pumpWidget(MaterialApp(
      home: Scaffold(
        body: TextField(controller: controller),
      ),
    ));

    // Simula entrada de texto
    await tester.enterText(find.byType(TextField), 'Flutter');
    expect(controller.text, 'Flutter');

    // Teste termina sem encerrar o controlador
  });

  testWidgets('Outro teste que falha devido ao estado residual', (WidgetTester tester) async {
    // Reutilização do controlador do teste anterior pode levar a falha
    final controller = TextEditingController();
    expect(controller.text.isEmpty, true); // Pode falhar por conta do estado residual
  });
}
```

### Exemplo sem o Residual State Test

Arquivo: `residual_state_test_without_smell.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';

void main() {
  testWidgets('Test sem residual state', (WidgetTester tester) async {
    // Configuração inicial do widget com controlador
    final controller = TextEditingController();
    await tester.pumpWidget(MaterialApp(
      home: Scaffold(
        body: TextField(controller: controller),
      ),
    ));

    // Simula entrada de texto
    await tester.enterText(find.byType(TextField), 'Flutter');
    expect(controller.text, 'Flutter');

    // Encerra o controlador ao final do teste
    controller.dispose();
  });

  testWidgets('Outro teste que é independente', (WidgetTester tester) async {
    // Novo controlador criado sem dependência de estados anteriores
    final controller = TextEditingController();
    expect(controller.text.isEmpty, true);
    controller.dispose();
  });
}
```

---

## ✅ Correções Sugeridas

- Sempre utilize métodos como `dispose()` para encerrar widgets ou serviços, como `TextEditingController` e `StreamController`.
- Configure o ambiente de teste adequadamente antes e depois de cada teste usando o `setUp` e o `tearDown`.
- Evite reutilizar instâncias compartilhadas entre testes, criando instâncias novas para cada caso.

---

## ✅ Exceções e Casos Especiais

- Em alguns casos, frameworks de teste gerenciam automaticamente os estados de widgets e recursos. No entanto, é uma boa prática explicitamente encerrar instâncias críticas.
- Ao testar dependências externas (como bancos de dados simulados), certifique-se de reiniciá-las entre os testes.

---

## ✅ Ferramentas de Detecção

- **Flutter Test Debugger**: pode ajudar a identificar acumuladores de estado.
- **Dart DevTools**: detecta vazamentos de memória ou instâncias não descartadas.
- Logs customizados para verificar listeners ou streams não encerrados.

---

## ✅ Referências e Estudos Relacionados

- [Documentação oficial do Flutter sobre Ciclo de Vida](https://docs.flutter.dev/)
- Artigo: *"Effective Widget Testing in Flutter"* - [Medium](https://medium.com/)
- Livro: *"Test Smells in Dart and Flutter"* (edição acadêmica)

---

## ✅ Nota

Esse problema é particularmente importante para aplicações Flutter que dependem de alto desempenho e gerenciamento eficiente de recursos. Uma abordagem disciplinada ajuda a garantir testes mais confiáveis e independentes.

---

Caso precise de ajustes ou tenha dúvidas, posso ajudar!
