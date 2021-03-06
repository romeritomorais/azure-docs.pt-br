---
title: Visão geral de versões do Azure Functions runtime
description: O Azure Functions é compatível com várias versões do runtime. Aprenda as diferenças entre elas e como escolher a certa para você.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 21a7b25087efd5d4adf2154c935636c263df9afd
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919746"
---
# <a name="azure-functions-runtime-versions-overview"></a>Visão geral de versões do Azure Functions runtime

As versões principais do tempo de execução do Azure Functions estão relacionadas à versão do .NET na qual o tempo de execução se baseia. A tabela a seguir indica a versão atual do tempo de execução, o nível de versão e a versão do .NET relacionada. 

| Versão de tempo de execução | Nível de liberação<sup>1</sup> | Versão do .NET | 
| --------------- | ------------- | ------------ |
| 3.x | GA | .NET Core 3,1 | 
| 2. x | GA | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | .NET Framework 4,6<sup>3</sup> |

<sup>1</sup> as versões de GA têm suporte para cenários de produção.   
<sup>2</sup> a versão 1. x está no modo de manutenção. Os aprimoramentos são fornecidos somente em versões posteriores.   
<sup>3</sup> dá suporte apenas ao desenvolvimento no portal do Azure ou localmente em computadores com Windows.

Este artigo detalha algumas das diferenças entre as várias versões, como você pode criar cada versão e como alterar versões.

## <a name="languages"></a>Linguagens

A partir da versão 2. x, o tempo de execução usa um modelo de extensibilidade de linguagem e todas as funções em um aplicativo de funções devem compartilhar o mesmo idioma. O idioma das funções em um aplicativo de funções é escolhido durante a criação do aplicativo e é mantido na configuração do [\_WORKER\_tempo de execução](functions-app-settings.md#functions_worker_runtime) . 

As linguagens experimentais Azure Functions 1. x não podem usar o novo modelo, portanto, não há suporte no 2. x. A tabela a seguir indica quais linguagens de programação são atualmente compatíveis com cada versão de runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Para obter mais informações, consulte [Linguagens com suporte](supported-languages.md).

## <a name="creating-1x-apps"></a>Executar em uma versão específica

Por padrão, os aplicativos de funções criados no portal do Azure e pelo CLI do Azure são definidos para a versão 3. x. Você pode modificar essa versão conforme necessário. Você só pode alterar a versão de tempo de execução para 1. x depois de criar seu aplicativo de funções, mas antes de adicionar qualquer função.  A movimentação entre 2. x e 3. x é permitida mesmo com aplicativos que têm funções, mas ainda é recomendável testar em um novo aplicativo primeiro.

## <a name="migrating-from-1x-to-later-versions"></a>Migrando do 1. x para versões posteriores

Você pode optar por migrar um aplicativo existente gravado para usar o tempo de execução da versão 1. x para, em vez disso, usar uma versão mais recente. A maioria das alterações que você precisa fazer está relacionada a alterações no tempo de execução de linguagem, C# como alterações de API entre o .NET Framework 4,7 e o .NET Core. Você também precisará verificar se seu código e suas bibliotecas são compatíveis com o runtime de linguagem escolhido. Por fim, não deixe de observar as alterações no gatilho, associações e recursos destacados abaixo. Para obter os melhores resultados de migração, você deve criar um novo aplicativo de funções em uma nova versão e portar seu código de função existente da versão 1. x para o novo aplicativo.  

Embora seja possível fazer uma atualização "in-loco" atualizando manualmente a configuração do aplicativo, passar de 1. x para uma versão superior inclui algumas alterações significativas. Por exemplo, no C#, o objeto de depuração é alterado de `TraceWriter` para `ILogger`. Ao criar um novo projeto de versão 3. x, você começa com as funções atualizadas com base nos modelos de versão 3. x mais recentes.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Alterações em gatilhos e associações após a versão 1. x

A partir da versão 2. x, você deve instalar as extensões para gatilhos e associações específicas usadas pelas funções em seu aplicativo. A única exceção para isso são os gatilhos de temporizador e HTTP, que não exigem uma extensão.  Para obter mais informações, confira [Registrar e instalar extensões de associação](./functions-bindings-register.md).

Também há algumas alterações no *Function. JSON* ou atributos da função entre versões. Por exemplo, a propriedade `path` do Hub de Eventos agora é `eventHubName`. Veja a [tabela de associação existente](#bindings) para obter links para a documentação de cada associação.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Alterações nos recursos e funcionalidade após a versão 1. x

Alguns recursos foram removidos, atualizados ou substituídos após a versão 1. x. Esta seção detalha as alterações que você vê em versões posteriores depois de ter usado a versão 1. x.

As seguintes alterações foram feitas na versão 2.x:

* As chaves para chamar pontos de extremidade HTTP são sempre armazenadas criptografadas no Armazenamento de Blobs do Azure. Na versão 1. x, as chaves foram armazenadas no armazenamento de arquivos do Azure por padrão. Ao atualizar um aplicativo da versão 1.x para a 2.x, os segredos existentes que estão no armazenamento de arquivos são redefinidos.

* A versão 2.x do runtime não inclui suporte interno para provedores de webhook. Essa alteração foi feita para melhorar o desempenho. Você ainda pode usar gatilhos HTTP como pontos de extremidade para webhooks.

* O arquivo de configuração de host (host.json) deve estar vazio ou ter a cadeia de caracteres `"version": "2.0"`.

* Para melhorar o monitoramento, o painel WebJobs no portal, o qual usou a configuração [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard), é substituído com o Azure Application Insights, que usa a configuração [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey). Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md).

* Todas as funções em um aplicativo de funções devem compartilhar a mesma linguagem de programação. Quando você cria um aplicativo de funções, você deve escolher para ele uma pilha de runtime. A pilha de runtime é especificada pelo valor [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) nas configurações do aplicativo. Esse requisito foi adicionado para melhorar o tempo de inicialização e o volume. Ao desenvolver localmente, você também deve incluir essa configuração no [arquivo local.settings.json](functions-run-local.md#local-settings-file).

* O tempo limite padrão para as funções em um plano do Serviço de Aplicativo foi alterado para 30 minutos. Você pode alterar manualmente o tempo limite para ilimitado, usando a configuração [functionTimeout](functions-host-json.md#functiontimeout) em host.json.

* As restrições de simultaneidade HTTP são implementadas por padrão para funções de plano de consumo, com um padrão de 100 solicitações simultâneas por instância. Você pode alterar isso na configuração [`maxConcurrentRequests`](functions-host-json.md#http) no arquivo host.json.

* Devido a [limitações do .NET Core](https://github.com/Azure/azure-functions-host/issues/3414), o F# suporte para funções de script (. fsx) foi removido. Funções F# compiladas (.fs) ainda são compatíveis.

* O formato da URL de webhooks de gatilho da Grade de Eventos foi alterado para `https://{app}/runtime/webhooks/{triggerName}`.

## <a name="migrating-from-2x-to-3x"></a>Migrando de 2. x para 3. x

Azure Functions versão 3. x é compatível com versões anteriores à versão 2. x.  Muitos aplicativos devem ser capazes de atualizar com segurança para 3. x sem nenhuma alteração de código.  Embora a mudança para 3. x seja incentivada, execute testes extensivos antes de alterar a versão principal em aplicativos de produção.

### <a name="breaking-changes-between-2x-and-3x"></a>Alterações significativas entre 2. x e 3. x

A seguir estão as alterações a serem observadas antes de atualizar um aplicativo 2. x para 3. x.

#### <a name="javascript"></a>JavaScript

* As associações de saída atribuídas por meio de `context.done` ou valores de retorno agora se comportam da mesma forma que a configuração em `context.bindings`.

* O objeto de gatilho de temporizador é camelCase em vez de PascalCase

* As funções disparadas pelo hub de eventos com `dataType` binário receberão uma matriz de `binary` em vez de `string`.

* A carga de solicitação HTTP não pode mais ser acessada via `context.bindingData.req`.  Ele ainda pode ser acessado como um parâmetro de entrada, `context.req`e em `context.bindings`.

* Não há mais suporte para node. js 8 e ele não será executado em funções 3. x.

#### <a name="net"></a>.NET

* [As operações de servidor síncronas são desabilitadas por padrão](https://docs.microsoft.com/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

### <a name="changing-version-of-apps-in-azure"></a>Como alterar a versão de aplicativos no Azure

A versão do runtime do Functions usada por aplicativos publicados no Azure é determinada pela configuração de aplicativo [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version). Há suporte para os seguintes valores de versão de tempo de execução principais:

| {1&gt;Valor&lt;1} | Destino de tempo de execução |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2. x |
| `~1` | 1.x |

>[!IMPORTANT]
> Não altere arbitrariamente essa configuração, pois outras alterações de configuração de aplicativo e alterações no código de função podem ser necessárias.

### <a name="locally-developed-application-versions"></a>Versões do aplicativo desenvolvidas localmente

Você pode fazer as seguintes atualizações para que os aplicativos funcionem para alterar localmente as versões de destino.

#### <a name="visual-studio-runtime-versions"></a>Versões de runtime do Visual Studio

No Visual Studio, você seleciona a versão de runtime quando cria um projeto. O Azure Functions Tools para Visual Studio dá suporte às três principais versões de tempo de execução. A versão correta é usada ao depurar e publicar com base nas configurações do projeto. As configurações de versão são definidas no arquivo `.csproj` nas seguintes propriedades:

##### <a name="version-1x"></a>Versão 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Versão 2.x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>Versão 3.x

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 3. x e .NET requer que a extensão `Microsoft.NET.Sdk.Functions` seja pelo menos `3.0.0`.

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Atualizando aplicativos 2. x para 3. x no Visual Studio

Você pode abrir uma função existente com destino 2. x e mover para 3. x editando o arquivo de `.csproj` e atualizando os valores acima.  O Visual Studio gerencia as versões de tempo de execução automaticamente com base nos metadados do projeto.  No entanto, é possível se você nunca tiver criado um aplicativo 3. x antes que o Visual Studio ainda não tenha os modelos e o tempo de execução para 3. x em seu computador.  Isso pode se apresentar com um erro como "nenhum tempo de execução do Functions disponível que corresponde à versão especificada no projeto".  Para buscar os modelos e o tempo de execução mais recentes, percorra a experiência para criar um novo projeto de função.  Quando chegar à tela de seleção de versão e modelo, aguarde até que o Visual Studio conclua a busca dos modelos mais recentes.  Depois que os modelos do .NET Core 3 mais recentes estiverem disponíveis e forem exibidos, você deverá ser capaz de executar e depurar qualquer projeto configurado para a versão 3. x.

> [!IMPORTANT]
> As funções da versão 3. x só podem ser desenvolvidas no Visual Studio se você estiver usando o Visual Studio versão 16,4 ou mais recente.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code e Azure Functions Core Tools

O [Azure Functions Core Tools](functions-run-local.md) é usado para o desenvolvimento de linha de comando e também pela [extensão do Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code. Para desenvolver na versão 3. x, instale a versão 3. x das principais ferramentas. O desenvolvimento da versão 2. x requer a versão 2. x das principais ferramentas e assim por diante. Para obter mais informações, confira [Instalar o Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

Para desenvolvimento no Visual Studio Code, você também precisará atualizar a configuração do usuário para o `azureFunctions.projectRuntime` corresponder à versão das ferramentas instaladas.  Essa configuração também atualiza os modelos e linguagens utilizados durante a criação do aplicativo de funções.  Para criar aplicativos no `~3` você atualizaria a configuração de `azureFunctions.projectRuntime` usuário para `~3`.

![Configuração do tempo de execução de Azure Functions extensão](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Aplicativos Maven e Java

Você pode migrar aplicativos Java da versão 2. x para 3. x [instalando a versão 3. x das ferramentas principais](functions-run-local.md#install-the-azure-functions-core-tools) necessárias para executar localmente.  Depois de verificar se seu aplicativo funciona corretamente em execução localmente na versão 3. x, atualize o arquivo de `POM.xml` do aplicativo para modificar a configuração de `FUNCTIONS_EXTENSION_VERSION` para `~3`, como no exemplo a seguir:

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

## <a name="bindings"></a>Associações

A partir da versão 2. x, o tempo de execução usa um novo [modelo de extensibilidade de associação](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) que oferece essas vantagens:

* Suporte para extensões de associação de terceiros.

* Desacoplamento de runtime e associações. Essa alteração permite que extensões de associação sejam lançadas independentemente e com controle de versão. Você pode, por exemplo, optar por atualizar para uma versão de uma extensão que se baseia em uma versão mais recente de um SDK subjacente.

* Um ambiente de execução mais leve, onde apenas as associações em uso são conhecidas e carregadas pelo runtime.

Com exceção dos gatilhos HTTP e de temporizador, todas as associações devem ser explicitamente adicionadas ao projeto do aplicativo de funções ou registradas no portal. Para obter mais informações, confira [Registrar extensões de associação](./functions-bindings-expressions-patterns.md).

A tabela a seguir mostra quais associações são compatíveis em cada versão de runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Para obter mais informações, consulte os seguintes recursos:

* [Codificar e testar o Azure Functions localmente](functions-run-local.md)
* [Como direcionar para versões do Azure Functions Runtime](set-runtime-version.md)
* [Notas de versão](https://github.com/Azure/azure-functions-host/releases)
