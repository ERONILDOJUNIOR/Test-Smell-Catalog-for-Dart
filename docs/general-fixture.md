# General Fixture

## 🔍 Descrição do Problema
**General Fixture** isso ocorre quando nem todos os métodos de teste utilizam a estrutura de fixtures do caso de teste, indicando que a configuração está muito genérica. Como consequência, a execução dos testes pode ficar mais lenta, já que a configuração pode se tornar complexa, com etapas desnecessárias para testes que não as requerem.

Testes devem ser independentes e focados, e as fixtures devem ser específicas para o contexto de cada teste. O uso excessivo de uma fixture genérica pode esconder dependências implícitas e reduzir a clareza do código.

---

## ⚠️ Sintomas e Impacto
- **Testes Dependentes de Outras Configurações**: Testes podem se tornar dependentes de configurações genéricas, tornando-os frágeis e difíceis de entender.
- **Baixa Modularidade**: Quando fixtures são compartilhadas entre muitos testes de forma inadequada, elas tornam o código difícil de modificar ou expandir.

---

## 🔑 Critérios de Identificação
Para identificar o **General Fixture**, procure por:
- Fixtures que são configuradas de maneira muito ampla, cobrindo vários cenários que não são necessários para o teste específico.
- Uso de objetos compartilhados que não são modificados ou contextualizados para cada teste individualmente.
- Testes que dependem de configurações ou dados globais que não são relevantes para o comportamento específico que estão tentando verificar.

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

  test('Deve adicionar user a database', () {
    fixture.createTestUser("Alice");
    expect(fixture.db.userExists("Alice"), isTrue);
  });

  test('SDeve adicionar outro user a database', () {
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

---

## 🌟 Exceções e Casos Especiais
Em casos onde uma configuração comum é necessária entre vários testes, considere usar métodos de setup/teardown para limpar ou ajustar o estado antes ou depois de cada teste. Porém, é importante garantir que a fixture esteja sempre contextualizada para o que cada teste necessita.

---

## 🛠 Ferramentas de Detecção
- **Linter Configurável**: Ferramentas como `dart analyze` podem ser configuradas para detectar fixtures compartilhadas de forma inadequada.
- **Test Coverage**: Ferramentas de cobertura de teste podem ajudar a identificar dependências não explícitas causadas por fixtures genéricas.

---

## 📝 Nota
O **General Fixture** é especialmente importante em projetos de longo prazo, onde a reutilização excessiva de fixtures pode afetar a clareza e a confiabilidade dos testes. Garantir que cada teste tenha sua própria configuração ou fixture dedicada ajuda a manter os testes independentes e confiáveis.

---

## 📚 Referências e Estudos Relacionados
- Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
- Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
- Van Deursen, A., et al. (2001). "Refactoring Test Code."