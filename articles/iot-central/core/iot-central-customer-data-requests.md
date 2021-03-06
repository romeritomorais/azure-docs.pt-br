---
title: Recursos de solicitação de dados do cliente no Azure IoT Central | Microsoft Docs
description: Este artigo descreve como identificar, excluir e exportar dados do cliente no aplicativo IoT Central do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 3cf88b4d3c4c74493235d2997db4d464bb055b81
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023745"
---
# <a name="summary-of-customer-data-request-features"></a>Resumo dos recursos de solicitação de dados do cliente

O Azure IoT Central é uma solução de software como serviço de IoT (Internet das Coisas) totalmente gerenciada que facilita a conexão, o monitoramento, o gerenciamento de seus ativos de IoT em escala e a criação de insights detalhados com base nos dados de IoT, possibilitando que você tome decisões informadas.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identificando os dados do cliente

As IDs de Objeto do Azure Active Directory são usadas para identificar usuários e atribuir funções. O portal do Azure IoT Central exibe os endereços de email do usuário para as atribuições de função, mas apenas a ID de Objeto do Azure Active Directory é armazenada; o endereço de email é consultado dinamicamente por meio do Azure Active Directory. Os administradores do Azure IoT Central podem exibir, exportar e excluir os usuários do aplicativo na seção de administração do usuário de um aplicativo do Azure IoT Central.

No aplicativo, os endereços de email podem ser configurados para receber alertas. Nesse caso, os endereços de email são armazenados no IoT Central e precisam ser gerenciados na página de administração de conta no aplicativo.

Com relação aos dispositivos, a Microsoft não mantém nenhuma informação e não tem acesso aos dados que permitem a correlação entre dispositivos e usuários. Muitos dos dispositivos gerenciados no Azure IoT Central não são dispositivos pessoais, por exemplo, uma máquina de vendas ou uma cafeteira elétrica. No entanto, os clientes podem considerar que alguns dispositivos contêm informações de identificação pessoal e, a seu critério, manter seus próprios sistemas de acompanhamento de ativos ou inventário que vinculam dispositivos a indivíduos. O Azure IoT Central gerencia e armazena todos os dados associados a dispositivos como se fossem dados pessoais.

Quando você usa os serviços corporativos da Microsoft, a Microsoft gera algumas informações, conhecidas como logs gerados pelo sistema. Esses logs constituem ações reais conduzidas no serviço e dados de diagnóstico relacionados a dispositivos individuais e não estão relacionados à atividade do usuário. Os logs gerados pelo sistema do Azure IoT Central não são acessíveis nem exportáveis por administradores de aplicativos.

## <a name="deleting-customer-data"></a>Excluindo os dados do cliente

A capacidade de excluir os dados do usuário só é fornecida por meio da página de administração do IoT Central. Os administradores de aplicativos podem selecionar o usuário a ser excluído e selecionar **excluir** no canto superior direito do aplicativo para excluir o registro. Os administradores de aplicativos também podem remover contas individuais que não estão mais associadas ao aplicativo em questão.

Depois que um usuário é excluído, nenhum alerta adicional é enviado por email a ele. No entanto, seu endereço de email precisa ser removido individualmente de cada alerta configurado.

## <a name="exporting-customer-data"></a>Exportando os dados do cliente

A capacidade de exportar os dados só é fornecida por meio da página de administração do IoT Central. Os dados do cliente, incluindo as funções atribuídas, podem ser selecionados, copiados e colados por um administrador de aplicativos.

## <a name="links-to-additional-documentation"></a>Links para documentação adicional

Para obter mais informações sobre a administração de conta, incluindo definições de função, consulte [Como administrar seu aplicativo](howto-administer.md).
