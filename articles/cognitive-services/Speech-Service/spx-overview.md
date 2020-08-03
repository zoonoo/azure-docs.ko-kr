---
title: Azure Speech CLI
titleSuffix: Azure Cognitive Services
description: 음성 CLI는 코드를 작성 하지 않고도 음성 서비스를 사용 하는 명령줄 도구입니다. 음성 CLI에는 최소 설정이 필요 하며, 음성 서비스의 주요 기능 실험을 바로 시작 하 여 사용 사례가 충족 될 수 있는지 확인 하는 것이 쉽습니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.custom: devx-track-azurecli
ms.openlocfilehash: ab019250e03feb9a776d628c06e792b884252260
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501817"
---
# <a name="what-is-the-speech-cli"></a>Speech CLI란?

음성 CLI는 코드를 작성 하지 않고도 음성 서비스를 사용 하는 명령줄 도구입니다. 음성 CLI에는 최소의 설정이 필요 하며, 음성 서비스의 주요 기능 실험을 바로 시작 하 여 사용 사례가 충족 될 수 있는지 확인 하는 것이 쉽습니다. 몇 분 내에 파일 디렉터리에서 일괄 처리 음성 인식과 같은 간단한 테스트 워크플로를 실행 하거나 파일에서 문자열 컬렉션에 대 한 텍스트-음성 변환 등의 간단한 테스트 워크플로를 실행할 수 있습니다. 간단한 워크플로 외에도, 음성 CLI는 프로덕션에 사용할 수 있으며 자동화 된 또는 셸 스크립트를 사용 하 여 더 큰 프로세스를 실행 하도록 확장할 수 있습니다 `.bat` .

Speech SDK의 대부분의 기본 기능은 음성 CLI에서 사용할 수 있지만, 일부 고급 기능 및 사용자 지정은 음성 CLI에서 간소화 되었습니다. 음성 CLI 또는 Speech SDK를 사용할 시기를 결정 하려면 다음 지침을 고려 하세요.

다음 경우에 음성 CLI를 사용 합니다.
* 최소한의 설정 및 코드 없이 음성 서비스 기능을 시험해 볼 수 있습니다.
* 음성 서비스를 사용 하는 프로덕션 응용 프로그램에 대 한 요구 사항이 비교적 단순 합니다.

다음과 같은 경우 Speech SDK를 사용 합니다.
* 특정 언어 또는 플랫폼 내에서 음성 서비스 기능을 통합 하려고 합니다 (예: c #, Python, c + +).
* 고급 서비스 요청이 필요할 수 있는 복잡 한 요구 사항이 있거나 응답 스트리밍을 비롯 한 사용자 지정 동작을 개발 하는 경우

## <a name="core-features"></a>핵심 기능

* 음성 인식-음성 텍스트를 오디오 파일에서 직접 또는 마이크에서 직접 변환 하거나 기록 된 대화를 높여줄 변환 합니다.

* 음성 합성-텍스트 파일의 입력 또는 명령줄에서 직접 입력을 사용 하 여 텍스트를 음성으로 변환 합니다. [SSML 구성과](speech-synthesis-markup.md) [표준 또는 신경망](speech-synthesis-markup.md#standard-neural-and-custom-voices)을 사용 하 여 음성 출력 특성을 사용자 지정 합니다.

* 음성 번역-소스 언어로 오디오를 대상 언어로 텍스트로 변환 합니다.

* Azure 계산 리소스에서 실행-를 사용 하 여 Azure 원격 계산 리소스에서 실행 되도록 SPX 명령을 보냅니다 `spx webjob` .

## <a name="get-started"></a>시작하기

음성 CLI를 시작 하려면 [기본 사항 문서](spx-basics.md)를 참조 하세요. 이 문서에서는 SPX를 사용 하 여 몇 가지 기본 명령을 실행 하는 방법을 보여 주고 음성 텍스트 및 텍스트 음성 변환에 대해 일괄 처리 작업을 실행 하기 위한 약간 더 고급 명령도 보여 줍니다. 기본 문서를 읽은 후에는 몇 가지 사용자 지정 명령을 작성 하거나 간단한 음성 작업을 자동화 하기 위해 SPX 구문을 충분히 이해 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Speech CLI 기본 사항](spx-basics.md)
- 사용 사례가 더 복잡 한 경우 [음성 SDK를 다운로드 하세요](speech-sdk.md) .
