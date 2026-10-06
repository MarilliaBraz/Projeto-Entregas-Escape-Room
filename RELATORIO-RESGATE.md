# Relatório de Resgate
- Equipe: Marillia Braz Neves Brito (@MarilliaBraz), Pedro Feitosa (@feitosapedrofatesg-svg), YanSantos-TI (@zod1827), @LucasMNDL3
- Branch de trabalho: `resgate/equipe-marilliabraz` (criada inicialmente como `resgate/discente-marilliabraz` e renomeada)

## Diagnóstico

| Porta | Problema | Evidência |
|---|---|---|
| 1 | O projeto não compilava | `a70ee84` passou a chamar `new Mercadoria(...)` sem `endereco` e `repository.gravar(...)`, método inexistente (o correto é `salvar`). Além disso, `EntregaService` importava `util.Validador`, que tinha sido apagado. |
| 2 | Arquivo excluído | `64f88f6` ("remover classe aparentemente sem uso") apagou `src/main/java/br/edu/entregas/util/Validador.java`, que ainda era usado por `EntregaService`. |
| 3 | Login aceitava credenciais inválidas | `9a6d3b0` trocou `USUARIO.equals(usuario) && SENHA.equals(senha)` por `USUARIO.equals(usuario) \|\| senha == SENHA`: bastava acertar o usuário. A branch `hotfix-login` (`a6f356d`, `NOTA-HOTFIX.txt`) confirmava a correção esperada: usar `equals` e exigir usuário **e** senha. |
| 4 | README inútil | `0cd80f6` substituiu o README completo por "Pergunte ao desenvolvedor como executar". A branch `docs-readme` (`7fe8faa`) tinha uma seção adicional de fluxo de contribuição. |
| 5 | Segredo versionado | `6572d8a` ("salvar credenciais para facilitar testes") adicionou `db.user`, `db.password` e `api.token` em `config/application.properties`. |
| 6 | Entrega na `main` | As correções foram feitas na branch de resgate e integradas na `main` com merge. |

## Comandos Git utilizados

| Comando | Finalidade |
|---|---|
| `git status` / `git diff` | Ver alterações pendentes na cópia de trabalho. |
| `git branch -a` | Listar as branches e encontrar pistas (`backup-antigo`, `docs-readme`, `feature-cadastro`, `hotfix-login`, `teste-login-descartavel`). |
| `git log --oneline --all --graph` | Visualizar o histórico de todas as branches e identificar o último estado funcional (`356ec6f`). |
| `git show <hash>` | Inspecionar o diff de cada commit suspeito. |
| `git log --oneline main..<branch>` / `git diff --stat main...<branch>` | Ver o que cada branch lateral trazia em relação à `main`. |
| `git switch -c resgate/discente-marilliabraz` | Criar a branch de resgate. |
| `git branch -m resgate/discente-marilliabraz resgate/equipe-marilliabraz` | Renomear a branch para o padrão `resgate/equipe-NOME` exigido na regra 4. |
| `git checkout 799c5ec -- DESAFIO.md` | Restaurar o enunciado original do desafio, que havia sido alterado no commit `4445798`. |
| `git push origin --delete resgate/discente-marilliabraz` | Remover do GitHub a branch com o nome antigo. |
| `git revert <hash>` | Desfazer commits problemáticos criando novos commits, sem reescrever o histórico (correção identificável). |
| `git merge --no-ff <branch>` | Integrar `docs-readme`, `feature-cadastro` e, ao final, a branch de resgate na `main`, mantendo um commit de merge visível. |
| `git grep -iE "SuperSenha\|TOKEN-NAO" HEAD` | Confirmar que a versão atual não contém mais as credenciais. |
| `git push origin <branch>` | Publicar as branches no GitHub. |

## Commits relevantes

| Hash | Situação |
|---|---|
| `356ec6f` | Último estado funcional ("implementar sistema de entregas funcional"), usado como referência. |
| `9a6d3b0` | Quebrou o login. **Revertido** em `b35ee87`. |
| `6572d8a` | Adicionou credenciais. **Neutralizado** em `3a8f7b3` (credenciais removidas da versão atual). |
| `0cd80f6` | Esvaziou o README. **Revertido** em `5c7d378`. |
| `a70ee84` | Quebrou o cadastro e a compilação. **Revertido** em `a99d581`. |
| `64f88f6` | Excluiu `Validador.java`. **Revertido** (arquivo recuperado) em `315d430`. |
| `7fe8faa` | Seção "Fluxo recomendado" do README (branch `docs-readme`). **Integrado** em `98ad9a1`. |
| `2624e5c` | Validação de status (branch `feature-cadastro`). **Integrado** em `bbb13c4`. |
| `a6f356d` | Nota do hotfix (branch `hotfix-login`). Usado como evidência para a correção do login. |

### Observação sobre o histórico da `main`
O commit `aa15cba` ("integrar branch resgate/discente-marilliabraz na main") foi um merge antecipado: a branch de resgate foi integrada na `main` antes das correções, levando junto os commits problemáticos (`9a6d3b0` a `64f88f6`). Para não reescrever um histórico já publicado, esse merge foi mantido, e as correções foram feitas depois na mesma branch de resgate e integradas na `main` pelo merge `6d15f52`. Por isso, entre `aa15cba` e `6d15f52` a `main` não compila; a versão final da `main` atende a todos os critérios de saída.

## Validação final

- **Compilação:** `mvn clean package` terminou sem erros e gerou `target/classes`.
- **Login válido:** `admin / 12345678` → "Acesso autorizado".
- **Login inválido:** `admin / errada`, `outro / 12345678` e `outro / errada` → "Acesso negado".
- **Cadastro:** após o login, o sistema lista `#1 | Notebook | ... | Entrega: Av. Goiás, 1000 - Sala 8, Goiânia/GO - CEP: 74000-000`, ou seja, a mercadoria é criada com um endereço.
- **README:** contém requisitos (Java 17+, Maven 3.8+), comandos para compilar e executar e o acesso de demonstração.
- **Segurança:** `config/application.properties` contém apenas `app.name`, e `git grep` na versão atual não encontra a senha nem o token. Observação: as credenciais continuam no histórico (`6572d8a`); em um cenário real elas devem ser consideradas comprometidas e trocadas.

### Observação sobre o nome da branch e o enunciado
O commit `4445798` alterou o `DESAFIO.md`, trocando o padrão de branch `resgate/equipe-NOME` por `resgate/discente-NOME`, e a branch de resgate foi criada como `resgate/discente-marilliabraz`. O enunciado original foi restaurado a partir do commit `799c5ec`, e a branch foi renomeada para `resgate/equipe-marilliabraz`. As mensagens dos merges `aa15cba` e `6d15f52` ainda citam o nome antigo porque foram mantidas como estavam no histórico publicado.
