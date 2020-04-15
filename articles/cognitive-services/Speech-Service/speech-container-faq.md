---
title: 음성 서비스 컨테이너 자주 묻는 질문(FAQ)
titleSuffix: Azure Cognitive Services
description: 음성 컨테이너를 설치하고 실행합니다. 음성-텍스트는 응용 프로그램, 도구 또는 장치가 사용하거나 표시할 수 있는 오디오 스트림을 실시간으로 텍스트로 전사합니다. 텍스트 음성 변환은 입력 텍스트를 인간과 유사한 합성 음성으로 변환합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: aahi
ms.openlocfilehash: 17582244aef173da6ac700c980f7bd7fb0fec307
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383092"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>음성 서비스 컨테이너 자주 묻는 질문(FAQ)

컨테이너와 함께 음성 서비스를 사용하는 경우 지원하도록 에스컬레이션하기 전에 자주 묻는 질문 모음에 의존합니다. 이 문서에서는 일반에서 기술까지 다양한 수준의 질문을 캡처합니다. 답변을 확장하려면 질문을 클릭합니다.

## <a name="general-questions"></a>일반적인 질문

<details>
<summary>
<b>음성 컨테이너는 어떻게 작동하고 어떻게 설정합니까?</b>
</summary>

**답변:** 프로덕션 클러스터를 설정할 때 고려해야 할 몇 가지 사항이 있습니다. 첫째, 동일한 컴퓨터에서 단일 언어, 여러 컨테이너를 설정하는 것은 큰 문제가 되지 않습니다. 문제가 발생하면 하드웨어 관련 문제일 수 있으므로 먼저 리소스를 살펴보겠습니다. CPU 및 메모리 사양.

잠시, `ja-JP` 컨테이너 및 최신 모델을 고려하십시오. 어쿠스틱 모델은 CPU에서 가장 까다로운 반면 언어 모델은 가장 많은 메모리를 요구합니다. 우리가 사용을 벤치 마킹 할 때, 오디오가 (마이크에서 등) 실시간으로 흐르는 경우 하나의 음성 - 텍스트 요청을 처리하는 데 약 0.6 CPU 코어가 걸립니다. 오디오를 실시간(예: 파일)보다 빠르게 공급하는 경우 해당 사용량이 두 배(1.2배 코어)될 수 있습니다. 한편, 아래에 나열된 메모리는 음성 디코딩을 위한 작동 메모리입니다. 파일 캐시에 상주할 언어 모델의 실제 전체 크기를 고려하지 *않습니다.* `ja-JP` 2GB가 추가로 추가로 제공됩니다. 에 `en-US`대해 더 많은 것일 수 있습니다(6-7 GB).

메모리가 부족한 컴퓨터가 있고 여러 언어를 배포하려는 경우 파일 캐시가 가득 찼을 수 있으며 OS에서 모델을 페이지 로 내보내야 합니다. 실행 중인 전사의 경우 재앙이 될 수 있으며 속도 저하 및 기타 성능 에 영향을 미칠 수 있습니다.

또한 [고급 벡터 확장(AVX2)](speech-container-howto.md#advanced-vector-extension-support) 명령 세트를 갖춘 기계에 대한 실행 가능한 사전 패키지입니다. AVX512 명령어 집합이 있는 컴퓨터에는 해당 대상에 대한 코드 생성이 필요하며 10개 언어에 대해 10개의 컨테이너를 시작하면 CPU가 일시적으로 소모될 수 있습니다. 이와 같은 메시지가 도커 로그에 나타납니다.

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

마지막으로 변수를 사용하여 `DECODER MAX_COUNT` *단일* 컨테이너 내에서 원하는 디코더 수를 설정할 수 있습니다. 그래서, 기본적으로, 우리는 당신의 SKU (CPU / 메모리)로 시작해야하고, 우리는 그것을 최대한 얻을 수있는 방법을 제안 할 수 있습니다. 좋은 출발점은 권장호스트 컴퓨터 리소스 사양을 참조하는 것입니다.

<br>
</details>

<details>
<summary>
<b>온프레미 스피치 컨테이너의 용량 계획 및 비용 추정에 도움을 줄 수 있습니까?</b>
</summary>

**답변:** 일괄 처리 모드에서 컨테이너 용량의 경우 각 디코더는 단일 인식을 위해 두 개의 CPU 코어로 실시간으로 2-3x를 처리할 수 있습니다. 컨테이너 인스턴스당 두 개 이상의 동시 인식을 유지하는 것은 권장되지 않지만 부하 분산 기 뒤에 안정성/가용성 이유로 컨테이너 인스턴스를 더 많이 실행하는 것이 좋습니다.

각 컨테이너 인스턴스가 더 많은 디코더로 실행될 수 있지만. 예를 들어 컨테이너 인스턴스당 7개의 디코더를 8개의 코어 머신(각각 2배 이상)에 설정하여 15배의 처리량을 생성할 수 있습니다. 주의해야 할 `DECODER_MAX_COUNT` 매개 변수가 있습니다. 극단적인 경우 처리량이 크게 증가하면서 안정성 및 대기 시간 문제가 발생합니다. 마이크의 경우 실시간으로 1회 작동합니다. 전체 사용량은 단일 인식의 경우 약 1코어여야 합니다.

배치 처리 모드에서 하루 1K/일 처리 시나리오의 경우 극단적인 경우 3개의 VM이 24시간 이내에 처리할 수 있지만 보장되지는 않습니다. 스파이크 일, 장애 조치, 업데이트 및 최소 백업/BCP를 제공하려면 클러스터당 3대가 아닌 4~5대의 컴퓨터를 처리하고 2개 이상의 클러스터를 사용할 것을 권장합니다.

하드웨어의 경우 표준 Azure `DS13_v2` VM을 참조로 사용합니다(각 코어는 2.6GHz 이상이어야 하며 AVX2 명령집합이 활성화되어 있어야 함).

| 인스턴스  | vCPU(들) | RAM    | 임시 저장 | AHB로 종량제 | AHB를 가진 1년 예비금 (% 저축) | AHB(% 저축) 3년 예약 |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112GiB      | 시간당 0.598 달러            | 시간당 $0.3528 (~41%)                 | 시간당 $0.2333 (~61%)                  |

설계 참조(1K 시간/일 오디오 배치 처리를 처리하는 5VM 클러스터 2개)에 따라 1년 하드웨어 비용이 듭니다.

> 2(클러스터) * 5(클러스터당 VM) * 0.3528/시간 * 365(일) * 24(시간) = $31K/년

물리적 컴퓨터에 매핑할 때 일반적인 추정은 1vCPU = 1 물리적 CPU 코어입니다. 실제로 1vCPU는 단일 코어보다 더 강력합니다.

온프레미의 경우, 이러한 모든 추가 요소가 작용합니다.

- 실제 CPU의 유형과 코어 수
- 동일한 상자/컴퓨터에서 함께 실행되는 CPU 수
- VM 설정 방법
- 하이퍼 스레딩/멀티 스레딩 사용 방법
- 메모리 공유 방법
- OS 등

일반적으로 Azure 환경으로 잘 조정되지 않습니다. 다른 오버 헤드를 고려하면 안전한 추정은 10 개의 물리적 CPU 코어 = 8 Azure vCPU라고 말할 것입니다. 인기 있는 CPU는 8개의 코어만 가지고 있습니다. 온프레미스 배포를 사용하면 Azure VM을 사용하는 것보다 비용이 더 높습니다. 또한 감가 상각률을 고려하십시오.

서비스 비용은 온라인 서비스와 동일합니다: 음성-텍스트의 경우 시간당 $1입니다. 음성 서비스 비용은 다음과 입니다.

> $1 * 1000 * 365 = $365K

Microsoft에 지불하는 유지 관리 비용은 서비스의 서비스 수준 및 콘텐츠에 따라 다릅니다. 그것은 다양 한 $29.99 기본 수준에 대 한 달 에서 수십만 관련 된 사이트 서비스. 대략적인 숫자는 서비스/유지 보수를 위해 시간당 $300입니다. 인원 비용은 포함되어 있지 않습니다. 다른 인프라 비용(예: 저장소, 네트워크 및 로드 밸로드)은 포함되지 않습니다.

<br>
</details>

<details>
<summary>
<b>전사에서 문장 부호가 누락된 이유는 무엇입니까?</b>
</summary>

**답변:** 탄소 `speech_recognition_language=<YOUR_LANGUAGE>` 클라이언트를 사용하는 경우 요청에서 명시적으로 구성해야 합니다.

다음은 그 예입니다.

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
출력은 다음과 같습니다.

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>음성 컨테이너에서 사용자 지정 어쿠스틱 모델 및 언어 모델을 사용할 수 있습니까?</b>
</summary>

현재 사용자 지정 언어 모델 또는 사용자 지정 어쿠스틱 모델 중 하나만 하나의 모델 ID만 전달할 수 있습니다.

**답변:** 어쿠스틱 모델과 언어 모델을 동시에 지원하지 *않기로* 결정했습니다. 이는 API 중단을 줄이기 위해 통합 식별자를 만들 때까지 유효합니다. 그래서, 불행히도 이것은 지금 지원되지 않습니다.

<br>
</details>

<details>
<summary>
<b>사용자 지정 음성-텍스트 컨테이너에서 이러한 오류를 설명할 수 있습니까?</b>
</summary>

**오류 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**답변 1:** 최신 사용자 지정 모델로 교육하는 경우 현재 지원하지 않습니다. 이전 버전으로 훈련하는 경우 사용할 수 있어야 합니다. 우리는 여전히 최신 버전을 지원하기 위해 노력하고 있습니다.

기본적으로 사용자 지정 컨테이너는 핼리산 또는 ONNX 기반 음향 모델을 지원하지 않습니다(사용자 지정 학습 포털의 기본값). 이는 사용자 지정 모델이 암호화되지 않았기 때문에 ONNX 모델을 노출하고 싶지 않습니다. 언어 모델은 괜찮습니다. 고객은 사용자 지정 교육을 위해 이전 비 ONNX 모델을 명시적으로 선택해야 합니다. 정확도는 영향을 받지 않습니다. 모델 크기는 더 클 수 있습니다(100MB).

> 지원 모델 > 20190220 (v4.5 통합)

**오류 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**답변 2:** 대/소문자를 구분하는 요청에 올바른 음성 이름을 제공해야 합니다. 전체 서비스 이름 매핑을 참조하십시오. 현재 텍스트 `en-US-JessaRUS`음성 `en-US-JessaNeural` 변환의 컨테이너 버전에서는 사용할 수 없는 대로 를 사용해야 합니다.

**오류 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**답변 3:** 코그너티브 서비스 리소스가 아닌 음성 리소스를 만들려면 대명사입니다.


<br>
</details>

<details>
<summary>
<b>어떤 API 프로토콜이 지원되며 REST 또는 WS?</b>
</summary>

**답변:** 음성-텍스트 및 사용자 지정 음성-텍스트 컨테이너의 경우 현재 웹소켓 기반 프로토콜만 지원합니다. SDK는 WS에서만 호출을 지원하지만 REST는 지원하지 않습니다. REST 지원을 추가할 계획이 있지만 현재로서는 ETA가 아닙니다. 항상 공식 문서를 참조, [쿼리 예측 끝점을](speech-container-howto.md#query-the-containers-prediction-endpoint)참조하십시오.

<br>
</details>

<details>
<summary>
<b>CentOS는 음성 컨테이너에 지원되고 있습니까?</b>
</summary>

**답변:** CentOS 7은 아직 파이썬 SDK에서 지원되지 않으며 우분투 19.04는 지원되지 않습니다.

Python Speech SDK 패키지는 다음과 같은 운영 체제용으로 제공됩니다.
- **윈도우** - x64 및 x86
- **맥** - 맥 OS X 버전 10.12 이상
- **리눅스** - 우분투 16.04, 우분투 18.04, 데비안 9 에 x64

환경 설정에 대한 자세한 내용은 [Python 플랫폼 설정을](quickstarts/setup-platform.md?pivots=programming-language-python)참조하십시오. 지금은, 우분투 18.04 권장 된 버전입니다.

<br>
</details>

<details>
<summary>
<b>LUIS 예측 끝점을 호출하려고 할 때 오류가 있는 이유는 무엇입니까?</b>
</summary>

IoT Edge 배포에서 LUIS 컨테이너를 사용하고 있으며 다른 컨테이너에서 LUIS 예측 끝점을 호출하려고 합니다. LUIS 컨테이너가 포트 5001에서 수신 대기 중이며 사용 중이던 URL은 다음과 같은 것입니다.

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

내가 얻는 오류는 다음과 다 됩니다.

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

LUIS 컨테이너 로그에서 요청이 표시되고 메시지가 표시됩니다.

```cmd
The request path /luis//predict" does not match a supported file type.
```

무슨 의미인가요? 내가 놓친 것은 무엇입니까? 나는 [여기에서](https://github.com/Azure-Samples/cognitive-services-speech-sdk)음성 SDK에 대한 예를 따르고 있었다. 시나리오는 PC 마이크에서 직접 오디오를 감지하고 학습한 LUIS 앱을 기반으로 의도를 결정하려고 하는 것입니다. 내가 연결 한 예제는 정확히 그렇게합니다. 또한 LUIS 클라우드 기반 서비스와 잘 작동합니다. Speech SDK를 사용하면 음성-텍스트 API에 대해 별도의 명시적 호출을 한 다음 LUIS에 대한 두 번째 호출을 할 필요가 없습니다.

따라서 클라우드에서 LUIS를 사용하는 시나리오에서 LUIS 컨테이너를 사용하는 시나리오로 전환하기만 하면 됩니다. 스피치 SDK가 하나용으로 작동하면 다른 하나는 작동하지 않을 것이라고 상상할 수 없습니다.

**답변:** 음성 SDK는 LUIS 컨테이너에 대해 사용해서는 안 됩니다. LUIS 컨테이너를 사용 하 여 LUIS SDK 또는 LUIS REST API를 사용 해야 합니다. 음성 SDK는 음성 컨테이너에 사용해야 합니다.

클라우드는 컨테이너와 다릅니다. 클라우드는 여러 집계된 컨테이너(마이크로 서비스라고도 함)로 구성될 수 있습니다. 따라서 LUIS 컨테이너가 있고 음성 컨테이너가 있습니다 - 두 개의 별도 컨테이너. 음성 컨테이너는 음성만 수행합니다. LUIS 컨테이너는 LUIS만 수행합니다. 클라우드에서는 두 컨테이너가 모두 배포되는 것으로 알려져 있고 원격 클라이언트가 클라우드로 이동하고 음성을 수행한 다음 다시 클라우드로 이동한 다음 LUIS를 수행하는 것은 성능이 좋지 않기 때문에 클라이언트가 음성으로 이동하고 클라우드에 머무르고 LUIS로 이동한 다음 클라이언트로 돌아갈 수 있는 기능을 제공합니다. 따라서 이 시나리오에서도 음성 SDK는 오디오가 있는 음성 클라우드 컨테이너로 이동한 다음 Speech 클라우드 컨테이너가 텍스트가 있는 LUIS 클라우드 컨테이너와 통신합니다. LUIS 컨테이너는 오디오를 수락하는 개념이 없습니다(LUIS 컨테이너가 스트리밍 오디오를 수락하는 것은 의미가 없습니다 - LUIS는 텍스트 기반 서비스입니다). 온 프레미스를 사용하면 고객이 두 컨테이너를 모두 배포했는지 확신할 수 없으며, 고객 의 구내에 있는 컨테이너 간에 오케스트레이션을 할 수 없으며, 두 컨테이너가 클라이언트에 더 로컬인 경우 SR을 먼저 클라이언트로 이동한 다음 고객이 해당 텍스트를 가져가LUIS로 이동하도록 하는 것은 부담이 되지 않습니다.

<br>
</details>

<details>
<summary>
<b>macOS, 스피치 컨테이너 및 Python SDK에 오류가 있는 이유는 무엇입니까?</b>
</summary>

전사할 .wav 파일을 보내면 다음과 같은 결과가 *나옵니다.*

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

웹소켓이 올바르게 설정되어 있다는 것을 알고 있습니다.

**답변:** 이 경우 [이 GitHub 문제를](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310)참조하십시오. 우리는 [여기에 제안](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)된 해결 방법을 가지고 있습니다.

카본은 버전 1.8에서 이를 고정했습니다.


<br>
</details>

<details>
<summary>
<b>Speech 컨테이너 끝점의 차이점은 무엇입니까?</b>
</summary>

테스트할 기능및 SDK 및 REST API를 테스트하는 방법을 포함하여 다음 테스트 메트릭을 채우는 데 도움이 될 수 있습니까? 특히, "대화 형"과 "대화"의 차이, 나는 기존의 문서 / 샘플에서 볼 수 없습니다.

| 엔드포인트                                                | 기능 테스트                                                   | SDK) | REST API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | 텍스트 합성(텍스트 음성 변환)                                  |     | 예      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | 코그너티브 서비스 온-프레임 받아쓰기 v1 웹소켓 엔드포인트        | 예 | 예       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | 코그너티브 서비스 온프레미 인터랙티브 v1 웹소켓 엔드포인트  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | 사전 대화 v1 웹소켓 엔드포인트에 대한 인지 서비스 |     |          |

**답변:** 이것은 다음과 같은 융합입니다.
- 컨테이너에 대한 받아쓰기 끝점을 시도하는 사람들(해당 URL을 어떻게 얻었는지 잘 모르겠습니다).
- 컨테이너에 있는<sup>1st</sup> 파티 끝점입니다.
- 메시지 대신 `speech.hypothesis` <sup>3rd</sup> 부품 끝점이 받아쓰기 끝점에 대해 반환하는<sup>1st</sup> 파티 끝점 입니다.
- 카본 퀵스타트 `RecognizeOnce` 모든 사용(대화형 모드)
- 탄소는 필요한 메시지에 `speech.fragment` 대해 대화형 모드에서 반환되지 않는다는 어설션을 가합니다.
- 탄소는 방출 빌드에서 불을 (프로세스를 죽이는)를 주장한다.

해결 방법은 코드에서 연속 인식을 사용 하 여 전환 하거나 (더 빠른) 컨테이너에서 대화형 또는 연속 끝점에 연결 합니다.
코드의 경우 끝점을 <호스트:포트>/음성/인식/대화형/인지 서비스/v1로 설정합니다.

다양한 모드는 음성 모드를 참조하십시오 .

[!INCLUDE [speech-modes](includes/speech-modes.md)]

적절한 수정은 온 - prem 지원이 SDK 1.8와 함께 오고있다 (올바른 엔드 포인트를 선택합니다, 그래서 우리는 온라인 서비스보다 더 나쁘지 않을 것입니다). 그 동안, 지속적인 인식을위한 샘플이있다, 왜 우리는 그것을 가리키지?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>다양한 오디오 파일에 어떤 모드를 사용해야 합니까?</b>
</summary>

**답변:** 다음은 [파이썬을 사용하는 빠른 시작입니다.](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python) 문서 사이트에서 연결된 다른 언어를 찾을 수 있습니다.

대화형, 대화 및 받아쓰기에 대해 명확히 하기만 하면 됩니다. 이는 서비스에서 음성 요청을 처리하는 특정 방법을 지정하는 고급 방법입니다. 안타깝게도 온프레미 컨테이너의 경우 전체 URI를 지정해야 하므로 로컬 컴퓨터가 포함되어 있으므로 이 정보가 추상화에서 유출됩니다. 우리는 SDK 팀과 협력하여 미래에 이를 더 쉽게 사용할 수 있도록 하고 있습니다.

<br>
</details>

<details>
<summary>
<b>트랜잭션/초/코어의 대략적인 척도를 어떻게 벤치마킹할 수 있습니까?</b>
</summary>

**답변:** 다음은 기존 모델에서 기대할 수있는 대략적인 숫자 중 일부입니다 (GA에서 출시 할 모델에서 더 나은 방향으로 변경됩니다).

- 파일의 경우 스로틀링은 음성 SDK에서 2x로 표시됩니다. 오디오의 처음 5초는 제한되지 않습니다. 디코더는 약 3배 의 실시간 작업을 수행할 수 있습니다. 이를 위해, 전체 CPU 사용량은 거의 될 것입니다 2 단일 인식에 대한 코어.
- 마이크의 경우, 그것은 1 배 실시간으로될 것입니다. 전체 사용량은 단일 인식의 경우 약 1코어여야 합니다.

이 모든 것을 docker 로그에서 확인할 수 있습니다. 실제로 세션 및 구/발언 통계와 함께 줄을 덤프하며 RTF 번호가 포함됩니다.


<br>
</details>

<details>
<summary>
<b>음성 컨테이너 사용을 위해 오디오 파일을 척으로 분할하는 것이 일반적입니까?</b>
</summary>

내 현재 계획은 기존 오디오 파일을 10 초 청크로 분할하여 컨테이너를 통해 보내는 것입니다. 허용 가능한 시나리오입니까?  컨테이너로 더 큰 오디오 파일을 처리하는 더 좋은 방법이 있습니까?

**답변:** 그냥 음성 SDK를 사용하고 파일을 제공, 그것은 옳은 일을 할 것입니다. 파일을 청크해야 하는 이유는 무엇입니까?


<br>
</details>

<details>
<summary>
<b>동일한 호스트에서 여러 컨테이너를 실행하려면 어떻게 해야 합니까?</b>
</summary>

문서는 내가하는 다른 포트를 노출하라고 말하지만 LUIS 컨테이너는 여전히 포트 5000에서 수신 대기 중입니까?

**답변:** 을 `-p <outside_unique_port>:5000`사용해 보십시오. `-p 5001:5000`)을 입력합니다.


<br>
</details>

## <a name="technical-questions"></a>기술적 질문

<details>
<summary>
<b>15초 길이의 오디오를 &lt;처리하기 위해 비배치 API를 얻으려면 어떻게 해야 합니까?</b>
</summary>

**답변:** `RecognizeOnce()` 대화형 모드에서는 발언이 짧을 것으로 예상되는 음성 명령 모드를 위한 모드이기 때문에 최대 15초의 오디오만 처리합니다. 받아쓰기 `StartContinuousRecognition()` 나 대화에 사용하는 경우 15 초 제한이 없습니다.


<br>
</details>

<details>
<summary>
<b>권장 되는 리소스, CPU 및 RAM은 무엇입니까; 50개의 동시 요청에 대해?</b>
</summary>

4 코어, 4 GB RAM 핸들은 얼마나 많은 동시 요청입니까? 예를 들어 50개의 동시 요청을 제공해야 하는 경우 얼마나 많은 코어 및 RAM이 권장되는가?

**답변:** 실시간으로 8최신 `en-US`이므로 6 개의 동시 요청 을 넘어 더 많은 docker 컨테이너를 사용 하는 것이 좋습니다. 16코어를 넘어서는 광란이 생기고, 불균일한 메모리 액세스(NUMA) 노드에 민감하게 됩니다. 다음 표는 각 Speech 컨테이너에 대한 최소 및 권장 리소스 할당에 대해 설명합니다.

# <a name="speech-to-text"></a>[음성 텍스트 변환](#tab/stt)

| 컨테이너      | 최소             | 권장         |
|----------------|---------------------|---------------------|
| 음성 텍스트 변환 | 2코어, 2GB 메모리 | 4코어, 4GB 메모리 |

# <a name="custom-speech-to-text"></a>[사용자 지정 음성-텍스트](#tab/cstt)

| 컨테이너             | 최소             | 권장         |
|-----------------------|---------------------|---------------------|
| 사용자 지정 음성-텍스트 | 2코어, 2GB 메모리 | 4코어, 4GB 메모리 |

# <a name="text-to-speech"></a>[텍스트 음성 변환](#tab/tts)

| 컨테이너      | 최소             | 권장         |
|----------------|---------------------|---------------------|
| 텍스트 음성 변환 | 1 코어, 2GB 메모리 | 2 코어, 3GB 메모리 |

# <a name="custom-text-to-speech"></a>[사용자 지정 텍스트 음성 변환](#tab/ctts)

| 컨테이너             | 최소             | 권장         |
|-----------------------|---------------------|---------------------|
| 사용자 지정 텍스트 음성 변환 | 1 코어, 2GB 메모리 | 2 코어, 3GB 메모리 |

***

- 각 코어는 2.6GHz 이상이어야 합니다.
- 파일의 경우 스로틀링은 음성 SDK에 2x(오디오의 처음 5초는 제한되지 않음)에 있습니다.
- 디코더는 약 2-3배 의 실시간 작업을 수행할 수 있습니다. 이를 위해, 전체 CPU 사용량은 단일 인식에 대한 두 개의 코어에 가깝습니다. 따라서 컨테이너 인스턴스당 두 개 이상의 활성 연결을 유지하지 않는 것이 좋습니다. 극단적 인 측면은 같은 `DS13_V2`8 코어 머신에서 2 배 실시간으로 약 10 디코더를 넣어하는 것입니다. 컨테이너 버전 1.3 이상에는 설정을 `DECODER_MAX_COUNT=20`시도할 수 있는 매개 변수가 있습니다.
- 마이크의 경우 실시간으로 1회 작동합니다. 전체 사용량은 단일 인식의 경우 약 1코어여야 합니다.

총 오디오 시간을 고려합니다. 안정성/가용성을 향상시키기 위해 숫자가 큰 경우 로드 밸런서 뒤에 단일 상자 또는 여러 상자에 더 많은 컨테이너 인스턴스를 실행하는 것이 좋습니다. 오케스트레이션은 Kubernetes (K8S)와 헬름을 사용하거나 Docker 작곡을 사용하여 수행 할 수 있습니다.

예를 들어 1000시간/24시간을 처리하기 위해 VM당 인스턴스/디코더가 10개있는 3-4VM을 설정하려고 했습니다.

<br>
</details>

<details>
<summary>
<b>음성 컨테이너가 문장 부호를 지원합니까?</b>
</summary>

**답변:** 우리는 온 프레임 컨테이너에서 사용할 수있는 대문자 (ITN)가 있습니다. 구두점은 언어에 따라 다르며 중국어및 일본어를 포함한 일부 언어에서는 지원되지 않습니다.

기존 *컨테이너에* 대한 암시적 및 기본 구두점 지원이 `off` 있지만 기본적으로 적용됩니다. 즉, 예제에서 `.` 문자를 얻을 수 있지만 문자는 `。` 얻을 수 없습니다. 이 암시적 논리를 사용하려면 음성 SDK를 사용하여 파이썬에서 수행하는 방법의 예가 있습니다 (다른 언어에서는 비슷합니다).

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>음성-텍스트 컨테이너에 데이터를 POST하려고 할 때 404 오류가 있는 이유는 무엇입니까?</b>
</summary>

다음은 HTTP POST 예제입니다.

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**답변:** 음성-텍스트 컨테이너에서 REST API를 지원하지 않으며 음성 SDK를 통해서만 WebSocket을 지원합니다. 항상 공식 문서를 참조, [쿼리 예측 끝점을](speech-container-howto.md#query-the-containers-prediction-endpoint)참조하십시오.

<br>
</details>

<details>
<summary>
<b>음성-텍스트 서비스를 사용할 때 이 오류가 발생되는 이유는 무엇입니까?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**답변:** 일반적으로 음성 인식 컨테이너보다 더 빠르게 오디오를 공급할 때 발생합니다. 클라이언트 버퍼가 채워지고 취소가 트리거됩니다. 오디오를 보내는 동시성과 RTF를 제어해야 합니다.

<br>
</details>

<details>
<summary>
<b>C++ 예제에서 이러한 텍스트 음성 변환 컨테이너 오류를 설명할 수 있습니까?</b>
</summary>

**답변:** 컨테이너 버전이 1.3보다 오래된 경우 이 코드를 사용해야 합니다.

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

이전 컨테이너에는 `FromHost` Api와 함께 작동하는 데 필요한 끝점이 없습니다. 버전 1.3에 사용되는 컨테이너의 경우 다음 코드를 사용해야 합니다.

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

다음은 API를 `FromEndpoint` 사용하는 예입니다.

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 이 `SetSpeechSynthesisVoiceName` 함수는 업데이트된 텍스트 음성 변환 엔진이 있는 컨테이너에 음성 이름이 필요하기 때문에 호출됩니다.

<br>
</details>

<details>
<summary>
<b>음성 컨테이너와 함께 음성 SDK의 v1.7을 사용해 사용해 두면 어떻게 해야 합니까?</b>
</summary>

**답변:** 음성 컨테이너에는 서로 다른 용도에 대한 세 가지 끝점이 있으며, 음성 모드로 정의됩니다.

[!INCLUDE [speech-modes](includes/speech-modes.md)]

그들은 다른 목적을 위해 이며 다르게 사용 됩니다.

파이썬 [샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py):
- 사용자 지정 끝점(즉, 단일 인식 모드)의 경우 `SpeechConfig` 끝점 매개 변수를 `speech_recognize_once_from_file_with_custom_endpoint_parameters()`사용 하 고 을 참조하십시오.
- 연속 인식(대화 모드)을 위해 위와 같이 사용자 지정 `speech_recognize_continuous_from_file()`끝점을 사용하도록 수정하기만 하면 을 참조하십시오.
- 위의 와 같은 샘플에서 받아쓰기를 활성화하려면 (실제로 필요한 경우에만) `speech_config`생성 `speech_config.enable_dictation()`직후에 코드를 추가합니다.

받아쓰기를 활성화하려면 C#에서 함수를 `SpeechConfig.EnableDictation()` 호출합니다.

### <a name="fromendpoint-apis"></a>`FromEndpoint`Api
| 언어 | API 세부 정보 |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | 현재 지원되지 않으며 계획되지도 않습니다. |

<br>
</details>

<details>
<summary>
<b>음성 컨테이너와 함께 음성 SDK의 v1.8을 사용해 사용해 두면 어떻게 해야 합니까?</b>
</summary>

**답변:** 새로운 `FromHost` API가 있습니다. 기존 API를 대체하거나 수정하지는 않습니다. 사용자 지정 호스트를 사용하여 음성 구성을 만드는 다른 방법을 추가합니다.

### <a name="fromhost-apis"></a>`FromHost`Api

| 언어 | API 세부 정보 |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | 현재 지원되지 않음 |

> 매개 변수: 호스트(필수), 구독 키(서비스를 사용하지 않고 사용할 수 있는 경우 선택 사항).

호스트의 `protocol://hostname:port` `:port` 형식은 선택 사항입니다(아래 참조).
- 컨테이너가 로컬로 실행 중인 경우 `localhost`호스트 이름은 입니다.
- 컨테이너가 원격 서버에서 실행 중인 경우 해당 서버의 호스트 이름 또는 IPv4 주소를 사용합니다.

음성-텍스트에 대한 호스트 매개 변수 예제:
- `ws://localhost:5000`- 포트 5000을 사용하여 로컬 컨테이너에 안전하지 않은 연결
- `ws://some.host.com:5000`- 원격 서버에서 실행 중인 컨테이너에 대한 비보안 연결

위에서 파이썬 샘플, `host` 하지만 대신 `endpoint`매개 변수를 사용 :

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [코그너티브 서비스 컨테이너](speech-container-howto.md)
