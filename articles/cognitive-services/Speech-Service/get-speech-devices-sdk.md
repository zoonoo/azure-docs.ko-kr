---
title: 음성 장치 SDK 가져오기
description: Speech Devices SDK에 대한 액세스 권한을 얻는 방법을 알아봅니다.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: f70b41cd7e3a7a6eddf32ae6ad024fa9ac040f29
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281785"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Cognitive Services Speech Devices SDK 가져오기

## <a name="requesting-access"></a>액세스 권한 요청

Speech Devices SDK가 제한된 미리 보기로 제공되므로 프로그램에 등록해야 합니다. 현재 Microsoft는 이 제품에 액세스할 수 있는 후보로 대기업을 선호합니다.

Speech Devices SDK에 대한 액세스 권한을 얻으려면 다음 단계를 따릅니다.

1. Microsoft Speech Devices SDK [등록 양식](https://aka.ms/sdsdk-signup)으로 이동합니다.
1. [사용권 계약](speech-devices-sdk-license.md)을 검토하세요.
1. 이 사용권 계약 조건에 동의하는 경우 “동의함”을 선택합니다.
1. 양식의 질문에 대답합니다.
1. 양식을 제출합니다. 
1. 메일 주소가 아직 Azure Active Directory에 포함되지 않은 경우 승인 시 아래와 같은 초대 메일을 받게 됩니다. 메일 주소가 이미 Azure Active Directory에 포함된 경우 승인 시 Microsoft Speech 팀에서 메일 메시지를 받게 되고 [Speech Devices SDK 다운로드](#download-the-speech-devices-sdk)로 건너뛸 수 있습니다.

## <a name="approval-e-mail"></a>승인 메일

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com> 
Subject: You're invited to the Microsoft organization 
```

![메일 메시지](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>액세스 허용
아래 단계를 수행하여 등록하는 동안 제공한 메일 주소로 Azure Active Directory에 연결합니다. 이 프로세스는 Speech Devices SDK의 [다운로드 사이트](https://shares.datatransfer.microsoft.com/)에 대한 액세스 권한을 부여합니다.

1. 받은 메일 메시지에서 **시작**을 클릭합니다. 조직이 이미 Office 365 고객이면 로그인하라는 메시지가 표시되며 8단계로 건너뛸 수 있습니다.

2. 시작된 브라우저 창에서 **다음**을 클릭합니다.

    ![인증 창](media/speech-devices-sdk/get-sdk-2.png)

3. 아직 계정이 없는 경우 Microsoft 계정을 만듭니다. 위의 6단계에서 초대 메일을 받은 동일한 메일 주소를 입력합니다.

    ![Microsoft 계정 만들기](media/speech-devices-sdk/get-sdk-3.png)

4. **다음**을 클릭하여 암호를 만듭니다.

5. 메일을 확인하라는 메시지가 표시되면 메일 받은 편지함으로 돌아가서 자신에게 전송된 확인 코드를 확인합니다.
 
7. 메일 메시지의 보안 코드를 대화 상자에 붙여넣거나 입력합니다. 이 예제에서는 “8406”입니다. 그런 후 **Next** 를 클릭합니다.

    ![메일 확인](media/speech-devices-sdk/get-sdk-6.png)
 
8. 브라우저 창에 액세스 패널 응용 프로그램이 표시되면 6단계의 메일 주소가 이제 Azure Active Directory의 일부임을 확인한 것입니다. 이제 Speech Devices SDK 다운로드 사이트에 액세스할 수 있습니다.

## <a name="download-the-speech-devices-sdk"></a>Speech Devices SDK 다운로드

[Speech Devices SDK 다운로드 사이트](https://shares.datatransfer.microsoft.com/)로 이동하여 이전에 만든 Microsoft 계정으로 로그인합니다. 이제 다음 단계에 따라 Speech Devices SDK, 관련 샘플 코드 및 참조 자료를 다운로드할 수 있습니다.

![sdk 다운로드 사이트](media/speech-devices-sdk/get-sdk-7.png)

1. 브라우저에 메시지가 표시되면 Aspera Connect 도구를 다운로드하여 설치합니다.

    ![Aspera Connect 다운로드](media/speech-devices-sdk/get-sdk-8.png)
 
1. **예**를 클릭하여 Aspera Connect로 전환합니다.

    ![Aspera Connect로 전환](media/speech-devices-sdk/get-sdk-9.png)
 
1. **허용**을 클릭하여 Aspera Connect를 사용한 파일 다운로드를 확인합니다.

    ![Aspera Connect로 다운로드](media/speech-devices-sdk/get-sdk-10.png)
 
1. 파일을 다운로드한 후에 Aspera Connect의 [전송] 창을 닫습니다.

    ![Aspera Connect 전송 창](media/speech-devices-sdk/get-sdk-11.png)
 
기본적으로 파일은 **다운로드** 폴더로 다운로드됩니다. 이제 이 사이트에서 로그아웃할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech Devices SDK 시작](speech-devices-sdk-qsg.md)
