[README.md](https://github.com/user-attachments/files/31668536/README.md)
# Painel PipeLovers × Grupo Sem Parar — Guia de publicação no GitHub Pages

Este pacote contém o dashboard estático de acompanhamento de consumo da Universidade PipeLovers pelo Grupo Sem Parar, com os dados do `Hierarquia.csv`, `consumo.csv` e do PDI (planilha do Drive) já processados. O layout segue o padrão do painel de referência (sidebar com árvore de navegação, header com filtros, KPI cards com gradiente, gráficos e dropdown de "Links de acesso" agrupado por hierarquia), mantendo a arquitetura de arquivos separados por recorte para isolamento real dos dados.

## O que tem aqui

```
index.html                 → Visão Geral (TODOS os times e áreas) — uso restrito à diretoria/administração
area/*.html                → 1 página por Área (VB, SEM PARAR, CTF, VP SR)
diretoria/*.html           → 1 página por Diretoria
gestor/*.html              → 1 página por Gestão
lideranca/*.html           → 1 página por Liderança direta
catalog.json                → índice de todas as páginas geradas (uso interno)
links_acesso.csv           → lista de TODOS os links, prontos para distribuir a cada liderança/gestão/diretoria
```

Cada página é **autocontida** (HTML+CSS+JS em um único arquivo) e traz **apenas os dados daquele recorte** — quem recebe o link de uma liderança específica não consegue ver os dados de outra liderança, gestão, diretoria ou área, mesmo olhando o código-fonte da página. Só quem recebe o link do `index.html` (Visão Geral) vê o todo.

O menu lateral (sidebar) e o dropdown **"🔗 Links de acesso"** de cada página mostram apenas os links do próprio recorte para baixo na hierarquia (nunca de fora dele) — exatamente como no painel de referência, mas com isolamento real garantido por arquivos físicos separados em vez de um único app com todos os dados carregados no navegador.

## Passo a passo para publicar no GitHub Pages

1. **Acesse o repositório**: https://github.com/thaynarasantos-sketch/GrupoSemParar
2. Clique em **Add file → Upload files**.
3. Arraste TODA a pasta descompactada deste pacote (mantendo a estrutura de pastas `area/`, `diretoria/`, `gestor/`, `lideranca/` e o `index.html` na raiz do repositório). O GitHub preserva as subpastas automaticamente quando você arrasta a árvore de arquivos.
4. Escreva uma mensagem de commit, ex: `Atualização do painel - 01/09/2026`, e clique em **Commit changes**.
5. Vá em **Settings → Pages** (menu lateral do repositório).
6. Em **Source**, selecione a branch `main` (ou `master`) e a pasta `/ (root)`. Clique em **Save**.
7. Aguarde 1–2 minutos. O GitHub mostrará o endereço público, algo como:
   `https://thaynarasantos-sketch.github.io/GrupoSemParar/`
8. Teste o link da Visão Geral: `https://thaynarasantos-sketch.github.io/GrupoSemParar/index.html`
9. Abra o arquivo **`links_acesso.csv`** — ele já traz o link final de cada liderança/gestão/diretoria/área para você distribuir. Alternativamente, dentro de cada página, use o botão **"🔗 Links de acesso"** para copiar o link de qualquer subnível daquele recorte.

## Como atualizar os dados todo dia (novo CSV "consumo")

O CSV `consumo` não é lido "ao vivo" pelo site (GitHub Pages é 100% estático, não roda servidor). Para atualizar os números:

1. Envie o novo arquivo `consumo.csv` para esta conversa com a Claude.
2. A Claude reprocessa os dados (consumo + PDI) e gera novamente todos os arquivos `.html`.
3. Você recebe o pacote atualizado e repete o **Passo a passo** acima (Add file → Upload files → Commit). Isso substitui os arquivos antigos no repositório e os links continuam os mesmos — só o conteúdo é atualizado.

> Dica: no passo de upload, o GitHub avisa que os arquivos já existem e pergunta se quer substituir — confirme a substituição para atualizar o painel mantendo os mesmos links.

## O que cada página permite fazer

- **Navegar pela árvore lateral** (sidebar) — clique em qualquer área/diretoria/gestão/liderança dentro do recorte para filtrar a página inteira (KPIs, gráficos, tabelas) para aquele grupo.
- **Filtrar por data da aula** (data em que a aula foi assistida/concluída) e por **membros específicos** — todos os indicadores recalculam na hora.
- **Ver gráficos**: aulas assistidas por dia, ativos vs. inativos (donut) e membros mais ativos (top 8).
- **Ver tabelas comparativas** por área/diretoria/gestão/liderança (conforme o recorte da página).
- **Clicar em um membro** para ver a lista completa de aulas assistidas com data, e o progresso do PDI (quando o membro tem PDI ativo).
- **Copiar links de acesso** de qualquer subnível dentro do próprio recorte (botão "🔗 Links de acesso").
- **Exportar CSV** — baixa exatamente os dados filtrados na tela.
- **Exportar PDF** — abre a janela de impressão do navegador; escolha "Salvar como PDF" no destino.

## Indicadores calculados

- **% que logou / assistiu no período** — membros com pelo menos 1 aula no intervalo de datas selecionado, sobre o total do recorte.
- **% que assistiu 3+ aulas** — membros com 3 ou mais aulas distintas no período, sobre o total do recorte.
- **Média de aulas por membro** — total de aulas assistidas no período ÷ total de membros do recorte.
- **Progresso do PDI** — para membros com PDI ativo (aulas do PDI identificadas a partir do PDF), % de aulas do PDI já concluídas (cruzando pelo nome da aula no CSV de consumo). Também é calculado o **% que concluiu o PDI** (chegou a 100%).

## Observações sobre os dados de PDI

- Nem todo membro tem PDI: alguns ainda não tiveram o PDI entregue ("Pendente"), outros não terão PDI ("Sem PDI"), e alguns não estavam na planilha de PDI enviada (aparecem como "—"). Nenhum desses casos tem progresso calculado, como solicitado.
- O cruzamento do progresso do PDI é feito pelo **nome exato da aula**: o nome extraído do PDF do PDI precisa bater com a coluna "Nome da aula" do CSV de consumo.
