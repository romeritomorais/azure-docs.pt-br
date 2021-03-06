---
title: Perguntas frequentes sobre Aplicativo Azure gateway
description: Encontre respostas para perguntas frequentes sobre Aplicativo Azure gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/31/2019
ms.author: victorh
ms.openlocfilehash: 27048a8464fc7380a5c11ab6bbb543e35c089774
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919593"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Perguntas frequentes sobre o gateway de aplicativo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Veja a seguir as perguntas comuns sobre Aplicativo Azure gateway.

## <a name="general"></a>Geral

### <a name="what-is-application-gateway"></a>O que é o Gateway de Aplicativo?

Aplicativo Azure Gateway fornece um ADC (controlador de entrega de aplicativos) como um serviço. Ele oferece vários recursos de balanceamento de carga de camada 7 para seus aplicativos. Esse serviço é altamente disponível, escalonável e totalmente gerenciado pelo Azure.

### <a name="what-features-does-application-gateway-support"></a>Quais recursos o Gateway de Aplicativo suporta?

O gateway de aplicativo dá suporte ao dimensionamento automático, descarregamento de SSL e SSL de ponta a ponta, um firewall de aplicativo Web (WAF), afinidade de sessão baseada em cookie, roteamento baseado em caminho de URL, hospedagem multissite e outros recursos. Para obter uma lista completa dos recursos com suporte, confira [Introdução ao Gateway de Aplicativo](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Como o gateway de aplicativo e Azure Load Balancer diferem?

O gateway de aplicativo é um balanceador de carga de camada 7, o que significa que ele funciona apenas com o tráfego da Web (HTTP, HTTPS, WebSocket e HTTP/2). Ele dá suporte a recursos como terminação de SSL, afinidade de sessão baseada em cookie e round robin para tráfego de balanceamento de carga. Load Balancer balancear a carga do tráfego na camada 4 (TCP ou UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Quais protocolos o Gateway de Aplicativo suporta?

O Gateway de Aplicativo fornece suporte HTTP, HTTPS, HTTP/2 e WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Como o Gateway de aplicativo dá suporte a HTTP/2?

Consulte [suporte a http/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Quais recursos têm suporte como parte de um pool de back-end?

Consulte [recursos de back-end com suporte](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>Em quais regiões o gateway de aplicativo está disponível?

O Gateway de Aplicativo está disponível em todas as regiões do Azure global. Ele também está disponível no [Azure China 21vianet](https://www.azure.cn/) e no [Azure governamental](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Essa implantação é dedicada à minha assinatura ou é compartilhada entre clientes?

O Gateway de Aplicativo é uma implementação dedicada em sua rede virtual.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>O gateway de aplicativo dá suporte ao redirecionamento de HTTP para HTTPS?

Há suporte para redirecionamento. Consulte [visão geral do redirecionamento do gateway de aplicativo](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>Em que ordem os ouvintes são processados?

Consulte a [ordem de processamento de ouvinte](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Onde encontro o IP e o DNS do gateway de aplicativo?

Se você estiver usando um endereço IP público como um ponto de extremidade, encontrará as informações de IP e DNS no recurso de endereço IP público. Ou encontrá-lo no portal, na página Visão geral do gateway de aplicativo. Se você estiver usando endereços IP internos, localize as informações na página Visão geral.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Quais são as configurações de tempo limite de Keep-Alive e tempo limite de ociosidade de TCP?

O *tempo limite Keep-Alive* controla por quanto tempo o gateway de aplicativo aguardará que um cliente envie outra solicitação HTTP em uma conexão persistente antes de reusá-lo ou fechá-lo. O *tempo limite de ociosidade de TCP* controla por quanto tempo uma conexão TCP é mantida aberta no caso de nenhuma atividade. 

O *tempo limite de Keep Alive* no SKU do gateway de aplicativo v1 é de 120 segundos e na SKU V2 é de 75 segundos. O *tempo limite de ociosidade de TCP* é um padrão de 4 minutos no IP virtual de front-end (VIP) do SKU v1 e v2 do gateway de aplicativo. 

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>O nome do IP ou DNS é alterado durante o tempo de vida do gateway de aplicativo?

No SKU do gateway de aplicativo v1, o VIP pode ser alterado se você parar e iniciar o gateway de aplicativo. Mas o nome DNS associado ao gateway de aplicativo não é alterado durante o tempo de vida do gateway. Como o nome DNS não é alterado, você deve usar um alias CNAME e apontar para o endereço DNS do gateway de aplicativo. No SKU do gateway de aplicativo v2, você pode definir o endereço IP como estático, portanto, o nome de IP e DNS não será alterado durante o tempo de vida do gateway de aplicativo. 

### <a name="does-application-gateway-support-static-ip"></a>O Gateway de Aplicativo suporta IP estático?

Sim, o SKU do gateway de aplicativo v2 dá suporte a endereços IP públicos estáticos. O v1 SKU suporta IPs internos estáticos.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>O Gateway de Aplicativo suporta vários IPs públicos no gateway?

Um gateway de aplicativo dá suporte a apenas um endereço IP público.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Quão grande devo criar minha sub-rede para o Application Gateway?

Consulte [considerações de tamanho de sub-rede do gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Posso implantar mais de um recurso de Gateway de Aplicativo em uma única sub-rede?

Sim. Além de várias instâncias de uma determinada implantação do gateway de aplicativo, você pode provisionar outro recurso de gateway de aplicativo exclusivo para uma sub-rede existente que contenha um recurso de gateway de aplicativo diferente.

Uma única sub-rede não pode dar suporte ao Standard_v2 e ao gateway de aplicativo Standard juntos.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>O Gateway de aplicativo dá suporte a cabeçalhos x-forwarded-for?

Sim. Confira [modificações em uma solicitação](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Quanto tempo leva para implantar um gateway de aplicativo? Meu gateway de aplicativo funcionará enquanto ele estiver sendo atualizado?

As novas implantações de SKU do Application Gateway v1 podem levar até 20 minutos para provisionar. As alterações no tamanho da instância ou na contagem não causam interrupções e o gateway permanece ativo durante esse tempo.

A maioria das implantações que usam a SKU v2 leva cerca de 6 minutos para ser provisionada. No entanto, pode levar mais tempo dependendo do tipo de implantação. Por exemplo, as implantações em vários Zonas de Disponibilidade com muitas instâncias podem levar mais de 6 minutos. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Posso usar o Exchange Server como um back-end com o gateway de aplicativo?

Não. O gateway de aplicativo não dá suporte a protocolos de email como SMTP, IMAP e POP3. 

## <a name="performance"></a>Desempenho

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Como o Application Gateway suporta alta disponibilidade e escalabilidade?

A SKU do gateway de aplicativo v1 dá suporte a cenários de alta disponibilidade quando você implantou duas ou mais instâncias. O Azure distribui essas instâncias entre domínios de atualização e de falha para garantir que as instâncias nem todas falhem ao mesmo tempo. O v1 SKU suporta escalabilidade adicionando várias instâncias do mesmo gateway para compartilhar a carga.

A SKU v2 garante automaticamente que novas instâncias sejam distribuídas entre domínios de falha e domínios de atualização. Se você escolher a redundância de zona, as instâncias mais recentes também serão distribuídas entre zonas de disponibilidade para oferecer resiliência de falha zonal.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Como fazer obter um cenário de recuperação de desastres entre data centers usando o gateway de aplicativo?

Use o Gerenciador de tráfego para distribuir o tráfego entre vários gateways de aplicativo em data centers diferentes.

### <a name="does-application-gateway-support-autoscaling"></a>O gateway de aplicativo dá suporte ao dimensionamento automático?

Sim, o SKU Application Gateway v2 suporta escalonamento automático. Para obter mais informações, consulte [dimensionamento automático e gateway de aplicativo com redundância de zona](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>O dimensionamento manual ou automático aumenta ou reduz verticalmente a causa do tempo de inatividade?

Não. As instâncias são distribuídas entre domínios de atualização e domínios de falha.

### <a name="does-application-gateway-support-connection-draining"></a>O Gateway de Aplicativo suporta a drenagem de conexão?

Sim. Você pode configurar o esgotamento de conexão para alterar os membros em um pool de back-end sem interrupções. Para obter mais informações, consulte a [seção descarga de conexão do gateway de aplicativo](overview.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Posso alterar o tamanho da instância de médio para grande sem interrupção?

Sim.

## <a name="configuration"></a>Configuração

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>O Application Gateway está sempre implantado em uma rede virtual?

Sim. O gateway de aplicativo sempre é implantado em uma sub-rede de rede virtual. Essa sub-rede pode conter somente gateways de aplicativo. Para obter mais informações, consulte [requisitos de rede virtual e de sub-rede](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>O gateway de aplicativo pode se comunicar com instâncias fora de sua rede virtual ou fora de sua assinatura?

Contanto que você tenha conectividade de IP, o gateway de aplicativo pode se comunicar com instâncias fora da rede virtual em que ela está. O gateway de aplicativo também pode se comunicar com instâncias fora da assinatura em que ela está. Se você planeja usar IPs internos como membros do pool de back-end, use o [emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md) ou o [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Posso implantar coisa na sub-rede do gateway de aplicativo?

Não. Mas você pode implantar outros gateways de aplicativo na sub-rede.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Os grupos de segurança de rede têm suporte na sub-rede do gateway de aplicativo?

Consulte [grupos de segurança de rede na sub-rede do gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>A sub-rede do gateway de aplicativo dá suporte a rotas definidas pelo usuário?

Consulte [rotas definidas pelo usuário com suporte na sub-rede do gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quais são os limites no Gateway de Aplicativo? Posso aumentar esses limites?

Consulte [limites do gateway de aplicativo](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Posso usar simultaneamente o gateway de aplicativo para tráfego interno e externo?

Sim. O gateway de aplicativo dá suporte a um IP interno e a um IP externo por gateway de aplicativo.

### <a name="does-application-gateway-support-virtual-network-peering"></a>O gateway de aplicativo dá suporte ao emparelhamento de rede virtual?

Sim. O emparelhamento de rede virtual ajuda a balancear a carga de tráfego em outras redes virtuais.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Posso falar com servidores locais quando eles estão conectados por túneis de ExpressRoute ou VPN?

Sim, contanto que o tráfego seja permitido.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Um pool de back-end pode atender a vários aplicativos em portas diferentes?

Há suporte para a arquitetura de microserviço. Para investigar em portas diferentes, você precisa definir várias configurações de HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>As investigações personalizadas dão suporte a curingas ou Regex nos dados de resposta?

Não. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Como as regras de roteamento são processadas no gateway de aplicativo?

Consulte [ordem de regras de processamento](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Para investigações personalizadas, o que o campo Host significa?

O campo host especifica o nome para o qual enviar a investigação quando você configurou multissite no gateway de aplicativo. Caso contrário, use ' 127.0.0.1 '. Esse valor é diferente do nome de host da máquina virtual. Seu formato é \<protocolo\>://\<\>do host:\<porta\>\<caminho\>.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Posso permitir o acesso do gateway de aplicativo a apenas alguns endereços IP de origem?

Sim. Consulte [restringir o acesso a IPS de origem específicos](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Posso usar a mesma porta para ouvintes públicos e voltados para o público?

Não.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Há alguma orientação disponível para migrar da SKU v1 para a SKU v2?

Sim. Para obter detalhes, consulte [migrar aplicativo Azure gateway e firewall do aplicativo Web da v1 para a v2](migrate-v1-v2.md).

### <a name="does-application-gateway-support-ipv6"></a>O gateway de aplicativo dá suporte a IPv6?

O gateway de aplicativo v2 não dá suporte a IPv6 no momento. Ele pode operar em uma VNet de pilha dupla usando somente IPv4, mas a sub-rede de gateway deve ser somente IPv4. O gateway de aplicativo v1 não dá suporte a pilha dupla VNets. 

## <a name="configuration---ssl"></a>Configuração-SSL

### <a name="what-certificates-does-application-gateway-support"></a>A quais certificados o gateway de aplicativo dá suporte?

O gateway de aplicativo dá suporte a certificados autoassinados, certificados de AC (autoridade de certificação), certificados EV (validação estendida) e certificados curinga.

### <a name="what-cipher-suites-does-application-gateway-support"></a>A quais conjuntos de codificação o gateway de aplicativo dá suporte?

O gateway de aplicativo dá suporte aos seguintes conjuntos de codificação. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Para obter informações sobre como personalizar as opções de SSL, consulte [Configurar versões de política SSL e conjuntos de codificação no gateway de aplicativo](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>O gateway de aplicativo dá suporte à recriptografia de tráfego para o back-end?

Sim. O gateway de aplicativo dá suporte ao descarregamento de SSL e ao SSL de ponta a ponta, que criptografa novamente o tráfego para o back-end.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Posso configurar a política SSL para controlar as versões do protocolo SSL?

Sim. Você pode configurar o gateway de aplicativo para negar TLS 1.0, TLS 1.1 e TLS 1.2. Por padrão, o SSL 2,0 e o 3,0 já estão desabilitados e não são configuráveis.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Posso configurar conjuntos de criptografia e ordem de política?

Sim. No gateway de aplicativo, você pode [configurar conjuntos de codificação](application-gateway-ssl-policy-overview.md). Para definir uma política personalizada, habilite pelo menos um dos seguintes conjuntos de codificação. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

O gateway de aplicativo usa SHA256 para o gerenciamento de back-end.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>A quantos certificados SSL há suporte para o gateway de aplicativo?

O gateway de aplicativo dá suporte a até 100 certificados SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>A quantos certificados de autenticação para recriptografia de back-end o gateway de aplicativo dá suporte?

O gateway de aplicativo dá suporte a até 100 certificados de autenticação.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>O gateway de aplicativo integra-se nativamente com o Azure Key Vault?

Sim, o SKU do gateway de aplicativo v2 dá suporte a Key Vault. Para obter mais informações, consulte [terminação SSL com certificados Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Como fazer configurar ouvintes HTTPS para sites. com e .net? 

Para vários roteamentos baseados em domínio (baseados em host), você pode criar ouvintes multissite, configurar ouvintes que usam HTTPS como o protocolo e associar os ouvintes às regras de roteamento. Para obter mais informações, consulte [hospedando vários sites usando o gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Posso usar caracteres especiais em minha senha de arquivo. pfx?

Não, use apenas caracteres alfanuméricos em sua senha de arquivo. pfx.

## <a name="configuration---web-application-firewall-waf"></a>Configuração-Firewall do aplicativo Web (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>O SKU do WAF oferece todos os recursos disponíveis no SKU Standard?

Sim. O WAF dá suporte a todos os recursos no SKU Standard.

### <a name="how-do-i-monitor-waf"></a>Como monitorar o WAF?

Monitore o WAF por meio do log de diagnóstico. Para obter mais informações, consulte [log de diagnóstico e métricas para o gateway de aplicativo](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Modo de detecção bloqueia o tráfego?

Não. O modo de detecção registra somente o tráfego que dispara uma regra WAF.

### <a name="can-i-customize-waf-rules"></a>Como posso personalizar regras WAF?

Sim. Para obter mais informações, consulte [Personalizar regras e grupos de regras de WAF](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Quais regras estão disponíveis no momento para WAF?

O WAF atualmente dá suporte a CRS [2.2.9](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229), [3,0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)e [3,1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31). Essas regras fornecem segurança de linha de base contra a maioria das 10 principais vulnerabilidades que abrem o OWASP (projeto de segurança de aplicativos Web) identifica: 

* Proteção contra injeção de SQL
* Proteção de scripts entre sites
* Proteção contra ataques comuns da Web, como injeção de comandos, indesejada de solicitação HTTP, divisão de resposta HTTP e ataque de inclusão de arquivo remoto
* Proteção contra violações de protocolo HTTP
* Proteção contra anomalias de protocolo HTTP, como ausência de host de agente do usuário e de cabeçalhos de aceitação
* Prevenção contra bots, rastreadores e scanners
* Detecção de incorretas configurações de aplicativo comuns (isto é, Apache, IIS e assim por diante)

Para obter mais informações, consulte [OWASP Top-10 vulnerabilidades](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>O WAF dá suporte à proteção contra DDoS?

Sim. Você pode habilitar a proteção contra DDoS na rede virtual em que o gateway de aplicativo é implantado. Essa configuração garante que o serviço de proteção contra DDoS do Azure também proteja o VIP (IP virtual) do gateway de aplicativo.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Há alguma orientação disponível para migrar da SKU v1 para a SKU v2?

Sim. Para obter detalhes, consulte [migrar aplicativo Azure gateway e firewall do aplicativo Web da v1 para a v2](migrate-v1-v2.md).

## <a name="configuration---ingress-controller-for-aks"></a>Configuração-controlador de entrada para AKS

### <a name="what-is-an-ingress-controller"></a>O que é um controlador de entrada?

Kubernetes permite a criação de `deployment` e `service` recurso para expor um grupo de pods internamente no cluster. Para expor o mesmo serviço externamente, um recurso de [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) é definido, o que fornece balanceamento de carga, término de SSL e hospedagem virtual baseada em nome.
Para atender a esse recurso de `Ingress`, é necessário um controlador de entrada que escuta quaisquer alterações em `Ingress` recursos e configure as políticas do balanceador de carga.

O controlador de entrada do gateway de aplicativo permite que [aplicativo Azure gateway](https://azure.microsoft.com/services/application-gateway/) seja usado como a entrada para um [serviço kubernetes do Azure](https://azure.microsoft.com/services/kubernetes-service/) também conhecido como um cluster AKs.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Uma única instância do controlador de entrada pode gerenciar vários gateways de aplicativo?

Atualmente, uma instância do controlador de entrada só pode ser associada a um gateway de aplicativo.

## <a name="diagnostics-and-logging"></a>Diagnóstico e registro em log

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Que tipos de logs o gateway de aplicativo fornece?

O gateway de aplicativo fornece três logs: 

* **ApplicationGatewayAccessLog**: o log de acesso contém cada solicitação enviada ao front-end do gateway de aplicativo. Os dados incluem o IP do chamador, a URL solicitada, a latência de resposta, o código de retorno e os bytes de entrada e saída. O log de acesso é coletado a cada 300 segundos. Ele contém um registro por gateway de aplicativo.
* **ApplicationGatewayPerformanceLog**: o log de desempenho captura informações de desempenho para cada gateway de aplicativo. As informações incluem a taxa de transferência em bytes, total de solicitações atendidas, contagem de solicitações com falha e contagem de instâncias de back-end íntegras e não íntegras.
* **ApplicationGatewayFirewallLog**: para gateways de aplicativo que você configura com o WAF, o log de firewall contém solicitações que são registradas por meio do modo de detecção ou do modo de prevenção.

Para obter mais informações, consulte [integridade de back-end, logs de diagnóstico e métricas para o gateway de aplicativo](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Como sei se meus membros do pool de back-end são saudáveis?

Verifique a integridade usando o cmdlet do PowerShell `Get-AzApplicationGatewayBackendHealth` ou o Portal. Para obter mais informações, consulte [Diagnósticos do Gateway de Aplicativo](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Qual é a política de retenção para os logs de diagnóstico?

Os logs de diagnóstico fluem para a conta de armazenamento do cliente. Os clientes podem definir a política de retenção com base em sua preferência. Os logs de diagnóstico também podem ser enviados para um hub de eventos ou logs de Azure Monitor. Para obter mais informações, consulte [Diagnósticos do Gateway de Aplicativo](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Como posso obter os logs de auditoria para o Gateway de aplicativo?

No portal, na folha do menu de um gateway de aplicativo, selecione **log de atividades** para acessar o log de auditoria. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Configurar alertas com o Gateway de aplicativo?

Sim. No gateway de aplicativo, os alertas são configurados nas métricas. Para obter mais informações, consulte [métricas do gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) e [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Como faço para analisar as estatísticas de tráfego do Application Gateway?

Você pode exibir e analisar os logs de acesso de várias maneiras. Use Azure Monitor logs, Excel, Power BI e assim por diante.

Você também pode usar um modelo do Resource Manager que instala e executa o popular [GoAccess](https://goaccess.io/) log Analyzer para logs de acesso do gateway de aplicativo. O GoAccess fornece estatísticas valiosas de tráfego HTTP, como visitantes exclusivos, arquivos solicitados, hosts, sistemas operacionais, navegadores e códigos de status HTTP. Para obter mais informações, no GitHub, consulte o [arquivo Leiame na pasta de modelos do Resource Manager](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>O que poderia fazer com que a integridade de back-end retorne um status desconhecido?

Normalmente, você vê um status desconhecido quando o acesso ao back-end é bloqueado por um NSG (grupo de segurança de rede), DNS personalizado ou UDR (roteamento definido pelo usuário) na sub-rede do gateway de aplicativo. Para obter mais informações, consulte [integridade de back-end, log de diagnóstico e métricas para o gateway de aplicativo](application-gateway-diagnostics.md).

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Há algum caso em que os logs de fluxo do NSG não mostrarão o tráfego permitido?

Sim. Se sua configuração corresponder ao cenário a seguir, você não verá o tráfego permitido em seus logs de fluxo do NSG:
- Você implantou o gateway de aplicativo v2
- Você tem um NSG na sub-rede do gateway de aplicativo
- Você habilitou os logs de fluxo do NSG no NSG

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Como fazer usar o gateway de aplicativo V2 com apenas endereço IP de front-end privado?

O gateway de aplicativo v2 atualmente não dá suporte apenas ao modo de IP privado. Ele dá suporte às seguintes combinações
* IP privado e IP público
* Somente IP público

Mas se você quiser usar o gateway de aplicativo v2 somente com o IP privado, você pode seguir o processo abaixo:
1. Criar um gateway de aplicativo com o endereço IP de front-end público e privado
2. Não crie nenhum ouvinte para o endereço IP de front-end público. O gateway de aplicativo não escutará nenhum tráfego no endereço IP público se nenhum ouvinte for criado para ele.
3. Crie e anexe um [grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview) para a sub-rede do gateway de aplicativo com a seguinte configuração na ordem de prioridade:
    
    a. Permita o tráfego da origem como a marca de serviço do **gatewaymanager** e o destino como **qualquer** porta de destino e como **65200-65535**. Esse intervalo de porta é necessário para a comunicação da infraestrutura do Azure. Essas portas são protegidas (bloqueadas) por autenticação de certificado. Entidades externas, incluindo os administradores de usuário do gateway, não podem iniciar alterações nesses pontos de extremidade sem os certificados apropriados em vigor
    
    b. Permitir o tráfego da origem como a marca de serviço **AzureLoadBalancer** e a porta de destino e destino como **qualquer**
    
    c. Negue todo o tráfego de entrada da origem como marca de serviço de **Internet** e porta de destino e destino como **qualquer**. Dê a essa regra a *prioridade mínima* nas regras de entrada
    
    d. Mantenha as regras padrão como permitir a entrada de VirtualNetwork para que o acesso no endereço IP privado não seja bloqueado
    
    e. A conectividade de internet de saída não pode ser bloqueada. Caso contrário, você enfrentará problemas de registro em log, métricas, etc.

Exemplo de configuração de NSG para acesso somente IP privado: ![a configuração de NSG do gateway de aplicativo v2 para acesso de IP privado somente](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>O cookie de afinidade do gateway de aplicativo dá suporte ao atributo SameSite?
Sim, a atualização do [Chromium Browser](https://www.chromium.org/Home) [V80](https://chromiumdash.appspot.com/schedule) introduziu uma exigência em cookies http sem que o atributo SameSite seja tratado como SameSite = LAX. Isso significa que o cookie de afinidade do gateway de aplicativo não será enviado pelo navegador em um contexto de terceiro Pary. Para dar suporte a esse cenário, o gateway de aplicativo injeta outro cookie chamado *ApplicationGatewayAffinityCORS* além do cookie *ApplicationGatewayAffinity* existente.  Esses cookies são semelhantes, mas o cookie *ApplicationGatewayAffinityCORS* tem mais dois atributos adicionados a ele: *SameSite = None; Proteger*. Esses atributos mantêm sessões adesivas mesmo para solicitações entre origens. Consulte a [seção afinidade baseada em cookie](configuration-overview.md#cookie-based-affinity) para obter mais informações.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Para saber mais sobre o gateway de aplicativo, consulte [o que é aplicativo Azure gateway?](overview.md).
