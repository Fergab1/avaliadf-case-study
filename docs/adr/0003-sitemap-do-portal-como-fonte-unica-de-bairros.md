# ADR 0003: Sitemap de busca do portal como fonte única de bairros

- **Status:** aceita
- **Data:** agosto de 2026

## Contexto

O menu de bairros do formulário vinha de uma tabela montada por uma rotina de extração que misturava fontes de qualidade desigual: os filtros oficiais do portal, os endereços digitados livremente pelos anunciantes e complementos automáticos sem conferência. Quadras, condomínios e erros de digitação entravam no menu como se fossem bairros, e cada um virava uma opção que devolvia avaliação vazia. A rotina também apagava e reinseria a tabela a cada execução.

No meio da correção, o portal passou a montar os filtros de bairro no navegador. O HTML servido deixou de trazer a lista, e a fonte oficial original parou de responder fora do navegador.

## Decisão

- A única fonte de bairros passa a ser o sitemap de páginas de busca do portal, que publica os nomes canônicos de bairro por cidade no mesmo formato de endereço que a avaliação usa e é atualizado diariamente. Um único download atende todas as cidades.
- A gravação passa de apagar e reinserir para atualização que preserva as colunas de validação. Se a fonte não trouxer uma cidade, o cadastro daquela cidade fica intocado.
- Uma rotina diária confere os bairros contra o portal em lotes, marca quais têm oferta e prioriza os nunca conferidos. A base inteira é percorrida ao longo da semana. Bairro sem oferta de venda é conferido também em aluguel.
- O formulário esconde os bairros conferidos sem oferta, mas nunca ao ponto de deixar a cidade sem opção. Em falha de rede o registro fica como está, para não esconder bairro válido.

## Alternativas consideradas

- **Manter as fontes misturadas e filtrar depois.** Rejeitada: o problema voltaria a crescer a cada atualização.
- **Ler os filtros diretamente da página do portal.** Era a fonte oficial original, mas deixou de estar disponível no HTML servido.

## Consequências

- O menu só oferece nomes que o portal reconhece. Base repovoada com 214 bairros em 26 cidades.
- A validação nunca apaga registro, então uma instabilidade do portal não derruba o menu.
- O cadastro depende de o portal manter o sitemap. Se ele sair do ar, nada é apagado e a correção fica restrita à troca de fonte.
