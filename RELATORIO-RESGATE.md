# Relatório de Resgate
- Equipe: Marillia Braz Neves Brito (@MarilliaBraz), Pedro Feitosa (@feitosapedrofatesg-svg), YanSantos-TI (@zod1827), @LucasMNDL3
- Branch de trabalho: `resgate/equipe-marilliabraz` (criada inicialmente como `resgate/discente-marilliabraz` e renomeada)

## Diagnóstico

### Investigação inicial do repositório (Etapa 2)
- **Branches existentes:** 7 — `main`, `release-dev`, `backup-antigo`, `docs-readme`, `feature-cadastro`, `hotfix-login` e `teste-login-descartavel`.
- **Branch inicialmente ativa:** `release-dev`.
- **Tags:** `v1.0.0-funcional` (aponta para `356ec6f`, última versão funcional) e `commit-perigoso` (aponta para `6572d8a`, commit com credenciais).
- **Commits relacionados aos problemas:** `9a6d3b0`, `6572d8a`, `0cd80f6`, `a70ee84` e `64f88f6`, todos na `release-dev`, feitos em 03/09/2026 depois da versão funcional.
- **Branches com versões úteis:** `docs-readme` (README completo + fluxo de contribuição), `hotfix-login` (nota com a correção validada do login) e `feature-cadastro` (validação de status). `backup-antigo` aponta para `356ec6f` (versão funcional) e `teste-login-descartavel` aponta para `9a6d3b0` (login quebrado); nenhuma das duas traz commits próprios.

### Problemas encontrados

| Porta | Problema | Evidência |
|---|---|---|
| 1 | O projeto não compilava | Erros do `mvn clean package` na versão recebida (`799c5ec`), todos em `EntregaService.java`: `[6,28] package br.edu.entregas.util does not exist`; `[14,9]`, `[15,9]`, `[16,9]`, `[17,9]` `cannot find symbol` (chamadas a `Validador`); `[19,33] constructor Mercadoria ... cannot be applied to given types`; `[20,19] cannot find symbol` (`repository.gravar`). Causas: o commit `a70ee84` removeu o `endereco` do construtor e trocou `salvar` por `gravar`, método inexistente; o commit `64f88f6` apagou a classe `Validador`. |
| 2 | Arquivo excluído | `git log --diff-filter=D --summary` mostrou `delete mode 100644 src/main/java/br/edu/entregas/util/Validador.java` no commit `64f88f6` ("refactor: remover classe aparentemente sem uso"), de **Igor Reis**. A classe ainda era usada por `EntregaService`. |
| 3 | Login aceitava credenciais inválidas | `git diff v1.0.0-funcional -- .../LoginService.java` mostrou que `9a6d3b0` trocou `USUARIO.equals(usuario) && SENHA.equals(senha)` por `USUARIO.equals(usuario) \|\| senha == SENHA`: bastava acertar o usuário, e a senha era comparada por referência (`==`). A branch `hotfix-login` (`a6f356d`, `NOTA-HOTFIX.txt`) confirmava a correção esperada. |
| 4 | README inútil | `git log --all -- README.md` mostrou que `0cd80f6` substituiu o README completo por "Pergunte ao desenvolvedor como executar". `git show docs-readme:README.md` exibiu a versão completa. |
| 5 | Segredo versionado | `git show commit-perigoso` mostrou que `6572d8a` adicionou `config/application.properties` com `db.user`, `db.password` e `api.token`. |
| 6 | Entrega na `main` | As correções foram feitas na branch de resgate e integradas na `main` com `git merge --no-ff`. |

### Autores registrados no histórico

| Hash | Autor | Data | Mensagem | Papel no problema |
|---|---|---|---|---|
| `4574eee` | Ana Lima | 01/09/2026 09:00 | chore: criar estrutura inicial do projeto | — |
| `356ec6f` | Bruno Costa | 01/09/2026 10:00 | feat: implementar sistema de entregas funcional | Versão funcional (tag `v1.0.0-funcional`) |
| `7fe8faa` | Carla Souza | 02/09/2026 09:00 | docs: detalhar fluxo de contribuição | Branch `docs-readme`, útil |
| `2624e5c` | Diego Alves | 02/09/2026 10:00 | feat: validar status da mercadoria | Branch `feature-cadastro`, útil |
| `a6f356d` | Eva Martins | 02/09/2026 11:00 | fix: documentar correção validada para autenticação | Branch `hotfix-login`, pista |
| `9a6d3b0` | Felipe Rocha | 03/09/2026 08:00 | fix: liberar login para homologacao | Quebrou o login (Porta 3) |
| `6572d8a` | Felipe Rocha | 03/09/2026 08:30 | chore: salvar credenciais para facilitar testes | Versionou segredo (Porta 5, tag `commit-perigoso`) |
| `0cd80f6` | Gustavo Melo | 03/09/2026 09:00 | docs: simplificar readme | Esvaziou o README (Porta 4) |
| `a70ee84` | Henrique Nunes | 03/09/2026 09:30 | feat: acelerar cadastro de mercadorias | Quebrou cadastro e compilação (Porta 1) |
| `64f88f6` | Igor Reis | 03/09/2026 10:00 | refactor: remover classe aparentemente sem uso | Excluiu `Validador.java` (Portas 1 e 2) |
| `799c5ec` | Coordenacao | 03/09/2026 10:30 | chore: adicionar instrucoes do resgate | Enunciado do desafio |

## Comandos Git utilizados

| Comando | Finalidade |
|---|---|
| `git status` / `git diff` | Ver o estado da cópia de trabalho e as alterações pendentes. |
| `git branch -a` | Listar as branches locais e remotas e encontrar pistas. |
| `git tag -n` | Listar as tags (`v1.0.0-funcional`, `commit-perigoso`). |
| `git log --oneline --all --graph --decorate` | Visualizar o histórico de todas as branches e tags e identificar o último estado funcional. |
| `git log --stat` | Ver quais arquivos cada commit alterou. |
| `git show <hash>` | Inspecionar o diff, o autor e a data de cada commit suspeito. |
| `git log -- <arquivo>` | Ver o histórico de um arquivo específico (`EntregaService.java`, `LoginService.java`, `README.md`, `config/application.properties`). |
| `git diff v1.0.0-funcional -- <arquivo>` | Comparar a versão atual de um arquivo com a versão funcional. |
| `git log --diff-filter=D --summary` | Encontrar o commit que excluiu `Validador.java`. |
| `git show docs-readme:README.md` | Ver o README completo guardado na branch `docs-readme`. |
| `git log --oneline main..<branch>` / `git diff --stat main...<branch>` | Ver o que cada branch lateral trazia em relação à `main`. |
| `git switch -c resgate/discente-marilliabraz` | Criar a branch de resgate. |
| `git branch -m resgate/discente-marilliabraz resgate/equipe-marilliabraz` | Renomear a branch para o padrão `resgate/equipe-NOME`. |
| `git checkout 799c5ec -- DESAFIO.md` | Restaurar o enunciado original do desafio, que havia sido alterado no commit `00c29d8`. |
| `git revert <hash>` | Desfazer commits problemáticos criando novos commits, sem reescrever o histórico. O `git revert 64f88f6` recuperou o arquivo excluído. |
| `git merge --no-ff <branch>` | Integrar `docs-readme`, `feature-cadastro` e a branch de resgate na `main`, mantendo um commit de merge visível. |
| `git rm config/application.properties` | Remover o arquivo de credenciais da versão atual. |
| `git grep -n "<valor da senha ou do token>" HEAD` | Confirmar que a versão atual não contém mais as credenciais. |
| `git push origin <branch>` / `git push origin --tags` | Publicar branches e tags no GitHub. |
| `git push origin --delete resgate/discente-marilliabraz` | Remover do GitHub a branch com o nome antigo. |
| `git filter-branch --env-filter ... --msg-filter ... -- main resgate/equipe-marilliabraz ^799c5ec ^docs-readme ^feature-cadastro` | Reorganizar os commits do resgate: definir o autor de cada commit entre os integrantes da equipe e simplificar as mensagens. Só os commits feitos pela equipe foram alterados; o conteúdo dos arquivos e os commits originais do projeto (incluindo os investigados) ficaram iguais. |
| `git diff refs/original/refs/heads/main main` | Confirmar que a reorganização não mudou nenhum arquivo (saída vazia). |
| `git push --force-with-lease origin main resgate/equipe-marilliabraz` | Publicar o histórico reorganizado. O `--force-with-lease` só sobrescreve se ninguém tiver enviado commits novos nesse meio-tempo. |

## Commits relevantes

| Hash | Situação |
|---|---|
| `356ec6f` | Último estado funcional (tag `v1.0.0-funcional`), usado como referência nas comparações. |
| `64f88f6` | Excluiu `Validador.java`. **Revertido** (arquivo recuperado) em `820d22e`. |
| `a70ee84` | Quebrou o cadastro e a compilação. **Revertido** em `48221f3`. |
| `0cd80f6` | Esvaziou o README. **Revertido** em `6baae00`. |
| `9a6d3b0` | Quebrou o login. **Revertido** em `d663813`. |
| `7fe8faa` | Seção "Fluxo recomendado" do README (branch `docs-readme`). **Integrado** em `9701167`. |
| `2624e5c` | Validação de status (branch `feature-cadastro`). **Integrado** em `8918601`. |
| `6572d8a` | Adicionou credenciais (tag `commit-perigoso`). Credenciais apagadas do arquivo em `ee9470e`; arquivo removido da versão atual e incluído no `.gitignore` em `4f97456`. |
| `a6f356d` | Nota do hotfix (branch `hotfix-login`). Usado como evidência para a correção do login. |

### Observação sobre o histórico da `main`
O commit `8587380` ("Merge branch 'resgate/discente-marilliabraz'") foi um merge antecipado: a branch de resgate foi integrada na `main` antes das correções, levando junto os commits problemáticos (`9a6d3b0` a `64f88f6`). Esse merge foi mantido, e as correções foram feitas depois na mesma branch de resgate e integradas na `main` pelos merges seguintes. Por isso, entre `8587380` e `5751ba3` a `main` não compila; a versão final da `main` atende a todos os critérios de saída.

### Observação sobre o nome da branch e o enunciado
O commit `00c29d8` alterou o `DESAFIO.md`, trocando o padrão de branch `resgate/equipe-NOME` por `resgate/discente-NOME`, e a branch de resgate foi criada como `resgate/discente-marilliabraz`. O enunciado original foi restaurado a partir do commit `799c5ec`, e a branch foi renomeada para `resgate/equipe-marilliabraz`. As mensagens dos merges `8587380` e `5751ba3` ainda citam o nome antigo porque foram feitos antes da renomeação.

### Observação sobre a reorganização dos commits da equipe
Depois da entrega inicial, os commits do resgate foram reorganizados com `git filter-branch` para definir o autor de cada um entre os integrantes e deixar as mensagens mais curtas. Como isso gera hashes novos, os hashes citados neste relatório já são os do histórico reorganizado, e a publicação exigiu `git push --force-with-lease`. Os commits anteriores ao resgate (`4574eee` a `799c5ec`, além das branches laterais e das tags) não foram alterados, então toda a investigação continua verificável.

## Auditoria de segurança (Etapa 7)
- **Ocorrência:** o arquivo `config/application.properties` foi versionado no commit `6572d8a` (tag `commit-perigoso`), de **Felipe Rocha**, em 03/09/2026 08:30, com `db.user=admin`, `db.password` e `api.token`.
- **Ação:** o arquivo foi removido da versão atual com `git rm` e adicionado ao `.gitignore` para não ser versionado novamente.
- **Por que apagar o arquivo não elimina o segredo:** o Git guarda cada versão de todos os arquivos. Remover o arquivo cria apenas um novo commit em que ele não existe; o commit `6572d8a` continua no histórico e qualquer pessoa com acesso ao repositório (inclusive clones e forks já feitos) consegue ver o segredo com `git show 6572d8a`. Eliminá-lo do histórico exigiria reescrever todos os commits seguintes (por exemplo, com `git filter-repo`) e forçar a atualização de todas as cópias, o que muda os hashes e não alcança cópias que já foram feitas. Por isso, a medida correta é considerar as credenciais **comprometidas e trocá-las** (nova senha e novo token), além de remover o arquivo e passar a usar variáveis de ambiente ou arquivos de configuração locais fora do Git.

## Validação final
Validação feita na `main` após o merge final:

- **Compilação:** `mvn clean package` → `BUILD SUCCESS`.
- **Login válido:** `admin / 12345678` → "Acesso autorizado".
- **Usuário incorreto:** `outro / 12345678` → "Acesso negado".
- **Senha incorreta:** `admin / errada` → "Acesso negado".
- **Usuário e senha incorretos:** `outro / errada` → "Acesso negado".
- **Cadastro:** após o login, o sistema lista `#1 | Notebook | Notebook corporativo | 2.1 kg | R$ 4500,00 | AGUARDANDO ENVIO | Entrega: Av. Goiás, 1000 - Sala 8, Goiânia/GO - CEP: 74000-000`, ou seja, a mercadoria é criada com um endereço de entrega.
- **README:** recuperado pelo histórico (sem reescrever do zero); informa requisitos (Java 17+, Maven 3.8+), comandos de compilação e execução e as credenciais de demonstração.
- **Segurança:** `config/application.properties` não existe mais na versão atual e está no `.gitignore`; `git grep` na versão atual não encontra a senha nem o token.
- **Histórico:** `git log --oneline --all --graph --decorate` mostra os commits de correção na branch de resgate e o merge na `main`.

## Pergunta de encerramento
**Como o uso adequado do controle de configuração reduz riscos técnicos, facilita auditorias e permite recuperar um projeto mesmo depois de alterações incorretas?**

O histórico do Git registra quem alterou o quê, quando e por quê. Neste resgate, isso permitiu identificar exatamente os cinco commits que introduziram os problemas e seus autores, comparar cada arquivo com a versão marcada como funcional (`v1.0.0-funcional`) e recuperar o arquivo excluído e o README sem reescrever nada do zero. Branches separadas guardaram versões úteis (`docs-readme`, `hotfix-login`, `feature-cadastro`) que puderam ser reaproveitadas, e o uso de `git revert` e `merge --no-ff` corrigiu o projeto sem apagar as evidências, mantendo a rastreabilidade para auditoria. Ao mesmo tempo, a atividade mostrou o limite dessa memória: um segredo versionado permanece no histórico, então a prevenção (`.gitignore`, revisão antes do merge, variáveis de ambiente) é tão importante quanto a correção.
