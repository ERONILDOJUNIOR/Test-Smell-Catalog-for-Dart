# Ignored Test

## 🔍 Descrição do Problema
O **Ignored Test** ocorre quando um teste é desativado ou ignorado intencionalmente usando anotações como `@Skip` (ou `@ignore` em algumas linguagens), sem uma justificativa clara ou uma previsão para reativá-lo. Ignorar testes é comum em situações temporárias, mas pode se tornar um problema quando muitos testes ficam desativados indefinidamente, resultando em uma falsa impressão de confiabilidade no sistema.

---

## ⚠️ Sintomas e Impacto
- **Falsa Segurança**: Pode parecer que o sistema está passando em todos os testes, quando, na realidade, os testes ignorados ocultam potenciais problemas.
- **Débito Técnico**: Ignorar testes sem documentação do motivo ou sem prazo para resolução cria uma dívida técnica crescente.
- **Falta de Confiabilidade**: O sistema se torna menos confiável, pois a cobertura real de testes está incompleta.

---

## 🔑 Critérios de Identificação
Para identificar o **Ignored Test**, busque:
- Testes marcados com `@Skip` ou `@ignore`.
- Testes com comentários como "TODO", "desativado temporariamente", ou "ignorado por erro".

### Detecção Automática
- Linters e analisadores estáticos podem identificar testes marcados como `@Skip` ou `@ignore` e verificar se há uma justificativa/documentação adequada.

---

## ✅ Exemplo de Código

### Exemplo com Ignored Test

```dart
import 'package:test/test.dart';

@Skip('Ignorado temporariamente devido a um erro de API')
void testUserLogin() {
  final user = User(id: 1);
  expect(user.isLoggedIn, isTrue);
}
```

### Exemplo sem Ignored Test

```dart
import 'package:test/test.dart';

void testUserLogin() {
  final user = User(id: 1);
  expect(user.isLoggedIn, isTrue, reason: "User should be logged in");
}
```

---

## 🚀 Correções Sugeridas
Para resolver o problema do **Ignored Test**:

- **Justifique a Ignoração**: Adicione uma descrição clara do motivo para o teste ser ignorado e, se possível, um prazo para sua reativação.
- **Utilize Workarounds Temporários**: Se a falha está relacionada a um problema externo (como uma API instável), considere implementar simulações para continuar os testes.
- **Monitore Ignorados**: Configure o pipeline de CI/CD para alertar sobre testes ignorados que persistem por muito tempo.

---

## 🌟 Exceções e Casos Especiais
Ignorar um teste é aceitável em situações de problemas temporários com uma resolução programada. Nesses casos, a justificativa e o plano de ação devem estar bem documentados.

---

## 🛠 Ferramentas de Detecção
- **Linters e Analisadores Estáticos**: Ferramentas como `dart analyze` podem verificar o uso de `@Skip`.
- **SonarQube**: Com plugins configuráveis, o SonarQube pode alertar sobre a presença de testes ignorados sem justificativa.

---

## 📚 Referências e Estudos Relacionados
- Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
- Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
- Google Testing Blog (2008). "Flaky Tests: The Good, The Bad, and The Ugly."

---

## 📝 Nota
Utilizar o `@Skip` como solução rápida pode ser útil em situações temporárias, mas os testes ignorados devem ser monitorados para evitar impacto na qualidade do software.
