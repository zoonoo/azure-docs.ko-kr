---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 4e3b87055246c3060ab9a35e6a112782275633ae
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035530"
---
## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음을 수행합니다.

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech 리소스 만들기<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [개발 환경을 설정하고 빈 프로젝트 만들기](../../../../quickstarts/setup-platform.md)

## <a name="create-a-new-website-folder"></a>새 웹 사이트 폴더 만들기

빈 폴더를 새로 만듭니다. 웹 서버에 샘플을 호스트하려면 웹 서버가 폴더에 액세스할 수 있는지 확인합니다.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>JavaScript용 Speech SDK를 해당 폴더에 압축 해제합니다.

Speech SDK를 [.zip 패키지](https://aka.ms/csspeech/jsbrowserpackage)로 다운로드하여 새로 만든 폴더에 압축 해체합니다. 이로 인해 두 파일은 `microsoft.cognitiveservices.speech.sdk.bundle.js` 및 `microsoft.cognitiveservices.speech.sdk.bundle.js.map`으로 압축 해제됩니다.
후자의 파일은 선택 사항이며, SDK 코드로 디버깅하는 데 유용합니다.

## <a name="create-an-indexhtml-page"></a>Index.html 페이지 만들기

폴더에 `index.html`라는 새 파일을 만들고 텍스트 편집기에서 이 파일을 엽니다.

1. 다음 HTML 기본 구조를 만듭니다.

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-tts.html)]

## <a name="create-the-token-source-optional"></a>토큰 소스 만들기(선택 사항)

웹 서버에 웹 페이지를 호스트하려는 경우 필요에 따라 데모 애플리케이션의 토큰 소스를 제공하면 됩니다.
이 방식을 사용하면 사용자가 권한 부여 코드를 직접 입력하지 않고 음성 기능을 사용할 수 있으면서도 구독 키가 절대 서버를 벗어나지 않습니다.

이름이 `token.php`인 새 파일을 만듭니다. 이 예제에서는 웹 서버에서 curl이 활성화된 PHP 스크립팅 언어를 지원한다고 가정하겠습니다. 다음 코드를 입력합니다.

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> 권한 부여 토큰의 수명은 제한되어 있습니다.
> 이 간단한 예제에서는 권한 부여 토큰을 자동으로 새로 고치는 방법을 보여주지 않습니다. 사용자는 수동으로 페이지를 다시 로드하거나 F5 키를 눌러 새로 고칠 수 있습니다.

## <a name="build-and-run-the-sample-locally"></a>로컬로 샘플을 빌드 및 실행

앱을 시작하려면 index.html 파일을 두 번 클릭하거나 선호하는 웹 브라우저를 사용하여 index.html을 엽니다. 구독 키 및 [지역](../../../../regions.md)을 입력하고 입력 테스트의 합성을 트리거할 수 있는 간단한 GUI가 제공됩니다.

## <a name="build-and-run-the-sample-via-a-web-server"></a>웹 서버를 통해 샘플 빌드 및 실행

앱을 시작하려면 즐겨 찾는 웹 브라우저를 열고 폴더를 호스트하는 공용 URL을 가리킨 다음, [지역](../../../../regions.md)을 입력하고 입력 테스트의 합성을 트리거합니다. 구성된 경우 토큰 소스에서 토큰을 획득합니다.


## <a name="next-steps"></a>다음 단계

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]