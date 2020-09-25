---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 4be8821a949527fefcc9005b1de7f4f7c438c568
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945749"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 이상.
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만듭니다](../create-communication-resource.md).

## <a name="setting-up"></a>설치

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

1. 터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

   ```console
   mkdir user-tokens-quickstart && cd user-tokens-quickstart
   ```

1. 텍스트 편집기를 사용하여 프로젝트 루트 디렉터리에 **issue-tokens.py**라는 파일을 만들고 기본 예외 처리를 비롯한 프로그램의 구조를 추가합니다. 이 빠른 시작의 모든 소스 코드를 다음 섹션의 이 파일에 추가합니다.

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - User Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>패키지 설치

애플리케이션 디렉터리에 있는 동안 `pip install` 명령을 사용하여 Python 패키지용 Azure Communication Services 관리 클라이언트 라이브러리를 설치합니다.

```console
pip install azure-communication-administration
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>클라이언트 인증

연결 문자열로 `CommunicationIdentityClient`를 인스턴스화합니다. 아래 코드는 `COMMUNICATION_SERVICES_CONNECTION_STRING`이라는 환경 변수에서 리소스에 대한 연결 문자열을 검색합니다. [리소스의 연결 문자열을 관리](../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다.

다음 코드를 `try` 블록 내에 추가합니다.

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

## <a name="create-a-user"></a>사용자 만들기

Azure Communication Services는 경량 ID 디렉터리를 유지 관리합니다. `create_user` 메서드를 사용하여 고유한 `Id`가 있는 디렉터리에 새 항목을 만듭니다. 애플리케이션의 사용자와 Communication Services에서 생성된 ID 간의 매핑을 유지 관리해야 합니다(예: 애플리케이션 서버의 데이터베이스에 저장).

```python
user = client.create_user()
print("\nCreated a user with ID: " + user.identifier + ":")
```

## <a name="issue-user-access-tokens"></a>사용자 액세스 토큰 발급

`issue_token` 메서드를 사용하여 Communication Services 사용자에 대한 액세스 토큰을 발급합니다. 선택적 `user` 매개 변수를 제공하지 않으면 새 사용자가 만들어지고 토큰과 함께 반환됩니다.

```python
# Issue an access token with the "voip" scope for a new user
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued a token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

사용자 액세스 토큰은 사용자에게 서비스 중단이 발생하지 않도록 하기 위해 다시 발급해야 하는 단기 자격 증명입니다. `expires_on` 응답 속성은 토큰의 수명을 나타냅니다.

## <a name="revoke-user-access-tokens"></a>사용자 액세스 토큰 취소

예를 들어, 사용자가 서비스 인증에 사용하는 암호를 변경하는 경우와 같이 사용자 액세스 토큰을 명시적으로 취소해야 하는 경우도 있습니다. 이 기능은 Azure Communication Services 관리 클라이언트 라이브러리를 통해 사용할 수 있습니다.

```python  
client.revoke_tokens(user)
print("\nSuccessfully revoked all tokens for user with ID: " + user.identifier)
```

## <a name="delete-a-user"></a>사용자 삭제

ID를 삭제하면 모든 활성 토큰이 취소되고 ID에 대한 후속 토큰을 발급할 수 없게 됩니다. 또한 사용자와 연결된 모든 지속형 콘텐츠를 제거합니다.

```python
client.delete_user(user)
print("\nDeleted the user with ID: " + user.identifier)
```

## <a name="run-the-code"></a>코드 실행

콘솔 프롬프트에서 *issue-token.py* 파일이 포함된 디렉터리로 이동한 후, 다음 `python` 명령을 실행하여 앱을 실행합니다.

```console
python ./issue-token.py
```
