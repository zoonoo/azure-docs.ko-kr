---
title: 포함 파일
description: 포함 파일
services: storage
author: codemillmatt
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: masoucou
ms.custom: include file
ms.openlocfilehash: 02586d38903c60ba8982753ca0bd3e15192d5deb
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006379"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Azure Portal에서 자격 증명 복사

샘플 애플리케이션이 Azure Storage에 대한 요청을 수행하는 경우 권한이 있어야 합니다. 요청에 권한을 부여하려면 스토리지 계정 자격 증명을 연결 문자열로 애플리케이션에 추가합니다. 다음 단계를 수행하여 스토리지 계정 자격 증명을 봅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 스토리지 계정을 찾습니다.
3. 스토리지 계정 개요의 **설정** 섹션에서 **액세스 키**를 선택합니다. 여기에서 계정 액세스 키 및 각 키의 전체 연결 문자열을 볼 수 있습니다.
4. **key1** 아래에서 **연결 문자열** 값을 찾고, **복사** 단추를 선택하여 연결 문자열을 복사합니다. 다음 단계에서 연결 문자열 값을 환경 변수에 추가합니다.

    ![Azure Portal에서 연결 문자열을 복사하는 방법을 보여주는 스크린샷](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>스토리지 연결 문자열 구성

연결 문자열을 복사한 후 *MainPage.xaml.cs* 파일의 클래스 수준 변수로 설정합니다. *MainPaage.xaml.cs*를 열고 `storageConnectionString` 변수를 찾습니다. `<yourconnectionstring>`을 실제 연결 문자열로 바꿉니다.

코드는 다음과 같습니다.

```csharp
string storageConnectionString = "<yourconnectionstring>";
```