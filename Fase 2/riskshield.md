# Olist RiskShield

Rascunho para a Beatriz revisar. Versão fechada a partir do mapa revisado, do prompt revisado e do plano (seções 4.1 a 4.8). Dado público e anonimizado. Tabelas usadas: customers, orders, order_reviews, sellers.

## Faz / não faz

**Faz.** Quando um pedido é confirmado, classifica o risco de atraso da rota UF do vendedor × UF do comprador nas três faixas que o prompt já escreve (baixo, médio, alto), cita o percentual de atraso calculado e alerta o seller. Em separado, se a nota do cliente em order_reviews for igual ou menor que 3, marca o cliente como cenário crítico, sem mexer no percentual da rota. O caso de Pernambuco na Black Friday, rota SP–PE, permanece o exemplo.

**Não faz.** Não calcula escore de 0–100%. Não chama o OmniCare. Não envia voucher de R$ 10, não grava desconto em programa de fidelidade e não faz upgrade de frete. Esses três efeitos podem aparecer só como recomendação, e uma pessoa confirma antes de qualquer um deles sair. Não usa CPF nem CNPJ. Não trata nota baixa como insumo do percentual da rota.

Cinco porquês, só neste agente, porque o caso pedia um agente que manda voucher e o prompt é uma regra de faixa:

1. A nota 1 acompanha o atraso: 46% quando há atraso, 7% quando a entrega está no prazo. Medido no dataset (seção 3).
2. O caso usa uma rota com histórico alto de atraso, SP–PE.
3. O prompt já transforma esse histórico em faixa, por percentual.
4. Voucher, desconto e frete são outro passo, e o prompt não os executa.
5. A fricção que a seção 3 mede é o atraso da rota. A faixa responde a isso. O dinheiro fica com uma pessoa.

## SIPOC

**Processo:** classificar a rota do pedido confirmado e alertar o seller.

**Resultado para quem recebe:** o seller recebe a faixa e o percentual da rota. O cliente só recebe comunicação de voucher, desconto ou frete depois que uma pessoa confirma a recomendação.

| Bloco | Conteúdo |
| :--- | :--- |
| Fornecedores | Pedido confirmado; orders (status e prazos); customers e sellers (UF); order_reviews (nota), pela chave customer_id já usada na seção 3 |
| Entradas | Pedido novo; par de UF; histórico de prazos da mesma rota; nota do cliente, se existir |
| Processo | 1. O pedido é confirmado. 2. A rota UF–UF é lida em orders, customers e sellers. 3. O percentual de atraso da rota vira uma das três faixas. 4. A nota igual ou menor que 3, se houver, marca o cliente como crítico, fora do percentual. 5. O seller é avisado. 6. Uma pessoa confirma voucher, desconto ou frete, se houver recomendação. 7. O cliente recebe comunicação só depois dessa confirmação. |
| Saídas | Faixa da rota com o percentual citado; alerta ao seller; recomendação aguardando confirmação |
| Clientes | Seller, que recebe o alerta no fluxo; cliente, que só é comunicado depois da confirmação |

**O que o desenho pedia e as oito tabelas não contêm.** Programa de fidelidade, voucher e upgrade de frete não são tabela. O mapa os tratava como ação gravada. Nesta versão ficam recomendação.

**Onde o item mais espera.** A recomendação de voucher, desconto ou frete espera a pessoa. O cliente não é avisado antes disso. O relatório não publica um tempo de espera em minutos.

## Matriz de diagnóstico

Passos que o mapa e o prompt já escrevem. A primeira rota que resolve fecha a linha.

| Tarefa já escrita | P1 regra estável? | P2 interpretação? | P3 caminho variável, verificável, recuperável? | P4 julgamento? | Rota | Como o erro aparece e quem pega |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Percentual de atraso por par de UF e três faixas | Sim. O prompt manda seguir o corte. | — | — | — | Regra | Faixa sem o percentual calculado. A pessoa que confirma a recomendação vê o número. O “cálculo probabilístico” do fluxo não tem conta no prompt e não entra. |
| Nota igual ou menor que 3 classifica o cliente como crítico, fora a rota | Sim. O corte é o 3 do fluxo revisado, aplicado em order_reviews. | — | — | — | Regra | Nota usada de novo dentro do percentual da rota. A versão não faz isso. |
| Voucher de R$ 10, desconto de fidelidade ou upgrade de frete | Não. O prompt não executa nenhum dos três. | Não | Não. Valor no meio, e o caso disparava o voucher sem pessoa. | Sim. A pessoa confirma. | Pessoa confirma. O sistema só recomenda. | Voucher ou desconto gravado sem confirmação, como o caso e o fluxo faziam. A pessoa segura o efeito. |

## Problema, usuários, dados e indicadores

**Problema.** A dor é a entrega atrasada virar nota 1. O impacto medido no dataset é 46% de nota 1 quando há atraso, contra 7% quando a entrega está no prazo, e cerca de 4.000 avaliações de 1 estrela por ano. A consequência que a seção 3 já escreve é a reputação no marketplace. O caso acrescenta a rota SP–PE, com 24% de atraso: é histórico citado no caso, não é conta publicada na seção 3 e não é premissa de benchmark. Não vira meta.

**Usuários.** O seller recebe o alerta no momento em que o pedido daquela rota é confirmado. O cliente de Pernambuco, no exemplo, só entra depois que uma pessoa confirma uma recomendação. Logística, diretoria e transportadora não voltam: a seção 4 os citava, o mapa revisado não.

**Dados.**

| Dado | Onde está | Dono no desenho | Qualidade conhecida |
| :--- | :--- | :--- | :--- |
| Status e prazos do pedido e do histórico da rota | orders | Dataset público | O relatório não descreve qualidade além de dado público e anonimizado |
| UF do comprador | customers | Dataset público | Idem |
| UF do vendedor | sellers | Dataset público | Idem |
| Nota do cliente | order_reviews, pela chave customer_id | Dataset público | Idem. O fluxo já usa o corte de nota igual ou menor que 3 |
| Limites numéricos das faixas | Não estão no prompt nem nas oito tabelas | A diretoria, como o prompt diz, sem número publicado | Sem valor publicado. A versão não inventa o corte |
| Voucher, desconto de fidelidade, upgrade de frete | Não são tabela | — | Fora da ação automática |

**Indicadores.** Alvo ou janela que o relatório não publica ficam em branco. Nenhum alvo novo.

| Indicador | Base | Alvo | Quando medir | Origem |
| :--- | :--- | :--- | :--- | :--- |
| Nota 1 com atraso, contra nota 1 no prazo | 46% com atraso; 7% no prazo | Não publicado | Não publicado | Medido no dataset |
| Avaliações de 1 estrela | Cerca de 4.000 por ano | Não publicado | Não publicado | Medido no dataset |
| Churn | 11% | 6% | Não publicado | Premissa de benchmark |
| Receita protegida | Base não publicada | R$ 139 mil por ano | Não publicado | Premissa de benchmark |
| Concordância com a faixa | A regra das três faixas | A faixa cita o percentual calculado | Não publicado | Métrica técnica desta versão. O relatório não fixa amostra nem percentual de acerto |

Números de regra ou de exemplo, não são indicador: nota igual ou menor que 3 (fluxo revisado); R$ 10 do voucher no caso; 24% da rota SP–PE no caso.

## Riscos

Salvaguarda igual à rota: a faixa é regra e cita o percentual; dinheiro não sai sozinho.

| Risco | Como acontece neste processo | Prob. | Sev. | Salvaguarda nesta versão | Detectar | Interromper | Avaliar | Recuperar |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Resposta sem fundamento na fonte | A faixa sai sem o percentual de orders, ou o fluxo chama a decisão de probabilística sem essa conta | Média, se a faixa andar sem o número | Alta | A saída cita o percentual calculado. Sem histórico da rota, não há faixa | Conferir se a faixa mostra o percentual | A pessoa que confirma a recomendação tira o alerta | Comparar a faixa com o percentual | Corrigir a faixa. Não cobrir com um escore de 0–100% |
| Dado pessoal a mais | O prompt antigo pedia CPF/CNPJ. Esta versão não | Baixa, com a salvaguarda | Alta | Contexto só com customer_id, UF, nota e prazos. Sem CPF/CNPJ | Olhar o que a saída grava | Parar a saída que trouxer documento | Ver o que já foi mostrado ao seller | Tirar o campo e ficar na chave customer_id |
| Decisão desigual entre grupos parecidos | A mesma rota recebe faixas diferentes, ou a nota baixa entra duas vezes, no percentual e de novo na regra complementar | Média, se as duas contas se misturarem | Alta | Um percentual, uma faixa. A nota igual ou menor que 3 só marca o cliente, fora da rota | Recortar a faixa pelo percentual, não pelo estado solto | Pausar a classificação que não mostra o percentual | Reles o par UF–UF e a nota | Separar de novo as duas regras |
| Voucher, desconto ou frete sem pessoa | O caso envia o voucher de R$ 10 e o fluxo grava desconto de fidelidade | Alta no desenho antigo | Alta | Só recomendação. A pessoa confirma. Sem confirmação, nada é enviado nem gravado | Saída com voucher ou desconto já disparado | A pessoa que confirma segura o efeito no mesmo dia | Ver se houve valor sem confirmação | Desfazer a comunicação ao cliente e tirar o desconto se tiver sido gravado |

**Quem tira a iniciativa do fluxo no mesmo dia:** a pessoa que confirma a recomendação. Papel do fluxo, sem nome inventado.

## Jornada

**Hipótese:** se uma recomendação por faixa ajudar o seller quando o pedido SP–PE é confirmado na Black Friday, esperamos o alerta com o percentual da rota, medido pela concordância com a regra, com voucher, desconto e frete sempre confirmados por uma pessoa.

**Papel nesta versão:** recomendar.

**Caso:** o do mapa. Cliente de Pernambuco, vendedor em SP, Black Friday, histórico de 24% de atraso nessa rota citado no caso. A classe que o caso já dá a essa rota é “Alto Risco”. A versão mostra o percentual junto da faixa.

| # | Contexto | Intenção | Decisão | Controle |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Pedido confirmado. Rota SP–PE, Black Friday | Saber o risco da rota antes do atraso | Abrir o pedido. Ainda não há faixa | Nenhum ainda |
| 2 | UF lida em sellers e customers; prazos em orders | Classificar a rota | Calcular o percentual de atraso do par SP–PE e aplicar a faixa da diretoria. Citar o percentual. Sem escore de 0–100% | Se não houver histórico da rota, não há faixa |
| 3 | Nota do customer_id em order_reviews | Ver se o cliente já tem nota baixa | Nota igual ou menor que 3 marca cenário crítico, sem alterar o percentual da rota | Nota ausente não vira crítica por suposição |
| 4 | Faixa pronta | Avisar quem despacha | Alertar o seller com a faixa e o percentual | O seller recebe o alerta. Não recebe ordem de voucher |
| 5 | Há recomendação de voucher de R$ 10, desconto de fidelidade ou upgrade de frete | Não disparar valor sozinho | A recomendação fica à parte da faixa | Uma pessoa confirma ou descarta. Só a confirmação segue. O cliente não é avisado antes |
| 6 | Quadro de recusa. Rota sem histórico bastante, ou alguém pede para enviar o voucher junto com a faixa | Não fabricar “Alto Risco” nem valor | Recusar a faixa e recusar o voucher automático | A pessoa segue sem recomendação de valor. O cliente não ganha mensagem de compensação |

**Sequência para atualizar o Draw.io.** Substituir o diagrama antigo por esta linha, sem tela:

1. Pedido confirmado.
2. Ler UF em customers e sellers, e prazos em orders.
3. Decisão: há histórico da rota? Se não, sair pelo quadro de recusa.
4. Calcular o percentual e aplicar a faixa. Mostrar o número.
5. Decisão: nota igual ou menor que 3 em order_reviews? Se sim, marcar o cliente como crítico, ao lado da faixa.
6. Alertar o seller.
7. Decisão humana: confirmar ou descartar voucher, desconto ou frete.
8. Saída: cliente comunicado só depois do sim. No não, fim sem valor.

## Entradas, saídas e escalonamento

**Papel observável:** recomendar a faixa da rota e alertar o seller.

**Objetivo:** na confirmação do pedido, a faixa cita o percentual de atraso do par de UF.

**Não faz:** escore de 0–100%, chamada ao OmniCare, voucher, desconto e frete automáticos, CPF/CNPJ.

| Pergunta | Resposta |
| :--- | :--- |
| Quem usa? | O seller, no alerta do pedido confirmado. A pessoa que confirma a recomendação, antes de qualquer valor. |
| Que informação recebe? | UF de origem, UF de destino, prazos em orders, nota em order_reviews se existir |
| Que decisão toma? | Uma das três faixas, mais o marca de crítico do cliente quando a nota é igual ou menor que 3 |
| Quando para e escala? | Sem histórico da rota; sem os limites publicados, não inventa corte; qualquer voucher, desconto ou frete |

**Entradas**

| Entrada | Obrigatória? | Se faltar |
| :--- | :--- | :--- |
| Pedido confirmado | Sim | Não há classificação |
| UF do vendedor em sellers | Sim | Não há rota e não há faixa |
| UF do comprador em customers | Sim | Não há rota e não há faixa |
| Prazos em orders para o histórico do par de UF | Sim | Não há percentual e não há faixa. Não completar por suposição |
| Nota em order_reviews | Não | Segue só a faixa da rota. Não marca crítico |
| customer_id | Sim, para a regra da nota | Sem a chave, a regra complementar não roda. A faixa da rota pode seguir |

**Saídas**

| Saída | Para quem | O que fica registrado |
| :--- | :--- | :--- |
| Faixa (baixo, médio ou alto) e o percentual calculado | Seller | Par de UF, percentual, faixa |
| Marca de cenário crítico do cliente | Seller, ao lado da faixa | customer_id e a nota usada, só quando a regra disparar |
| Recomendação de voucher, desconto ou frete, ou silêncio | Pessoa que confirma | A recomendação não é envio. O registro diz se houve confirmação |

**Capacidades desta versão:** recomendar. Não executa valor.

### Matriz de escalonamento

| Situação | Ação |
| :--- | :--- |
| Dados suficientes e regra clara | Aplica a faixa, cita o percentual e alerta o seller |
| Dado incompleto | Sem UF ou sem histórico, não há faixa. Sem nota, não há marca de crítico |
| Ambíguo ou sem critério estável | Sem o número do limite publicado pela diretoria, não inventa o corte e não usa escore de 0–100% |
| Impacto sensível | Voucher de R$ 10, desconto de fidelidade e upgrade de frete ficam com a pessoa. Sem confirmação, não disparam |

**Como saber que o protótipo está errado.** A faixa não cita o percentual calculado em orders. Ou a nota igual ou menor que 3 entrou no percentual da rota. Ou o cliente recebeu voucher sem confirmação.

## Prompt

**Objetivo.** Classificar o risco de atraso da rota no pedido confirmado e alertar o seller.

**Contexto.** orders (status e prazos), customers (UF do comprador), sellers (UF do vendedor), order_reviews (nota) por customer_id. As três faixas usam os limites que a diretoria definir; o relatório não publica esses limites. Nota igual ou menor que 3 é o corte do fluxo revisado, só para o cliente.

**Instrução principal.** Calcule o percentual de atraso do par de UF, aplique a faixa, cite o percentual e avise o seller. A nota baixa não entra nessa conta. Voucher, desconto e frete ficam como recomendação para uma pessoa.

**Resultado esperado.** Tabela com UF do vendedor, UF do comprador, percentual de atraso e grau de risco; alerta ao seller; recomendação de valor só se uma pessoa for confirmá-la.

### Texto

Você é o Olist RiskShield. No pedido confirmado, você classifica o risco de atraso da rota e alerta o seller. Você não envia compensação e não chama outro agente.

Contexto que você pode ler:

- orders: status e prazos do pedido e do histórico da mesma rota.
- sellers: UF do vendedor.
- customers: UF do comprador.
- order_reviews: nota do cliente, pela chave customer_id.

Não leia CPF, CNPJ nem outra tabela.

Instrução:

1. Identifique a UF de origem em sellers e a UF de destino em customers.
2. Em orders, reúna o histórico do mesmo par de UF. Se não houver histórico, pare. Não crie faixa.
3. Calcule o tempo médio de entrega e o percentual de pacotes atrasados nesse par. O percentual entra na saída.
4. Aplique só estas faixas, com os limites definidos pela diretoria. Não transforme o resultado em escore de 0–100%. Não chame o cálculo de probabilístico.
   - Baixo risco: atraso inferior ao primeiro limite.
   - Médio risco: atraso entre o primeiro e o segundo limite.
   - Alto risco: atraso superior ao segundo limite.
   Se o limite numérico não foi informado, pare e diga que a faixa não pode ser fechada.
5. Em separado, leia a nota em order_reviews desse customer_id. Se a nota for igual ou menor que 3, marque o cliente como cenário crítico. Não some essa nota ao percentual da rota.
6. Grave a tabela com UF do vendedor, UF do comprador, percentual de atraso e grau de risco. Alerte o seller com essa tabela.
7. Voucher de R$ 10, desconto de fidelidade e upgrade de frete não são ação sua. Se for o caso de mencioná-los, entregue uma recomendação para uma pessoa confirmar. Sem essa confirmação, não envie mensagem ao cliente e não grave desconto.

Resultado: a tabela da rota, o alerta ao seller e, no máximo, uma recomendação de valor ainda não confirmada.
