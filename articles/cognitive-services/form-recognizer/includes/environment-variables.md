---
title: 환경 변수
description: 환경 변수 설정
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: d63e902a59411a549235c955a39d7dbc4be068ba
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156534"
---
만든 리소스의 키 및 엔드포인트를 사용하여 인증을 위한 두 가지 환경 변수를 만듭니다.

* `FORM_RECOGNIZER_KEY` - 요청을 인증하기 위한 리소스 키입니다.
* `FORM_RECOGNIZER_ENDPOINT` - API 요청을 보내기 위한 리소스 엔드포인트입니다. 다음과 같이 표시됩니다. 
  * `https://<your-custom-subdomain>.cognitiveservices.azure.com`

>[!NOTE]
> 2019년 7월 1일 이후에 생성된 리소스의 엔드포인트는 아래에 표시된 사용자 지정 하위 도메인 형식을 사용합니다. 자세한 내용 및 지역별 엔드포인트의 전체 목록은 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)을 참조하세요. 

운영 체제에서 환경 변수를 설정하려면 다음 지침을 따르세요.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FORM_RECOGNIZER_KEY <replace-with-your-form-recognizer-key>
setx FORM_RECOGNIZER_ENDPOINT <replace-with-your-form-recognizer-endpoint>
```

환경 변수를 추가한 후 콘솔 창을 닫았다가 다시 엽니다.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

환경 변수를 추가한 후에는 콘솔 창에서 `source ~/.bashrc` 명령을 실행하여 변경 내용을 적용합니다.

#### <a name="macos"></a>[macOS](#tab/unix)

`.bash_profile`을 편집하고, 환경 변수를 추가합니다.

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

환경 변수를 추가한 후 콘솔 창에서 `source .bash_profile` 명령을 실행하여 변경 내용을 적용합니다.
***
