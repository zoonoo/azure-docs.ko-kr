---
title: 오디오 콘텐츠 생성-음성 서비스
titleSuffix: Azure Cognitive Services
description: 오디오 콘텐츠 만들기는 앱 및 제품에 대 한 Microsoft의 텍스트 음성 변환 출력을 사용자 지정 하 고 미세 조정할 수 있는 온라인 도구입니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 7d4d1acac591bfe6ce40efcf6166357d43718363
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024962"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>오디오 콘텐츠 생성 도구를 사용 하 여 합성 향상

[오디오 콘텐츠 생성](https://aka.ms/audiocontentcreation) 은 다양 한 시나리오 (예: 오디오 서적, 뉴스 브로드캐스트, 비디오 설명 및 채팅 봇)에 대해 매우 자연 스러운 오디오 콘텐츠를 빌드할 수 있는 사용 하기 쉽고 강력한 도구입니다. 오디오 콘텐츠를 만드는 경우 텍스트 음성 변환 음성을 미세 조정 하 고 효율적이 고 저렴 한 방식으로 사용자 지정 된 오디오 환경을 디자인할 수 있습니다.

도구는 [SSML (음성 합성 마크업 언어)](speech-synthesis-markup.md)을 기반으로 합니다. 음성 문자, 음성 스타일, 말하는 속도, 발음 및 prosody와 같은 실시간 또는 일괄 처리 합성에서 텍스트 음성 변환 출력 특성을 조정할 수 있습니다.

고성능 신경망을 포함 하 여 150 개 이상의 미리 작성 된 50 음성에 대 한 액세스를 제공 하 고, 사용자 지정 음성을 구축한 경우 사용자 지정 음성을 만들 수 있습니다. 

오디오 콘텐츠를 만들기 위한 [비디오 자습서](https://www.youtube.com/watch?v=O1wIJ7mts_w) 를 참조 하세요.

## <a name="how-to-get-started"></a>시작 하는 방법

오디오 콘텐츠 만들기는 무료 도구 이지만 사용 하는 Azure Speech service에 대 한 비용을 지불 합니다. 도구로 작업 하려면 Azure 계정으로 로그인 하 여 음성 리소스를 만들어야 합니다. 각 Azure 계정에 대 한 월간 무료 음성 할당량은 신경망의 신경망에 대해 50만 문자, 표준 및 사용자 지정 음성 (월)의 500만 문자 및 사용자 지정 음성 끝점 호스팅 서비스 (월 당)를 포함 합니다. 매월 할당 된 금액은 일반적으로 3-5 명의 사용자를 위한 소규모 콘텐츠 팀에 게 충분 합니다. Azure 계정을 만들고 음성 리소스를 가져오는 방법에 대 한 단계는 다음과 같습니다. 

### <a name="step-1---create-an-azure-account"></a>1 단계-Azure 계정 만들기

오디오 콘텐츠 생성을 사용 하려면 [Microsoft 계정](https://account.microsoft.com/account) 및 [Azure 계정이](https://azure.microsoft.com/free/ai/)있어야 합니다. [계정을 설정](./overview.md#try-the-speech-service-for-free)하려면 다음 지침을 따르세요. 

[Azure Portal](https://portal.azure.com/) 은 Azure 계정을 관리할 수 있는 중앙 집중식입니다. 음성 리소스를 만들고, 제품 액세스를 관리 하 고, 간단한 웹 앱에서 복잡 한 클라우드 배포까지 모든 것을 모니터링할 수 있습니다. 

### <a name="step-2---create-a-speech-resource"></a>2 단계-음성 리소스 만들기

Azure 계정에 등록 한 후에는 음성 서비스에 액세스 하기 위해 Azure 계정에서 음성 리소스를 만들어야 합니다. [음성 리소스를 만드는 방법](./overview.md#create-the-azure-resource)에 대 한 지침을 확인 합니다. 

새 Speech 리소스를 배포하는 데 몇 분 정도 걸립니다. 배포가 완료 되 면 오디오 콘텐츠 생성 경험을 시작할 수 있습니다. 

 >[!NOTE]
   > 신경망을 사용할 계획인 경우 [신경망을 지 원하는 지역](regions.md#standard-and-neural-voices)에서 리소스를 만들어야 합니다.
 
### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>3 단계-Azure 계정 및 음성 리소스를 사용 하 여 오디오 콘텐츠 생성에 로그인

1. Azure 계정과 음성 리소스를 가져온 후 **시작** 을 클릭 하 여 [오디오 콘텐츠 생성](https://aka.ms/audiocontentcreation) 에 로그인 할 수 있습니다.
2. **음성 리소스** 페이지가 표시 됩니다. 작업 하려는 음성 리소스를 선택 합니다. **스튜디오로 이동** 을 클릭 하 여 오디오 만들기를 시작 합니다. **새로 만들기** 를 클릭 하 여 새 음성 리소스를 만들 수도 있습니다. 다음 번에 오디오 콘텐츠 생성 도구에 로그인 할 때 현재 음성 리소스 아래의 오디오 작업 파일에 직접 연결 됩니다. 
3. 위쪽 탐색 창에 있는 **설정** 옵션을 사용 하 여 언제 든 지 음성 리소스를 수정할 수 있습니다.

## <a name="how-to-use-the-tool"></a>도구를 사용 하는 방법

이 다이어그램에서는 텍스트 음성 변환 출력을 미세 조정 하는 데 필요한 단계를 보여 줍니다. 각 단계에 대 한 자세한 내용을 보려면 아래 링크를 사용 하세요.

:::image source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="텍스트 음성 변환 출력을 미세 조정 하는 데 필요한 단계 다이어그램":::


1. 작업 하려는 음성 리소스를 선택 합니다.
2. 일반 텍스트 또는 SSML 스크립트를 사용 하 여 [오디오 튜닝 파일을 만듭니다](#create-an-audio-tuning-file) . 오디오 콘텐츠 생성에 콘텐츠를 입력 하거나 업로드 합니다.
3. 스크립트 콘텐츠에 대 한 음성 및 언어를 선택 합니다. 오디오 콘텐츠 생성에는 모든 [Microsoft 텍스트 음성 변환 음성이](language-support.md#text-to-speech)포함 됩니다. 표준, 신경망 또는 고유한 사용자 지정 음성을 사용할 수 있습니다.
   >[!NOTE]
   > 제어 된 액세스는 사용자 지정 신경망에 사용할 수 있으며,이를 통해 자연 스런 음성 처럼 고화질 음성을 만들 수 있습니다. 자세한 내용은 [제어 프로세스](./text-to-speech.md)를 참조 하세요.

4. **재생** 아이콘 (삼각형)을 클릭 하 여 기본 합성 출력을 미리 봅니다. 그런 다음 발음, 나누기, 피치, 요율, intonation, 음성 스타일 등을 조정 하 여 출력을 향상 시킵니다. 전체 옵션 목록은 [음성 합성 마크업 언어](speech-synthesis-markup.md)를 참조 하세요. 오디오 콘텐츠를 생성 하 여 음성 출력을 미세 조정 하는 방법을 보여 주는 [비디오](https://www.youtube.com/watch?v=O1wIJ7mts_w) 는 다음과 같습니다. 
5. [튜닝 오디오를](#export-tuned-audio)저장 하 고 내보냅니다. 시스템에서 튜닝 트랙을 저장할 때 계속 해 서 작업 하 고 출력을 반복할 수 있습니다. 출력에 만족 하는 경우 내보내기 기능을 사용 하 여 오디오 만들기 작업을 만들 수 있습니다. 내보내기 작업의 상태를 관찰 하 고 앱 및 제품에 사용할 출력을 다운로드할 수 있습니다.

## <a name="create-an-audio-tuning-file"></a>오디오 튜닝 파일 만들기

오디오 콘텐츠 생성 도구에 콘텐츠를 가져오는 방법에는 두 가지가 있습니다.

**옵션 1:**

1. 오른쪽 위에서 **새 파일** 아이콘을 클릭 하 여 새 오디오 튜닝 파일을 만듭니다.
2. 콘텐츠를 편집 창에 입력 하거나 붙여 넣습니다. 각 파일의 문자는 최대 2만입니다. 스크립트가 2만 자 보다 길면 옵션 2를 사용 하 여 콘텐츠를 자동으로 여러 파일로 분할할 수 있습니다. 
3. 저장 하는 것을 잊지 마세요.

**옵션 2:**

1. **업로드** 를 클릭 하 여 하나 이상의 텍스트 파일을 가져옵니다. 일반 텍스트와 SSML은 모두 지원 됩니다. 스크립트 파일이 2만 자를 초과 하는 경우 문자 또는 정규식을 기준으로 파일을 단락으로 분할 하세요. 
3. 텍스트 파일을 업로드 하는 경우 파일이 이러한 요구 사항을 충족 하는지 확인 합니다.

   | 속성 | 값/메모 |
   |----------|---------------|
   | 파일 형식 | 일반 텍스트(.txt)<br/> SSML 텍스트 (.txt)<br/> Zip 파일은 지원 되지 않습니다. |
   | 인코딩 형식 | UTF-8 |
   | 파일 이름 | 각 파일의 이름은 고유 해야 합니다. 중복은 지원 되지 않습니다. |
   | 텍스트 길이 | 텍스트 파일의 문자 제한은 2만입니다. 파일이 제한을 초과 하는 경우 도구에 있는 지침을 사용 하 여 파일을 분할 하세요. |
   | SSML 제한 사항 | 각 SSML 파일은 SSML의 단일 부분을 포함할 수 있습니다. |

**일반 텍스트 예**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```
**SSML 텍스트 예**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>튜닝 오디오 내보내기

오디오 출력을 검토 하 고 조정 및 조정에 만족 하는 경우 오디오를 내보낼 수 있습니다.

1. **내보내기** 를 클릭 하 여 오디오 만들기 작업을 만듭니다. 긴 오디오 출력과 전체 오디오 출력 환경을 지원 하므로 **오디오 라이브러리로 내보내기가** 권장 됩니다. 로컬 디스크에 직접 오디오를 다운로드할 수도 있지만 처음 10 분 동안만 사용할 수 있습니다.
2. 튜닝 된 오디오의 출력 형식을 선택 합니다. 지원 되는 형식 및 샘플 속도 목록은 아래에서 볼 수 있습니다.
3. 작업 **내보내기** 탭에서 작업 상태를 볼 수 있습니다. 태스크가 실패 하는 경우 전체 보고서에 대 한 자세한 정보 페이지를 참조 하세요.
4. 작업이 완료 되 면 오디오 **라이브러리** 탭에서 오디오를 다운로드할 수 있습니다.
5. **다운로드** 를 클릭합니다. 이제 앱 또는 제품에서 사용자 지정 조정 된 오디오를 사용할 준비가 되었습니다.

**지원되는 오디오 형식**

| 서식 | 16Khz 샘플 주기 | 24khz 샘플 요금 |
|--------|--------------------|--------------------|
| wav | riff-16khz-16 비트 | riff-24khz-16 비트 |
| mp3 | 오디오-16khz-128kbitrate 전송률-mono-mp3 | 오디오-24khz-160kbitrate 전송률-mono-mp3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>오디오 콘텐츠 생성 사용자를 추가/제거 하는 방법

둘 이상의 사용자가 오디오 콘텐츠 생성을 사용 하려는 경우 Azure 계정과 암호를 사용자와 공유 하거나 Azure 구독 및 음성 리소스에 대 한 사용자 액세스 권한을 부여할 수 있습니다. Azure 구독에 사용자를 추가 하는 경우 사용자는 Azure 구독에 있는 모든 리소스에 액세스할 수 있습니다. 하지만 사용자를 음성 리소스에 추가 하는 경우에만 사용자가 음성 리소스에 액세스할 수 있으며,이 Azure 구독에서 다른 리소스에 액세스할 수 없습니다. 음성 리소스에 대 한 액세스 권한이 있는 사용자는 오디오 콘텐츠 생성을 사용할 수 있습니다.

### <a name="add-users-to-a-speech-resource"></a>음성 리소스에 사용자 추가

오디오 콘텐츠 생성을 사용할 수 있도록 사용자를 음성 리소스에 추가 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 **인지 서비스** 를 검색 하 고, 사용자를 추가 하려는 음성 리소스를 선택 합니다.
2. **액세스 제어(IAM)** 를 클릭합니다. **역할 할당** 탭을 클릭하여 이 구독의 모든 역할 할당을 봅니다.
    :::image source="media/audio-content-creation/access-control-roles.png" alt-text="역할 할당 탭":::
1. **추가** > **역할 할당 추가** 를 클릭하여 역할 할당 추가 창을 엽니다. 역할 드롭다운 목록에서 **인지 서비스 사용자** 역할을 선택 합니다. 사용자에 게이 음성 리소스의 소유권을 부여 하려면 **소유자** 역할을 선택할 수 있습니다.
1. 목록에서 사용자를 선택합니다. 목록에 사용자가 표시 되지 않으면 선택 상자에를 입력 하 여 디렉터리에서 표시 이름 및 전자 메일 주소를 검색할 수 있습니다. 사용자가이 디렉터리에 없는 경우 Azure active directory에서 신뢰할 수 있는 사용자의 [Microsoft 계정](https://account.microsoft.com/account) 를 입력할 수 있습니다.
1. **저장** 을 클릭하여 역할을 할당합니다. 몇 분 후에 사용자에 게 음성 리소스 범위에서 인식 서비스 사용자 역할이 할당 됩니다.

    :::image source="media/audio-content-creation/add-role-first.png" alt-text="역할 추가 대화 상자":::

1. 추가한 사용자에 게 초대 전자 메일이 수신 됩니다. 초대 수락 동의 **Accept invitation**  >  **를 클릭 하 여 Azure에 가입** 하면 [오디오 콘텐츠 생성](https://aka.ms/audiocontentcreation)을 사용할 수 있습니다.

동일한 음성 리소스에 있는 사용자는 오디오 콘텐츠 생성 스튜디오에서 서로의 작업을 볼 수 있습니다. 개별 사용자가 오디오 콘텐츠를 만들 때 고유한 개인 작업 공간을 갖도록 하려면 각 사용자에 대 한 [새 음성 리소스를 만들고](#step-2---create-a-speech-resource) 각 사용자에 게 음성 리소스에 대 한 고유한 액세스를 제공 하세요. 

### <a name="remove-users-from-a-speech-resource"></a>음성 리소스에서 사용자 제거
1. Azure Portal에서 **인지 서비스** 를 검색 하 고, 사용자를 제거할 음성 리소스를 선택 합니다.
2. **액세스 제어(IAM)** 를 클릭합니다. 이 음성 리소스에 대 한 모든 역할 할당을 보려면 **역할 할당** 탭을 클릭 합니다.
3. 제거 하려는 사용자를 선택 하 고 확인 **제거** 를 클릭  >  **Ok** 합니다.
    :::image source="media/audio-content-creation/remove-user.png" alt-text="제거 단추":::

### <a name="enable-users-to-grant-access"></a>사용자가 액세스 권한을 부여할 수 있도록 설정
사용자 중 한 명에 게 다른 사용자에 대 한 액세스 권한을 부여 하려면 사용자에 게 음성 리소스에 대 한 소유자 역할을 부여 하 고 사용자를 Azure 디렉터리 판독기로 설정 해야 합니다. 
1. 사용자를 음성 리소스의 소유자로 추가 합니다. [음성 리소스에 사용자를 추가 하는 방법을](#add-users-to-a-speech-resource)참조 하세요.
    :::image source="media/audio-content-creation/add-role.png" alt-text="역할 소유자 필드":::
1. 왼쪽 위에서 축소 된 메뉴를 선택 합니다. **Azure Active Directory** 를 클릭 한 다음 **사용자** 를 클릭 합니다.
1. 사용자의 Microsoft 계정 검색 하 고 사용자의 세부 정보 페이지로 이동 합니다. **할당 된 역할** 을 클릭 합니다.
1. **할당 추가**  ->  **디렉터리 판독기** 를 클릭 합니다.

## <a name="see-also"></a>추가 정보

* [긴 오디오 API](./long-audio-api.md)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
