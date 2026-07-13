# Desafio DevSecOps — Gerenciador de Tarefas

## Sobre o Projeto
Este repositório faz parte do desafio prático do módulo de DevSecOps da ADA Tech.
Você receberá este projeto com vulnerabilidades propositais e uma pipeline incompleta.
Seu objetivo é **implementar a pipeline de segurança** e **corrigir as vulnerabilidades**.

## Estado atual
A pipeline está **incompleta**. Os steps de segurança precisam ser implementados por você.

## Sua missão
1. Implementar os steps de segurança no `pipeline.yml`
2. Fazer a pipeline **quebrar** ao detectar os problemas
3. Corrigir as vulnerabilidades encontradas
4. Fazer a pipeline **passar** com tudo verde ✅
5. Documentar o funcionamento da pipeline neste README

## O que implementar
- [ ] Secrets Scanning com **Gitleaks**
- [ ] SAST com **Semgrep**
- [ ] SCA com **Grype**
- [ ] Deploy com **GitHub Pages**

## Como a pipeline funciona

A *pipeline* é composta por 6 passos, alguns dos quais realizam checagens em busca de erros e vulnerabilidades que possam afetar a segurança da aplicação, interrompendo sua publicação em caso de falha. Segue abaixo detalhamento dos passos:

1. **Checkout do código:** Obtém o repositório de código para trabalho

2. **Build:** Realiza o *build* da aplicação. Neste caso já se encontra feito, então so verifica.

3. **Secrets Scanning:** Utiliza a ferramenta Gitleaks para vericar se não há segredos como senhas ou chaves de API expostos no código, algo que poderia ser explorado por um visitante utilizando a ferramenta de inspeção de código do navegador ou mesmo o próprio repositório do GitHub. Caso encontre algo errado, o passo falha para que a *pipeline* não coloque no ar uma aplicação vulnerável e com segredos expostos. O relatorio gerado por este passo pode ser consultado na execução do *GitHub Actions* do projeto no GitHub.

4. **SAST - Semgrep:** Utiliza a ferramenta *Semgrep* para realizar a análise estática do código em busca de vulnerabilidades conhecidas, oriundas de más práticas de desenvolvimento. Caso encontre algo errado, o passo falha para que a *pipeline* não coloque no ar uma aplicação vulnerável. O relatório deste passo é enviado para a aba *Segurança de Qualidade* do projeto no GitHub.

5. **SCA - Trivy:** Utiliza a ferramenta *Trivy* para realizar a análise de cadeia de suprimentos de *software*, procurando por bibliotecas ou outros componentes de software com alguma vulnerabilidade conhecida, o que por consequencia torna vulnerável a aplicação que os utiliza. Caso encontre algo errado com grau de critidade *HIGH* ou *CRITICAL*, o passo falha para que a *pipeline* não coloque no ar uma aplicação vulnerável.

6. **Configurar GitHub Pages:** Publica no *GitHub Pages* o resultado do *build* do projeto, caso tenha passado pelas validações dos passos 3 a 5, tornando-o a aplicação acessível através da internet.

### Questões identificados que não foram apontadas pelas ferramentas no pipeline.

* Em `src/script.js` o Gitleaks identificou vazamento de API key, mas não de senha do banco de dados. É possível configurar o a ferramenta para acusar trechos de código com a palavra "password", mas isso pode gerar falsos positivos.

* É importante frisar que o melhor momento para identificar o vazamento de credenciais é antes do commit. No presente caso, as credenciais já foram "eternizadas" no repositório git antes mesmo da execução da ferramenta.

* Em `src/script.js` o uso de `innerHTML` torna a página vulnerável a XSS, uma vez que possui comportamento análogo ao uso de `eval()`, executando como código o input do usuário. É recomendado o uso de `innerText`, `textContent` ou `insertAdjacentText()`. A ferramenta Semgrep identificou corretamente o uso de `eval()`, mas não o de `innerHTML`, pois o processo de taint analysis não é capaz de identificar que a `constant input` se refere a entrada de usuário. É possível configurar a ferramenta para buscar padõres específicos de código configurando o `semgrep-rules.yaml`.

* Os componentes nas versões listadas do `package.json` são antigos e contêm vulnerabilidades conhecidas. A ferramenta de SCA não os acusou pois não foi realizado um build real com npm install, e portanto não há arquivo `package-lock.json` ("SBOM") no pacote analisado.

## URL de Produção
https://nunks.github.io/projeto-devsecop-desafio/
