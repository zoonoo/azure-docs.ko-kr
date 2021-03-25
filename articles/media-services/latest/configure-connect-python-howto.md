---
title: Azure Media Services v3 API에 연결-Python
description: 이 문서에서는 Python을 사용 하 여 Media Services v3 API에 연결 하는 방법을 보여 줍니다.
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
ms.openlocfilehash: de78008a4645690cfc900f77670204bb892daf51
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105042971"
---
# <a name="connect-to-media-services-v3-api---python"></a>Media Services v3 API에 연결-Python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 서비스 사용자 로그인 메서드를 사용 하 여 Azure Media Services v3 Python SDK에 연결 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Python.org](https://www.python.org/downloads/) 에서 Python 다운로드
- 환경 변수를 설정 해야 합니다. `PATH`
- [Media Services 계정 만들기](./create-account-howto.md) 리소스 그룹 이름과 Media Services 계정 이름을 기억해야 합니다.
- [액세스 api](./access-api-howto.md) 항목의 단계에 따라 서비스 주체 인증 방법을 선택 합니다. `SubscriptionId`이후 단계에서 필요한 구독 id (), 응용 프로그램 클라이언트 ID ( `AadClientId` ), 인증 키 ( `AadSecret` ) 및 테 넌 트 id ()를 기록 합니다 `AadTenantId` .

> [!IMPORTANT]
> [명명 규칙](media-services-apis-overview.md#naming-conventions)을 검토합니다.

## <a name="install-the-modules"></a>모듈 설치

Python을 사용 하 여 Azure Media Services 작업 하려면 이러한 모듈을 설치 해야 합니다.

* `azure-mgmt-resource`모듈은 Active Directory 용 Azure 모듈을 포함 합니다.
* `azure-mgmt-media`Media Services 엔터티를 포함 하는 모듈입니다.

    [최신 버전의 Python 용 MEDIA SERVICES SDK](https://pypi.org/project/azure-mgmt-media/)를 다운로드 해야 합니다.

명령줄 도구를 열고 다음 명령을 사용 하 여 모듈을 설치 합니다.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==3.0.0
```

## <a name="connect-to-the-python-client"></a>Python 클라이언트에 연결

1. 확장명을 사용 하 여 파일 만들기 `.py`
1. 자주 사용 하는 편집기에서 파일을 엽니다.
1. 파일에 다음 코드를 추가 합니다. 이 코드는 필요한 모듈을 가져오고 Media Services에 연결 하는 데 필요한 Active Directory 자격 증명 개체를 만듭니다.

      변수의 값을 [액세스 api](./access-api-howto.md)에서 가져온 값으로 설정 합니다. `ACCOUNT_NAME`및 변수를 `RESOUCE_GROUP_NAME` 해당 리소스를 만들 때 사용 되는 Media Services 계정 이름 및 리소스 그룹 이름으로 업데이트 합니다.

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

- [PYTHON SDK](https://aka.ms/ams-v3-python-sdk)를 사용 합니다.
- Media Services [Python 참조](/python/api/overview/azure/mediaservices/management) 설명서를 검토하세요.
