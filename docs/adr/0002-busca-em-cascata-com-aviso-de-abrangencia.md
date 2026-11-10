# ADR 0002: Busca em cascata com aviso de abrangência

- **Status:** aceita
- **Data:** agosto de 2026

## Contexto

A avaliação monta um endereço de busca no portal imobiliário a partir da cidade, do bairro e do tipo de imóvel, e calcula a estimativa sobre os anúncios que voltam. O portal só reconhece um conjunto restrito de nomes de bairro nesse endereço. Quando o nome não é reconhecido, a página vem sem anúncios e a avaliação sai vazia, mesmo com o imóvel anunciado.

Medido em produção: entre 36% e 47% dos bairros do formulário não retornavam nada, e 16 regiões administrativas falhavam em 100% das buscas, porque o portal as publica como bairro de outra cidade.

## Decisão

Quando o nível pedido não retorna anúncios, a busca desce por níveis de apoio dentro da mesma cidade:

1. Bairro no endereço de busca (nível exato).
2. Bairro como palavra-chave, com cada anúncio conferido contra o bairro antes de entrar no cálculo, para não contaminar a estimativa com imóveis de outra região.
3. Cidade inteira.
4. Se nem a cidade responde, a cascata é repetida sob as cidades candidatas em que o portal publica aquela região como bairro.

A resposta informa a abrangência usada, e a página de resultado e a pesquisa de mercado interna mostram um aviso quando a estimativa não veio do bairro exato.

## Alternativas consideradas

- **Manter a avaliação vazia.** Rejeitada: o visitante sai sem resposta, e a avaliação é o produto principal do site.
- **Ampliar para a cidade sem avisar.** Rejeitada: entrega um número sem contexto, que pode estar longe do valor real do bairro.

## Consequências

- Nenhuma busca termina vazia quando há oferta na cidade pesquisada. As 16 regiões passaram a responder.
- O laudo deixa claro de onde veio a amostra.
- Consultas que descem a cascata fazem mais requisições ao portal. Falha transitória em um nível vira coleta vazia naquele nível, em vez de derrubar a consulta.
- A coleta mensal dos índices recusa gravar um número de cidade sob o rótulo de um bairro, para que o recurso de apoio da avaliação não distorça os índices publicados.
