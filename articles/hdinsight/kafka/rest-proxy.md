---
title: Apache Kafka REST 프록시-Azure HDInsight
description: Azure HDInsight에서 Kafka REST 프록시를 사용 하 여 작업을 Apache Kafka 하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.custom: has-adal-ref
ms.openlocfilehash: affdbfba125b7e9b3f3fe250a56af30e9efe816e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611009"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>REST 프록시를 사용 하 여 Azure HDInsight에서 Apache Kafka 클러스터와 상호 작용

Kafka REST 프록시를 사용 하면 HTTP를 통해 REST API를 통해 Kafka 클러스터와 상호 작용할 수 있습니다. 이 작업을 통해 Kafka 클라이언트는 가상 네트워크 외부에 있을 수 있습니다. 클라이언트는 kafka 라이브러리를 사용 하는 대신 Kafka 클러스터에 대 한 간단한 HTTP 호출을 만들 수 있습니다. 이 문서에서는 REST 프록시 사용 Kafka 클러스터를 만드는 방법을 보여 줍니다. 또한 REST 프록시를 호출 하는 방법을 보여 주는 샘플 코드를 제공 합니다.

## <a name="rest-api-reference"></a>REST API 참조

Kafka REST API에서 지 원하는 작업의 경우 [HDInsight KAFKA REST 프록시 API 참조](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)를 참조 하세요.

## <a name="background"></a>배경

![Kafka REST 프록시 디자인](./media/rest-proxy/rest-proxy-architecture.png)

API에서 지원 되는 작업의 전체 사양은 [APACHE KAFKA REST 프록시 API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)를 참조 하세요.

### <a name="rest-proxy-endpoint"></a>REST 프록시 끝점

REST 프록시를 사용 하 여 HDInsight Kafka 클러스터를 만들면 클러스터에 대 한 새 공용 끝점을 만듭니다 .이 끝점은 Azure Portal의 HDInsight 클러스터 **속성** 에서 찾을 수 있습니다.

### <a name="security"></a>보안

Kafka REST 프록시에 대 한 액세스는 Azure Active Directory 보안 그룹을 사용 하 여 관리 됩니다. Kafka 클러스터를 만들 때 REST 끝점 액세스를 사용 하 여 Azure AD 보안 그룹을 제공 합니다. REST 프록시에 대 한 액세스가 필요한 kafka 클라이언트는 그룹 소유자가이 그룹에 등록 해야 합니다. 그룹 소유자는 포털을 통해 또는 PowerShell을 통해 등록할 수 있습니다.

REST 프록시 끝점 요청의 경우 클라이언트 응용 프로그램은 OAuth 토큰을 가져와야 합니다. 토큰은 보안 그룹 구성원 자격을 확인 하는 데 사용 됩니다. OAuth 토큰을 가져오는 방법을 보여 주는 아래 [클라이언트 응용 프로그램 샘플](#client-application-sample) 찾기 클라이언트 응용 프로그램은 HTTP 요청의 OAuth 토큰을 REST 프록시로 전달 합니다.

> [!NOTE]
> AAD 보안 그룹에 대해 자세히 알아보려면 [Azure Active Directory 그룹을 사용 하 여 앱 및 리소스 액세스 관리](../../active-directory/fundamentals/active-directory-manage-groups.md)를 참조 하세요. OAuth 토큰의 작동 방식에 대 한 자세한 내용은 [oauth 2.0 코드 부여 흐름을 사용 하 여 Azure Active Directory 웹 응용 프로그램에 대 한 액세스 권한 부여](../../active-directory/develop/v1-protocols-oauth-code.md)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

1. Azure AD에 애플리케이션을 등록합니다. Kafka REST 프록시와 상호 작용 하기 위해 작성 하는 클라이언트 응용 프로그램은이 응용 프로그램의 ID와 암호를 사용 하 여 Azure에 인증 합니다.

1. Azure AD 보안 그룹을 만듭니다. Azure AD에 등록 한 응용 프로그램을 그룹의 **구성원** 으로 보안 그룹에 추가 합니다. 이 보안 그룹은 REST 프록시와 상호 작용할 수 있는 응용 프로그램을 제어 하는 데 사용 됩니다. Azure AD 그룹을 만드는 방법에 대 한 자세한 내용은 [기본 그룹 만들기 및 Azure Active Directory를 사용 하 여 구성원 추가](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조 하세요.

    그룹이 **보안**형식 인지 확인 합니다.
    ![보안 그룹](./media/rest-proxy/rest-proxy-group.png)

    응용 프로그램이 그룹의 구성원 인지 확인 합니다.
    ![멤버 자격 확인](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>REST 프록시를 사용 하는 Kafka 클러스터 만들기

1. Kafka 클러스터 만들기 워크플로 중 **보안 + 네트워킹** 탭에서 **KAFKA REST 프록시 사용** 옵션을 선택 합니다.

     ![Kafka REST 프록시를 사용 하도록 설정 하 고 보안 그룹을 선택 합니다.](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. **보안 그룹 선택**을 클릭 합니다. 보안 그룹 목록에서 REST 프록시에 액세스 하려는 보안 그룹을 선택 합니다. 검색 상자를 사용 하 여 적절 한 보안 그룹을 찾을 수 있습니다. 맨 아래에 있는 **선택** 단추를 클릭 합니다.

     ![Kafka REST 프록시를 사용 하도록 설정 하 고 보안 그룹을 선택 합니다.](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. [Azure Portal를 사용 하 여 Azure HDInsight에서 Apache Kafka 클러스터 만들기](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)에 설명 된 대로 클러스터를 만드는 나머지 단계를 완료 합니다.

1. 클러스터가 만들어지면 클러스터 속성으로 이동 하 여 Kafka REST 프록시 URL을 기록 합니다.

     ![REST 프록시 URL 보기](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>클라이언트 응용 프로그램 샘플

아래 python 코드를 사용 하 여 Kafka 클러스터에서 REST 프록시와 상호 작용할 수 있습니다. 코드 샘플을 사용 하려면 다음 단계를 수행 합니다.

1. Python이 설치 된 컴퓨터에 샘플 코드를 저장 합니다.
1. 을 실행 `pip3 install msal`하 여 필요한 python 종속성을 설치 합니다.
1. **이러한 속성을 구성** 하는 코드 섹션을 수정 하 고 사용자 환경에 대 한 다음 속성을 업데이트 합니다.

    |속성 |설명 |
    |---|---|
    |테넌트 ID|구독이 인 Azure 테 넌 트.|
    |클라이언트 ID|보안 그룹에 등록 한 응용 프로그램의 ID입니다.|
    |클라이언트 암호|보안 그룹에 등록 한 응용 프로그램에 대 한 암호입니다.|
    |Kafkarest_endpoint|[배포 섹션](#create-a-kafka-cluster-with-rest-proxy-enabled)에 설명 된 대로 클러스터 개요의 **속성** 탭에서이 값을 가져옵니다. 다음 형식 이어야 합니다.`https://<clustername>-kafkarest.azurehdinsight.net`|

1. 명령줄에서 다음을 실행 하 여 python 파일을 실행 합니다.`sudo python3 <filename.py>`

이 코드는 다음 작업을 수행 합니다.

1. Azure AD에서 OAuth 토큰을 인출 합니다.
1. Kafka REST 프록시에 대 한 요청을 수행 하는 방법을 보여 줍니다.

Python에서 OAuth 토큰을 가져오는 방법에 대 한 자세한 내용은 [Python AuthenticationContext 클래스](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)를 참조 하세요. Kafka REST 프록시를 `topics` 통해 생성 되거나 삭제 되지 않는 지연 시간이 표시 될 수 있습니다. 이 지연은 캐시 새로 고침으로 인해 발생 합니다.

```python
#Required python packages
#pip3 install msal

import msal

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

# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
    )

# The pattern to acquire a token looks like this.
result = None

result = app.acquire_token_for_client(scopes=[scope])

print(result)
accessToken = result['access_token']

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Azure REST 프록시에서 토큰을 가져오는 방법에 대 한 다른 샘플은 말아 넘기기 명령을 사용 합니다. **토큰을 가져오는 동안 지정 `scope=https://hib.azurehdinsight.net/.default` 된가 필요 합니다.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>다음 단계

* [Kafka REST 프록시 API 참조 문서](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
