---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: 5089af4a4e1714d49b844a1b6823487a3f6a8dcf
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "74483017"
---
아래 Azure 리소스 중 하나를 만들어 Anomaly Detector 서비스 사용을 시작합니다.

* <a href="https://azure.microsoft.com/try/cognitive-services/#decision" target="_blank" rel="noopener">평가판 리소스 만들기(새 탭에서 열림)</a>
    * Azure 구독이 필요하지 않습니다. 
    * 평가판은 7일 동안 유효합니다. 등록 후 [Azure 웹 사이트](https://azure.microsoft.com/try/cognitive-services/my-apis/)에서 평가판 키 및 엔드포인트를 사용할 수 있습니다. 
    * 이 옵션은 Anomaly Detector를 시도하지만 Azure 구독이 없는 경우에 유용합니다.

* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank" rel="noopener">Anomaly Detector 리소스 만들기(새 탭에서 열림)</a>:
    * 리소스를 삭제할 때까지 Azure Portal을 통해 사용할 수 있습니다.
    * 평가판 가격 책정 계층을 사용하여 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드합니다.



### <a name="create-an-environment-variable"></a>환경 변수 만들기

>[!NOTE]
> 2019년 7월 1일 이후에 생성된 비평가판 리소스의 엔드포인트는 아래에 표시된 사용자 지정 하위 도메인 형식을 사용합니다. 자세한 내용 및 지역별 엔드포인트의 전체 목록은 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)을 참조하세요. 

만든 리소스의 키 및 엔드포인트를 사용하여 인증을 위한 두 가지 환경 변수를 만듭니다.

* `ANOMALY_DETECTOR_KEY` - 요청을 인증하기 위한 리소스 키입니다.
* `ANOMALY_DETECTOR_ENDPOINT` - API 요청을 보내기 위한 리소스 엔드포인트입니다. 다음과 같이 표시됩니다. 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

운영 체제에 대한 지침을 사용합니다.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

환경 변수를 추가한 후 콘솔 창을 다시 시작합니다.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

환경 변수를 추가한 후에는 콘솔 창에서 `source ~/.bashrc` 명령을 실행하여 변경 내용을 적용합니다.

#### <a name="macos"></a>[macOS](#tab/unix)

`.bash_profile`을 편집하고, 환경 변수를 추가합니다.

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

환경 변수를 추가한 후에는 콘솔 창에서 `source .bash_profile` 명령을 실행하여 변경 내용을 적용합니다.

***