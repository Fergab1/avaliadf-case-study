# ADR 0004: Coleta mensal em fila retomável com orçamento de tempo

- **Status:** aceita
- **Data:** julho de 2026

## Contexto

Os índices mensais de mercado dependem de uma coleta agendada que percorre cidades, bairros e tipos de imóvel no portal. Ao investigar, encontrei duas causas para a coleta nunca ter rodado pelo agendamento.

A primeira era de autenticação: o agendamento chamava as funções com uma credencial que elas não aceitavam, e não havia nenhuma execução agendada registrada.

A segunda era de tamanho: mesmo autenticada, a coleta não caberia em uma invocação. A função é encerrada em cerca de 150 segundos, e uma única cidade pode exigir centenas de consultas, com espera entre elas.

## Decisão

- As tarefas de cada cidade são montadas em ordem determinística, e a coleta executa com um orçamento de tempo, devolvendo o índice de onde parou.
- O estado do trabalho vive em uma tabela de fila no banco. Um agendamento de continuação avança a fila em lotes.
- O agendamento diário apenas enfileira a fatia do dia e responde na hora.
- A fila fica fechada no banco: só o servidor grava, e só administradores leem.

## Alternativas consideradas

- **Uma invocação por cidade.** Rejeitada: não cabe no limite de tempo da função.
- **A função chamar a si mesma por HTTP para continuar.** Rejeitada: a requisição é cancelada quando a instância é destruída, então a cadeia se perde em silêncio.

## Consequências

- Uma invocação que morra no meio não perde nada: a próxima retoma do último índice gravado.
- A coleta passa a ter estado visível no banco, o que facilita acompanhar o progresso.
- Há uma tabela de estado e um agendamento a mais para manter.
