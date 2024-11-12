# General Fixture

## 🔍 Descrição do Problema
**General Fixture** ocorre quando uma fixture de teste (um objeto ou configuração compartilhada entre múltiplos testes) é usada de forma excessivamente genérica ou inadequada. Esse smell ocorre quando a fixture é configurada de maneira que não reflete claramente o propósito dos testes, o que pode levar a falhas na cobertura de testes e confusão sobre os requisitos de cada teste.

Testes devem ser independentes e focados, e as fixtures devem ser específicas para o contexto de cada teste. O uso excessivo de uma fixture genérica pode esconder dependências implícitas e reduzir a clareza do código.

---

## ⚠️ Sintomas e Impacto
- **Testes Dependentes de Outras Configurações**: Testes podem se tornar dependentes de configurações genéricas, tornando-os frágeis e difíceis de entender.
- **Redução de Clareza**: A falta de especificidade em fixtures pode tornar os testes mais difíceis de entender e manter, já que o contexto de cada teste fica obscurecido.
- **Baixa Modularidade**: Quando fixtures são compartilhadas entre muitos testes de forma inadequada, elas tornam o código difícil de modificar ou expandir.

---

## 🔑 Critérios de Identificação
Para identificar o **General Fixture**, procure por:
- Fixtures que são configuradas de maneira muito ampla, cobrindo vários cenários que não são necessários para o teste específico.
- Uso de objetos compartilhados que não são modificados ou contextualizados para cada teste individualmente.
- Testes que dependem de configurações ou dados globais que não são relevantes para o comportamento específico que estão tentando verificar.

### Detecção Automática
Ferramentas de análise estática e linters podem ser configuradas para detectar quando uma fixture está sendo compartilhada entre testes sem considerar o contexto individual de cada um.

---

## ✅ Exemplo de Código

### Exemplo com General Fixture

```dart
import 'package:test/test.dart';

class Database {
  List<String> users = [];

  void addUser(String user) {
    users.add(user);
  }

  void clear() {
    users.clear();
  }

  bool userExists(String user) {
    return users.contains(user);
  }
}

// General Fixture usada para configurar o estado compartilhado.
class UserDatabaseFixture {
  Database db = Database();

  void createTestUser(String user) {
    db.addUser(user);
  }
}

void main() {
  final fixture = UserDatabaseFixture(); // General Fixture compartilhada por todos os testes.

  test('Should add user to the database', () {
    fixture.createTestUser("Alice");
    expect(fixture.db.userExists("Alice"), isTrue);
  });

  test('Should add another user to the database', () {
    fixture.createTestUser("Bob");
    expect(fixture.db.userExists("Bob"), isTrue);
  });
}

```

### Exemplo sem General Fixture

```dart
import 'package:test/test.dart';

class Database {
  List<String> users = [];

  void addUser(String user) {
    users.add(user);
  }

  void clear() {
    users.clear();
  }

  bool userExists(String user) {
    return users.contains(user);
  }
}

void main() {
  // Em vez de uma General Fixture, cada teste configura seu próprio estado.
  Database createIsolatedDatabase() {
    return Database();
  }

  test('Should add user to the database', () {
    final db = createIsolatedDatabase();
    db.addUser("Alice");
    expect(db.userExists("Alice"), isTrue);
  });

  test('Should add another user to the database', () {
    final db = createIsolatedDatabase();
    db.addUser("Bob");
    expect(db.userExists("Bob"), isTrue);
  });
}

```

---

## 🚀 Correções Sugeridas
Para resolver o **General Fixture**:

- **Seja Específico**: Crie fixtures que sejam específicas para o comportamento ou cenário de teste que você está verificando.
- **Evite Dados Globais**: Tente não compartilhar dados ou configurações globais entre diferentes testes. Use métodos de configuração e teardown (limpeza) para isolar os testes.
- **Modularize as Fixtures**: Quando possível, modularize suas fixtures para que elas sejam reutilizáveis, mas focadas no teste específico.

---

## 🌟 Exceções e Casos Especiais
Em casos onde uma configuração comum é necessária entre vários testes, considere usar métodos de setup/teardown para limpar ou ajustar o estado antes ou depois de cada teste. Porém, é importante garantir que a fixture esteja sempre contextualizada para o que cada teste necessita.

---

## 🛠 Ferramentas de Detecção
- **Linter Configurável**: Ferramentas como `dart analyze` podem ser configuradas para detectar fixtures compartilhadas de forma inadequada.
- **Test Coverage**: Ferramentas de cobertura de teste podem ajudar a identificar dependências não explícitas causadas por fixtures genéricas.

---

## 📚 Referências e Estudos Relacionados
- Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
- Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
- Van Deursen, A., et al. (2001). "Refactoring Test Code."

---

## 📝 Nota
O **General Fixture** é especialmente importante em projetos de longo prazo, onde a reutilização excessiva de fixtures pode afetar a clareza e a confiabilidade dos testes. Garantir que cada teste tenha sua própria configuração ou fixture dedicada ajuda a manter os testes independentes e confiáveis.
