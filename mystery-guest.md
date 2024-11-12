# Mystery Guest

## 🔍 Descrição do Problema
O **Mystery Guest** ocorre quando um teste depende de dados ou estados externos, como configurações globais ou dados de um banco externo, que não estão diretamente visíveis no teste. Isso cria uma dependência implícita e torna o comportamento do teste mais difícil de entender, pois informações importantes para a execução e sucesso do teste estão "escondidas" fora do próprio código de teste.

Em outras palavras, o **Mystery Guest** age como um "convidado misterioso" no teste, pois os valores externos que ele usa não são claros no contexto do próprio teste, dificultando a compreensão e manutenção do código.

---

## ⚠️ Sintomas e Impacto
- **Dificuldade de Depuração**: Testes falham ou se comportam de maneira inconsistente devido a dependências externas ou variáveis não explícitas.
- **Dificuldade de Configuração**: Outros desenvolvedores podem ter dificuldade para reproduzir o ambiente exato do teste se ele depender de configurações externas.
- **Menor Confiabilidade**: O teste se torna menos confiável devido à dependência de elementos fora do seu escopo imediato.

---

## 🔑 Critérios de Identificação
Para identificar o **Mystery Guest**, procure por:
- Testes que dependem de estados ou dados externos, como variáveis globais, arquivos de configuração, ou dados de um banco de dados.
- Testes que requerem configurações ou dependências específicas para executar corretamente, mas não informam essas dependências no próprio código de teste.

### Detecção Automática
É desafiador para ferramentas de análise estática detectarem o **Mystery Guest**, mas você pode configurá-las para verificar referências a estados globais ou dependências externas não documentadas.

---

## ✅ Exemplo de Código

### Exemplo com Mystery Guest

```dart
void testUserProfile() {
  final userProfile = fetchUserProfile();
  assert(userProfile.name == "Alice"); // Depende de um usuário "Alice" configurado no banco de dados
}
```

### Exemplo sem Mystery Guest

```dart
void testUserProfile() {
  final testUser = User(id: 1, name: "Alice");
  final userProfile = fetchUserProfile(testUser.id);
  assert(userProfile.name == testUser.name, "Expected user name to be Alice for test user with ID 1");
}
```

---

## 🚀 Correções Sugeridas
Para resolver o problema de **Mystery Guest**:

- **Usar Dados Simulados (Mocking)**: Defina explicitamente os dados necessários no próprio teste, para que ele seja independente de recursos externos.
- **Isolar o Ambiente de Teste**: Utilize dados específicos para o teste que não sejam dependentes de recursos de produção ou variáveis globais.
- **Documentar Dependências**: Se um recurso externo é necessário, documente-o claramente no próprio teste ou em um setup de teste.

---

## 🌟 Exceções e Casos Especiais
Em alguns cenários de integração ou testes de aceitação, onde a validação do sistema completo é o foco, o uso de dados reais ou dependências externas pode ser aceitável. Mesmo assim, é importante documentar e isolar essas dependências.

---

## 🛠 Ferramentas de Detecção
- **Ferramentas de Mocking e Injeção de Dependência**: Ferramentas como `mockito` podem ser usadas para criar versões simuladas dos dados, evitando dependências externas.
- **Linters**: Ferramentas de lint podem ser configuradas para sinalizar referências a estados globais ou dependências externas em testes.

---

## 📚 Referências e Estudos Relacionados
- Fowler, M. (1999). *Refactoring: Improving the Design of Existing Code*
- Meszaros, G. (2007). *xUnit Test Patterns: Refactoring Test Code*
- Van Deursen, A., et al. (2001). "Refactoring Test Code."

---

## 📝 Nota
Evitar o **Mystery Guest** ajuda a garantir que os testes sejam independentes e confiáveis, facilitando a execução e manutenção, além de reduzir a possibilidade de erros devido a dependências externas.
