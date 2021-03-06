---
title: Controles de Acesso à Rede
description: Visão geral dos controles de acesso à rede para o banco de dados SQL do Azure e data warehouse para gerenciar o acesso e configurar um banco de dados único ou em pool.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 16ba90aab52c00f77af590f854217cd989df53b3
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251899"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Controles de acesso à rede do banco de dados SQL do Azure e do data warehouse

> [!NOTE]
> Este artigo se aplica ao SQL Server do Azure e aos bancos de dados SQL Database e SQL Data Warehouse criados no servidor SQL do Azure. Para simplificar, o banco de dados SQL é usado quando se refere ao Banco de Dados SQL e ao SQL Data Warehouse.

> [!IMPORTANT]
> Este artigo faz *não* se aplica à **Instância Gerenciada do Banco de Dados SQL do Azure**. para obter mais informações sobre a configuração de rede, consulte [conectando-se a um instância gerenciada](sql-database-managed-instance-connect-app.md) .

Quando você cria uma nova SQL Server [do Azure do portal do Azure](sql-database-single-database-get-started.md), o resultado é um ponto de extremidade público no formato *yourservername.Database.Windows.net*. Por design, todo o acesso ao ponto de extremidade público é negado. Você pode usar os seguintes controles de acesso à rede para permitir seletivamente o acesso ao banco de dados SQl por meio do ponto de extremidade público
- Permitir serviços do Azure:-quando definido como ativado, outros recursos dentro do limite do Azure, por exemplo, uma máquina virtual do Azure, podem acessar o banco de dados SQL

- Regras de firewall de IP:-Use esse recurso para permitir explicitamente conexões de um endereço IP específico, por exemplo, de computadores locais.

- Regras de firewall de rede virtual:-Use esse recurso para permitir o tráfego de uma rede virtual específica dentro do limite do Azure

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Permitir serviços do Azure 
Durante a criação de uma nova SQL Server do Azure [do portal do Azure](sql-database-single-database-get-started.md), essa configuração é deixada desmarcada.

 ![Captura de tela da nova criação do servidor][1]

Você também pode alterar essa configuração por meio do painel Firewall depois que a SQL Server do Azure for criada da seguinte maneira.
  
 ![Captura de tela de gerenciar o Firewall do servidor][2]

Quando definido como **no** Azure SQL Server permite a comunicação de todos os recursos dentro do limite do Azure, que pode ou não fazer parte de sua assinatura.

Em muitos casos, a configuração **on** é mais permissiva do que a maioria dos clientes desejam. Eles podem querer definir essa configuração como **off** e substituí-la por regras de firewall IP mais restritivas ou regras de firewall de rede virtual. Isso afeta os seguintes recursos que são executados em VMs no Azure que não fazem parte de sua VNet e, portanto, se conectam ao banco de dados SQL por meio de um endereço IP do Azure.

### <a name="import-export-service"></a>Serviço de Importação/Exportação
O serviço de exportação de importação não funciona **permitir que os serviços do Azure acessem o servidor** definido como desativado. No entanto, você pode contornar o problema [executando manualmente SqlPackage. exe de uma VM do Azure ou executando a exportação](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) diretamente em seu código usando a API DACFx.

### <a name="data-sync"></a>Sincronização de Dados
Para usar o recurso de sincronização de dados com a opção **permitir que os serviços do Azure acessem o servidor** definido como desativado, você precisa criar entradas de regra de firewall individuais para [adicionar endereços IP](sql-database-server-level-firewall-rule.md) da **marca de serviço do SQL** para a região que hospeda o banco de dados de **Hub** .
Adicione essas regras de firewall no nível de servidor aos servidores lógicos que hospedam bancos de dados de **Hub** e **membro** (que podem estar em regiões diferentes)

Use o seguinte script do PowerShell para gerar os endereços IP correspondentes à marca de serviço do SQL para a região oeste dos EUA
```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Get-AzNetworkServiceTag retorna o intervalo global para a marca de serviço do SQL, apesar de especificar o parâmetro de local. Certifique-se de filtrá-lo para a região que hospeda o banco de dados de Hub usado pelo seu grupo de sincronização

Observe que a saída do script do PowerShell está na notação CIDR (roteamento entre domínios sem classificação) e isso precisa ser convertido em um formato de endereço IP inicial e final usando [Get-IPrangeStartEnd. ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) como este
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Execute as seguintes etapas adicionais para converter todos os endereços IP do CIDR para o formato de endereço IP inicial e final.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
Agora você pode adicioná-las como regras de firewall distintas e, em seguida, definir **permitir que os serviços do Azure acessem o servidor** como desativado.


## <a name="ip-firewall-rules"></a>Regras de firewall de IP
O firewall baseado em IP é um recurso do Azure SQL Server que impede todo o acesso ao seu servidor de banco de dados até que você adicione explicitamente os [endereços IP](sql-database-server-level-firewall-rule.md) dos computadores cliente.


## <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual

Além das regras de IP, o firewall de SQL Server do Azure permite que você defina *regras de rede virtual*.  
Para saber mais, consulte [pontos de extremidade de serviço de rede virtual e regras para o banco de dados SQL do Azure](sql-database-vnet-service-endpoint-rule-overview.md) ou assista a este vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Terminologia de rede do Azure  
Esteja atento aos seguintes termos de rede do Azure ao explorar as regras de firewall de rede virtual

**Rede virtual:** Você pode ter redes virtuais associadas à sua assinatura do Azure 

**Sub-rede:** uma rede virtual contém **sub-redes**. Todas as máquinas virtuais do Azure (VMs) que você tem são atribuídas a sub-redes. Uma sub-rede pode conter várias VMs ou outros nós de computadores. Nós de computadores que estão fora da sua rede virtual não podem acessar sua rede virtual, a menos que você configure a segurança para permitir o acesso.

**Ponto de extremidade de serviço de rede virtual:** um [ponto de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes de tipo formais de serviço do Azure. Neste artigo, estamos interessados no nome do tipo de **Microsoft.Sql**, que faz referência ao serviço do Azure chamado banco de dados SQL.

**Regra da rede virtual:** uma regra da rede virtual para o servidor de Banco de dados SQL do Microsoft Azure é uma sub-rede listada na lista de controle de acesso (ACL) do seu servidor de Banco de dados SQL do Microsoft Azure. Para estar na ACL do seu banco de dados SQL, a sub-rede deve conter o nome do tipo **Microsoft.Sql**. Uma regra da rede virtual informa o servidor de Banco de dados SQL do Microsoft Azure para aceitar comunicações de cada nó que está na sub-rede.


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP versus regras de firewall de rede virtual

O Firewall do Azure SQL Server permite que você especifique os intervalos de endereços IP dos quais as comunicações são aceitas no banco de dados SQL. Essa abordagem é adequada para endereços IP estáveis que estão fora da rede privada do Azure. No entanto, as máquinas virtuais (VMs) na rede privada do Azure são configuradas com endereços IP *dinâmicos* . Os endereços IP dinâmicos podem mudar quando sua VM é reiniciada e, por sua vez, invalidar a regra de firewall baseada em IP. Seria ilusório especificar um endereço IP dinâmico em uma regra de firewall, em um ambiente de produção.

Você pode contornar essa limitação obtendo um endereço IP *estático* para sua VM. Para obter detalhes, consulte [configurar endereços IP privados para uma máquina virtual usando o portal do Azure](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). No entanto, a abordagem de IP estático pode se tornar difícil de gerenciar e é cara quando realizada em escala. 

As regras de rede virtual são uma alternativa mais fácil de estabelecer e gerenciar o acesso de uma sub-rede específica que contém suas VMs.

> [!NOTE]
> Ainda não é possível ter o Banco de Dados SQL em uma sub-rede. Se seu servidor do banco de dados SQL do Azure for um nó em uma sub-rede em sua rede virtual, todos os nós dentro da rede virtual podem se comunicar com seu banco de dados SQL. Nesse caso, suas VMs podem se comunicar com o Banco de Dados SQL sem a necessidade de nenhuma regra da rede virtual ou regras de IP.

## <a name="next-steps"></a>Próximas etapas

- Para obter um guia de início rápido sobre como criar uma regra de firewall de IP no nível de servidor, consulte [criar um banco de dados SQL do Azure](sql-database-single-database-get-started.md).

- Para obter um início rápido sobre como criar uma regra de firewall de vnet no nível de servidor, consulte [pontos de extremidade de serviço de rede virtual e regras para o banco de dados SQL do Azure](sql-database-vnet-service-endpoint-rule-overview.md).

- Para obter ajuda com a conexão com um banco de dados SQL do Azure de software livre ou aplicativos de terceiros, consulte [exemplos de código de início rápido do cliente para banco de dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Para obter mais informações sobre as portas adicionais que você pode precisar abrir, consulte a seção **Banco de Dados SQL: fora versus dentro** de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md)

- Para obter uma visão geral da conectividade do banco de dados SQL do Azure, consulte [arquitetura de conectividade do SQL do Azure](sql-database-connectivity-architecture.md)

- Para obter uma visão geral de segurança do Banco de Dados SQL do Azure, consulte [Protegendo seu banco de dados](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png
