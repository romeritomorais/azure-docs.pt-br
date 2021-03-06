---
title: Instalação do Windows da central de segurança do Azure para agente de IoT | Microsoft Docs
description: Saiba mais sobre como instalar a central de segurança do Azure para agente de IoT em dispositivos Windows de 32 bits ou 64 bits.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: acc99f260931de7fd8c7566a3ff6daf43f34c5ef
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597220"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Implantar uma central de segurança do Azure C#para agente de segurança baseado em IOT para Windows

Este guia explica como instalar a central de segurança do Azure para C#o agente de segurança baseado em IOT no Windows.

Neste guia, você aprenderá a: 
> [!div class="checklist"]
> * Instalar
> * Verificar implantação
> * Desinstalar o agente
> * Solução de problemas 

## <a name="prerequisites"></a>Prerequisites

Para outras plataformas e tipos de agente, consulte [escolher o agente de segurança certo](how-to-deploy-agent.md).

1. Direitos de administrador local no computador em que você deseja instalar. 

1. [Criar um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação 

Para instalar o agente de segurança, use o seguinte fluxo de trabalho:

1. Instale a central de segurança do Azure para C# o agente do Windows IOT no dispositivo. Baixe a versão mais recente em seu computador na central de segurança do Azure para o [repositório GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS)do IOT.

1. Extraia o conteúdo do pacote e navegue até a pasta /Install.

1. Abra o Windows PowerShell como administrador. 
1. Adicione permissões em execução ao script InstallSecurityAgent executando:<br>
    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```
    
    em seguida, execute:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Por exemplo:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Para obter mais informações sobre parâmetros de autenticação, consulte [como configurar a autenticação](concept-security-agent-authentication-methods.md).

Esse script executa as seguintes ações:

- Instala pré-requisitos.

- Adiciona um usuário de serviço (com logon interativo desabilitado).

- Instala o agente como um **Serviço do Sistema**.

- Configura o agente com os parâmetros de autenticação fornecidos.


Para obter ajuda adicional, use o comando Get-Help no PowerShell <br>Exemplo de Get-Help:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Verificar o status da implantação

- Verifique o status da implantação do agente executando:<br>
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente:

1. Execute o seguinte script do PowerShell com o parâmetro **-mode** definido como **Uninstall**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Solução de problemas

Se o agente não conseguir iniciar, ative o log (por padrão, o log fica *desativado*) para obter mais informações.

Para ativar o log:

1. Abra o arquivo de configuração (General. config) para edição usando um editor de arquivo padrão.

1. Edite os valores a seguir:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > É recomendável deixar o registro em log **desativado** após a conclusão da solução de problemas. Deixar o log **ativado** aumenta o tamanho do arquivo de log e o uso de dados. 

1. Reinicie o agente executando o seguinte PowerShell ou a linha de comando:

    **Powershell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   ou

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Examine o arquivo de log para obter mais informações sobre a falha.

   Localização do arquivo de log: `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Próximas etapas
- Leia a [visão geral](overview.md) da central de segurança do Azure para serviços de IOT
- Saiba mais sobre a [arquitetura](architecture.md) da central de segurança do Azure para IOT
- Habilite o [serviço](quickstart-onboard-iot-hub.md)
- Leia as [Perguntas frequentes](resources-frequently-asked-questions.md)
- Entenda os [alertas](concept-security-alerts.md)