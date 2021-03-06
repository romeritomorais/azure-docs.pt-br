---
title: Solucionar problemas de LDAP seguro no Azure AD Domain Services | Microsoft Docs
description: Saiba como solucionar problemas de LDAP seguro (LDAPs) para um domínio gerenciado Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 22d1b6e2344256b52cfdbc48720a680a770a4216
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132159"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Solucionar problemas de conectividade LDAP segura para um Azure Active Directory Domain Services domínio gerenciado

Os aplicativos e serviços que usam o protocolo LDAP para se comunicar com Azure Active Directory Domain Services (AD DS do Azure) podem ser [configurados para usar o LDAP seguro](tutorial-configure-ldaps.md). Um certificado apropriado e as portas de rede necessárias devem estar abertos para que o LDAP seguro funcione corretamente.

Este artigo ajuda você a solucionar problemas com acesso LDAP seguro no Azure AD DS.

## <a name="common-connection-issues"></a>Problemas comuns de conexão

Se você tiver problemas para se conectar a um domínio gerenciado do Azure AD DS usando o LDAP seguro, examine as etapas de solução de problemas a seguir. Após cada etapa de solução de problemas, tente se conectar ao domínio gerenciado do Azure AD DS novamente:

* A cadeia de emissor do certificado LDAP seguro deve ser confiável no cliente. Você pode adicionar a AC (autoridade de certificação) raiz ao repositório de certificados raiz confiáveis no cliente para estabelecer a relação de confiança.
    * Certifique-se de [exportar e aplicar o certificado aos computadores cliente][client-cert].
* Verifique se o certificado LDAP seguro para seu domínio gerenciado tem o nome DNS no atributo *assunto* ou *nomes alternativos da entidade* .
    * Examine os [requisitos de certificado LDAP seguro][certs-prereqs] e crie um certificado de substituição, se necessário.
* Verifique se o cliente LDAP, como *LDP. exe* , conecta-se ao ponto de extremidade de LDAP seguro usando um nome DNS, não o endereço IP.
    * O certificado aplicado ao domínio gerenciado AD DS do Azure não inclui os endereços IP do serviço, somente os nomes DNS.
* Verifique o nome DNS que o cliente LDAP se conecta. Ele deve resolver para o endereço IP público para LDAP seguro no domínio gerenciado AD DS do Azure.
    * Se o nome DNS for resolvido para o endereço IP interno, atualize o registro DNS para resolver para o endereço IP externo.
* Para conectividade externa, o grupo de segurança de rede deve incluir uma regra que permita o tráfego para a porta TCP 636 da Internet.
    * Se você puder se conectar ao domínio gerenciado do Azure AD DS usando o LDAP seguro dos recursos conectados diretamente à rede virtual, mas não a conexões externas, certifique-se de [criar uma regra de grupo de segurança de rede para permitir o tráfego LDAP seguro][ldaps-nsg].

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Se você ainda tiver problemas, [abra uma solicitação de suporte do Azure][azure-support] para obter assistência de solução de problemas adicional.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
