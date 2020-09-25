---
title: 자격 증명 관리에 ID Broker (미리 보기) 사용-Azure HDInsight
description: 도메인에 가입 된 Apache Hadoop 클러스터에 대 한 인증을 간소화 하기 위해 HDInsight ID Broker에 대해 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/12/2019
ms.openlocfilehash: 12d98406b21ed9a3ea27f9aa4abc0db6f536468d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91251918"
---
# <a name="azure-hdinsight-id-broker-preview"></a>Azure HDInsight ID 브로커 (미리 보기)

이 문서에서는 Azure HDInsight에서 HDInsight ID Broker () 기능을 설정 하 고 사용 하는 방법을 설명 합니다. 이 기능을 사용 하 여 Azure Active Directory Domain Services (AAD)에서 레거시 암호 해시가 필요 하지 않고 Multi-Factor Authentication (MFA)를 적용 하는 동안 Apache Ambari에 대 한 최신 OAuth 인증을 가져올 수 있습니다.

## <a name="overview"></a>개요

다음과 같은 경우에는 복잡 한 인증 설정이 간소화 됩니다.

* 조직에서는 페더레이션에 의존 하 여 클라우드 리소스에 액세스 하는 사용자를 인증 합니다. 이전에는 Enterprise Security Package (ESP) 클러스터를 사용 하려면 온-프레미스 환경에서 Azure Active Directory (Azure AD)로 암호 해시 동기화를 사용 하도록 설정 해야 했습니다. 일부 조직에서는이 요구 사항이 어렵거나 바람직하지 않을 수 있습니다.

* 조직에서 Apache Ambari 및 기타 클러스터 리소스에 대 한 웹/HTTP 기반 액세스를 위한 MFA를 적용 하고자 합니다.

설정 b는 암호 해시를 AAD와 동기화 할 필요 없이 OAuth (최신)에서 Kerberos (레거시)로의 프로토콜 전환을 가능 하 게 하는 인증 인프라를 제공 합니다. 이 인프라는 Windows Server VM (ID 브로커 노드)에서 실행 되는 구성 요소와 함께 클러스터 게이트웨이 노드와 함께 구성 됩니다.

다음 다이어그램에서는 ID Broker를 사용 하도록 설정한 후 페더레이션된 사용자를 포함 하 여 모든 사용자에 대 한 최신 OAuth 기반 인증 흐름을 보여 줍니다.

![ID Broker를 사용 하 여 인증 흐름](./media/identity-broker/identity-broker-architecture.png)

이 다이어그램에서 클라이언트 (예: 브라우저 또는 앱)는 먼저 OAuth 토큰을 획득 한 다음 HTTP 요청에서 게이트웨이에 토큰을 제공 해야 합니다. Azure Portal와 같은 다른 Azure 서비스에 이미 로그인 한 경우에는 SSO (Single Sign-On) 환경을 사용 하 여 HDInsight 클러스터에 로그인 할 수 있습니다.

기본 인증 (즉, 사용자 이름/암호)만 지 원하는 많은 레거시 응용 프로그램이 있을 수 있습니다. 이러한 시나리오의 경우 여전히 HTTP 기본 인증을 사용 하 여 클러스터 게이트웨이에 연결할 수 있습니다. 이 설정에서는 게이트웨이 노드에서 페더레이션 끝점 (ADFS 끝점)으로의 네트워크 연결을 확인 하 여 게이트웨이 노드에서 직접 시야를 확인 해야 합니다.

다음 표를 사용 하 여 조직 요구 사항에 따라 가장 적합 한 인증 옵션을 결정 합니다.

|인증 옵션 |HDInsight 구성 | 고려해 야 할 요소 |
|---|---|---|
| 완전 OAuth | ESP + 전 2 | 1. 가장 안전한 옵션 (MFA 지원 됨) 2.    패스 해시 동기화는 필요 하지 않습니다. 3.  온-프레미스 계정에 대 한 ssh/kinit/keytab 액세스는 없으며, AAD에 암호 해시가 없습니다. 4.   클라우드 전용 계정은 여전히 ssh/kinit/keytab 수 있습니다. 5. Oauth 6을 통한 Ambari에 대 한 웹 기반 액세스  OAuth를 지원 하려면 레거시 앱 (JDBC/ODBC 등)을 업데이트 해야 합니다.|
| OAuth + 기본 인증 | ESP + 전 2 | 1. Oauth 2를 통한 Ambari에 대 한 웹 기반 액세스 레거시 앱은 계속 기본 인증을 사용 합니다. 3. 기본 인증 액세스에 대해 MFA를 사용 하지 않도록 설정 해야 합니다. 4. 패스 해시 동기화는 필요 하지 않습니다. 5. 온-프레미스 계정에 대 한 ssh/kinit/keytab 액세스는 없으며, AAD에 암호 해시가 없습니다. 6. 클라우드 전용 계정은 여전히 ssh/kinit 할 수 있습니다. |
| 완전 한 기본 인증 | ESP | 1. 온-프레미스 설정과 가장 유사 합니다. 2. AAD에 암호 해시 동기화가 필요 합니다. 3. 온-프레미스 계정은 ssh/kinit 또는 keytab를 사용할 수 있습니다. 4. 백업 저장소를 ADLS Gen2 하는 경우 MFA를 사용 하지 않도록 설정 해야 합니다. |


## <a name="enable-hdinsight-id-broker"></a>HDInsight ID Broker 사용

ID Broker를 사용 하는 ESP 클러스터를 만들려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. ESP 클러스터에 대 한 기본 만들기 단계를 따릅니다. 자세한 내용은 [ESP를 사용 하 여 HDInsight 클러스터 만들기](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)를 참조 하세요.
1. **HDINSIGHT ID Broker 사용**을 선택 합니다.

ID Broker 기능은 클러스터에 추가 VM 하나를 추가 합니다. 이 VM은 ID Broker 노드가 며 인증을 지 원하는 서버 구성 요소를 포함 합니다. ID Broker 노드는 Azure AD DS 도메인에 가입 된 도메인입니다.

![ID Broker를 사용 하도록 설정 하는 옵션](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 사용
다음 특성을 사용 하 여 라는 새 역할을 `idbrokernode` 템플릿의 계산 프로필에 추가 하면 ID broker 노드가 활성화 된 상태에서 클러스터가 생성 됩니다.

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
            "targetInstanceCount": 1,
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

## <a name="tool-integration"></a>도구 통합

HDIsngith 도구는 기본적으로 OAuth를 지원 하도록 업데이트 되었습니다. 클러스터에 대 한 최신 OAuth 기반 액세스에 이러한 도구를 사용 하는 것이 좋습니다. HDInsight [IntelliJ 플러그](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) 인은 Scala와 같은 JAVA 기반 응용 프로그램에 사용할 수 있습니다. [VS Code에 대 한 Spark & Hive 도구](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) 를 사용 하 여 PySpark 및 hive 작업을 수행할 수 있습니다. 일괄 처리 및 대화형 작업을 모두 지원 합니다.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Azure AD DS에서 암호 해시가 없는 SSH 액세스

|SSH 옵션 |고려해 야 할 요소 |
|---|---|
| 로컬 VM 계정 (예: sshuser) | 1. 클러스터를 만들 때이 계정을 제공 했습니다. 2.  이 계정에 대 한 kerberos 인증을 사용할 수 없습니다. |
| 클라우드 전용 계정 (예: alice@contoso.onmicrosoft.com ) | 1. 암호 해시는 AAD-DS 2에서 사용할 수 있습니다. Kerberos 인증은 SSH kerberos를 통해 가능 합니다. |
| 온-프레미스 계정 (예: alice@contoso.com ) | 1. SSH Kerberos 인증은 AAD에서 암호 해시를 사용할 수 있는 경우에만 가능 합니다. 그렇지 않으면이 사용자가 클러스터에 대해 SSH를 수행할 수 없습니다. |

도메인에 가입 된 VM으로 SSH 하거나 명령을 실행 하려면 암호를 `kinit` 제공 해야 합니다. SSH Kerberos 인증을 사용 하려면 AAD에서 해시를 사용할 수 있어야 합니다. 관리 시나리오에만 SSH를 사용 하려는 경우 클라우드 전용 계정을 하나 만들어 클러스터에 대 한 SSH로 사용할 수 있습니다. 다른 온-프레미스 사용자는 AAD에서 암호 해시를 사용할 수 없는 경우에도 Ambari 또는 HDInsight 도구나 HTTP 기본 인증을 사용할 수 있습니다.

조직에서 암호 해시를 AAD와 동기화 하지 않는 경우 모범 사례에 따라 Azure AD에서 클라우드 전용 사용자를 하나 만들어 클러스터 관리자로 할당 하 고 클러스터를 만들 때이를 클러스터 관리자로 할당 하 고이를 사용 하 여 SSH를 통해 Vm에 대 한 루트 액세스 가져오기를 포함 하는 관리 목적으로 사용 합니다.

인증 문제를 해결 하려면이 [가이드](https://docs.microsoft.com/azure/hdinsight/domain-joined/domain-joined-authentication-issues)를 참조 하세요.

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-hib"></a>OAuth를 사용 하 여 대상 클라이언트를 사용 하 여 HDInsight 게이트웨이에 연결

설정 b 설정에서 게이트웨이에 연결 하는 사용자 지정 앱 및 클라이언트를 업데이트 하 여 필요한 OAuth 토큰을 먼저 획득할 수 있습니다. 이 [문서의](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) 단계에 따라 다음 정보를 포함 하는 토큰을 가져올 수 있습니다.

*   OAuth 리소스 uri: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   권한: (이름: Cluster. ReadWrite, id: 8f89faa0-ffef-4007-974d-4989b39ad77d)

OAuth 토큰을 획득 한 후에는 클러스터 게이트웨이 (예: https:// <clustername> -int.azurehdinsight.net)에 대 한 HTTP 요청의 인증 헤더에서이 토큰을 사용할 수 있습니다. 예를 들어 Apache Livy API에 대 한 샘플 말아 명령은 다음과 같이 표시 될 수 있습니다.
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

## <a name="next-steps"></a>다음 단계

* [Azure Active Directory Domain Services를 사용 하 여 Enterprise Security Package를 사용 하 여 HDInsight 클러스터 구성](apache-domain-joined-configure-using-azure-adds.md)
* [Azure Active Directory 사용자를 HDInsight 클러스터와 동기화](../hdinsight-sync-aad-users-to-cluster.md)
* [클러스터 성능 모니터링](../hdinsight-key-scenarios-to-monitor.md)
