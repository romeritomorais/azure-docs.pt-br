---
title: Controles de segurança para Azure Cosmos DB
description: Obtenha uma lista de verificação de controles de segurança, como rede, monitoramento, identidade e proteção de dados para avaliar Azure Cosmos DB
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.openlocfilehash: d06afb3670b1c3af3f1acf0bd690c1e3e266a11c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771799"
---
# <a name="security-controls-for-azure-cosmos-db"></a>Controles de segurança para Azure Cosmos DB

Este artigo documenta os controles de segurança internos do Azure Cosmos DB.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim |  |
| Suporte à injeção de VNet| Sim | Com o ponto de extremidade do serviço VNet, você pode configurar uma conta de Azure Cosmos DB para permitir o acesso somente de uma sub-rede específica de uma rede virtual (VNet). Você também pode combinar o acesso VNet com as regras de firewall. Para saber mais, confira [acessar Azure Cosmos DB de redes virtuais](VNet-service-endpoint.md). |
| Isolamento de rede e suporte a firewall| Sim | Com o suporte a firewall, você pode configurar sua conta do Azure Cosmos para permitir o acesso somente de um conjunto aprovado de endereços IP, um intervalo de endereços IP e/ou serviços de nuvem. Para saber mais, confira [Configurar o firewall de IP no Azure Cosmos DB](how-to-configure-firewall.md).|
| Suporte a túnel forçado| Sim | Pode ser configurado no lado do cliente na VNet em que as máquinas virtuais estão localizadas.   |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Todas as solicitações enviadas para Azure Cosmos DB são registradas. Há suporte para o [monitoramento do Azure](../azure-monitor/overview.md), as métricas do Azure, o log de auditoria do Azure.  Você pode registrar informações correspondentes às solicitações de plano de dados, estatísticas de tempo de execução de consulta, texto de consulta, solicitações do MongoDB. Você também pode configurar alertas. |
| Registro e auditoria do plano de gerenciamento e controle| Sim | Log de atividades do Azure para operações de nível de conta, como firewalls, VNets, acesso de chaves e IAM. |
| Log e auditoria do plano de dados | Sim | Diagnóstico monitoramento de log para operações em nível de contêiner, como criar contêiner, provisionar taxa de transferência, políticas de indexação e operações CRUD em documentos. |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/não | Observações|
|---|---|--|
| Autenticação| Sim | Sim, no nível da conta do banco de dados; no nível do plano de dados, Cosmos DB usa tokens de recurso e acesso à chave. |
| Autorização| Sim | Com suporte na conta do Azure cosmos com chaves mestras (primária e secundária) e tokens de recurso. Você pode obter acesso de leitura/gravação ou somente leitura aos dados com chaves mestras. Tokens de recurso permitem acesso de tempo limitado a recursos como documentos e contêineres. |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/não | Observações |
|---|---|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | Todos os bancos de dados e backups do Azure cosmos são criptografados por padrão; consulte [criptografia de dados em Azure Cosmos DB](database-encryption-at-rest.md). |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim | Consulte [Configurar chaves gerenciadas pelo cliente para sua conta de Azure Cosmos DB](how-to-setup-cmk.md)  |
| Criptografia em nível de coluna (serviços de dados do Azure)| Sim | Somente na API de tabelas Premium. Nem todas as APIs dão suporte a esse recurso. Consulte [introdução ao Azure Cosmos DB: API de tabela](table-introduction.md). |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | Todos os dados de Azure Cosmos DB são criptografados em trânsito. |
| Chamadas criptografadas à API| Sim | Todas as conexões com Azure Cosmos DB dão suporte a HTTPS. Azure Cosmos DB também dá suporte a TLS 1,2.<br>É possível impor uma versão mínima do TLS do lado do servidor. Para fazer isso, entre em contato com [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com). |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Não  | | 

## <a name="additional-security-controls-for-cosmos-db"></a>Controles de segurança adicionais para Cosmos DB

| Controle de segurança | Sim/não | Observações|
|---|---|--|
| CORS (compartilhamento de recursos entre origens) | Sim | Consulte [Configurar o CORS (compartilhamento de recursos entre origens)](how-to-configure-cross-origin-resource-sharing.md). |

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).