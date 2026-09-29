# Dataset público Brazilian E-Commerce (Olist)

Fonte: [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) (`olistbr/brazilian-ecommerce`).

Os nove CSV desta pasta são os arquivos desse dataset. O dicionário abaixo usa os nomes de coluna como estão nos cabeçalhos. Volumes, relações conferidas e anomalias estão em [exploracao-inicial.md](exploracao-inicial.md).

## O que é

A página do Kaggle apresenta um dataset público de e-commerce brasileiro com pedidos feitos na Olist Store, em vários marketplaces no Brasil, entre 2016 e 2018. A página descreve o conjunto como informação de cerca de 100 mil pedidos. O mesmo pedido pode ser visto por status, preço, pagamento, frete, localização do cliente, atributos do produto e avaliações. Há também uma tabela de geolocalização que relaciona prefixos de CEP a latitude e longitude.

A Olist, descrita na página como a maior loja de departamentos nos marketplaces brasileiros, conecta pequenos negócios a canais de venda com um único contrato. Os lojistas vendem pela Olist Store e enviam com os parceiros logísticos da Olist. Depois da compra, o vendedor é avisado para cumprir o pedido. Quando o cliente recebe o produto, ou quando a data estimada de entrega vence, o cliente recebe por e-mail uma pesquisa de satisfação, com nota e comentários.

A própria página fixa três regras de leitura:

- um pedido pode ter vários itens;
- cada item pode ser atendido por um vendedor diferente;
- os dados comerciais são reais e foram anonimizados; nos textos de avaliação, referências a empresas e parceiros foram trocadas por nomes de casas de Game of Thrones.

## Licença e termos

A página do dataset indica a licença **CC BY-NC-SA 4.0** (Creative Commons Atribuição–NãoComercial–CompartilhaIgual 4.0): [creativecommons.org/licenses/by-nc-sa/4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/).

Os termos dessa licença são atribuição do autor, uso não comercial e compartilhamento de obras derivadas sob a mesma licença. A página do Kaggle não publica um termo adicional além dessa licença e do aviso de anonimização citado acima.

## Tabelas

Há nove arquivos em `Olist/dados/`.

### `olist_customers_dataset.csv`

Cliente ligado a cada pedido. `customer_id` é a chave usada em `olist_orders_dataset.csv`. No arquivo, cada `customer_id` aparece uma vez. `customer_unique_id` identifica a pessoa e pode repetir quando há mais de um pedido.

| Coluna | Significado |
| --- | --- |
| `customer_id` | Identificador do cliente neste pedido. |
| `customer_unique_id` | Identificador da pessoa, estável entre pedidos. |
| `customer_zip_code_prefix` | Prefixo do CEP do cliente. No arquivo, os 99.441 valores têm 5 caracteres. |
| `customer_city` | Cidade do cliente. |
| `customer_state` | Unidade federativa do cliente. No arquivo, os valores têm 2 caracteres. |

### `olist_orders_dataset.csv`

Um registro por pedido: status e marcas de tempo do ciclo, da compra até a entrega estimada e a entrega realizada.

| Coluna | Significado |
| --- | --- |
| `order_id` | Identificador do pedido. No arquivo, cada valor aparece uma vez. |
| `customer_id` | Cliente deste pedido. Liga a `olist_customers_dataset.csv`. |
| `order_status` | Situação do pedido. Valores presentes no arquivo: `created`, `approved`, `invoiced`, `processing`, `shipped`, `delivered`, `unavailable`, `canceled`. |
| `order_purchase_timestamp` | Data e hora da compra. |
| `order_approved_at` | Data e hora da aprovação do pagamento. Pode vir vazio. |
| `order_delivered_carrier_date` | Data e hora em que o pedido foi entregue à transportadora. Pode vir vazio. |
| `order_delivered_customer_date` | Data e hora da entrega ao cliente. Pode vir vazio. |
| `order_estimated_delivery_date` | Data estimada de entrega. Preenchida em todos os registros. |

### `olist_order_items_dataset.csv`

Itens do pedido. Um pedido pode ter várias linhas. Cada linha aponta para um produto e um vendedor.

| Coluna | Significado |
| --- | --- |
| `order_id` | Pedido. Liga a `olist_orders_dataset.csv`. |
| `order_item_id` | Número do item dentro do pedido. |
| `product_id` | Produto. Liga a `olist_products_dataset.csv`. |
| `seller_id` | Vendedor que atende o item. Liga a `olist_sellers_dataset.csv`. |
| `shipping_limit_date` | Data limite de envio do item pelo vendedor. |
| `price` | Preço do item. |
| `freight_value` | Valor do frete atribuído ao item. |

### `olist_order_payments_dataset.csv`

Pagamentos do pedido. Um pedido pode ter mais de uma linha quando há mais de um meio ou mais de uma parcela de registro.

| Coluna | Significado |
| --- | --- |
| `order_id` | Pedido. Liga a `olist_orders_dataset.csv`. |
| `payment_sequential` | Sequência da linha de pagamento dentro do pedido. |
| `payment_type` | Meio de pagamento. Valores presentes no arquivo: `credit_card`, `boleto`, `voucher`, `debit_card`, `not_defined`. |
| `payment_installments` | Número de parcelas informado na linha. |
| `payment_value` | Valor da linha de pagamento. |

### `olist_order_reviews_dataset.csv`

Respostas da pesquisa de satisfação ligada ao pedido. A página do Kaggle descreve essa pesquisa como o e-mail enviado depois do recebimento do produto ou do vencimento da data estimada.

| Coluna | Significado |
| --- | --- |
| `review_id` | Identificador da avaliação. No arquivo, o valor não é único: o mesmo `review_id` aparece em pedidos diferentes. |
| `order_id` | Pedido avaliado. Liga a `olist_orders_dataset.csv`. |
| `review_score` | Nota da experiência. Os valores presentes no arquivo são 1, 2, 3, 4 e 5. |
| `review_comment_title` | Título do comentário. Pode vir vazio. |
| `review_comment_message` | Texto do comentário. Pode vir vazio. |
| `review_creation_date` | Data de criação da avaliação. |
| `review_answer_timestamp` | Data e hora da resposta à pesquisa. |

### `olist_products_dataset.csv`

Cadastro do produto: categoria, tamanho dos textos, quantidade de fotos e medidas. O nome do produto não está no arquivo.

Dois cabeçalhos mantêm a grafia original `lenght`.

| Coluna | Significado |
| --- | --- |
| `product_id` | Identificador do produto. No arquivo, cada valor aparece uma vez. |
| `product_category_name` | Categoria em português. Pode vir vazio. Liga a `product_category_name_translation.csv` quando há tradução. |
| `product_name_lenght` | Comprimento do nome do produto. Pode vir vazio. |
| `product_description_lenght` | Comprimento da descrição do produto. Pode vir vazio. |
| `product_photos_qty` | Quantidade de fotos. Pode vir vazio. |
| `product_weight_g` | Peso em gramas. Pode vir vazio. |
| `product_length_cm` | Comprimento em centímetros. Pode vir vazio. |
| `product_height_cm` | Altura em centímetros. Pode vir vazio. |
| `product_width_cm` | Largura em centímetros. Pode vir vazio. |

### `olist_sellers_dataset.csv`

Vendedor que atende itens. Cada `seller_id` aparece uma vez.

| Coluna | Significado |
| --- | --- |
| `seller_id` | Identificador do vendedor. |
| `seller_zip_code_prefix` | Prefixo do CEP do vendedor. |
| `seller_city` | Cidade do vendedor. |
| `seller_state` | Unidade federativa do vendedor. No arquivo, os valores têm 2 caracteres. |

### `olist_geolocation_dataset.csv`

Coordenadas associadas a prefixos de CEP. O prefixo não é único: o mesmo CEP tem várias linhas, com latitude, longitude e, em parte dos casos, mais de uma grafia de cidade.

| Coluna | Significado |
| --- | --- |
| `geolocation_zip_code_prefix` | Prefixo do CEP. |
| `geolocation_lat` | Latitude. |
| `geolocation_lng` | Longitude. |
| `geolocation_city` | Cidade registrada para aquele ponto. |
| `geolocation_state` | Unidade federativa registrada para aquele ponto. |

O vínculo com clientes e vendedores é pelo prefixo (`customer_zip_code_prefix` ou `seller_zip_code_prefix`). Não há uma coluna de identificador compartilhado com as outras tabelas.

### `product_category_name_translation.csv`

Tradução da categoria de português para inglês. O arquivo começa com uma marca BOM UTF-8 antes do primeiro cabeçalho. A coluna, lida sem essa marca, chama-se `product_category_name`.

| Coluna | Significado |
| --- | --- |
| `product_category_name` | Categoria em português, no mesmo texto usado em `olist_products_dataset.csv`. |
| `product_category_name_english` | Nome da categoria em inglês. |
