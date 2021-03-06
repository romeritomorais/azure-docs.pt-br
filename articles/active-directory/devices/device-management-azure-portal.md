---
title: Como gerenciar dispositivos usando o portal do Azure | Microsoft Docs
description: Saiba como usar o portal do Azure para gerenciar dispositivos.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09de5911ca0946bfcbcb77d1ad4131c8feac9f0
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654657"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Gerenciar identidades de dispositivo usando o portal do Azure

Com o gerenciamento de identidade do dispositivo no Azure Active Directory (AD do Azure), você pode garantir que os usuários estejam acessando seus recursos de dispositivos que atendam aos seus padrões de segurança e conformidade.

Este artigo:

- Presume que você esteja familiarizado com a [introdução ao gerenciamento de identidade do dispositivo no Azure Active Directory](overview.md)
- Fornece informações sobre como gerenciar suas identidades de dispositivo usando o portal do AD do Azure

## <a name="manage-device-identities"></a>Gerenciar identidades do dispositivo

O portal do AD do Azure fornece um local central para gerenciar suas identidades de dispositivo. Você pode chegar a esse local usando um [link direto](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) ou:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory** **dispositivos** > .

A página de **Dispositivos** permite que você:

- Configurar suas configurações de dispositivo
- Localizar dispositivos
- Executar tarefas de gerenciamento de identidade do dispositivo
- Examinar os logs de auditoria relacionados ao dispositivo  
  
## <a name="configure-device-settings"></a>Definir configurações do dispositivo

Para gerenciar suas identidades de dispositivo usando o portal do AD do Azure, seus dispositivos precisam ser [registrados ou ingressados](overview.md) no Azure AD. Como administrador, você pode ajustar o processo de registro e ingresso de dispositivos definindo as configurações do dispositivo.

A página Configurações do dispositivo permite que você defina as configurações relacionadas às identidades dos dispositivos:

![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/21.png)

- **Os usuários podem ingressar dispositivos no Azure ad** – essa configuração permite que você selecione os usuários que podem registrar seus dispositivos como dispositivos ingressados no Azure AD. O padrão é **Todos**.

> [!NOTE]
> **Os usuários podem ingressar dispositivos na configuração do Azure ad** é aplicável somente ao ingresso no Azure AD no Windows 10.

- **Outros administradores locais nos dispositivos associados ao Azure AD** – você pode selecionar os usuários que têm direitos de administrador local em um dispositivo. Os usuários adicionados aqui são adicionados à função *Administradores do dispositivo* no Azure AD. Os administradores globais no Azure AD e os proprietários do dispositivo recebem direitos de administrador local por padrão. Essa opção é uma funcionalidade Premium Edition disponível por meio de produtos como o Azure AD Premium ou o EMS (Enterprise Mobility Suite).
- **Os usuários podem registrar seus dispositivos com o Azure ad** -você precisa definir essa configuração para permitir que dispositivos Windows 10 Personal, Ios, Android e macOs sejam registrados com o Azure AD. Se você selecionar **nenhum**, os dispositivos não terão permissão para se registrar no Azure AD. O registro com o Microsoft Intune ou o MDM (Gerenciamento de Dispositivo Móvel) para o Office 365 exige registro. Se você tiver configurado qualquer um desses serviços, a opção **TODOS** estará selecionada e **NENHUM** não estará disponível.
- **Exigir autenticação multifator para ingressar em dispositivos** – você pode escolher se os usuários precisam fornecer um fator de autenticação adicional para ingressar seu dispositivo no Azure AD. O padrão é **No**. É recomendável exigir a autenticação multifator ao registrar um dispositivo. Antes de habilitar a autenticação multifator para este serviço, você deve garantir que a autenticação multifator esteja configurada para os usuários que registram seus dispositivos. Para saber mais sobre os diferentes serviços de autenticação multifator do Azure, consulte [Introdução à autenticação multifator do Azure](../authentication/concept-mfa-whichversion.md). 

> [!NOTE]
> **Exigir a configuração de autenticação multifator para ingressar dispositivos** se aplica a dispositivos que estão ingressados no Azure ad ou o Azure ad registrado. Essa configuração não se aplica a dispositivos ingressados no Azure AD híbrido.

- **Número máximo de dispositivos** – essa configuração permite que você selecione o número máximo de dispositivos registrados no Azure ad ou do AD do Azure que um usuário pode ter no Azure AD. Se um usuário atingir esta cota, ele não poderá adicionar mais dispositivos até que um ou mais dos seus dispositivos existentes sejam removidos. O valor padrão é **20**.

> [!NOTE]
> A configuração **de número máximo de dispositivos** se aplica a dispositivos que estão ingressados no Azure ad ou no Azure ad registrados. Essa configuração não se aplica a dispositivos ingressados no Azure AD híbrido.

- **Os usuários podem sincronizar configurações e dados de aplicativo em dispositivos** – por padrão, essa configuração é definida como **NENHUM**. Selecionar usuários específicos ou grupos ou TODOS permite que as configurações do usuário e os dados de aplicativo sejam sincronizados em seus dispositivos Windows 10. Saiba mais sobre como a sincronização funciona no Windows 10.
Essa opção é uma funcionalidade premium disponível por meio de produtos como o Azure AD Premium ou o EMS (Enterprise Mobility Suite).

## <a name="locate-devices"></a>Localizar dispositivos

Você tem duas opções para localizar dispositivos registrados e associados:

- **Todos os dispositivos** na seção **Gerenciar** da página **Dispositivos**  
- **Dispositivos** na seção **Gerenciar** de uma página **Usuário**

Com as duas opções, você pode obter uma exibição que:

- Permite pesquisar dispositivos usando o nome de exibição ou a ID do dispositivo como filtro.
- Fornece visão geral detalhada de dispositivos registrados e associados
- Permite que você execute tarefas comuns de gerenciamento de dispositivo

![Todos os dispositivos](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* Se você vir um dispositivo que é "ingressado no Azure AD híbrido" com um estado "pendente" na coluna registrado, ele indica que o dispositivo foi sincronizado do Azure AD Connect e está aguardando a conclusão do registro do cliente. Leia mais sobre como [planejar sua implementação de ingresso no Azure ad híbrido](hybrid-azuread-join-plan.md). Informações adicionais podem ser encontradas no artigo, perguntas frequentes sobre [dispositivos](faq.md).
>
>   ![Dispositivos pendentes](./media/device-management-azure-portal/75.png)
>
>* Para alguns dispositivos iOS, os nomes de dispositivo que contém apóstrofos podem usar caracteres diferentes que se parecem com apóstrofos. Portanto, Pesquisar por tais dispositivos é um pouco complicado – se você não estiver vendo os resultados da pesquisa corretamente, verifique se a cadeia de caracteres de pesquisa contém o caractere de apóstrofo correspondente.

## <a name="device-identity-management-tasks"></a>Tarefas de gerenciamento de identidade do dispositivo

Como administrador global ou administrador de dispositivo de nuvem, você pode gerenciar os dispositivos registrados ou ingressados. Os administradores de Serviço do Intune podem:

- Atualizar dispositivos – exemplos são operações diárias, como habilitar/desabilitar dispositivos
- Excluir dispositivos – quando um dispositivo está desativado e deve ser excluído do Azure AD

Esta seção fornece informações sobre tarefas comuns de gerenciamento de identidade de dispositivo.

### <a name="manage-an-intune-device"></a>Gerenciar um dispositivo do Intune

Se você for um administrador do Intune, será possível gerenciar dispositivos marcados como **Microsoft Intune**. Se o dispositivo não estiver registrado com Microsoft Intune a opção "gerenciar" ficará esmaecida.

![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Habilitar/desabilitar um dispositivo do Azure Active Directory

Para ativar / desativar um dispositivo, você tem duas opções:

- O menu de tarefas ("...") na página **Todos os dispositivos**

   ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/71.png)

- A barra de ferramentas na página **Dispositivos**

   ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/32.png)

**Comentários:**

- Você precisa ser um administrador global ou um administrador de dispositivo de nuvem no Azure AD para habilitar/desabilitar um dispositivo. 
- A desabilitação de um dispositivo impede que um dispositivo seja autenticado com êxito com o Azure AD, impedindo que o dispositivo acesse seus recursos do Azure AD protegidos pela AC do dispositivo ou usando suas credenciais do WH4B.
- A desabilitação do dispositivo revogará o token de atualização primário (PRT) e os tokens de atualização (RT) no dispositivo.

### <a name="delete-an-azure-ad-device"></a>Excluir um dispositivo do Azure Active Directory

Para excluir um dispositivo, você tem duas opções:

- O menu de tarefas ("...") na página **Todos os dispositivos**

   ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/72.png)

- A barra de ferramentas na página **Dispositivos**

   ![Excluir um dispositivo](./media/device-management-azure-portal/34.png)

**Comentários:**

- Você precisa ser um administrador global ou um administrador do Intune no Azure AD para excluir um dispositivo.
- Excluindo um dispositivo:
   - Impede que um dispositivo acesse seus recursos do Azure Active Directory.
   - Remove todos os detalhes que estão relacionados ao dispositivo, por exemplo, chaves do BitLocker para dispositivos Windows.  
   - Representa uma atividade não recuperável e não é recomendável a menos que necessário.

Se um dispositivo for gerenciado por outra autoridade de gerenciamento (por exemplo, Microsoft Intune), verifique se o dispositivo foi apagado/desativado antes de excluir o dispositivo no Azure AD. Examine como [gerenciar dispositivos obsoletos](device-management-azure-portal.md) antes de excluir qualquer dispositivo.

### <a name="view-or-copy-device-id"></a>Exibir ou copiar a ID do dispositivo

Você pode usar uma ID de dispositivo para verificar os detalhes de ID de dispositivo no dispositivo ou usar o PowerShell durante a solução de problemas. Para acessar a opção de cópia, clique no dispositivo.

![Exibir uma ID do dispositivo](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Exibir ou copiar as chaves do BitLocker

É possível exibir e copiar as chaves do BitLocker para ajudar os usuários a recuperar sua unidade criptografada. Essas chaves só estão disponíveis para dispositivos Windows que são criptografados e têm suas chaves armazenadas no Azure AD. Você pode copiar essas chaves ao acessar detalhes do dispositivo.

![Exibir chaves do BitLocker](./media/device-management-azure-portal/36.png)

Para exibir ou copiar as chaves do BitLocker, você precisa ser o proprietário do dispositivo ou um usuário que tenha pelo menos uma das seguintes funções atribuídas:

- Administrador de Dispositivo de Nuvem
- Administrador global
- Administrador de assistência técnica
- Administrador de serviços do Intune
- Administrador de segurança
- Leitor de segurança

> [!NOTE]
> Os dispositivos do Windows 10 ingressados no Azure AD híbrido não têm um proprietário. Portanto, se você está procurando por um dispositivo pelo proprietário e não o encontrou, pesquise pela ID do dispositivo.

## <a name="audit-logs"></a>Logs de auditoria

As atividades de dispositivo estão disponíveis por meio dos logs de atividade. Esses logs incluem atividades disparadas pelo serviço de registro de dispositivo e pelos usuários:

- Criação de dispositivo e adição de usuários/proprietários no dispositivo
- Alterações nas configurações do dispositivo
- Operações de dispositivo como excluir ou atualizar um dispositivo

O ponto de entrada para os dados de auditoria é **Logs de auditoria**, na seção **Atividade** da página **Dispositivos**.

O log de auditoria tem uma exibição de lista padrão que mostra:

- A data e a hora da ocorrência
- Os destinos
- O iniciador/ator (quem) de uma atividade
- A atividade (o quê)

![Logs de auditoria](./media/device-management-azure-portal/63.png)

Você pode personalizar o modo de exibição de lista clicando em **Colunas** na barra de ferramentas.

![Logs de auditoria](./media/device-management-azure-portal/64.png)

Para restringir os dados relatados a um nível que funciona para você, filtre os dados de auditoria usando os seguintes campos:

- Categoria
- Tipo de recurso de atividade
- Atividade
- Intervalo de datas
- Destino
- Iniciado por (ator)

Além dos filtros, você pode pesquisar itens específicos.

![Logs de auditoria](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Próximas etapas

[Como gerenciar dispositivos obsoletos no Azure AD](manage-stale-devices.md)
