[README.md](https://github.com/user-attachments/files/31669721/README.md)
# Painel PipeLovers × Grupo Sem Parar

Painel de acompanhamento de consumo da Universidade PipeLovers pelo Grupo Sem Parar, construído com a **mesma arquitetura e lógica** do painel de referência (`gabrielsbrasil/redarbor`): uma única página (`index.html`) que busca os dados **ao vivo** direto do seu repositório no GitHub (via `raw.githubusercontent.com`), sem backend e sem build — só GitHub Pages.

A única diferença estrutural em relação ao painel de referência é que a hierarquia da Grupo Sem Parar tem **um nível a mais**: **Área → Diretoria → Gerência (Gestor do Líder) → Liderança (Líder) → Membros**, em vez de Área → Gerência → Liderança. Toda a navegação, filtros, exportação e links de acesso foram estendidos para respeitar essa estrutura.

## Estrutura de arquivos

```
index.html                              → o painel inteiro (HTML+CSS+JS em um único arquivo)
vendor/chart.umd.min.js                 → Chart.js vendorizado localmente (não usa CDN)
data/membros.csv                        → hierarquia: Area, Diretoria, Gerencia, Lider, Nome Completo, E-mail
data/consumo.csv                        → aulas assistidas: Email, Conteúdo, Data (envie novos arquivos "consumo*.csv" aqui)
data/pdi.csv                            → plano de desenvolvimento individual: Email, Aula PDI (1 linha por aula)
data/manifest.json                      → lista dos CSVs de consumo (gerada automaticamente pelo GitHub Actions)
.github/workflows/build-data-manifest.yml → workflow que regenera data/manifest.json a cada push em data/
```

## Passo a passo para publicar no GitHub Pages

1. Acesse o repositório: **https://github.com/thaynarasantos-sketch/GrupoSemParar**
2. Clique em **Add file → Upload files**.
3. Arraste TODA a pasta descompactada deste pacote (mantendo a estrutura `data/`, `vendor/` e `.github/workflows/`, com o `index.html` na raiz).
4. Escreva uma mensagem de commit (ex.: `Publicação inicial do painel`) e clique em **Commit changes**.
5. Vá em **Settings → Pages**. Em **Source**, selecione a branch `main` e a pasta `/ (root)`. Clique em **Save**.
6. Aguarde 1–2 minutos. O GitHub mostrará o endereço público:
   `https://thaynarasantos-sketch.github.io/GrupoSemParar/`
7. Abra esse link — o painel carrega os dados de `data/membros.csv`, `data/consumo*.csv` e `data/pdi.csv` automaticamente.

> Na primeira publicação, o GitHub Actions ainda vai rodar pela primeira vez para gerar `data/manifest.json` — leva menos de 1 minuto. Se você já subiu o `data/manifest.json` incluso neste pacote, o painel funciona imediatamente mesmo antes do Actions rodar.

## Como atualizar os dados (novo CSV de consumo) — **isso é automático**

Diferente do modelo anterior (arquivos HTML estáticos por recorte), este painel busca os dados **ao vivo** a cada carregamento de página. Ou seja:

1. Envie um novo arquivo `consumo*.csv` (ex.: `consumo.csv`, `consumo_2026-09.csv`) para a pasta `data/` do repositório (Add file → Upload files → substituir/adicionar → Commit).
2. O GitHub Actions (`.github/workflows/build-data-manifest.yml`) roda automaticamente e regenera `data/manifest.json` com a lista de todos os arquivos `consumo*.csv` presentes na pasta.
3. Na próxima vez que alguém abrir o painel (ou clicar em **"⟳ Atualizar dados"**), os números já aparecem atualizados — **sem precisar gerar ou reenviar nenhum arquivo HTML**.

Você pode manter um único `consumo.csv` e sempre substituí-lo, **ou** ir empilhando arquivos novos (`consumo_2026-09.csv`, `consumo_2026-10.csv`, ...) — o painel soma automaticamente todos os arquivos que começam com "consumo" e terminam em ".csv".

Para atualizar `data/membros.csv` (mudanças na hierarquia) ou `data/pdi.csv` (novos PDIs), o processo é o mesmo: suba o arquivo atualizado com o mesmo nome/caminho e o painel reflete a mudança no próximo carregamento.

## Links de acesso e níveis de hierarquia

O botão **"🔗 Links de acesso"** (visível apenas na visão geral, sem parâmetros de URL) gera um link para cada nível da hierarquia, agrupado por Área → Diretoria → Gerência → Liderança:

- **Link geral** (sem parâmetro): mostra tudo — visão da diretoria/administração.
- `?area=<slug>` — mostra a Área inteira: todas as suas diretorias, gerências, líderes e membros.
- `?diretoria=<slug>` — mostra a Diretoria: seus gestores (gerências), os líderes de cada gestor e os membros de cada líder.
- `?gerencia=<slug>` — mostra a Gerência (o "Gestor do Líder"): seus líderes e os membros de cada um.
- `?time=<slug>` — mostra só aquela Liderança e a lista de membros dela (esconde o menu lateral por completo).

Exemplo de link travado em uma gerência:
`https://thaynarasantos-sketch.github.io/GrupoSemParar/?gerencia=vb--afonsa-vianna--wagner-marini`

**Importante — igual ao painel de referência**: esses parâmetros de URL são uma conveniência de **navegação** (abrem o painel já filtrado, escondendo o resto do menu), e **não são um mecanismo de segurança**. O painel é uma página estática: qualquer pessoa com acesso ao link técnico completo do repositório/CSV poderia, em tese, inspecionar os dados carregados no navegador. Trate a distribuição dos links de acesso como um controle organizacional (cada liderança só recebe o link do seu próprio recorte), não como uma barreira técnica.

## O que o painel calcula

- **% que logou / assistiu no período** — membros com pelo menos 1 aula no intervalo de datas selecionado.
- **% que assistiu 3+ aulas** — membros com 3+ aulas distintas no período.
- **Média de aulas por membro** — total de aulas assistidas no período ÷ total de membros do recorte.
- Todos os indicadores acima são recalculados automaticamente para **cada nível** (geral, por área, por diretoria, por gerência, por liderança) conforme você navega ou filtra.
- **Progresso do PDI** — para membros com PDI atribuído (`data/pdi.csv`), % de aulas do PDI já concluídas (cruzando pelo nome da aula com `data/consumo.csv`). Membros sem PDI aparecem sem indicador de progresso, sem contar no denominador.

## Observações sobre os dados de PDI

- Nem todo membro tem PDI: alguns ainda não tiveram o PDI entregue, outros não terão PDI, e o painel simplesmente não mostra progresso nesses casos (não entra na média).
- O cruzamento é feito pelo **nome exato da aula** (normalizado: sem acentos, minúsculo, sem pontuação final) — o nome extraído do plano de PDI precisa bater com a coluna "Conteúdo" de `data/consumo.csv`.
