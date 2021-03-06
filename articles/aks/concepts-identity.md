---
title: Conceitos – Acesso e identidade em Serviços de Kubernetes do Azure (AKS)
description: Saiba mais sobre acesso e identidade no Serviço de Kubernetes do Azure (AKS) incluindo a integração do Microsoft Azure Active Directory, controle de acesso baseado em função (RBAC) dos Kubernetes e funções e vínculos.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: e4945535417f7d8d33308121267ba97e1f835e13
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596361"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Acesso e opções de identidade para o Serviço de Kubernetes do Azure (AKS)

Há diferentes maneiras de autenticar e proteger clusters de Kubernetes. Usando controles de acesso baseado em função (RBAC), você pode conceder aos usuários ou grupos o acesso a apenas os recursos necessários. Com o Serviço de Kubernetes do Azure (AKS), você pode aprimorar ainda mais a estrutura de segurança e permissões usando o Microsoft Azure Active Directory. Essas abordagens ajudam a proteger suas cargas de trabalho do aplicativo e os dados do cliente.

Este artigo apresenta os principais conceitos que ajudam você a se autenticar e atribuir permissões no AKS:

- [Contas de serviço do Kubernetes](#kubernetes-service-accounts)
- [Integração do Microsoft Azure Active Directory](#azure-active-directory-integration)
- [Controle de acesso baseado em função (RBAC)](#role-based-access-controls-rbac)
- [Funções e ClusterRoles](#roles-and-clusterroles)
- [RoleBindings e ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Contas de serviço do Kubernetes

Um dos tipos de usuário primário no Kubernetes é uma *conta de serviço*. Uma conta de serviço existe e é gerenciada pela API do Kubernetes. As credenciais para contas de serviço são armazenadas como segredos do Kubernetes, que permite que sejam utilizados pelo pods autorizados para se comunicar com o servidor de API. A maioria das solicitações de API fornecem um token de autenticação para uma conta de serviço ou uma conta de usuário normal.

Contas de usuário normal permitem o acesso mais tradicional para administradores humanos ou desenvolvedores, não apenas serviços e processos. O kubernetes em si não fornece uma solução de gerenciamento de identidades onde contas de usuário e senhas regulares são armazenadas. Em vez disso, as soluções de identidade externa podem ser integradas em Kubernetes. Para clusters AKS, essa solução integrada de identidade é o Microsoft Azure Active Directory.

Para obter mais informações sobre as opções de identidade no kubernetes, consulte [autenticação do kubernetes][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Integração do Microsoft Azure Active Directory

A segurança dos clusters AKS pode ser aprimorada com a integração do Microsoft Azure Active Directory (AD). Embasado em décadas de gerenciamento de identidades corporativas, o Azure Active Directory é um diretório multilocatário baseado em nuvem e o serviço de gerenciamento de identidade que combina os principais serviços de diretório, gerenciamento de acesso do aplicativo e proteção de identidade. Com o Microsoft Azure Active Directory, você pode integrar identidades locais em clusters AKS para fornecer uma única fonte para segurança e gerenciamento de conta.

![Integração do Microsoft Azure Active Directory com os clusters AKS](media/concepts-identity/aad-integration.png)

Com os clusters AKS integrados ao Microsoft Azure Active Directory, você pode conceder a usuários ou grupos o acesso a recursos dentro do Kubernetes dentro de um namespace ou através do cluster. Para obter um contexto de configuração `kubectl`, um usuário pode executar o comando [AZ AKs Get-Credentials][az-aks-get-credentials] . Quando um usuário interage com o cluster do AKS com `kubectl`, será solicitado a entrar com suas credenciais do Microsoft Azure Active Directory. Essa abordagem fornece uma única fonte para o gerenciamento de contas de usuário e as credenciais de senha. O usuário só pode acessar os recursos, conforme definido pelo administrador do cluster.

Autenticação do Microsoft Azure Active Directory em clusters AKS usa o OpenID Connect, uma camada de identidade criada sobre o protocolo OAuth 2.0. OAuth 2.0 define os mecanismos para obter e usar tokens de acesso para acessar recursos protegidos, e o OpenID Connect implementa a autenticação como uma extensão para o processo de autorização do OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [documentação do Open ID Connect][openid-connect]. Para verificar os tokens de autenticação obtidos do Microsoft Azure Active Directory por meio do OpenID Connect, clusters AKS usam autenticação de Token de Webhook do Kubernetes. Para obter mais informações, consulte a [documentação de autenticação de token do webhook][webhook-token-docs].

## <a name="role-based-access-controls-rbac"></a>Controle de acesso baseado em função (RBAC)

Para fornecer filtragem granulares das ações que os usuários podem executar, os Kubernetes usam controles de acesso baseado em função (RBAC). Esse mecanismo de controle permite que você atribua usuários ou grupos de usuários, permissão para fazer coisas como criar ou modificar os recursos ou visualizar logs de cargas de trabalho de aplicativo. Essas permissões podem ser um único namespace no escopo ou concedidas em todo o cluster AKS. Com o Kubernetes RBAC, você cria *funções* para definir permissões e, em seguida, atribuir essas funções a usuários com *associações de função*.

Para obter mais informações, consulte [usando a autorização do RBAC][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Controle de acesso baseado em função (RBAC) do Azure
Um mecanismo adicional para controlar o acesso aos recursos são os controle de acesso baseado em função (RBAC) do Azure. A RBAC do Kubernetes foi projetada para trabalhar em recursos dentro do cluster do AKS e a RBAC do Azure foi projetada para trabalhar em recursos dentro de sua assinatura do Azure. Com o RBAC do Azure, você cria um *definição de função* que descreve as permissões a serem aplicadas. Um usuário ou grupo, em seguida, é atribuído a essa definição de função para um determinado *escopo*, que pode ser um recurso individual, um recurso de grupo, ou através da assinatura.

Para obter mais informações, consulte [o que é o RBAC do Azure?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Funções e ClusterRoles

Antes de você atribuir permissões a usuários com RBAC de Kubernetes, você primeiro define essas permissões como uma *Função*. As funções de Kubernetes *concedem* permissões. Não há nenhum conceito de *negar* permissão.

Funções são usadas para conceder permissões em um namespace. Se você precisar conceder permissões em todo o cluster ou a recursos de cluster fora de um namespace específico, você pode usar *ClusterRoles*.

Um ClusterRole funciona da mesma forma para conceder permissões para recursos, mas pode ser aplicado a recursos em todo o cluster, não um namespace específico.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings e ClusterRoleBindings

Depois que as funções são definidas para conceder permissões a recursos, você atribui essas permissões de RBAC Kubernetes com um *RoleBinding*. Se o cluster do AKS é integrado ao Microsoft Azure Active Directory, as associações são como os usuários do Microsoft Azure Active Directory são concedidos permissões para executar ações dentro do cluster.

Associações de função são usadas para atribuir funções para um namespace específico. Essa abordagem permite separar logicamente um único cluster AKS, com os usuários que só odem acessar os recursos do aplicativo em seu namespace atribuído. Se você precisar conceder permissões em todo o cluster ou a recursos de cluster fora de um namespace específico, você pode usar *ClusterRoles*.

Um ClusterRole funciona da mesma forma para conceder permissões para recursos, mas pode ser aplicado a recursos em todo o cluster, não um namespace específico. Essa abordagem permite que você conceda ou aos engenheiros de suporte acesso a todos os recursos no cluster AKS.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o Azure AD e o kubernetes RBAC, consulte [integrar Azure Active Directory com AKs][aks-aad].

Para obter as práticas recomendadas associadas, consulte [práticas recomendadas para autenticação e autorização no AKs][operator-best-practices-identity].

Para obter informações adicionais sobre os principais conceitos do Kubernetes e do AKS, consulte os seguintes artigos:

- [Clusters e cargas de trabalho do kubernetes/AKS][aks-concepts-clusters-workloads]
- [Segurança do kubernetes/AKS][aks-concepts-security]
- [Redes virtuais kubernetes/AKS][aks-concepts-network]
- [Armazenamento kubernetes/AKS][aks-concepts-storage]
- [Escala de kubernetes/AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
