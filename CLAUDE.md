# Guia do projeto: Classes Gramaticais (e base para novos jogos de estudo)

Este arquivo resume as decisões, o funcionamento e as lições do jogo `index.html`,
para que uma nova conversa possa continuar este jogo ou criar outro no mesmo estilo.

## Objetivo do jogo
Aprender o máximo possível em poucas horas: **muitas repetições rápidas**, **memorização visual
por cores** e **foco no que o jogador ainda não sabe**. Jogo em português do Brasil.

## Arquitetura (manter assim em jogos novos)
- **Um único arquivo `index.html`**, com HTML, CSS e JavaScript juntos, sem backend e sem dependências
  (só Google Fonts). Abre em qualquer navegador e pode ser hospedado de graça no GitHub Pages.
- **Sem IA durante o jogo**: as frases ficam num banco fixo, revisado à mão. Assim o jogo é grátis,
  funciona offline e não ensina errado. (Gerar frases com IA na hora tem custo por jogador, exige
  servidor para esconder a chave e pode classificar errado.)
- **Progresso salvo em `localStorage`**, separado por navegador e jogador:
  - `classesGramaticais.v1`: estado (nível, pontos, domínio, preferências);
  - `classesGramaticais.hist.v1`: histórico completo de respostas.

## Formato do banco de frases
Uma frase por linha, com tokens separados por espaço: `palavra|classe.subclasse|nota_opcional`
- `_` no lugar de espaço, tanto em locuções (`para_que|conj.fin`) quanto nas notas.
- Pontuação vira um token separado (`.` `,` `!` `?` `;`).
- A nota explica casos difíceis e aparece quando o jogador erra.
- Exemplo: `O|art.def livro|sub.cc que|pron.rel|retoma_'livro'_(=_o_qual) comprei|v.pri é|v.lig ótimo|adj .`
- Hoje são 494 frases. Cada um dos 47 tipos da árvore tem pelo menos 5 exemplos.
- **Evitar casos em que as gramáticas discordam** (ex.: "nunca", "só", "também", "onde" relativo,
  "de repente", "se" apassivador, pronome reto depois de preposição como "para ela").
- Validar por script: tags válidas, frases sem duplicatas, contagem mínima por tipo.

## Mecânicas que funcionaram (reaproveitar)
1. **Cores fixas por classe**, com tons da mesma cor para as subclasses. A palavra fica colorida e
   rotulada assim que é respondida. Legenda sempre visível.
2. **Feedback instantâneo**: acerto avança sozinho em menos de 0,5 s; erro mostra a resposta certa e
   uma explicação de 1 linha, e o jogador continua com Enter.
3. **Frase termina e a próxima começa sozinha**, depois de 1,5 s mostrando a frase toda colorida.
4. **Teclado completo**: teclas 1–9 e 0 respondem, Enter/Espaço continuam, H pede dica, L mostra a legenda.
5. **Ordem recomendada: verbo primeiro.** O jogador marca todos os verbos e depois classifica o resto,
   porque o verbo define as orações e isso decide casos como "que", "se" e "como".
   Também existe a opção "esquerda → direita".
6. **12 níveis com dificuldade crescente**, com subida automática (85% nas últimas 20 respostas),
   descida automática (abaixo de 50%) e seleção manual:
   - 1–2: classes (4 → 10 opções);
   - 3–6: subclasses aos poucos, com opções reduzidas e pegadinhas plausíveis; o nível 6 prioriza ambíguos;
   - 7–10: **árvore completa** (classe → tipo → subtipo, sempre com TODAS as opções); o 9 tira as cores
     dos botões; o 10 embaralha as opções e tira a dica;
   - 11–12: **escrita por extenso** só do que o jogador já domina (etapa 3+ no 11, 2+ no 12). A correção
     ignora acentos, maiúsculas, "de/do caso" e a ordem das palavras, e aceita 1 letra errada em
     palavras com 5 letras ou mais.
7. **Domínio por etapas (repetição espaçada por contagem)**: acertar 5→3→2→1→1→1 vezes seguidas faz a
   pergunta ser pulada 5→10→20→40→80→160 vezes. Um erro volta ao início. Mais de 5 dias sem responder
   também faz a pergunta voltar. Acerto com dica não conta. Cada tipo de pergunta (só a classe /
   classe + subclasse / árvore) tem contagem própria. Frases com o que o jogador ainda não domina
   aparecem mais.
8. **Histórico completo**: cada resposta é guardada com data, nível, modo, frase, palavra, resposta
   certa, resposta dada e uso de dica. Estatísticas mostram filtros e um botão "Copiar CSV".
9. **Proteção contra clique duplo**: ignora um segundo clique em menos de 320 ms e cliques em botões
   que acabaram de aparecer. O teclado não sofre esse bloqueio.
10. **Dica** elimina metade das opções erradas e mostra o "teste prático" da classe; o acerto com dica
    vale metade dos pontos.

## Como testar
- Playwright já vem instalado no ambiente de nuvem: `require('playwright')` funciona direto.
- Testes que valeram a pena: jogar níveis variados pelo teclado sem erro no console; semear o
  `localStorage` para testar domínio e escrita; testar a correção da escrita isolada, com uma lista de
  respostas certas e erradas.
- Nos testes, espaçar os cliques em mais de 320 ms, por causa da proteção contra clique duplo.

## Publicação
- GitHub Pages: repositório público com `index.html` na raiz → Settings → Pages → branch `main`, pasta `/`.
- Lições: a sessão do Claude **não consegue criar repositórios nem ativar o Pages**; o usuário cria o
  repositório vazio e liga o Pages. Para o Claude enviar código, o app do Claude no GitHub precisa ter
  acesso ao repositório (github.com/settings/installations → Claude → Configure).
- Também dá para publicar como artefato no claude.ai (link privado até o dono compartilhar).

## Ideias para próximos jogos no mesmo estilo
- Trocar o "banco de frases" por outro conteúdo anotado: funções sintáticas (sujeito, predicado,
  objeto…), tempos e modos verbais, figuras de linguagem, regência, crase, ortografia, vocabulário
  em outra língua, tabuada, capitais, etc.
- Manter: um único HTML, banco revisado, cores fixas por categoria, árvore de classificação,
  domínio por etapas, histórico, teclado e ritmo rápido.
