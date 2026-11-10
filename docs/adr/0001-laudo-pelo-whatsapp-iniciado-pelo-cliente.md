# ADR 0001: Laudo pelo WhatsApp iniciado pelo cliente, com código gerado no site

- **Status:** aceita
- **Data:** junho de 2026

## Contexto

O laudo da avaliação precisava chegar ao cliente pelo WhatsApp, mas o envio automático não funcionava. O diagnóstico mostrou duas limitações. O número comercial do cliente estava sob gestão exclusiva de outra plataforma, e pela regra da Meta cada número só pode ser operado por uma plataforma autorizada por vez. Além disso, mandar uma mensagem ativa para alguém que ainda não falou com o número exige modelos de mensagem aprovados, porque fica fora da janela de conversa aberta pelo cliente.

O cliente já tinha um segundo número, usado pelo sistema de atendimento dele, que podia receber mais de um aplicativo inscrito.

## Decisão

Inverter o fluxo: o cliente inicia a conversa e o bot responde.

1. Na página de resultado, o botão "Receber no WhatsApp" gera um código curto, grava o pedido ligado ao PDF e abre o WhatsApp com a mensagem pronta contendo o código.
2. O AvaliaDF ganha um aplicativo próprio na Meta, inscrito no mesmo número do atendimento. A Meta entrega uma cópia de cada mensagem para cada aplicativo inscrito.
3. O webhook do AvaliaDF só trata mensagens que contêm um código válido: envia o PDF, marca o pedido como entregue e registra o contato como lead. Mensagens sem código são descartadas sem gravar nada.
4. O webhook do atendimento recebe uma regra mínima: mensagem com código de laudo é ignorada, sem abrir ticket nem menu.

## Alternativas consideradas

- **Envio ativo para o telefone informado no formulário.** Rejeitada: fora da janela de conversa exige modelos aprovados, e o envio continuaria dependendo do número comercial bloqueado.
- **Integrar com a plataforma de CRM que opera o número comercial.** Mantida como caminho de médio prazo, se o cliente quiser que toda a comunicação saia de um só número. Depende de outro fornecedor, de custo e de prazo que não controlamos.
- **Adicionar o AvaliaDF como segundo aplicativo no número comercial.** Desaconselhada sem alinhamento entre as equipes, pelo risco de interferir nas conversas do CRM.

## Consequências

- A resposta sempre acontece dentro da janela de conversa, porque o cliente acabou de escrever.
- O download anônimo vira um contato qualificado no canal preferido do cliente.
- O reconhecimento do código precisa ser idêntico nos dois webhooks: se divergir, ninguém responde ou os dois respondem. O formato fica em uma constante única e coberto por testes.
- O webhook do AvaliaDF vê todas as mensagens do número, então descartar as que não têm código é uma exigência de privacidade, não só de organização.
- O aplicativo da Meta precisa manter uma URL de política de privacidade acessível ao validador para continuar publicado. Por isso a política passou a ser servida no domínio do produto, em HTML completo.
