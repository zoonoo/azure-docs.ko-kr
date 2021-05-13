---
title: Azure Media Services v3 API에 연결 - Python
description: 이 문서에서는 Python을 사용하여 Media Services v3 API에 연결하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-python
ms.openlocfilehash: 24e2ba4027dc818256dc9572f697fe7ec5a5a56b
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960710"
---
# <a name="connect-to-media-services-v3-api---python"></a>Media Services v3 API에 연결 - Python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 서비스 주체 로그인 메서드를 사용하여 Azure Media Services v3 Python SDK에 연결하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 구성 요소

- [python.org](https://www.python.org/downloads/)에서 Python 다운로드
- `PATH` 환경 변수를 설정해야 합니다.
- [Media Services 계정 만들기](./account-create-how-to.md) 리소스 그룹 이름과 Media Services 계정 이름을 기억해야 합니다.
- [액세스 API](./access-api-howto.md) 토픽의 단계를 따라 서비스 주체 인증 방법을 선택합니다. 이후 단계에서 필요한 구독 ID(`SubscriptionId`), 애플리케이션 클라이언트 ID(`AadClientId`), 인증 키(`AadSecret`), 테넌트 ID(`AadTenantId`)를 기록합니다.

> [!IMPORTANT]
> [명명 규칙](media-services-apis-overview.md#naming-conventions)을 검토합니다.

## <a name="install-the-modules"></a>모듈 설치

Python을 사용하여 Azure Media Services로 작업하려면 해당 모듈을 설치해야 합니다.

* Active Directory의 Azure 모듈을 포함하는 `azure-mgmt-resource` 모듈.
* Media Services 엔터티를 포함하는 `azure-mgmt-media` 모듈.

    [최신 버전의 Python용 Media Services SDK](https://pypi.org/project/azure-mgmt-media/)를 얻어야 합니다.

명령줄 도구를 열고 다음 명령을 사용하여 모듈을 설치합니다.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==3.0.0
```

## <a name="connect-to-the-python-client"></a>Python 클라이언트에 연결

1. `.py` 확장명으로 파일 만들기
1. 원하는 편집기에서 파일 열기
1. 다음 코드를 파일에 추가합니다. 이 코드는 필요한 모듈을 가져오고 Media Services에 연결하는 데 필요한 Active Directory 자격 증명 개체를 만듭니다.

      변수의 값을 [액세스 API](./access-api-howto.md)에서 가져온 값으로 설정합니다. `ACCOUNT_NAME`과 `RESOUCE_GROUP_NAME` 변수를 해당 리소스를 만들 때 사용되는 Media Services 계정 이름과 리소스 그룹 이름으로 업데이트합니다.

      ```
      import adal
      from azure.mgmt.media import AzureMediaServices
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD

      RESOURCE = "https://management.core.windows.net/"
      # Tenant ID for your Azure Subscription (AadTenantId)
      TENANT_ID = "00000000-0000-0000-000000000000"
      # Your Service Principal App ID (AadClientId)
      CLIENT = "00000000-0000-0000-000000000000"
      # Your Service Principal Password (AadSecret)
      KEY = "00000000-0000-0000-000000000000"
      # Your Azure Subscription ID (SubscriptionId)
      SUBSCRIPTION_ID = "00000000-0000-0000-000000000000"
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = "amsaccount"
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = "amsResourceGroup"

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + "/" + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials, RESOURCE, CLIENT, KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print(client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. 파일 실행

## <a name="next-steps"></a>다음 단계

- [Python SDK](https://aka.ms/ams-v3-python-sdk)를 사용합니다.
- Media Services [Python 참조](/python/api/overview/azure/mediaservices/management) 설명서를 검토하세요.
