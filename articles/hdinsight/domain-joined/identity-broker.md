---
title: Azure HDInsight ID Broker (계층 2)
description: 도메인에 가입 된 Apache Hadoop 클러스터에 대 한 인증을 간소화 하기 위해 Azure HDInsight ID Broker에 대해 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 9a2bda0a526c307ae17d8415f6f24423ddf51b63
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917769"
---
# <a name="azure-hdinsight-id-broker-hib"></a>Azure HDInsight ID Broker (계층 2)

이 문서에서는 Azure HDInsight ID 브로커 기능을 설정 하 고 사용 하는 방법을 설명 합니다. Azure Active Directory Domain Services (Azure AD DS)에서 레거시 암호 해시가 필요 하지 않고 다단계 인증을 적용 하는 동안이 기능을 사용 하 여 Apache Ambari에 대 한 최신 OAuth 인증을 가져올 수 있습니다.

## <a name="overview"></a>개요

HDInsight ID Broker는 다음과 같은 시나리오에서 복잡 한 인증을 단순화 합니다.

* 조직에서는 페더레이션에 의존 하 여 클라우드 리소스에 액세스 하는 사용자를 인증 합니다. 이전에는 HDInsight Enterprise Security Package 클러스터를 사용 하려면 온-프레미스 환경에서 Azure Active Directory (Azure AD)로 암호 해시 동기화를 사용 하도록 설정 해야 했습니다. 일부 조직에서는이 요구 사항이 어렵거나 바람직하지 않을 수 있습니다.
* 조직에서 Apache Ambari 및 기타 클러스터 리소스에 대 한 웹 기반 또는 HTTP 기반 액세스를 위한 다단계 인증을 적용 하려고 합니다.

HDInsight ID Broker는 Azure AD DS에 대 한 암호 해시를 동기화 할 필요 없이 OAuth (최신)에서 Kerberos (레거시)로의 프로토콜 전환을 가능 하 게 하는 인증 인프라를 제공 합니다. 이 인프라는 HDInsight ID Broker 노드가 활성화 된 Windows Server VM (가상 머신)에서 실행 되는 구성 요소와 함께 클러스터 게이트웨이 노드와 함께 구성 됩니다.

조직의 요구 사항에 따라 가장 적합 한 인증 옵션을 확인 하려면 다음 표를 사용 합니다.

|인증 옵션 |HDInsight 구성 | 고려해 야 할 요소 |
|---|---|---|
| 완전 OAuth | Enterprise Security Package + HDInsight ID Broker | 가장 안전한 옵션입니다. (다단계 인증이 지원 됩니다.) 패스 해시 동기화는 필요 *하지 않습니다* . Azure AD DS에 암호 해시가 없는 온-프레미스 계정에 대 한 ssh/kinit/keytab 액세스는 없습니다. 클라우드 전용 계정은 여전히 ssh/kinit/keytab 수 있습니다. OAuth를 통한 Ambari에 대 한 웹 기반 액세스 OAuth를 지원 하려면 레거시 앱 (예: JDBC/ODBC)을 업데이트 해야 합니다.|
| OAuth + 기본 인증 | Enterprise Security Package + HDInsight ID Broker | OAuth를 통한 Ambari에 대 한 웹 기반 액세스 레거시 앱은 계속 기본 인증을 사용 합니다. 기본 인증 액세스를 위해서는 다단계 인증을 사용 하지 않도록 설정 해야 합니다. 패스 해시 동기화는 필요 *하지 않습니다* . Azure AD DS에 암호 해시가 없는 온-프레미스 계정에 대 한 ssh/kinit/keytab 액세스는 없습니다. 클라우드 전용 계정은 여전히 ssh/kinit 할 수 있습니다. |
| 완전 한 기본 인증 | Enterprise Security Package | 온-프레미스 설정과 가장 유사 합니다. Azure AD DS에 대 한 암호 해시 동기화가 필요 합니다. 온-프레미스 계정은 ssh/kinit 또는 keytab를 사용할 수 있습니다. 백업 저장소를 Azure Data Lake Storage Gen2 하는 경우 다단계 인증을 사용 하지 않도록 설정 해야 합니다. |

다음 다이어그램은 HDInsight ID Broker를 사용 하도록 설정한 후 페더레이션된 사용자를 포함 하 여 모든 사용자에 대 한 최신 OAuth 기반 인증 흐름을 보여 줍니다.

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="HDInsight ID Broker의 인증 흐름을 보여 주는 다이어그램입니다.":::

이 다이어그램에서 클라이언트 (브라우저 또는 앱)는 먼저 OAuth 토큰을 획득 해야 합니다. 그런 다음 HTTP 요청에서 게이트웨이에 토큰을 제공 합니다. Azure Portal와 같은 다른 Azure 서비스에 이미 로그인 한 경우 Single Sign-On 환경을 사용 하 여 HDInsight 클러스터에 로그인 할 수 있습니다.

기본 인증 (즉, 사용자 이름 및 암호)만 지 원하는 많은 레거시 응용 프로그램이 있을 수 있습니다. 이러한 시나리오의 경우 여전히 HTTP 기본 인증을 사용 하 여 클러스터 게이트웨이에 연결할 수 있습니다. 이 설정에서는 게이트웨이 노드에서 직접 시야를 볼 수 있도록 게이트웨이 노드에서 Active Directory Federation Services (AD FS) 끝점으로의 네트워크 연결을 확인 해야 합니다.

다음 다이어그램에서는 페더레이션된 사용자에 대 한 기본 인증 흐름을 보여 줍니다. 먼저 게이트웨이는 [Ropc flow](../../active-directory/develop/v2-oauth-ropc.md)를 사용 하 여 인증을 완료 하려고 시도 합니다. Azure AD에 동기화 된 암호 해시가 없는 경우 AD FS 끝점을 검색 하 고 AD FS 끝점에 액세스 하 여 인증을 완료 하는 것으로 대체 됩니다.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="기본 인증을 사용 하 여 아키텍처를 보여 주는 다이어그램입니다.":::


## <a name="enable-hdinsight-id-broker"></a>HDInsight ID Broker 사용

HDInsight ID Broker를 사용 하 여 Enterprise Security Package 클러스터를 만들려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Enterprise Security Package 클러스터에 대 한 기본 만들기 단계를 수행 합니다. 자세한 내용은 [Enterprise Security Package를 사용 하 여 HDInsight 클러스터 만들기](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)를 참조 하세요.
1. **HDINSIGHT ID Broker 사용** 을 선택 합니다.

HDInsight ID Broker 기능은 클러스터에 추가 VM 하나를 추가 합니다. 이 VM은 HDInsight ID 브로커 노드 이며 인증을 지 원하는 서버 구성 요소를 포함 합니다. HDInsight ID 브로커 노드는 Azure AD DS 도메인에 가입 된 도메인입니다.

![HDInsight ID Broker를 사용 하도록 설정 하는 옵션을 보여 주는 다이어그램입니다.](./media/identity-broker/identity-broker-enable.png)

### <a name="use-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 사용

다음 특성을 사용 하 여 라는 새 역할을 `idbrokernode` 템플릿의 계산 프로필에 추가 하면 HDINSIGHT ID Broker 노드가 활성화 된 상태로 클러스터가 생성 됩니다.

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 2,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

ARM 템플릿의 전체 샘플을 보려면 [여기](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)에 게시 된 템플릿을 참조 하세요.


## <a name="tool-integration"></a>도구 통합

HDInsight 도구는 기본적으로 OAuth를 지원 하도록 업데이트 됩니다. 이러한 도구를 사용 하 여 클러스터에 대 한 최신 OAuth 기반 액세스를 사용할 수 있습니다. HDInsight [IntelliJ 플러그](../spark/apache-spark-intellij-tool-plugin.md#integrate-with-hdinsight-identity-broker-hib) 인은 Scala와 같은 Java 기반 응용 프로그램에 사용할 수 있습니다. [Visual Studio Code에 대 한 Spark 및 Hive 도구](../hdinsight-for-vscode.md) 를 사용 하 여 PySpark 및 hive 작업을 수행할 수 있습니다. 이 도구는 batch 및 대화형 작업을 모두 지원 합니다.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Azure AD DS에서 암호 해시가 없는 SSH 액세스

|SSH 옵션 |고려해 야 할 요소 |
|---|---|
| 로컬 VM 계정 (예: sshuser) | 클러스터를 만들 때이 계정을 제공 했습니다. 이 계정에 대 한 Kerberos 인증이 없습니다. |
| 클라우드 전용 계정 (예: alice@contoso.onmicrosoft.com ) | 암호 해시는 Azure AD DS에서 사용할 수 있습니다. Kerberos 인증은 SSH Kerberos를 통해 가능 합니다. |
| 온-프레미스 계정 (예: alice@contoso.com ) | SSH Kerberos 인증은 Azure AD DS에서 암호 해시를 사용할 수 있는 경우에만 가능 합니다. 그렇지 않으면이 사용자는 클러스터에 대해 SSH를 사용할 수 없습니다. |

도메인에 가입 된 VM으로 SSH 하거나 명령을 실행 하려면 `kinit` 암호를 제공 해야 합니다. SSH Kerberos 인증을 사용 하려면 Azure AD DS에서 해시를 사용할 수 있어야 합니다. 관리 시나리오에만 SSH를 사용 하려는 경우 클라우드 전용 계정을 하나 만들어 클러스터에 대 한 SSH에 사용할 수 있습니다. 다른 온-프레미스 사용자는 Azure AD DS에서 암호 해시를 사용할 수 없는 경우에도 Ambari 또는 HDInsight 도구나 HTTP 기본 인증을 사용할 수 있습니다.

조직에서 Azure AD DS에 대 한 암호 해시를 동기화 하지 않는 경우 Azure AD에서 클라우드 전용 사용자 하나를 만드는 것이 가장 좋습니다. 그런 다음 클러스터를 만들 때이를 클러스터 관리자로 할당 하 고 관리 목적으로 사용 합니다. 이를 사용 하 여 SSH를 통해 Vm에 대 한 루트 액세스를 가져올 수 있습니다.

인증 문제를 해결 하려면 [이 가이드](./domain-joined-authentication-issues.md)를 참조 하세요.

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>OAuth를 사용 하 여 HDInsight ID Broker를 통해 HDInsight 게이트웨이에 연결 하는 클라이언트

HDInsight ID Broker 설정에서 게이트웨이에 연결 하는 사용자 지정 앱 및 클라이언트를 업데이트 하 여 필요한 OAuth 토큰을 먼저 획득할 수 있습니다. [이 문서의](../../storage/common/storage-auth-aad-app.md) 단계에 따라 다음 정보를 사용 하 여 토큰을 가져옵니다.

*   OAuth 리소스 uri: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   권한: (이름: Cluster. ReadWrite, id: 8f89faa0-ffef-4007-974d-4989b39ad77d)

OAuth 토큰을 획득 한 후에는 클러스터 게이트웨이에 대 한 HTTP 요청의 인증 헤더 (예: https://-int.azurehdinsight.net)에이 토큰을 사용 <clustername> 합니다. Apache Livy API에 대 한 샘플 말아 명령은 다음 예제와 같이 보일 수 있습니다.
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

Beeline 및 Livy를 사용 하는 경우 [여기](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/HIB/HIBSamples) 에 제공 된 샘플 코드를 따라 OAuth를 사용 하도록 클라이언트를 설정 하 고 클러스터에 연결할 수도 있습니다.

## <a name="faq"></a>FAQ
### <a name="what-app-is-created-by-hdinsight-in-aad"></a>AAD에서 HDInsight에 의해 생성 되는 앱은 무엇 인가요?
각 클러스터에 대해 타사 응용 프로그램은 identifierUri (예:)로 클러스터 uri를 사용 하 여 AAD에 등록 됩니다 https://clustername.azurehdinsight.net .

### <a name="why-are-users-prompted-for-consent-before-using-hib-enabled-clusters"></a>사용자가 설정 된 클러스터를 사용 하기 전에 동의를 요청 하는 이유는 무엇 인가요?
AAD에서 사용자를 인증 하거나 데이터에 액세스 하기 전에 모든 타사 응용 프로그램에 대 한 동의가 필요 합니다.

### <a name="can-the-consent-be-approved-programatically"></a>동의를 프로그래밍 방식으로 승인할 수 있습니까?
Microsoft Graph api를 사용 하 여 동의를 자동화할 수 있습니다. 동의를 자동화 하는 시퀀스는 [api 설명서](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant?view=graph-rest-1.0) 를 참조 하세요.

* 앱을 등록 하 고 응용 프로그램에 대 한 권한을 부여 합니다. ReadWrite. 앱에 액세스 하려면 Microsoft Graph
* 클러스터를 만든 후에는 식별자 uri를 기준으로 클러스터 앱을 쿼리 합니다.
* 앱에 대 한 동의 등록

클러스터가 삭제 되 면 HDInsight는 앱을 삭제 하 고 모든 동의를 정리할 필요가 없습니다.

 


## <a name="next-steps"></a>다음 단계

* [Azure Active Directory Domain Services를 사용 하 여 Enterprise Security Package를 사용 하 여 HDInsight 클러스터 구성](apache-domain-joined-configure-using-azure-adds.md)
* [Azure Active Directory 사용자를 HDInsight 클러스터와 동기화](../hdinsight-sync-aad-users-to-cluster.md)
* [클러스터 성능 모니터링](../hdinsight-key-scenarios-to-monitor.md)
