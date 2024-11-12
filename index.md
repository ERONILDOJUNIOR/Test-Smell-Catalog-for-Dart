# Catálogo de Test Smells para Dart/Flutter

Bem-vindo ao **Catálogo de Test Smells para Dart/Flutter** – uma coleção cuidadosamente organizada para ajudar desenvolvedores e equipes de QA a identificar, entender e resolver test smells comuns que podem comprometer a qualidade dos testes e do software.

## 📌 O que são Test Smells?

**Test Smells** são padrões de código problemáticos dentro de testes que, embora não causem necessariamente falhas, indicam potenciais problemas de design ou manutenção. Assim como *code smells* em código de produção, esses "cheiros" nos testes podem impactar negativamente a legibilidade, a confiabilidade e a eficiência dos testes.

Este catálogo traz uma lista dos principais test smells conhecidos em outras linguagens e frameworks, adaptados para o contexto de desenvolvimento em **Dart** e **Flutter**.

---

## 🎯 Objetivo do Catálogo

O catálogo visa:
- 📖 **Definir e descrever** os test smells mais comuns, com exemplos práticos para cada um.
- 🛠 **Oferecer critérios de identificação** para facilitar a detecção de cada smell nos testes.
- ✨ **Sugerir abordagens de correção** e boas práticas, ajudando você a manter seus testes claros, robustos e de fácil manutenção.
- 📊 **Proporcionar uma visão mais profunda** sobre o impacto de cada smell na qualidade do código e da manutenção.

---

## 📚 Organização do Catálogo

Cada test smell neste catálogo segue um formato estruturado, contendo:
- **Nome do Smell** e descrição
- **Sintomas e impacto** sobre a qualidade do teste
- **Critérios de identificação**, com exemplos de código
- **Soluções sugeridas** para refatoração ou melhoria
- **Exceções** ou casos em que o smell pode ser aceitável

---

## 📖 Guia de Uso

1. **Navegue pelo índice abaixo** para explorar cada test smell.
2. **Identifique os smells** que podem estar presentes no seu código e veja os exemplos e critérios para reconhecê-los.
3. **Aplique as soluções sugeridas** para melhorar a qualidade e manutenção dos seus testes em Dart/Flutter.
4. **Mantenha-se atualizado**! Este catálogo será expandido com novos smells específicos para Dart/Flutter à medida que surgirem.

---

## 🗂 Índice de Test Smells

1. [Assertion Roulette](assertion-roulette.md)
2. [Conditional Test Logic](conditional-test-logic.md)
3. [Constructor Initialization](constructor-initialization.md)
4. [Default Test](default-test.md)
5. [Dependent Test](dependent-test.md)
6. [Duplicate Assert](duplicate-assert.md)
7. [Eager Test](eager-test.md)
8. [Empty Test](empty-test.md)
9. [Exception Handling](exception-handling.md)
10. [General Fixture](general-fixture.md)
11. [Ignored Test](ignored-test.md)
12. [Lazy Test](lazy-test.md)
13. [Magic Number Test](magic-number-test.md)
14. [Mystery Guest](mystery-guest.md)
15. [Redundant Print](redundant-print.md)
16. [Redundant Assertion](redundant-assertion.md)
17. [Resource Optimism](resource-optimism.md)
18. [Sensitive Equality](sensitive-equality.md)
19. [Sleepy Test](sleepy-test.md)
20. [Unknown Test](unknown-test.md)
21. [Verbose Test](verbose-test.md)

---

## 🔗 Contribuição e Expansão

Este catálogo é um trabalho em andamento, e contribuições são bem-vindas! Se você encontrar um test smell específico de Dart/Flutter ou tiver sugestões para melhorar o catálogo, sinta-se à vontade para abrir uma issue ou enviar um pull request.

---

**📝 Nota:** Este catálogo é voltado para desenvolvedores e testadores que buscam melhorar a qualidade dos testes em **Dart/Flutter**. Por favor, use-o como um guia prático e evolua com ele!

---

Aproveite a leitura, e que o seu código esteja sempre livre de smells!
