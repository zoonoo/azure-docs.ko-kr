---
title: 아파치 카프카 레스트 프록시 - Azure HDInsight
description: Azure HDInsight에서 카프카 REST 프록시를 사용하여 아파치 카프카 작업을 수행하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 6bf34f8fb15bf8fddb1ba398ed678d5c98b8c84f
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667778"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>REST 프록시를 사용하여 Azure HDInsight에서 아파치 카프카 클러스터와 상호 작용

카프카 REST 프록시는 HTTP를 통해 REST API를 통해 카프카 클러스터와 상호 작용할 수 있습니다. 이 작업은 Kafka 클라이언트가 가상 네트워크 외부에 있을 수 있음을 의미합니다. 클라이언트는 Kafka 라이브러리에 의존하는 대신 Kafka 클러스터에 대한 간단한 HTTP 호출을 할 수 있습니다. 이 문서에서는 REST 프록시 를 만드는 방법을 보여 드리며 Kafka 클러스터를 사용 했습니다. 또한 REST 프록시를 호출하는 방법을 보여 주는 샘플 코드를 제공합니다.

## <a name="rest-api-reference"></a>REST API 참조

카프카 REST API에서 지원하는 작업에 대해서는 [HDInsight 카프카 REST 프록시 API 참조를](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)참조하십시오.

## <a name="background"></a>배경

![카프카 레스트 프록시 디자인](./media/rest-proxy/rest-proxy-architecture.png)

API에서 지원하는 작업의 전체 사양은 [아파치 카프카 REST 프록시 API를](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)참조하십시오.

### <a name="rest-proxy-endpoint"></a>REST 프록시 끝점

REST 프록시를 사용 하 여 HDInsight Kafka 클러스터를 만들면 Azure 포털의 HDInsight 클러스터 **속성에서** 찾을 수 있는 클러스터에 대 한 새 공용 끝점이 만들어집니다.

### <a name="security"></a>보안

Kafka REST 프록시에 대한 액세스는 Azure Active Directory 보안 그룹으로 관리됩니다. Kafka 클러스터를 만들 때 Azure AD 보안 그룹에 REST 끝점 액세스를 제공합니다. REST 프록시에 액세스해야 하는 Kafka 클라이언트는 그룹 소유자가 이 그룹에 등록해야 합니다. 그룹 소유자는 포털또는 PowerShell을 통해 등록할 수 있습니다.

REST 프록시 끝점 요청의 경우 클라이언트 응용 프로그램은 OAuth 토큰을 받아야 합니다. 토큰은 보안 그룹 구성원 자격을 확인하는 데 사용됩니다. OAuth 토큰을 얻는 방법을 보여 주는 클라이언트 [응용 프로그램 샘플을](#client-application-sample) 아래에서 찾습니다. 클라이언트 응용 프로그램은 HTTP 요청에서 OAuth 토큰을 REST 프록시에 전달합니다.

> [!NOTE]  
> [Azure Active Directory 그룹을 사용하여 앱 및 리소스 액세스 관리를](../../active-directory/fundamentals/active-directory-manage-groups.md)참조하여 AAD 보안 그룹에 대해 자세히 알아봅니다. OAuth 토큰의 작동 방식에 대한 자세한 내용은 [OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 응용 프로그램에 대한 액세스 권한 부여를](../../active-directory/develop/v1-protocols-oauth-code.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

1. Azure AD에 애플리케이션을 등록합니다. Kafka REST 프록시와 상호 작용하기 위해 작성하는 클라이언트 응용 프로그램은 이 응용 프로그램의 ID와 비밀을 사용하여 Azure를 인증합니다.

1. Azure AD 보안 그룹을 만듭니다. Azure AD에 등록한 응용 프로그램을 그룹의 **구성원으로** 보안 그룹에 추가합니다. 이 보안 그룹은 REST 프록시와 상호 작용할 수 있는 응용 프로그램을 제어하는 데 사용됩니다. Azure AD 그룹 만들기에 대한 자세한 내용은 [Azure Active Directory를 사용하여 기본 그룹 만들기 및 구성원 추가를](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)참조합니다.

    그룹이 **보안**형식임을 확인합니다.
    ![보안 그룹](./media/rest-proxy/rest-proxy-group.png)

    응용 프로그램이 그룹의 구성원임을 확인합니다.
    ![멤버십 확인](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>REST 프록시를 사용하도록 설정한 Kafka 클러스터 만들기

1. Kafka 클러스터 생성 워크플로 동안 **보안 + 네트워킹** 탭에서 **Kafka REST 프록시 사용** 옵션을 확인합니다.

     ![카프카 REST 프록시 활성화 및 보안 그룹 선택](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. **보안 그룹 선택을 클릭합니다.** 보안 그룹 목록에서 REST 프록시에 액세스할 보안 그룹을 선택합니다. 검색 상자를 사용하여 적절한 보안 그룹을 찾을 수 있습니다. 하단의 **선택** 버튼을 클릭합니다.

     ![카프카 REST 프록시 활성화 및 보안 그룹 선택](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Azure [포털을 사용하여 Azure HDInsight의 아파치 카프카 클러스터 만들기에](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)설명된 대로 클러스터를 만드는 데 남은 단계를 완료합니다.

1. 클러스터가 만들어지면 클러스터 속성으로 이동하여 Kafka REST 프록시 URL을 기록합니다.

     ![나머지 프록시 URL 보기](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>클라이언트 응용 프로그램 샘플

아래의 파이썬 코드를 사용하여 Kafka 클러스터의 REST 프록시와 상호 작용할 수 있습니다. 코드 샘플을 사용하려면 다음 단계를 따르십시오.

1. 파이썬이 설치된 컴퓨터에서 샘플 코드를 저장합니다.
1. 실행 `pip3 install adal` 및 `pip install msrestazure`을 실행하여 필요한 파이썬 종속성을 설치합니다.
1. 코드 수정 섹션 **이러한 속성을 구성하고** 환경에 대한 다음 속성을 업데이트합니다.

    |속성 |설명 |
    |---|---|
    |테넌트 ID|구독이 있는 Azure 테넌트입니다.|
    |클라이언트 ID|보안 그룹에 등록한 응용 프로그램의 ID입니다.|
    |클라이언트 암호|보안 그룹에 등록한 응용 프로그램의 비밀입니다.|
    |Kafkarest_endpoint|[배포 섹션에](#create-a-kafka-cluster-with-rest-proxy-enabled)설명된 대로 클러스터 개요의 **속성** 탭에서 이 값을 가져옵니다. 다음 형식이어야 합니다.`https://<clustername>-kafkarest.azurehdinsight.net`|

1. 명령줄에서 실행하여 파이썬 파일을 실행합니다.`python <filename.py>`

이 코드는 다음과 같은 작업을 수행합니다.

1. Azure AD에서 OAuth 토큰을 가져옵니다.
1. Kafka REST 프록시에 요청하는 방법을 보여 둡습니다.

파이썬에서 OAuth 토큰을 얻는 방법에 대한 자세한 내용은 [파이썬 인증 컨텍스트 클래스를](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)참조하십시오. Kafka REST `topics` 프록시를 통해 생성되거나 삭제되지 않은 경우 지연이 표시될 수 있습니다. 이 지연은 캐시 새로 고침으로 인해 시작됩니다.

```python
#Required python packages
#pip3 install adal
#pip install msrestazure

import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
import requests

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

#getting token
login_endpoint = AZURE_PUBLIC_CLOUD.endpoints.active_directory
resource = "https://hib.azurehdinsight.net"
context = adal.AuthenticationContext(login_endpoint + '/' + tenant_id)

token = context.acquire_token_with_client_credentials(
    resource,
    client_id,
    client_secret)

accessToken = 'Bearer ' + token['accessToken']

print(accessToken)

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

curl 명령을 사용하여 Azure for REST 프록시에서 토큰을 얻는 방법에 대한 다른 샘플을 아래에서 찾아보십시오. 토큰을 `resource=https://hib.azurehdinsight.net` 받는 동안 지정된 것이 필요합니다.

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&resource=https://hib.azurehdinsight.net' 'https://login.microsoftonline.com/<tenantid>/oauth2/token'
```

## <a name="next-steps"></a>다음 단계

* [카프카 REST 프록시 API 참조 문서](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
