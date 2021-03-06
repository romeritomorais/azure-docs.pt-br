---
title: Crie um back-end de aplicativo móvel sem servidor com Azure Functions e outros serviços
description: Saiba mais sobre os serviços de computação usados para criar um back-end sólido de aplicativo móvel sem servidor.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: ec7091a32a1be8d875e16d8e0a9b20b5e80de387
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76291950"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Crie componentes de back-end móveis com serviços de computação
Cada aplicativo móvel precisa de um back-end responsável pelo armazenamento de dados, pela lógica de negócios e pela segurança. O gerenciamento da infraestrutura para hospedar e executar o código de back-end exige que você dimensione, provisione e dimensione vários servidores. Você também precisa gerenciar atualizações do sistema operacional e o hardware envolvido e aplicar patches de segurança. Em seguida, você precisa monitorar todos esses componentes de infraestrutura para desempenho, disponibilidade e tolerância a falhas. 

A arquitetura sem servidor é útil para esse tipo de cenário porque você não tem servidores para gerenciar e nenhum sistema operacional ou atualizações de hardware ou software relacionado a serem gerenciados. A arquitetura sem servidor economiza tempo e custo do desenvolvedor, o que significa um tempo mais rápido para o mercado e a energia concentrada na criação de aplicativos.

## <a name="benefits-of-compute"></a>Benefícios da computação
- A abstração de servidores significa que não há necessidade de se preocupar com hospedagem, aplicação de patches e segurança, o que permite que você se concentre exclusivamente no código.
- O dimensionamento imediato e eficiente garante que os recursos sejam provisionados automaticamente ou sob demanda em qualquer escala necessária.
- Alta disponibilidade e tolerância a falhas.
- A microcobrança garante que você seja cobrado apenas quando seu código estiver realmente em execução.
- O código é executado na nuvem escrita na linguagem de sua escolha.

Use os seguintes serviços para habilitar recursos de computação sem servidor em seus aplicativos móveis.

## <a name="azure-functions"></a>Funções do Azure
[Azure Functions](https://azure.microsoft.com/services/functions/) é uma experiência de computação controlada por evento que você pode usar para executar seu código, escrito na linguagem de programação de sua escolha, sem se preocupar com servidores. Você não precisa gerenciar o aplicativo ou a infraestrutura para executá-lo. O Functions é dimensionado sob demanda e você paga apenas pela hora em que seu código é executado. O Azure Functions é uma ótima maneira de implementar uma API para um aplicativo móvel. Eles são fáceis de implementar e manter e podem ser acessados por meio de HTTP.

**Principais recursos**
- Orientado a eventos e escalonável onde você pode usar gatilhos e associações para definir quando uma função é invocada e para quais dados ele se conecta.
- Traga suas próprias dependências porque o Functions oferece suporte a NuGet e NPM, para que você possa usar suas bibliotecas favoritas.
- Segurança integrada para que você possa proteger funções disparadas por HTTP com provedores OAuth, como Azure Active Directory, Facebook, Google, Twitter e conta da Microsoft.
- Integração simplificada com diferentes [Serviços do Azure](/azure/azure-functions/functions-overview) e ofertas de SaaS (software como serviço).
- Desenvolvimento flexível para que você possa codificar suas funções diretamente no portal do Azure ou configurar a integração contínua e implantar seu código por meio do GitHub, Azure DevOps Services e outras ferramentas de desenvolvimento com suporte.
- O tempo de execução do Functions é de software livre e está disponível no [GitHub](https://github.com/azure/azure-webjobs-sdk-script).
- Experiência de desenvolvimento aprimorada em que você pode codificar, testar e depurar localmente usando seu editor preferido ou uma interface da Web fácil de usar com monitoramento com ferramentas integradas e recursos internos de DevOps.
- Variedade de linguagens de programação e opções de hospedagem para desenvolvimento C#, como node. js, Java, JavaScript ou Python.
- Modelo de preços de pagamento por uso significa que você paga apenas pelo tempo gasto na execução do código.

**Referências**
- [Azure portal](https://portal.azure.com)
- [Documentação do Azure Functions](/azure/azure-functions/)
- [Guia do desenvolvedor do Azure Functions](/azure/azure-functions/functions-reference)
- [Inícios Rápidos](/azure/azure-functions/functions-create-first-function-vs-code)
- [Amostras](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Serviço de Aplicativos do Azure
Com o [serviço Azure app](https://azure.microsoft.com/services/app-service/), você pode criar e hospedar aplicativos Web e APIs RESTful na linguagem de programação de sua escolha sem gerenciar a infraestrutura. Ele oferece dimensionamento automático e alta disponibilidade, dá suporte ao Windows e ao Linux e permite implantações automatizadas do GitHub, do Azure DevOps ou de qualquer repositório git.

**Principais recursos**
- Suporte a vários idiomas e estruturas para ASP.NET, ASP.NET Core, Java, Ruby, Node. js, PHP ou Python. Você também pode executar o PowerShell e outros scripts ou executáveis como serviços em segundo plano.
- Otimização de DevOps por meio de integração e implantação contínuas com o Azure DevOps, GitHub, BitBucket, Hub do Docker ou registro de contêiner do Azure. Gerencie seus aplicativos no serviço de aplicativo usando Azure PowerShell ou a CLI (interface de linha de comando) de plataforma cruzada.
- Escala global com alta disponibilidade para escalar verticalmente ou horizontalmente de forma manual ou automática.
- Conexões com plataformas SaaS e dados locais para escolher entre mais de 50 conectores para sistemas empresariais, como SAP, serviços SaaS, como Salesforce e serviços de Internet, como o Facebook. Acesse dados locais usando conexões híbridas e redes virtuais do Azure.
- O serviço de Azure App é compatível com ISO, SOC e PCI. Autentique usuários com Azure Active Directory ou com a entrada para mídia social, como Google, Facebook, Twitter e Microsoft. Criar restrições de endereço IP e gerenciar identidades de serviço.
- Modelos de aplicativos a serem escolhidos em uma lista extensa de modelos de aplicativos no Azure Marketplace, como WordPress, Joomla e Drupal.
- A integração do Visual Studio com ferramentas dedicadas no Visual Studio simplifica o trabalho de criação, implantação e depuração.

**Referências**
- [Azure portal](https://portal.azure.com/)
- [Documentação do serviço de Azure App](/azure/app-service/)
- [Inícios Rápidos](/azure/app-service/app-service-web-get-started-dotnet)
- [Amostras](/azure/app-service/samples-cli)
- [Tutoriais](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Serviço do Kubernetes do Azure
O [AKS (Serviço de Kubernetes do Azure)](https://azure.microsoft.com/services/kubernetes-service/) gerencia seu ambiente Kubernetes hospedado. O AKS acelera e facilita a implantação e o gerenciamento de aplicativos em contêineres sem conhecimento de orquestração de contêineres. Ele também elimina a carga de operações e manutenção contínuas. AKS provisiona, atualiza e dimensiona recursos sob demanda, sem colocar seus aplicativos offline.

**Principais recursos**
- Migre facilmente aplicativos existentes para contêineres e execute em AKS.
- Simplifique a implantação e o gerenciamento de aplicativos baseados em microserviços.
- Proteja o DevOps para AKS a fim de obter o equilíbrio entre velocidade e segurança e fornecer código mais rápido em escala.
- Dimensione com facilidade usando AKS e instâncias de contêiner do Azure para provisionar pods dentro de instâncias de contêiner que começam em segundos.
- Implante e gerencie dispositivos IoT sob demanda.
- Treine os modelos de aprendizado de máquina com o uso de ferramentas como TensorFlow e KubeFlow.

**Referências**
- [Azure portal](https://portal.azure.com/)
- [Documentação do serviço kubernetes do Azure](/azure/aks/)
- [Inícios Rápidos](/azure/aks/kubernetes-walkthrough-portal)
- [Tutoriais](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Instâncias de Contêiner do Azure
As [instâncias de contêiner do Azure](https://azure.microsoft.com/services/container-instances/) são uma ótima solução para qualquer cenário que possa operar em contêineres isolados, como aplicativos simples, automação de tarefas e trabalhos de compilação. Desenvolva aplicativos rapidamente sem gerenciar VMs.

**Principais recursos**
- Os tempos de inicialização rápidos como instâncias de contêiner podem iniciar contêineres no Azure em segundos, sem a necessidade de provisionar e gerenciar VMs.
- Conectividade IP pública e nome DNS personalizado.
- A segurança em nível de hipervisor que garante que seu aplicativo seja isolada em um contêiner, como seria em uma VM.
- Tamanhos personalizados para a utilização ideal, permitindo especificações exatas de núcleos de CPU e memória. Você paga com base no que precisar e é cobrado por segundo, assim, é possível ajustar seus gastos eficientemente com base em suas necessidades reais.
- Armazenamento persistente para recuperar e persistir o estado. As instâncias de contêiner oferecem a montagem direta de compartilhamentos de arquivos do Azure.
- Contêineres do Linux e do Windows agendados com a mesma API.

**Referências**
- [Azure portal](https://portal.azure.com/)
- [Documentação das instâncias de contêiner do Azure](/azure/container-instances/)
- [Inícios Rápidos](/azure/container-instances/container-instances-quickstart-portal)
- [Amostras](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Tutoriais](/azure/container-instances/container-instances-tutorial-prepare-app)