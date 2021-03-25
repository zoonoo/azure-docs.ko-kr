---
ms.openlocfilehash: b37ebebdb99530ab615a313d9b269b9ce937b7f1
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110758"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>통신 서비스 솔루션에 관리 id 추가

### <a name="install-the-sdk-packages"></a>SDK 패키지 설치

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-sdk-packages"></a>SDK 패키지 사용

`import`Azure id를 사용 하는 코드에 다음을 추가 합니다.

```python
from azure.identity import DefaultAzureCredential
```

아래 예제에서는 [DefaultAzureCredential](/python/api/azure.identity.defaultazurecredential)를 사용 합니다. 이 자격 증명은 프로덕션 환경과 개발 환경에 적합 합니다.

개발 환경에 응용 프로그램을 등록 하 고 환경 변수를 설정 하려면 [관리 id를 사용 하 여 액세스 권한 부여](../managed-identity-from-cli.md) 를 참조 하세요.

### <a name="create-an-identity-and-issue-a-token"></a>Id 만들기 및 토큰 발급

다음 코드 예제에서는 관리 되는 id를 사용 하 여 서비스 클라이언트 개체를 만든 다음 클라이언트를 사용 하 여 새 사용자에 대 한 토큰을 발급 하는 방법을 보여 줍니다.

```python
from azure.communication.identity import CommunicationIdentityClient 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Azure 관리 id를 사용 하 여 SMS 보내기

다음 코드 예제에서는 Azure 관리 id를 사용 하 여 서비스 클라이언트 개체를 만든 다음 클라이언트를 사용 하 여 SMS 메시지를 보내는 방법을 보여 줍니다.

```python
from azure.communication.sms import SmsClient

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```
