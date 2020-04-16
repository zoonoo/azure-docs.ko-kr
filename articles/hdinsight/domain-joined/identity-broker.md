---
title: 자격 증명 관리를 위해 ID 브로커(미리 보기) 사용 - Azure HDInsight
description: 도메인 가입 아파치 하두롭 클러스터에 대한 인증을 간소화하려면 HDInsight ID 브로커에 대해 알아보십시오.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 508eac08284f91821223a78cafdfee7b4c9c540b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410876"
---
# <a name="use-id-broker-preview-for-credential-management"></a>자격 증명 관리를 위해 ID 브로커(미리 보기) 사용

이 문서에서는 Azure HDInsight에서 ID 브로커 기능을 설정하고 사용하는 방법에 대해 설명합니다. 이 기능을 사용하여 Azure 다단계 인증을 통해 아파치 암바리에 로그인하고 Azure Active Directory 도메인 서비스(Azure AD DS)에서 암호 해시없이 필요한 Kerberos 티켓을 얻을 수 있습니다.

## <a name="overview"></a>개요

ID 브로커는 다음 시나리오에서 복잡한 인증 설정을 단순화합니다.

* 조직은 클라우드 리소스에 액세스하기 위해 사용자를 인증하기 위해 페더레이션을 사용합니다. 이전에는 HDInsight 엔터프라이즈 보안 패키지(ESP) 클러스터를 사용하려면 온-프레미스 환경에서 Azure Active Directory로 암호 해시 동기화를 사용하도록 설정해야 했습니다. 일부 조직에서는 이 요구 사항이 어렵거나 바람직하지 않을 수 있습니다.

* 서로 다른 인증 메커니즘을 사용하는 기술을 사용하는 솔루션을 빌드하고 있습니다. 예를 들어, 아파치 하두프와 아파치 레인저는 Kerberos에 의존하는 반면, Azure 데이터 레이크 스토리지는 OAuth에 의존합니다.

ID 브로커는 통합 인증 인프라를 제공하고 Azure AD DS에 암호 해시를 동기화하는 요구 사항을 제거합니다. ID 브로커는 클러스터 게이트웨이 노드와 함께 Windows Server VM(ID 브로커 노드)에서 실행되는 구성 요소로 구성됩니다. 

다음 다이어그램은 ID 브로커를 사용하도록 설정한 후 페더레이션된 사용자를 포함한 모든 사용자에 대한 인증 흐름을 보여 주며 있습니다.

![ID 브로커를 위한 인증 흐름](./media/identity-broker/identity-broker-architecture.png)

ID 브로커를 사용하면 암호를 제공하지 않고 다단계 인증을 사용하여 ESP 클러스터에 로그인할 수 있습니다. Azure 포털과 같은 다른 Azure 서비스에 이미 로그인한 경우 단일 사인온(SSO) 환경을 사용하여 HDInsight 클러스터에 로그인할 수 있습니다.

## <a name="enable-hdinsight-id-broker"></a>HDInsight ID 브로커 사용

ID 브로커를 사용하도록 설정한 ESP 클러스터를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. ESP 클러스터의 기본 생성 단계를 따릅니다. 자세한 내용은 [ESP를 사용하여 HDInsight 클러스터 만들기를](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)참조하십시오.
1. **HDInsight ID 브로커 활성화를**선택합니다.

ID 브로커 기능은 클러스터에 VM을 하나 더 추가합니다. 이 VM은 ID 브로커 노드이며 인증을 지원하는 서버 구성 요소를 포함합니다. ID 브로커 노드는 Azure AD DS 도메인에 조인된 도메인입니다.

![ID 브로커를 활성화하는 옵션](./media/identity-broker/identity-broker-enable.png)

## <a name="tool-integration"></a>공구 통합

HDInsight [IntelliJ 플러그인은](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) OAuth를 지원하도록 업데이트됩니다. 이 플러그인을 사용하여 클러스터에 연결하고 작업을 제출할 수 있습니다.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Azure AD DS에서 암호 해시 없이 SSH 액세스

ID 브로커를 사용하도록 설정한 후에도 도메인 계정이 있는 SSH 시나리오에 대해 Azure AD DS에 저장된 암호 해시가 계속 필요합니다. 도메인에 가입한 VM에 SSH를 입력하거나 `kinit` 명령을 실행하려면 암호를 제공해야 합니다. 

SSH 인증을 사용하려면 Azure AD DS에서 해시를 사용할 수 있어야 합니다. 관리 시나리오에만 SSH를 사용하려는 경우 하나의 클라우드 전용 계정을 만들고 클러스터에 SSH에 사용할 수 있습니다. 다른 사용자는 Azure AD DS에서 사용할 수 있는 암호 해시없이 Ambari 또는 HDInsight 도구(예: IntelliJ 플러그인)를 계속 사용할 수 있습니다.

## <a name="clinets-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>ID 브로커 설정으로 HDInsight 게이트웨이에 연결하기 위해 OAuth를 사용하는 클라인트

ID 브로커 설정에서 게이트웨이에 연결하는 사용자 지정 앱 및 클라이언트를 업데이트하여 필요한 OAuth 토큰을 먼저 획득할 수 있습니다. 이 [문서의](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) 단계를 수행하여 다음 정보를 사용하여 토큰을 획득할 수 있습니다.

*   OAuth 자원 uri:https://hib.azurehdinsight.net 
* AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   권한: (이름: Cluster.ReadWrite, id:8f89faa0-ffef-4007-974d-4989b39ad77d)

## <a name="next-steps"></a>다음 단계

* [Azure Active Directory 도메인 서비스를 사용하여 엔터프라이즈 보안 패키지로 HDInsight 클러스터 구성](apache-domain-joined-configure-using-azure-adds.md)
* [Azure Active Directory 사용자를 HDInsight 클러스터와 동기화](../hdinsight-sync-aad-users-to-cluster.md)
* [클러스터 성능 모니터링](../hdinsight-key-scenarios-to-monitor.md)
