# Olist SeasonMaster

Rascunho para a Beatriz revisar. Versão fechada a partir do mapa revisado, do prompt revisado e do plano (seções 4.1 a 4.8). Dado público e anonimizado. Tabelas usadas: orders, order_items, products.

## Faz / não faz

**Faz.** Monta o cronograma de compras do lojista para o período que ele pedir. O exemplo já escrito é meados de setembro, Black Friday, horizonte de 60 dias. Lê o histórico de vendas em orders, order_items e products, devolve a lista ao lojista e, no máximo, três produtos em alta da mesma categoria. Os 14,3% de atraso em novembro e os 21,4% em março continuam o problema que esse cronograma ataca.

**Não faz.** Não abre painel de capacidade para a diretoria, não fala com transportadora e não alimenta o RiskShield. Não dispara a compra. Não inventa estoque, lead time nem taxa de crescimento: os três são dado externo e, se não forem informados, o passo que depende deles para. Não passa de três produtos na seção de sugestão.

## SIPOC

**Processo:** cronograma de compras pedido pelo lojista.

**Resultado para quem recebe:** o lojista recebe o relatório e decide o que comprar.

| Bloco | Conteúdo |
| :--- | :--- |
| Fornecedores | Lojista; orders; order_items; products. Estoque atual, lead time de reposição e taxa de crescimento só se alguém os informar por fora do dataset |
| Entradas | Pedido de projeção com o período (no exemplo, 60 dias até a Black Friday); histórico do mesmo período no ano anterior |
| Processo | 1. O lojista pede a projeção. 2. O histórico de itens sai de orders, order_items e products. 3. Estoque, lead time e crescimento entram só se estiverem declarados como dado externo. 4. O relatório é gravado, com no máximo três produtos em alta. 5. O lojista decide a compra. |
| Saídas | Relatório com produtos e quantidades no tempo, mais a seção “Sugestão de Outros Produtos em Alta” |
| Clientes | O lojista que pediu a projeção |

**O que o desenho pedia e as oito tabelas não contêm.** Estoque atual, lead time de reposição e taxa de crescimento. O fluxo e o prompt já usam os três. Não há amostra pública. Sem o dado, a versão não completa o número.

**Onde o item mais espera.** O relatório espera o lojista decidir a compra. O agente não compra. O relatório não publica um tempo de espera em minutos.

## Matriz de diagnóstico

Passos que o mapa e o prompt já escrevem. A primeira rota que resolve fecha a linha.

| Tarefa já escrita | P1 regra estável? | P2 interpretação? | P3 caminho variável, verificável, recuperável? | P4 julgamento? | Rota | Como o erro aparece e quem pega |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Histórico do lojista, déficit contra estoque, crescimento e cronograma | Sim, para a conta, e só se o estoque tiver sido informado | — | — | — | Conta determinística | Unidades “a adquirir” com estoque que ninguém informou. Sem estoque nas oito tabelas, o passo para. Não vira agente. |
| Três produtos em alta na categoria | Sim, se a conta usar só orders e products: os três com maior destaque | — | — | — | Regra de ordenação | Lista com mais de três itens, ou item cuja categoria não está em products |
| O lojista comprar o que está na lista | Não | Não | Não | Sim | Pessoa. A saída revisada é o relatório gravado | Compra disparada a partir do relatório. O lojista é quem decide |

## Problema, usuários, dados e indicadores

**Problema.** A dor é o pico de entregas. O impacto medido no dataset é 14,3% de atraso na Black Friday, em novembro, e 21,4% em março. A consequência que a seção 3 já escreve é a falta de folga da operação nesse pico. O trabalho desta versão é o cronograma que o lojista usa para se antecipar, no exemplo de 60 dias.

**Usuários.** O lojista pede a projeção na plataforma e recebe o relatório no mesmo fluxo. Diretoria, gerência de estoque e transportadora não voltam: estavam na seção 4, não no mapa revisado.

**Dados.**

| Dado | Onde está | Dono no desenho | Qualidade conhecida |
| :--- | :--- | :--- | :--- |
| Histórico de pedidos do lojista no período correspondente do ano anterior | orders | Dataset público | O relatório não descreve qualidade além de dado público e anonimizado |
| Produto e quantidade vendida | order_items | Dataset público | Idem |
| Categoria do produto | products | Dataset público | Idem |
| Estoque atual | Fora das oito tabelas | Dado externo, só se for informado | Sem amostra pública |
| Lead time de reposição | Fora das oito tabelas | Dado externo, só se for informado | Sem amostra pública |
| Taxa de crescimento esperada | Fora das oito tabelas | Dado externo, só se for informada | Sem amostra pública. O relatório não publica o percentual |

**Indicadores.** Alvo ou janela que o relatório não publica ficam em branco. Nenhum alvo novo.

| Indicador | Base | Alvo | Quando medir | Origem |
| :--- | :--- | :--- | :--- | :--- |
| Taxa de atraso em novembro (Black Friday) | 14,3% | Não publicado | Não publicado | Medido no dataset |
| Taxa de atraso em março | 21,4% | Não publicado | Não publicado | Medido no dataset |
| Receita salva no fim de ano | Base não publicada | R$ 231 mil | Não publicado | Premissa de benchmark |
| Economia de horas extras | Base não publicada | R$ 45 mil | Não publicado | Premissa de benchmark |
| Concordância do cronograma com o histórico | A lista parte de orders, order_items e products | A quantidade não aparece sem estoque informado | Não publicado | Métrica técnica desta versão. O relatório não fixa amostra nem percentual de acerto |

Números de exemplo ou de regra, não são indicador: horizonte de 60 dias no caso; no máximo 3 produtos no prompt.

## Riscos

Salvaguarda igual à rota: sem estoque informado não há quantidade; a compra fica com o lojista; a sugestão para em três itens.

| Risco | Como acontece neste processo | Prob. | Sev. | Salvaguarda nesta versão | Detectar | Interromper | Avaliar | Recuperar |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Resposta sem fundamento na fonte | Quantidade “a adquirir” calculada com estoque que as oito tabelas não têm, ou crescimento aplicado sem taxa informada | Alta, se o passo rodar sem o dado externo | Alta | Sem estoque, lead time ou taxa informados, o passo correspondente para. O histórico em orders permanece | Relatório com unidades e sem estoque declarado | O lojista descarta o relatório no mesmo dia | Ver qual número não tem fonte | Tirar a quantidade inventada e deixar só o histórico |
| Dado pessoal a mais | O cronograma puxa cliente, nota ou dado de pagamento que a lista não usa | Baixa, se a leitura ficar em orders, order_items e products | Alta | Não lê customers, order_reviews, payments nem geolocation | Olhar as tabelas citadas na saída | Parar a saída que trouxer outra tabela | Ver o que o lojista já recebeu | Reenviar o relatório só com pedido, item e produto |
| Decisão desigual entre grupos parecidos | A sugestão de produtos em alta muda de categoria ou passa de três itens para um lojista e não para outro | Média, se a regra dos três não for a mesma | Média | Os três itens saem de orders e products, na mesma categoria, pela mesma ordenação | Contar os itens da seção de sugestão | O lojista descarta a seção | Comparar a categoria em products | Cortar a lista em três |
| Compra tratada como já decidida | A lista “a adquirir” segue sozinha para um pedido de compra | Média, se a saída não disser que é relatório | Alta | A saída é o relatório gravado. O lojista decide. Os três produtos são sugestão, não decisão | Relatório que dispare compra | O lojista não confirma a compra | Ver se houve pedido sem o lojista | Cancelar o que tiver saído sem essa decisão |

**Quem tira a iniciativa do fluxo no mesmo dia:** o lojista, ao descartar o relatório.

## Jornada

**Hipótese:** se um relatório de compras ajudar o lojista quando ele pede, em meados de setembro, a projeção da Black Friday em 60 dias, esperamos o cronograma baseado no histórico, medido pela presença de fonte para cada quantidade, com a compra sempre decidida pelo lojista.

**Papel nesta versão:** recomendar.

**Caso:** o do mapa. Lojista parceiro, meados de setembro, Black Friday, horizonte de 60 dias. O agente devolve o cronograma e sugere até três produtos de uma subcategoria em alta.

| # | Contexto | Intenção | Decisão | Controle |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Meados de setembro. O lojista pede a projeção na plataforma, período de 60 dias até a Black Friday | Saber o que comprar para o pico | Abrir o pedido do lojista. Ainda não há lista | Nenhum ainda |
| 2 | Histórico do mesmo período no ano anterior | Montar a base da lista | Ler produtos e quantidades em orders, order_items e products | Sem histórico do lojista, não há cronograma |
| 3 | Estoque, lead time e taxa de crescimento | Não inventar unidades | Usar o dado só se foi informado como externo. Se faltar, esse passo para | Ninguém completa estoque por suposição |
| 4 | Histórico pronto e, se houver, o dado externo | Entregar a lista | Distribuir no tempo só com o lead time informado. Acrescentar no máximo três produtos da mesma categoria, ordenados em orders e products | A seção de sugestão não passa de três |
| 5 | Relatório gravado | O lojista decidir a compra | O agente para no relatório | O lojista compra, adia ou descarta |
| 6 | Quadro de recusa. Estoque não informado, ou a sugestão viria com mais de três itens | Não fabricar unidades nem alongar a lista | Recusar a quantidade e cortar a sugestão em três. O histórico de orders pode seguir sozinho, sem quantidade “a adquirir” | O lojista vê o que faltou, não um número inventado |

**Sequência para atualizar o Draw.io.** Substituir o diagrama antigo por esta linha, sem tela:

1. Lojista pede a projeção (exemplo: setembro, 60 dias, Black Friday).
2. Ler histórico em orders, order_items e products.
3. Decisão: estoque, lead time e taxa de crescimento foram informados? Se não, não calcular unidades.
4. Montar o relatório.
5. Decisão: há produtos da mesma categoria em orders? Se sim, incluir no máximo três.
6. Gravar o relatório para o lojista.
7. Decisão humana: o lojista compra ou descarta.
8. Saída de recusa: sem estoque, fim sem quantidade inventada.

## Entradas, saídas e escalonamento

**Papel observável:** recomendar o cronograma de compras do período pedido.

**Objetivo:** o lojista vê o histórico e, só com dado externo informado, as quantidades no tempo.

**Não faz:** comprar, painel de capacidade, handoff ao RiskShield, inventar estoque.

| Pergunta | Resposta |
| :--- | :--- |
| Quem usa? | O lojista, na projeção que ele mesmo pediu |
| Que informação recebe? | Período, histórico em orders, order_items e products, e o dado externo se tiver sido informado |
| Que decisão toma? | Nenhuma compra. Entrega o relatório e até três sugestões |
| Quando para e escala? | Sem histórico do lojista; sem estoque, não há quantidade; a compra é do lojista |

**Entradas**

| Entrada | Obrigatória? | Se faltar |
| :--- | :--- | :--- |
| Pedido do lojista com o período | Sim | Não há cronograma |
| Histórico em orders | Sim | Não há cronograma |
| Item e quantidade em order_items | Sim, para a lista por produto | Sem item, não há quantidade vendida |
| Categoria em products | Sim, para os três produtos em alta | Sem categoria, não há a seção de sugestão. O histórico do próprio lojista pode seguir |
| Estoque atual | Não. Dado externo | Não calcula unidades a adquirir |
| Lead time de reposição | Não. Dado externo | Não distribui as compras no tempo |
| Taxa de crescimento | Não. Dado externo | Não aplica crescimento |

**Saídas**

| Saída | Para quem | O que fica registrado |
| :--- | :--- | :--- |
| Relatório de produtos e quantidades no tempo, ou só o histórico se o estoque não foi informado | Lojista | Período, tabelas lidas, e a marca de qual dado externo entrou |
| Seção “Sugestão de Outros Produtos em Alta”, com no máximo três itens | Lojista | Categoria em products e a ordenação usada em orders |
| Decisão de compra | Lojista, fora do agente | O agente não grava compra |

**Capacidades desta versão:** recomendar. Não executa compra.

### Matriz de escalonamento

| Situação | Ação |
| :--- | :--- |
| Dados suficientes e regra clara | Entrega o histórico e, com estoque, lead time e taxa informados, o cronograma. Até três produtos em alta |
| Dado incompleto | Sem histórico, para. Sem estoque, não inventa unidades. Sem lead time, não distribui no tempo. Sem taxa, não aplica crescimento |
| Ambíguo ou sem critério estável | “Alto índice de crescimento” sem número publicado não vira um corte novo. A sugestão fica nos três maiores destaques já escritos no prompt, em orders e products |
| Impacto sensível | A compra fica com o lojista. O relatório não dispara pedido |

**Como saber que o protótipo está errado.** O cronograma traz unidades de estoque que ninguém informou. Ou a seção de sugestão tem mais de três produtos. Ou a compra saiu sem o lojista.

## Prompt

**Objetivo.** Criar o cronograma de compras do lojista para o período pedido, para ele decidir o que comprar antes do pico.

**Contexto.** orders, order_items e products. Estoque atual, lead time de reposição e taxa de crescimento são dado externo, separados do histórico. O exemplo de período é o do caso: meados de setembro, Black Friday, 60 dias.

**Instrução principal.** Liste o histórico do mesmo período no ano anterior. Só calcule déficit, crescimento e datas de compra se o dado externo correspondente tiver sido informado. Feche com no máximo três produtos em alta. Não compre.

**Resultado esperado.** Relatório para o lojista, com a seção “Sugestão de Outros Produtos em Alta” limitada a três itens, e a compra por conta dele.

### Texto

Você é o Olist SeasonMaster. Você prepara o cronograma de compras que o lojista pediu. Você não compra, não abre painel para a diretoria e não chama o RiskShield.

Contexto que você pode ler:

- orders, order_items e products: o que o lojista vendeu no mesmo período do ano anterior, e a categoria.
- Estoque atual, lead time de reposição e taxa de crescimento, somente se tiverem sido informados como dado externo. Não estão nas oito tabelas. Se não foram informados, não os invente.

Instrução:

1. Liste os produtos e as quantidades vendidas pelo lojista no período correspondente do ano passado, a partir de orders, order_items e products. Se não houver histórico, pare.
2. Compare com o estoque atual só se ele foi informado. Se não foi, não escreva quantidade a adquirir.
3. Aplique taxa de crescimento só se ela foi informada. O relatório não publica essa taxa. Não escolha um percentual.
4. Distribua as compras no período pedido, descontando o lead time, só se o lead time foi informado. No exemplo já escrito, o período é de 60 dias até a Black Friday, pedido em meados de setembro.
5. Na mesma categoria em products, escolha no máximo três produtos com maior destaque de vendas em orders. Não passe de três. Não trate essa lista como decisão de compra.

Regras: a seção final se chama “Sugestão de Outros Produtos em Alta”. A compra fica com o lojista.

Resultado: o relatório gravado para o lojista, com o que veio do histórico e, só onde o dado externo existir, as quantidades no tempo.
