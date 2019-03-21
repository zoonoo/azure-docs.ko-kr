---
title: 음성 디바이스 SDK 가져오기
titleSuffix: Azure Cognitive Services
description: Speech Services는 다양한 디바이스 및 오디오 원본에서 작동합니다. 이제 일치하는 하드웨어 및 소프트웨어를 사용하여 음성 애플리케이션을 한 단계 업그레이드할 수 있습니다. 이 문서에서는 Speech Devices SDK에 대한 액세스를 가져오고 개발을 시작하는 방법을 알아봅니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 3c5874625ee9d1932c401238c1586ad89d5d206d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57856725"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Cognitive Services Speech Devices SDK 가져오기

Speech Devices SDK는 제한된 미리 보기로 있으므로 프로그램에 등록해야 합니다. 현재 Microsoft는 이 제품에 액세스할 수 있는 후보로 대기업을 선호합니다.

## <a name="request-access"></a>액세스 요청

Speech Devices SDK에 액세스하려면 다음을 수행합니다.

1. Microsoft Speech Devices SDK [등록 양식](https://aka.ms/sdsdk-signup)으로 이동합니다.
1. [사용권 계약](speech-devices-sdk-license.md)을 검토하세요.
1. 사용권 계약 조건에 동의하는 경우 **동의함**을 선택합니다.
1. 양식의 질문에 대답합니다.
1. 양식을 제출합니다.
1. 이메일 주소가 아직 Azure AD(Active Directory)에 포함되지 않은 경우 액세스가 승인되면 다음 예제와 같은 초대 이메일을 받게 됩니다. 이메일 주소가 Azure AD에 이미 있는 경우 액세스가 승인되면 Microsoft Speech 팀으로부터 이메일 메시지를 받고 [Speech Devices SDK 다운로드](#download-the-speech-devices-sdk)로 건너뛸 수 있습니다.

## <a name="approval-e-mail"></a>승인 메일

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com>
Subject: You're invited to the Microsoft organization
```

![메일 메시지](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>액세스 허용

다음 단계를 수행하여 등록 시 제공한 이메일 주소를 Azure AD와 결합합니다. 이 프로세스는 Speech Devices SDK [다운로드 사이트](https://shares.datatransfer.microsoft.com/)에 대한 액세스 권한을 부여합니다.

1. 받은 이메일 메시지에서 **시작**을 선택합니다. 조직이 이미 Office 365 고객이면 로그인하라는 메시지가 표시되며 7단계로 건너뛸 수 있습니다.

2. 브라우저 창이 열리면 **다음**을 선택합니다.

    ![인증 창](media/speech-devices-sdk/get-sdk-2.png)

3. 아직 계정이 없는 경우 Microsoft 계정을 만듭니다. 받은 초대 이메일과 동일한 이메일 주소를 입력합니다.

    ![Microsoft 계정 만들기](media/speech-devices-sdk/get-sdk-3.png)

4. **다음**을 선택하여 암호를 만듭니다.

5. 이메일을 확인하라는 메시지가 표시되면 받은 초대 이메일에서 확인 코드를 받습니다.

7. 이메일 메시지의 보안 코드를 대화 상자에 붙여넣거나 입력합니다. 이 예에서 보안 코드는 **8406**입니다. **다음**을 선택합니다.

    ![이메일 확인](media/speech-devices-sdk/get-sdk-6.png)

8. 브라우저에 액세스 패널 응용 프로그램이 표시되면 이메일 주소가 Azure AD의 일부임이 확인된 것입니다. 이제 Speech Devices SDK 다운로드 사이트에 액세스할 수 있습니다.

## <a name="download-the-speech-devices-sdk"></a>Speech Devices SDK 다운로드

[Speech 장치 SDK 다운로드 사이트](https://shares.datatransfer.microsoft.com/)로 이동합니다. 앞에서 만든 Microsoft 계정으로 로그인합니다.

![SDK 다운로드 사이트](media/speech-devices-sdk/get-sdk-7.png)

Speech Devices SDK, 관련 샘플 코드 및 참조 자료를 다운로드하려면 다음을 수행합니다.

1. 브라우저에 메시지가 표시되면 Aspera Connect 도구를 다운로드하여 설치합니다.

    ![Aspera Connect 다운로드](media/speech-devices-sdk/get-sdk-8.png)

1. **예**를 선택하여 앱을 Aspera Connect로 전환합니다.

    ![Aspera Connect로 전환](media/speech-devices-sdk/get-sdk-9.png)

1. **허용**을 선택하여 Aspera Connect를 통한 파일 다운로드를 확인합니다.

    ![Aspera Connect를 통한 다운로드](media/speech-devices-sdk/get-sdk-10.png)

1. 파일이 다운로드되면 [Aspera Connect 전송] 창을 닫습니다.

    ![Aspera Connect 전송 창](media/speech-devices-sdk/get-sdk-11.png)

기본적으로 파일은 **다운로드** 폴더로 다운로드됩니다. 이제 이 사이트에서 로그아웃할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech Devices SDK 시작](speech-devices-sdk-qsg.md)
