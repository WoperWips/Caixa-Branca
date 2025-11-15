# Projeto: Teste de Caixa Branca — Verificação de Usuário

## Objetivo
Analisar estaticamente o código `User.java` que valida login de usuário no banco de dados, aplicar técnica de caixa-branca (grafo, complexidade ciclomática, caminhos básicos) e corrigir código para melhores práticas.

---

## Arquivos no repositório
- `src/login/User.java` — código fonte (versão corrigida e comentada)
- `planilha_caixa_branca.xlsx` — planilha com a análise estática (respostas)
- `grafo_fluxo.png` — imagem do grafo de fluxo (opcional)
- `README.md` — este arquivo

---

## 1) Análise estática (resumo)
- O código original **não estava documentado**.
- Uso de variáveis com nomes genéricos (`sql`, `result`).
- Falta de verificação de `null` para `Connection`.
- Recursos (Connection, Statement, ResultSet) **não eram fechados** → risco de vazamento.
- SQL concatenado → vulnerável a SQL Injection.
- Credenciais hardcoded no código (senha exposta) — inseguro.

---

## 2) Notação do grafo de fluxo
Nós mapeados:
1 → 2 → 3 → 4 → 5 → 6 → 7 → [se true] → 8 → 9 → 10
                                            [se false] → 10
Erro SQL → 11 → 10

(Diagrama em `grafo_fluxo.png`)

---

## 3) Cálculo da complexidade ciclomática
- Nós (N) = 12  
- Arestas (E) = 13  
- Componentes (P) = 1

`M = E − N + 2P = 13 − 12 + 2 = 3`

**Complexidade ciclomática = 3**

---

## 4) Caminhos básicos
1. **Conexão falha / Erro SQL**: retorno `false`.  
2. **Login inválido (rs.empty)**: retorno `false`.  
3. **Login válido (rs.next == true)**: retorno `true` e `nome` preenchido.

---

## 5) Casos de teste sugeridos
- **CT01 — Falha de conexão**: configurar BD inacessível → `verificarUsuario("x","y")` = `false`.  
- **CT02 — Login inválido**: credenciais que não existem → `false`.  
- **CT03 — Login válido**: credenciais existentes → `true`, `getNome()` retorna o nome do usuário.

---

## 6) Observações sobre implantação
- Remover credenciais do código. Usar variáveis de ambiente / arquivo de configuração com `.gitignore`.
- Usar PreparedStatement (já aplicado no código desta entrega).
- Adicionar logging em vez de `System.err.println` em produção.

---

## 7) Como executar / configurar (local)
1. Configurar banco MySQL com a tabela `usuarios` e colunas `login`, `senha`, `nome`.
2. Ajustar `DB_URL`, `DB_USER`, `DB_PASS` no `User.java` ou (melhor) ler via variáveis de ambiente.
3. Compilar e rodar testes unitários que chamem `verificarUsuario(...)`.

