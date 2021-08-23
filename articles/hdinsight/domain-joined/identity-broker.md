---
title: Azure HDInsight ID Broker(HIB)
description: 도메인 가입 Apache Hadoop 클러스터에 대한 인증을 간소화하는 Azure HDInsight ID Broker에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 8201935f9aff99c9fa2709fe0b1418b0dab56e87
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528770"
---
# <a name="azure-hdinsight-id-broker-hib"></a>Azure HDInsight ID Broker(HIB)

이 문서에서는 Azure HDInsight ID Broker 기능을 설정하고 사용하는 방법을 설명합니다. 이 기능을 사용하면 Azure AD DS(Azure Active Directory Domain Services)에서 레거시 암호 해시 없이 다단계 인증을 적용하면서 Apache Ambari에 대한 최신 OAuth 인증을 가져올 수 있습니다.

## <a name="overview"></a>개요

HDInsight ID Broker는 다음과 같은 시나리오에서 복잡한 인증 설정을 간소화합니다.

* 조직에서는 페더레이션을 사용하여 클라우드 리소스에 액세스하도록 사용자를 인증합니다. 이전에는 HDInsight Enterprise Security Package 클러스터를 사용하려면 온-프레미스 환경에서 Azure AD(Azure Active Directory)로 암호 해시 동기화를 사용하도록 설정해야 했습니다. 일부 조직에서는 이 요구 사항이 어렵거나 바람직하지 않을 수 있습니다.
* 조직에서 Apache Ambari 및 기타 클러스터 리소스에 대한 웹 기반 또는 HTTP 기반 액세스를 위한 다단계 인증을 적용하려고 합니다.

HDInsight ID Broker는 암호 해시를 Azure AD DS에 동기화할 필요 없이 OAuth(최신)에서 Kerberos(레거시)로 프로토콜을 전환할 수 있는 인증 인프라를 제공합니다. 이 인프라는 클러스터 게이트웨이 노드와 함께 HDInsight ID Broker 노드가 사용 설정된 Windows Server 가상 머신(VM)에서 실행되는 구성 요소로 구성됩니다.

다음 표를 사용하여 조직의 요구 사항에 가장 부합하는 인증 옵션을 확인하세요.

|인증 옵션 |HDInsight 구성 | 고려할 요인 |
|---|---|---|
| 완전 OAuth | Enterprise Security Package + HDInsight ID Broker | 가장 안전한 옵션입니다. (다단계 인증이 지원됩니다.) 암호 해시 동기화는 필요하지 ‘않습니다’. Azure AD DS에 암호 해시가 없는 온-프레미스 계정에 대한 ssh/kinit/keytab 액세스는 없습니다. 클라우드 전용 계정은 여전히 ssh/kinit/keytab을 통해 연결할 수 있습니다. OAuth를 통한 Ambari에 대한 웹 기반 액세스입니다. OAuth를 지원하려면 레거시 앱(예: JDBC/ODBC)을 업데이트해야 합니다.|
| OAuth + 기본 인증 | Enterprise Security Package + HDInsight ID Broker | OAuth를 통한 Ambari에 대한 웹 기반 액세스입니다. 레거시 앱은 기본 인증을 계속 사용합니다. 기본 인증 액세스를 위해서는 다단계 인증을 사용하지 않도록 설정해야 합니다. 암호 해시 동기화는 필요하지 ‘않습니다’. Azure AD DS에 암호 해시가 없는 온-프레미스 계정에 대한 ssh/kinit/keytab 액세스는 없습니다. 클라우드 전용 계정은 여전히 ssh/kinit를 통해 연결할 수 있습니다. |
| 완전 기본 인증 | Enterprise Security Package | 온-프레미스 설정과 가장 유사합니다. Azure AD DS에 대한 암호 해시 동기화가 필요합니다. 온-프레미스 계정은 ssh/kinit 또는 keytab을 통해 연결할 수 있습니다. 백업 스토리지가 Azure Data Lake Storage Gen2인 경우 다단계 인증을 사용하지 않도록 설정해야 합니다. |

다음 다이어그램은 HDInsight ID Broker를 사용하도록 설정한 후 페더레이션된 사용자를 포함하여 모든 사용자에 대한 최신 OAuth 기반 인증 흐름을 보여 줍니다.

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="HDInsight ID Broker의 인증 흐름을 보여 주는 다이어그램" border="false":::

이 다이어그램에서 클라이언트(브라우저 또는 앱)는 먼저 OAuth 토큰을 획득해야 합니다. 그런 다음 HTTP 요청에서 게이트웨이에 토큰을 제공합니다. Azure Portal과 같은 다른 Azure 서비스에 이미 로그인한 경우 Single Sign-On 환경을 사용하여 HDInsight 클러스터에 로그인할 수 있습니다.

기본 인증(즉, 사용자 이름 및 암호)만 지원하는 많은 레거시 애플리케이션이 있을 수 있습니다. 이와 같은 시나리오의 경우 여전히 HTTP 기본 인증을 사용하여 클러스터 게이트웨이에 연결할 수 있습니다. 이 설정에서는 게이트웨이 노드에서 직접 시야를 확보할 수 있도록 게이트웨이 노드에서 AD FS(Active Directory Federation Services) 엔드포인트로의 네트워크 연결을 확인해야 합니다.

다음 다이어그램은 페더레이션된 사용자에 대한 기본 인증 흐름을 보여 줍니다. 먼저 게이트웨이는 [ROPC 흐름](../../active-directory/develop/v2-oauth-ropc.md)을 사용하여 인증을 완료하려고 시도합니다. Azure AD에 동기화된 암호 해시가 없는 경우 AD FS 엔드포인트를 다시 검색하고 AD FS 엔드포인트에 액세스하여 인증을 완료합니다.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="기본 인증을 사용하는 아키텍처를 보여 주는 다이어그램" border="false":::

## <a name="enable-hdinsight-id-broker"></a>HDInsight ID Broker 사용

HDInsight ID Broker가 사용 설정된 Enterprise Security Package 클러스터를 만들려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Enterprise Security Package 클러스터에 대한 기본 만들기 단계를 수행합니다. 자세한 내용은 [Enterprise Security Package로 HDInsight 클러스터 만들기](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)를 참조하세요.
1. **HDInsight ID Broker 사용** 을 선택합니다.

HDInsight ID Broker 기능은 클러스터에 하나의 추가 VM을 추가합니다. 이 VM은 HDInsight ID Broker 노드이며 인증을 지원하는 서버 구성 요소를 포함합니다. HDInsight ID Broker 노드는 Azure AD DS 도메인에 가입된 도메인입니다.

:::image type="content" source="./media/identity-broker/identity-broker-enable.png" alt-text="HDInsight ID Broker를 사용하도록 설정하는 옵션을 보여 주는 다이어그램" border="true":::

### <a name="use-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 사용

다음 특성이 있는 `idbrokernode`라는 새 역할을 템플릿의 컴퓨팅 프로필에 추가하면 HDInsight ID Broker 노드가 사용 설정된 클러스터가 생성됩니다.

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

ARM 템플릿의 전체 샘플을 보려면 [여기](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)에 게시된 템플릿을 참조하세요.


## <a name="tool-integration"></a>도구 통합

HDInsight 도구는 기본적으로 OAuth를 지원하도록 업데이트됩니다. 클러스터에 대한 최신 OAuth 기반 액세스에 이 도구를 사용하세요. HDInsight [IntelliJ 플러그 인](../spark/apache-spark-intellij-tool-plugin.md#integrate-with-hdinsight-identity-broker-hib)은 Scala와 같은 Java 기반 애플리케이션에 사용할 수 있습니다. [Visual Studio Code용 Spark 및 Hive 도구](../hdinsight-for-vscode.md)는 PySpark 및 Hive 작업에 사용할 수 있습니다. 이 도구는 일괄 작업과 대화형 작업을 모두 지원합니다.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Azure AD DS에서 암호 해시가 없는 SSH 액세스

|SSH 옵션 |고려할 요인 |
|---|---|
| 로컬 VM 계정(예: sshuser) | 클러스터를 만드는 동안 이 계정을 제공했습니다. 이 계정에 대한 Kerberos 인증이 없습니다. |
| 클라우드 전용 계정(예: alice@contoso.onmicrosoft.com) | 암호 해시는 Azure AD DS에서 사용할 수 있습니다. Kerberos 인증은 SSH Kerberos를 통해 가능합니다. |
| 온-프레미스 계정(예: alice@contoso.com) | SSH Kerberos 인증은 Azure AD DS에서 암호 해시를 사용할 수 있는 경우에만 가능합니다. 그렇지 않으면 이 사용자는 클러스터에 SSH를 통해 연결할 수 있습니다. |

도메인 가입 VM에 SSH를 통해 연결하거나 `kinit` 명령을 실행하려면 암호를 제공해야 합니다. SSH Kerberos 인증을 사용하려면 Azure AD DS에서 해시를 사용할 수 있어야 합니다. 관리 시나리오에만 SSH를 사용하려는 경우 클라우드 전용 계정을 하나 만들어 클러스터에 SSH를 통해 연결하는 데 사용할 수 있습니다. 다른 온-프레미스 사용자는 Azure AD DS에서 암호 해시를 사용할 수 없는 경우에도 Ambari 또는 HDInsight 도구나 HTTP 기본 인증을 사용할 수 있습니다.

조직에서 Azure AD DS에 암호 해시를 동기화하지 않는 경우 Azure AD에서 하나의 클라우드 전용 사용자를 만드는 것이 가장 좋습니다. 그런 다음 클러스터를 만들 때 이를 클러스터 관리자로 할당하고 관리 목적으로 사용합니다. 이를 사용하면 SSH를 통해 VM에 대한 루트 액세스 권한을 가져올 수 있습니다.

인증 문제를 해결하려면 [이 가이드](./domain-joined-authentication-issues.md)를 참조하세요.

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>HDInsight ID Broker로 HDInsight 게이트웨이에 연결하기 위해 OAuth를 사용하는 클라이언트

HDInsight ID Broker 설정에서 게이트웨이에 연결하는 사용자 지정 앱 및 클라이언트를 업데이트하여 필요한 OAuth 토큰을 먼저 획득할 수 있습니다. [이 문서](../../storage/common/storage-auth-aad-app.md)의 단계에 따라 다음 정보를 사용하여 토큰을 가져옵니다.

*    OAuth 리소스 uri: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*    권한: (이름: Cluster.ReadWrite, id: 8f89faa0-ffef-4007-974d-4989b39ad77d)

OAuth 토큰을 획득한 후에는 클러스터 게이트웨이에 대한 HTTP 요청의 인증 헤더(예: https://\<clustername\>-int.azurehdinsight.net)에 이 토큰을 사용합니다. Apache Livy API에 대한 샘플 curl 명령은 다음 예와 같이 표시될 수 있습니다.
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

Beeline 및 Livy를 사용하는 경우 [여기](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/HIB/HIBSamples)에 제공된 샘플 코드를 따라 OAuth를 사용하도록 클라이언트를 설정하고 클러스터에 연결할 수도 있습니다.

## <a name="faq"></a>FAQ
### <a name="what-app-is-created-by-hdinsight-in-aad"></a>AAD의 HDInsight에서 만든 앱은 무엇인가요?
각 클러스터에 대해 타사 애플리케이션은 identifierUri(예: `https://clustername.azurehdinsight.net`)로 클러스터 uri를 사용하여 AAD에 등록됩니다.

### <a name="why-are-users-prompted-for-consent-before-using-hib-enabled-clusters"></a>HIB가 사용 설정된 클러스터를 사용하기 전에 사용자에게 동의를 요청하는 이유는 무엇인가요?
AAD에서 사용자를 인증하거나 데이터에 액세스하기 전에 모든 타사 애플리케이션에 대한 동의가 필요합니다.

### <a name="can-the-consent-be-approved-programatically"></a>동의를 프로그래밍 방식으로 승인할 수 있나요?
Microsoft Graph API를 사용하면 동의를 자동화할 수 있습니다. [API 문서](/graph/api/resources/oauth2permissiongrant)를 참조하세요. 동의를 자동화하는 순서는 다음과 같습니다.

* 앱을 등록한 후 Microsoft Graph에 액세스할 수 있도록 Application.ReadWrite.All 권한 부여
* 클러스터를 만든 후 식별자 uri를 기준으로 클러스터 앱 쿼리
* 앱에 대한 동의 등록

클러스터가 삭제되면 HDInsight는 앱을 삭제하므로 모든 동의를 정리할 필요가 없습니다.

 


## <a name="next-steps"></a>다음 단계

* [Azure Active Directory Domain Services를 사용하여 Enterprise Security Package로 HDInsight 클러스터 구성](apache-domain-joined-configure-using-azure-adds.md)
* [Azure Active Directory 사용자를 HDInsight 클러스터와 동기화](../hdinsight-sync-aad-users-to-cluster.md)
* [클러스터 성능 모니터링](../hdinsight-key-scenarios-to-monitor.md)