---
title: Reagindo a Azure App eventos de valor de chave de configuração
description: Use a grade de eventos do Azure para assinar eventos de configuração de aplicativo.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: a4f61d147ba1abf73ada6360b8d0d965d8e063a5
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523791"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reagindo a Azure App eventos de configuração

Azure App eventos de configuração permitem que os aplicativos reajam às alterações nos valores de chave. Isso é feito sem a necessidade de código complicado ou serviços de sondagem caros e ineficientes. Em vez disso, os eventos são enviados por Push por meio da [grade de eventos do Azure](https://azure.microsoft.com/services/event-grid/) para assinantes como [Azure Functions](https://azure.microsoft.com/services/functions/), [aplicativos lógicos do Azure](https://azure.microsoft.com/services/logic-apps/)ou até mesmo para seu próprio ouvinte HTTP personalizado. Criticamente, você paga apenas pelo que usar.

Azure App eventos de configuração são enviados para a grade de eventos do Azure, que fornece serviços de entrega confiáveis para seus aplicativos por meio de políticas de repetição avançadas e entrega de mensagens mortas. Para saber mais, confira [entrega e repetição de mensagem da grade de eventos](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Os cenários de evento de configuração de aplicativo comuns incluem a atualização da configuração do aplicativo, o disparo de implantações ou qualquer fluxo de trabalho orientado à configuração. Quando as alterações não forem frequentes, mas seu cenário exigir uma capacidade de resposta imediata, a arquitetura baseada em eventos pode ser especialmente eficaz.

Dê uma olhada na [rota Azure app eventos de configuração para um ponto de extremidade da Web personalizado-CLI](./howto-app-configuration-event.md) para obter um exemplo rápido. 

![Modelo da Grade de Eventos](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Azure App eventos de configuração disponíveis
A Grade de eventos usa [assinaturas de evento](../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para os assinantes. Azure App as assinaturas de evento de configuração podem incluir dois tipos de eventos:  

> |Nome do evento|DESCRIÇÃO|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Acionado quando um valor de chave é criado ou substituído|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Acionado quando um valor de chave é excluído|

## <a name="event-schema"></a>Esquema do evento
Azure App eventos de configuração contêm todas as informações de que você precisa para responder às alterações em seus dados. Você pode identificar um evento de configuração de aplicativo porque a propriedade eventType começa com "Microsoft. AppConfiguration". Encontre informações adicionais sobre o uso de propriedades de evento da Grade de Eventos em [Esquema de eventos da Grade de Eventos](../event-grid/event-schema.md).  

> |Propriedade|Type|DESCRIÇÃO|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |topic|string|ID de Azure Resource Manager completo da configuração do aplicativo que emite o evento.|
> |subject|string|O URI do valor de chave que é o assunto do evento.|
> |eventTime|string|A data/hora em que o evento foi gerado, no formato ISO 8601.|
> |eventType|string|"Microsoft. AppConfiguration. KeyValueModified" ou "Microsoft. AppConfiguration. KeyValueDeleted".|
> |ID|string|Um identificador exclusivo deste evento.|
> |dataVersion|string|A versão do esquema do objeto de dados.|
> |metadataVersion|string|A versão do esquema de propriedades de nível superior.|
> |data|objeto|Coleção de dados de eventos específicos de configuração de Azure App|
> |Data. Key|string|A chave do valor de chave que foi modificado ou excluído.|
> |data.label|string|O rótulo, se houver, do valor de chave que foi modificado ou excluído.|
> |data.etag|string|Por `KeyValueModified` a eTag do novo valor de chave. Por `KeyValueDeleted` a eTag do valor-chave que foi excluído.|

Aqui está um exemplo de um evento KeyValueModified:
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

Para obter mais informações, consulte [Azure app esquema de eventos de configuração](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Práticas para consumo de eventos
Os aplicativos que lidam com eventos de configuração de aplicativo devem seguir estas práticas recomendadas:
> [!div class="checklist"]
> * Várias assinaturas podem ser configuradas para rotear eventos para o mesmo manipulador de eventos, portanto, não presuma que os eventos sejam de uma fonte específica. Em vez disso, verifique o tópico da mensagem para garantir que a instância de configuração do aplicativo envie o evento.
> * Verifique o eventType e não presuma que todos os eventos recebidos serão os tipos esperados.
> * Use os campos ETag para entender se suas informações sobre objetos ainda estão atualizadas.  
> * Use os campos do Sequencer para entender a ordem dos eventos em qualquer objeto específico.
> * Use o campo assunto para acessar a chave-valor que foi modificado.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a grade de eventos e dê uma tentativa de Azure App eventos de configuração:

- [Sobre a Grade de Eventos](../event-grid/overview.md)
- [Rotear eventos de configuração de Azure App para um ponto de extremidade da Web personalizado](./howto-app-configuration-event.md)