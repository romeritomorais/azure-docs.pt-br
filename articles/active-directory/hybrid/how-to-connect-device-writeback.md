---
title: 'Azure AD Connect: Habilitando o write-back de dispositivo | Microsoft Docs'
description: Este documento fornece detalhes sobre como habilitar write-back de dispositivo usando o Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 632f6f80184c6ba3409bd30ae070cbaefc77f036
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109494"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: Habilitando o write-back de dispositivo
> [!NOTE]
> Uma assinatura do Azure AD Premium é necessária para write-back do dispositivo.
> 
> 

A documentação a seguir fornece informações sobre como habilitar o recurso de write-back do dispositivo no Azure AD Connect. Write-back de dispositivo é usado nas seguintes situações:

* Habilitar [Windows Hello para empresas usando implantação híbrida de confiança do certificado](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust-prereqs#device-registration)
* Habilitar o acesso condicional com base em dispositivos ao ADFS (2012 R2 ou superior) (terceira parte) de aplicativos protegidos.

Isso fornece segurança adicional e a garantia de que o acesso aos aplicativos é concedido somente a dispositivos confiáveis. Para obter mais informações sobre o acesso condicional, consulte [gerenciamento de riscos com acesso condicional](../active-directory-conditional-access-azure-portal.md) e [Configurando o acesso condicional no local, usando o registro de dispositivo do Active Directory do Azure](../../active-directory/active-directory-device-registration-on-premises-setup.md).

> [!IMPORTANT]
> <li>Os dispositivos devem estar localizados na mesma floresta que os usuários. Como o write-back dos dispositivos deve ser feito em uma única floresta, esse recurso não é compatível com uma implantação com várias florestas de usuário.</li>
> <li>Somente um objeto de configuração de registro de dispositivo pode ser adicionado à floresta local do Active Directory. Esse recurso não é compatível com uma topologia onde o Active Directory local é sincronizado a vários diretórios do AD do Azure.</li>

## <a name="part-1-install-azure-ad-connect"></a>Parte 1: Instalar o Azure AD Connect
Instale o Azure AD Connect usando configurações expressas ou personalizadas. A Microsoft recomenda iniciar com todos os usuários e grupos sincronizados com êxito antes de habilitar o write-back do dispositivo.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Parte 2: Habilitar o write-back de dispositivo no Azure AD Connect
1. Execute o assistente de instalação novamente. Selecione **Configurar opções de dispositivo** na página Tarefas Adicionais e clique em **Avançar**. 

    ![Configurar opções do dispositivo](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > As novas opções de dispositivo de configuração estão disponíveis somente na versão 1.1.819.0 e mais recente.

2. Na página de opções do dispositivo, selecione **Configurar o write-back de dispositivo**. Opção **Desabilitar write-back de dispositivo** não estará disponível até que o write-back do dispositivo esteja habilitado. Clique em **Próximo** para mover para a próxima página do assistente.
    ![Escolha operação do dispositivo](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. Na página de write-back, você verá o domínio fornecido como a floresta de write-back de dispositivo padrão.
   ![Florestas de destino do write-back de dispositivo da Instalação Personalizada](./media/how-to-connect-device-writeback/writebackforest.png)

4. A página **Contêiner de dispositivo** fornece a opção de preparação do active directory, usando uma das duas opções disponíveis:

    a. **Fornecer credenciais de administrador corporativo**: Se as credenciais de administrador corporativo forem fornecidas para a floresta em que é necessário fazer o write-back dos dispositivos, o Azure AD Connect preparará a floresta automaticamente durante a configuração do write-back de dispositivo.

    b. **Baixar o script do PowerShell**: O Azure AD Connect gera automaticamente um script do PowerShell que pode preparar o Active Directory para o write-back de dispositivo. As credenciais de administrador corporativo não podem ser fornecidas no Azure AD Connect, recomenda-se realizar o download do script do PowerShell. Forneça o script PowerShell baixado **CreateDeviceContainer.psq** para o administrador da empresa da floresta onde os dispositivos serão gravados de volta.
    ![Preparar a floresta do Active Directory](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    As seguintes operações são executadas para preparar a floresta do active directory:
    * Se ainda não existirem, ele criará e configurará novos contêineres e objetos em CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn].
    * Se não existir, cria e configura novos contêineres e objetos em CN=RegisteredDevices,[domain-dn]. Objetos de dispositivo serão criados neste contêiner.
    * Define as permissões necessárias na conta do Azure AD Connector para gerenciar dispositivos em seu Active Directory.
    * Só precisa ser executado em uma floresta, mesmo que o Azure AD Connect esteja instalado em várias florestas.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Verifique se que dispositivos estão sincronizados com o Active Directory
O write-back do dispositivo agora deve estar funcionando corretamente. Pode levar até 3 horas para que os objetos do dispositivo sejam gravados no Active Directory.  Para verificar se os dispositivos estão sendo sincronizados corretamente, faça o seguinte, depois de concluir as regras de sincronização:

1. Inicie o Centro Administrativo do Active Directory.
2. Expanda RegisteredDevices dentro do domínio que está sendo federado.

   ![Dispositivos registrados do Centro de Administração do Active Directory](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Dispositivos registrados atuais serão listados lá.

   ![Lista de dispositivos registrados do Centro de Administração do Active Directory](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Habilitar o acesso condicional
Instruções detalhadas para habilitar esse cenário estão disponíveis em [Definindo o acesso condicional local usando o registro do dispositivo do Active Directory do Azure](../../active-directory/active-directory-device-registration-on-premises-setup.md).

## <a name="troubleshooting"></a>solução de problemas
### <a name="the-writeback-checkbox-is-still-disabled"></a>A caixa de seleção de write-back é desabilitada
Se a caixa de seleção para write-back do dispositivo não estiver habilitada, mesmo se você tiver seguido as etapas acima, as etapas seguir o guiarão através do que o assistente de instalação estiver verificando antes de a caixa ser habilitada.

Primeiro as prioridades:

* A floresta em que os dispositivos estão presentes precisa ter o esquema de floresta atualizado para o nível do Windows 2012 R2, de modo que o objeto de dispositivo e os atributos associados estejam presentes.
* Se já estiver executando o assistente de instalação, as alterações não serão detectadas. Nesse caso, conclua o assistente de instalação e execute-o novamente.
* Verifique se a conta fornecida no script de inicialização é, na verdade, o usuário correto usado pelo Active Directory Connector. Para fazer isso, siga essas etapas:
  * No menu Iniciar, abra **Serviço de sincronização**.
  * Abra a guia **Conectores** .
  * Localize o conector com o tipo de Serviços de Domínio do Active Directory e selecione-o.
  * Em **Ações**, selecione **Propriedades**.
  * Vá para **Conectar-se à floresta do Active Directory**. Verifique o nome de usuário e domínio especificado nessa correspondência de tela a conta fornecida para o script.
    ![Conta do conector no Sync Service Manager](./media/how-to-connect-device-writeback/connectoraccount.png)

Verifique a configuração no Active Directory:

* Verifique se o serviço de registro do dispositivo está localizado no local abaixo (CN = DeviceRegistrationService, CN = Serviços de registro de dispositivo, CN = Configuração do registro de dispositivo, CN = Services, CN = Configuration) no contexto de nomenclatura da configuração.

![Solucionar problemas, DeviceRegistrationService no namespace de configuração](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Verifique se há apenas um objeto de configuração pesquisando o namespace de configuração. Se houver mais de um, exclua a cópia.

![Solucionar problemas, procurar por objetos duplicados](./media/how-to-connect-device-writeback/troubleshoot2.png)

* No objeto do serviço de registro de dispositivo, verifique se o atributo msDS-DeviceLocation está presente e tem um valor. Pesquise esse local e verifique se ele está presente com o atributo msDS-DeviceContainer objectType.

![Solucionar problemas, msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Solucionar problemas, classe de objeto RegisteredDevices](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Verifique se a conta usada pelo conector do Active Directory tem as permissões necessárias no contêiner dos Dispositivos Registrados encontradas pela etapa anterior. Permissões esperadas neste contêiner:

![Solucionar problemas, verificar permissões no contêiner](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Verifique se a conta do Active Directory tem permissões no objeto CN = Configuração do registro de dispositivo, CN = Services, CN= Configuração.

![Solucionar problemas, verificar permissões na configuração de registro do dispositivo](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Informações adicionais
* [Gerenciamento de riscos com acesso condicional](../active-directory-conditional-access-azure-portal.md)
* [Configurando o acesso condicional no local usando o registro do dispositivo do Azure Active Directory](../../active-directory/active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).

