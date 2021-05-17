---
ms.openlocfilehash: c642b0e5f459b2412bca6588c8ae625142f0f59f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450279"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>통신 서비스 솔루션에 관리 ID 추가

### <a name="install-the-sdk-packages"></a>SDK 패키지 설치

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-sdk-packages"></a>SDK 패키지 사용

Azure ID를 사용하려면 코드에 다음 `import`를 추가합니다.

```python
from azure.identity import DefaultAzureCredential
```

아래 예제는 [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential)을 사용합니다. 이 자격 증명은 프로덕션 환경과 개발 환경에 적합합니다.

관리 ID 인증을 사용하여 쉽게 이동하는 방법은 [관리 ID로 액세스 권한 부여](../managed-identity-from-cli.md)를 참조하세요.

DefaultAzureCredential 개체의 작동 방식과 이 빠른 시작에 지정되지 않은 방식으로 이를 사용하는 방법에 대한 자세한 내용은 [Python용 Azure ID 클라이언트 라이브러리](https://docs.microsoft.com/python/api/overview/azure/identity-readme)를 참조하세요.

### <a name="create-an-identity-and-issue-a-token"></a>ID 만들기 및 토큰 발급

다음 코드 예제에서는 관리 ID로 서비스 클라이언트 개체를 만든 다음, 클라이언트를 사용하여 새 사용자에 대한 토큰을 발급하는 방법을 보여줍니다.

```python
from azure.communication.identity import CommunicationIdentityClient

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Azure 관리 ID를 사용하여 SMS 보내기
다음 코드 예제에서는 Azure 관리 ID로 서비스 클라이언트 개체를 만든 다음, 클라이언트를 사용하여 SMS 메시지를 보내는 방법을 보여줍니다.

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

### <a name="list-all-your-purchased-phone-numbers"></a>구매한 전화 번호 모두 나열

다음 코드 예제에서는 Azure 관리 ID로 서비스 클라이언트 개체를 만든 다음, 클라이언트를 사용하여 리소스에 포함된 구매한 모든 전화 번호를 확인하는 방법을 보여줍니다.

```python
from azure.communication.phonenumbers import PhoneNumbersClient

def list_purchased_phone_numbers(resource_endpoint):
     credential = DefaultAzureCredential()
     phone_numbers_client = PhoneNumbersClient(resource_endpoint, credential)

     return phone_numbers_client.list_purchased_phone_numbers()
```