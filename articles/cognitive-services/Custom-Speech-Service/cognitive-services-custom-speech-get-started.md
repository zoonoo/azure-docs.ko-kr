---
title: Azure에서 Custom Speech Service 시작 | Microsoft Docs
description: Custom Speech Service를 구독하고 서비스 활동 모델을 Azure 구독에 연결하여 모델을 학습하고 배포를 수행합니다.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: bf674261a58aab4fee37920d12ce6a2ac54b58b8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975786"
---
# <a name="get-started-with-custom-speech-service"></a>Custom Speech Service 시작

Custom Speech Service의 주요 기능을 탐색하고 응용 프로그램 요구 사항에 대한 어쿠스틱 및 언어 모델을 빌드, 배포 및 사용하는 방법을 알아봅니다. Custom Speech Service 포털에 등록하면 더 광범위한 설명서 및 단계별 지침을 이용할 수 있습니다.

## <a name="samples"></a>샘플  
[여기](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)에 사용자가 수행할 수 있는 좋은 샘플이 있습니다.

## <a name="prerequisites"></a>필수 조건  

### <a name="subscribe-to-custom-speech-service-and-get-a-subscription-key"></a>Custom Speech Service에 가입하고 구독 키를 가져옵니다.
위의 예제를 재생하려면 Custom Speech Service에 가입하고 구독 키를 가져와야 합니다. [구독](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/CustomSpeech)을 참조하거나 [여기](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-subscribe.md)에 나온 설명을 수행합니다. 기본 키와 보조 키 모두 이 자습서에서 사용할 수 있습니다. API 키 비밀 및 보안 유지를 위해 모범 사례를 수행해야 합니다.

### <a name="get-the-client-library-and-example"></a>클라이언트 라이브러리 및 예제 가져오기
[SDK](https://www.microsoft.com/cognitive-services/en-us/SDK-Sample?api=bing%20speech&category=sdk)를 통해 클라이언트 라이브러리 및 예제를 다운로드할 수 있습니다. 다운로드한 zip 파일은 사용자가 선택한 폴더에 추출되어야 하며, 많은 사용자가 Visual Studio 2015 폴더를 선택합니다.

## <a name="creating-a-custom-acoustic-model"></a>사용자 지정 어쿠스틱 모델 만들기
특정 도메인에 어쿠스틱 모델을 사용자 지정하려면 음성 데이터의 컬렉션이 필요합니다. 이 컬렉션은 음성 데이터의 오디오 파일 집합과 각 오디오 파일의 전사에 대한 텍스트 파일로 구성됩니다. 오디오 데이터는 인식기를 사용하고자 하는 시나리오를 대표해야 합니다.

예: 시끄러운 공장 환경에서 음성을 더 잘 식별하도록 하려면 오디오 파일은 잡음이 있는 공장에서 시끄러운 공장에서 말하는 사람으로 구성되어야 합니다.
가령 FDR의 Fireside Chats을 모두 전사하려는 경우와 같이 단일 스피커의 성능 최적화에 관심이 있으면 오디오 파일은 해당 스피커의 많은 예제로만 구성되어야 합니다.

[여기](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-acoustic-model.md) 사용자 지정 어쿠스틱 모델을 만드는 방법에서 자세한 설명을 확인할 수 있습니다.

## <a name="creating-a-custom-language-model"></a>사용자 지정 언어 모델 만들기
사용자 지정 언어 모델을 만드는 절차는 오디오 데이터가 없고 텍스트만 있다는 점 외에는 어쿠스틱 모델을 만드는 것과 비슷합니다. 텍스트는 사용자가 말할 것으로 예상되는 여러 가지 질문이나 발언으로 구성되거나, 응용 프로그램에 사용자의 말(또는 타이핑)을 기록해야 합니다.

[여기](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-language-model.md)에서 사용자 지정 언어 모델을 만드는 방법에 관한 자세한 설명을 확인할 수 있습니다.

## <a name="creating-a-custom-speech-to-text-endpoint"></a>사용자 지정 음성-텍스트 엔드포인트 만들기
사용자 지정 어쿠스틱 모델 및/또는 언어 모델을 만들면 사용자 지정 음성-텍스트 엔드포인트에 배포할 수 있습니다. 새로운 사용자 지정 엔드포인트를 만들려면 페이지 상단에 있는 “CRIS” 메뉴에서 “배포”를 클릭합니다. 그러면 현재 사용자 지정 엔드포인트의 “배포” 라는 테이블로 이동합니다. 아직 엔드포인트를 만들지 않은 경우 테이블은 비어있게 됩니다. 현재 로캘이 테이블 제목에 반영됩니다. 다른 언어에 대한 배포를 만들려는 경우 “로캘 변경”을 클릭합니다. 지원되는 언어에 대한 추가 정보는 로캘 변경 섹션에서 확인할 수 있습니다.

[여기](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md)에서 사용자 지정 음성-텍스트 엔드포인트를 만드는 방법에 관한 자세한 설명을 확인할 수 있습니다.

## <a name="using-a-custom-speech-endpoint"></a>사용자 지정 음성 엔드포인트 사용
Microsoft Cognitive Services 음성 엔드포인트와 매우 유사한 방법으로 CRIS 음성-텍스트 엔드포인트에 요청을 보낼 수 있습니다. 이러한 엔드포인트는 기능적으로 Speech API의 기본 엔드포인트와 동일합니다. 따라서 클라이언트 라이브러리 또는 Speech API에 대한 REST API를 통해 사용할 수 있는 동일한 기능은 사용자 지정 엔드포인트에서도 사용할 수 있습니다.

[여기](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-use-endpoint.md)에서 사용자 지정 음성-텍스트 엔드포인트를 사용하는 방법에 관한 자세한 설명을 확인할 수 있습니다.


CRIS를 통해 만든 엔드포인트는 구독이 연결된 계층에 따라 다양한 동시 요청 수를 처리할 수 있습니다. 인식이 요청된 것보다 더 많으면 오류 코드 429(너무 많은 요청)가 반환됩니다. 자세한 내용은 [가격 책정 정보](https://www.microsoft.com/cognitive-services/en-us/pricing)를 참조하세요. 또한 평가판 계층에 대한 요청의 월별 할당량도 있습니다. 월별 할당량을 초과하는 체험판 계층의 엔드포인트에 액세스하는 경우 서비스에서 오류 코드 403 사용 권한 없음을 반환합니다.

서비스는 오디오가 실시간으로 전송된다고 가정합니다. 더 빠르게 전송되면 요청은 실제 해당 기간이 지나갈 때까지 실행 중인 것으로 간주됩니다.

* [개요](cognitive-services-custom-speech-home.md)
* [FAQ](cognitive-services-custom-speech-faq.md)
* [용어](cognitive-services-custom-speech-glossary.md)
