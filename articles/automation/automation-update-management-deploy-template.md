---
title: Usar modelos de Azure Resource Manager para carregar Gerenciamento de Atualizações | Microsoft Docs
description: Você pode usar um modelo de Azure Resource Manager para carregar a solução de Gerenciamento de Atualizações de automação do Azure.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/27/2020
ms.openlocfilehash: a8b382663b56d7481da876979e33194fb0ac533d
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925794"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Integração de Gerenciamento de Atualizações solução usando Azure Resource Manager modelo

Você pode usar [modelos de Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) para habilitar a solução de gerenciamento de atualizações de automação do Azure em seu grupo de recursos. Este artigo fornece um modelo de exemplo que automatiza o seguinte:

* Criação de um Azure Monitor espaço de trabalho Log Analytics.
* Criação de uma conta de automação do Azure.
* Vincula a conta de automação ao espaço de trabalho Log Analytics se ainda não estiver vinculada.
* Carregar a solução de Gerenciamento de Atualizações de automação do Azure

O modelo não automatiza a integração de uma ou mais VMs do Azure ou não Azure.

Se você já tiver um espaço de trabalho Log Analytics e uma conta de automação implantada em uma região com suporte em sua assinatura, elas não estarão vinculadas e o espaço de trabalho ainda não tiver a solução Gerenciamento de Atualizações implantada, usando esse modelo criará com êxito o link e implanta a solução Gerenciamento de Atualizações. 

## <a name="api-versions"></a>Versões de API

A tabela a seguir lista a versão de API para os recursos usados neste exemplo.

| Recurso | Tipo de recurso | Versão da API |
|:---|:---|:---|
| Workspace | workspaces | 2017-03-15-preview |
| Conta de automação | automação | 2015-10-31 | 
| Solução | soluções | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Antes de usar o modelo

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo Azure PowerShell AZ. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisa fazer a atualização, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure. Com o Azure PowerShell, a implantação usa [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que você esteja executando o CLI do Azure versão 2.1.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Com CLI do Azure, essa implantação usa [AZ Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

O modelo JSON está configurado para solicitar:

* O nome do workspace
* A região na qual criar o espaço de trabalho
* O nome da conta de automação
* A região na qual criar a conta

O modelo JSON especifica um valor padrão para os outros parâmetros que provavelmente seriam usados como uma configuração padrão em seu ambiente. Você pode armazenar o modelo em uma conta de armazenamento do Azure para acesso compartilhado em sua organização. Para obter mais informações sobre como trabalhar com modelos, consulte [implantar recursos com modelos do Resource Manager e CLI do Azure](../azure-resource-manager/templates/deploy-cli.md).

Os seguintes parâmetros no modelo são definidos com um valor padrão para o espaço de trabalho Log Analytics:

* SKU – assume por padrão o novo tipo de preço por GB lançado no modelo de preço de abril de 2018
* retenção de dados-padrão para trinta dias

>[!WARNING]
>Se criar ou configurar um espaço de trabalho do Log Analytics em uma assinatura que tiver aceitado o novo modelo de preços de abril de 2018, o único tipo de preço válido do Log Analytics **PerGB2018**.
>

>[!NOTE]
>Antes de usar esse modelo, examine [detalhes adicionais](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) para entender totalmente as opções de configuração do espaço de trabalho, como modo de controle de acesso, tipo de preço, retenção e nível de reserva de capacidade. Se você for novo no Azure Monitor logs e ainda não tiver implantado um espaço de trabalho, examine as diretrizes de [design do espaço de trabalho](../azure-monitor/platform/design-logs-deployment.md) para saber mais sobre o controle de acesso e uma compreensão das estratégias de implementação de design recomendadas para sua organização.

## <a name="deploy-template"></a>Implantar modelo

1. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "pricingTier": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "australiacentral",
                "australiaeast",
                "australiasoutheast",
                "brazilsouth",
                "canadacentral",
                "centralindia",
                "centralus",
                "eastasia",
                "eastus",
                "eastus2",
                "francecentral",
                "japaneast",
                "koreacentral",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus",
                "westus2"
            ],
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
        }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": { 
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[resourceGroup().location]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
      ]
    }
    ```

2. Edite o modelo para atender às suas necessidades.

3. Salve esse arquivo como deployUMSolutiontemplate. JSON em uma pasta local.

4. Você está pronto para implantar o modelo. Você pode usar o PowerShell ou o CLI do Azure. Quando for solicitado um nome de espaço de trabalho e de conta de automação, forneça um nome que seja globalmente exclusivo em todas as assinaturas do Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **CLI do Azure**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    A implantação pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem semelhante que inclui o resultado:

    ![Resultados de exemplo, quando a implantação for concluída](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Agora que você tem a solução Gerenciamento de Atualizações implantada, você pode habilitar VMs para gerenciamento, examinar avaliações de atualização e implantar atualizações para colocá-las em conformidade.

- Da sua [conta de automação do Azure](automation-onboard-solutions-from-automation-account.md) para um ou mais computadores do Azure e manualmente para computadores não Azure.

- Para uma única VM do Azure da página da máquina virtual no portal do Azure. Esse cenário está disponível para VMs [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) e [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) .

- Para [várias VMs do Azure](manage-update-multi.md) , selecione-as na página **máquinas virtuais** no portal do Azure. 