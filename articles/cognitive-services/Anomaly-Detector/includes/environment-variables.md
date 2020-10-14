---
title: Anomaly Detector 환경 변수
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/30/2020
ms.author: mbullwin
ms.openlocfilehash: 74e5373ebafbd39748f8b002069eaa6d732be069
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018479"
---
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