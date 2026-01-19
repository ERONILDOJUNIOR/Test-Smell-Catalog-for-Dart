# Expected Resolution Omission (ERO)

## Descrição do Problema
O **Expected Resolution Omission (ERO)** é um test smell que ocorre quando há falhas no uso correto de `await`, `expect`, ou `expectLater` em testes assíncronos no Dart. Esse problema pode levar a:

- **Testes que passam erroneamente**, mesmo com comportamentos incorretos.
- **Falhas por problemas de sincronização**, onde o código testado não é executado no momento correto.
- **Uso desnecessário de `await`** em contextos que não requerem essa palavra-chave, tornando o teste confuso e redundante.

Testes que não esperam corretamente pela resolução de um `Future` ou que utilizam `await` de forma inadequada podem mascarar erros, resultando em uma validação ineficaz do comportamento assíncrono.

---

## Sintomas e Impacto

- **Testes falsos positivos**: Testes que passam mesmo quando o comportamento esperado está incorreto.
- **Testes intermitentes**: Resultados inconsistentes devido à falta de sincronização com o código assíncrono.
- **Erros de lógica**: Uso inadequado de `await` pode causar interpretações erradas do fluxo do teste.
- **Código confuso**: Await ou matchers inapropriados dificultam a compreensão do comportamento esperado.

---

## Critérios de Identificação

1. **Expectativa sem `await` ou `expectLater`**:
   - Quando é esperado que um `Future` retorne um valor, mas não há `await` para aguardar sua resolução ou `expectLater` para validar seu comportamento.

2. **Uso de `await` em código não assíncrono**:
   - Usar `await` para valores ou operações que não são `Future`, introduzindo redundância no teste.

3. **Falta de `throwsA` ao testar exceções**:
   - Testar um `Future` que lança uma exceção sem usar o matcher `throwsA`.

4. **Uso inadequado de `completes`**:
   - Utilizar `expect` com `await` ao invés de usar `expectLater` com o matcher `completes` para verificar se um `Future` conclui sem erros.

5. **Ignorar comportamento assíncrono**:
   - Testar diretamente o objeto `Future` sem `await` ou `expectLater`, causando resultados errados ou falsos positivos.

---

## Exemplo de Código

### **Exemplo Problemático**
```dart
void testExample() {
  // Exemplo 1: Falta de await ou expectLater
  final futureResult = Future.value(42);
  expect(futureResult, equals(42)); // Falta await ou expectLater

  // Exemplo 2: Await desnecessário
  final result = 42;
  expect(await Future.value(result), equals(42)); // Await desnecessário

  // Exemplo 3: Falta de throwsA
  final futureResult = Future.error(Exception('Falhou'));
  expect(await futureResult, isA<Exception>()); // Matcher incorreto

  // Exemplo 4: Uso inadequado de completes
  final futureResult = Future.value(42);
  expect(await futureResult, completes); // Matcher incorreto

  // Exemplo 5: Ignorar comportamento assíncrono
  final futureResult = Future.delayed(Duration(seconds: 1), () => 42);
  expect(futureResult, equals(42)); // Sem await, pode passar incorretamente
}
```

### **Exemplo Corrigido**
```dart
void testExample() {
  // Correção 1: Expectativa com await ou expectLater
  final futureResult = Future.value(42);
  expect(await futureResult, equals(42)); // Com await
  // ou
  expectLater(futureResult, completes);  // Com expectLater

  // Correção 2: Evitar await em código não assíncrono
  final result = 42;
  expect(result, equals(42)); // Não usa await

  // Correção 3: Uso de throwsA ao testar exceções
  final futureResult = Future.error(Exception('Falhou'));
  expect(futureResult, throwsA(isA<Exception>())); // Matcher correto

  // Correção 4: Validação de conclusão de um Future
  final futureResult = Future.delayed(Duration(seconds: 1), () => 42);
  expectLater(futureResult, completes); // Matcher correto

  // Correção 5: Sincronização com await ou expectLater
  final futureResult = Future.delayed(Duration(seconds: 1), () => 42);
  expect(await futureResult, equals(42)); // Garante sincronização
}
```

---

## Correções Sugeridas

1. Sempre utilize `await` ou `expectLater` para aguardar a resolução de um `Future`.
2. Evite usar `await` em código não assíncrono; valide valores diretamente.
3. Para testar exceções, use o matcher `throwsA` com a classe ou tipo esperado.
4. Utilize `expectLater` com o matcher `completes` para verificar se um `Future` conclui sem erros.
5. Certifique-se de que todo comportamento assíncrono é aguardado antes de validar resultados.

---

## Exceções e Casos Especiais

- Se o comportamento assíncrono não é essencial para o teste, ele pode ser substituído por uma implementação síncrona simulada.
- Em casos onde não é necessário validar a resolução de um `Future`, é aceitável ignorar `await` ou `expectLater` desde que documentado claramente.

---

## Ferramentas de Detecção

- **Linters**: Regras personalizadas podem ser criadas para detectar uso inadequado de `await` e matchers em testes assíncronos.
- **Analisadores Estáticos**: Ferramentas como `dart analyze` podem ser configuradas para identificar problemas comuns em testes.

---

## Referências e Estudos Relacionados

- Documentação oficial do Dart: [Testes Assíncronos](https://dart.dev/guides/testing/async)
- Artigos sobre boas práticas de testes em Flutter e Dart.
- Estudos acadêmicos sobre **test smells** e soluções para código assíncrono.

---

## Nota
Identificar e corrigir o **Expected Resolution Omission** é essencial para garantir testes confiáveis e robustos em Dart/Flutter, especialmente em aplica

