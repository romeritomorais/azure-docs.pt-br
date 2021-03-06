---
title: Configurar uma cadeia de conexão
titleSuffix: Azure Storage
description: Configure uma cadeia de conexão para uma conta de Armazenamento do Azure. Uma cadeia de conexão contém as informações necessárias para autorizar o acesso a uma conta de armazenamento de seu aplicativo em tempo de execução usando a autorização de chave compartilhada.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f617beec8a53570ede7755040cfbb92a7d1712b7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460565"
---
# <a name="configure-azure-storage-connection-strings"></a>Configurar cadeias de conexão do Armazenamento do Azure

Uma cadeia de conexão inclui as informações de autorização necessárias para que seu aplicativo acesse dados em uma conta de armazenamento do Azure em tempo de execução usando a autorização de chave compartilhada. Você pode configurar cadeias de conexão para:

* Conecte-se ao emulador de armazenamento do Azure.
* Acesse uma conta de armazenamento no Azure.
* Acessar recursos especificados no Azure por uma SAS (Assinatura de Acesso Compartilhado).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-and-copy-a-connection-string"></a>Exibir e copiar uma cadeia de conexão

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="store-a-connection-string"></a>Armazenar uma cadeia de conexão

Seu aplicativo precisara acessar a cadeia de conexão no runtime para autorizar as solicitações feitas para o Armazenamento do Microsoft Azure. Você tem várias opções diferentes para armazenar a cadeia de conexão:

* Você pode armazenar a cadeia de conexão em uma variável de ambiente.
* Um aplicativo em execução na área de trabalho ou em um dispositivo pode armazenar a cadeia de conexão em um arquivo **app.config** ou **web.config**. Adicione a cadeia de conexão à seção **AppSettings** nesses arquivos.
* Um aplicativo em execução em um serviço de nuvem do Azure pode armazenar a cadeia de conexão no [arquivo de esquema (.cscfg) de configuração de serviço do Azure](https://msdn.microsoft.com/library/ee758710.aspx). Adicionar a cadeia de conexão à seção **ConfigurationSettings** do arquivo de configuração de serviço.

Armazenar a cadeia de conexão em um arquivo de configuração facilita a atualização da cadeia de conexão para alternar entre o emulador de armazenamento e uma conta de Armazenamento do Azure na nuvem. Você precisa apenas editar a cadeia de conexão para apontar para seu ambiente de destino.

Você pode usar o [Gerenciador de Configuração do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) para acessar a cadeia de conexão no runtime, independentemente do local em que seu aplicativo esteja sendo executado.

## <a name="configure-a-connection-string-for-the-storage-emulator"></a>Configurar uma cadeia de conexão para o emulador de armazenamento

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Consulte [Usar o emulador do Armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md) para obter mais informações sobre o emulador de armazenamento.

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Configurar uma cadeia de conexão para uma conta de armazenamento do Azure

Para criar uma cadeia de conexão para sua conta de Armazenamento do Azure, use o formato de cadeia de conexão a seguir. Indique se você deseja se conectar à conta de armazenamento por meio de HTTPS (recomendado) ou HTTP, substitua `myAccountName` pelo nome da sua conta de armazenamento e substitua `myAccountKey` pela chave de acesso da sua conta:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Por exemplo, a cadeia de conexão pode parecer com o seguinte:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Embora o Armazenamento do Azure dê suporte a HTTP e HTTPS em uma cadeia de conexão, *usar HTTPS é altamente recomendável*.

> [!TIP]
> Você pode encontrar as cadeias de conexão da conta de armazenamento no [Portal do Azure](https://portal.azure.com). Navegue até **CONFIGURAÇÕES** > **Chaves de acesso** na folha de menu da sua conta de armazenamento para ver as cadeias de conexão tanto para a chave de acesso primária quanto para a secundária.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Criar uma cadeia de conexão usando uma assinatura de acesso compartilhado

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Criar uma cadeia de conexão para um ponto de extremidade explícito do armazenamento

Você pode especificar explicitamente os pontos de extremidade de serviço na sua cadeia de conexão em vez dos pontos de extremidade padrão. Para criar uma cadeia de conexão que especifique um ponto de extremidade explícito, especifique o ponto de extremidade de serviço completo para cada serviço, incluindo a especificação do protocolo (HTTP ou HTTPS, sendo este o recomendado) usando o seguinte formato:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Um cenário em que pode ser útil especificar um ponto de extremidade explícito é se você mapeou o ponto de extremidade do Armazenamento de Blobs para um [domínio personalizado](../blobs/storage-custom-domain-name.md). Nesse caso, você pode especificar o ponto de extremidade personalizado para o armazenamento de Blobs em sua cadeia de conexão. Opcionalmente, você poderá especificar os pontos de extremidade padrão para os outros serviços se eles forem usados pelo seu aplicativo.

Este é um exemplo de uma cadeia de conexão que especifica um ponto de extremidade explícito para o serviço Blob:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Este exemplo especifica pontos de extremidade explícitos para todos os serviços, incluindo um domínio personalizado para o serviço Blob:

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

Os valores de ponto de extremidade em uma cadeia de conexão são usados para construir os URIs de solicitação para os serviços de armazenamento e determinar a forma de quaisquer URIs retornados ao seu código.

Se você tiver mapeado um ponto de extremidade de armazenamento para um domínio personalizado e omitir esse ponto de extremidade de uma cadeia de conexão, você não poderá usar essa cadeia de conexão para acessar os dados nesse serviço do seu código.

> [!IMPORTANT]
> Valores de ponto de extremidade de serviço em suas cadeias de conexão devem ser URIs bem formados, incluindo `https://` (recomendado) ou `http://`. Já que o armazenamento do Azure ainda não dá suporte a HTTPS para domínios personalizados, você *deve* especificar `http://` para qualquer URI de ponto de extremidade que aponta para um domínio personalizado.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Criar uma cadeia de conexão com um sufixo de ponto de extremidade

Para criar uma cadeia de conexão para um serviço de armazenamento em regiões ou instâncias com sufixos de ponto de extremidade diferentes, como para o Azure China 21Vianet ou Azure governamental, use o seguinte formato de cadeia de conexão. Indique se deseja se conectar à conta de armazenamento por meio de HTTP ou HTTPS (recomendado), substitua `myAccountName` pelo nome da sua conta de armazenamento, substitua `myAccountKey` pela chave de acesso da sua conta e substitua `mySuffix` pelo sufixo do URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Aqui está um exemplo de cadeia de conexão para serviços de armazenamento na 21Vianet do Azure na China:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analisando uma cadeia de conexão

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Próximos passos

* [Usar o emulador de Armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md)
* [Gerenciadores do Armazenamento do Azure](storage-explorers.md)
* [Usando SAS (Assinatura de Acesso Compartilhado)](storage-sas-overview.md)
