---
title: 원격 모니터링 솔루션의 장치 스키마 - Azure | Microsoft Docs
description: 이 문서에서는 원격 모니터링 솔루션에서 시뮬레이션된 장치를 정의하는 JSON 스키마에 대해 설명합니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c153153313511640f7969938f63ea9fbe7b0847c
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282654"
---
# <a name="understand-the-device-model-schema"></a>장치 모델 스키마 이해

원격 모니터링 솔루션에서 시뮬레이션된 장치를 사용하여 해당 동작을 테스트할 수 있습니다. 원격 모니터링 솔루션을 배포하면 시뮬레이션된 장치 컬렉션이 자동으로 프로비전됩니다. 기존 시뮬레이션된 장치를 사용자 지정하거나 고유한 시뮬레이션된 장치를 만들 수 있습니다.

이 문서에서는 시뮬레이션된 장치의 기능 및 동작을 지정하는 장치 모델 스키마에 대해 설명합니다. 장치 모델은 JSON 파일에 저장됩니다.

다음 문서는 현재 문서와 관련이 있습니다.

* [장치 모델 동작 구현](iot-accelerators-remote-monitoring-device-behavior.md)에서는 시뮬레이션된 장치의 동작을 구현하는 데 사용하는 JavaScript 파일에 대해 설명합니다.
* [새 시뮬레이션된 장치 만들기](iot-accelerators-remote-monitoring-test.md)에서는 장치를 만들고 새 시뮬레이션된 장치 유형을 솔루션에 배포하는 방법을 보여 줍니다.

이 문서에서는 다음 방법을 설명합니다.

>[!div class="checklist"]
> * JSON 파일을 사용하여 시뮬레이션된 장치 모델 정의
> * 시뮬레이션된 장치의 속성 지정
> * 시뮬레이션된 장치가 전송하는 원격 분석 지정
> * 장치가 응답하는 클라우드-장치 메서드 지정

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>다음 단계

이 문서에서는 고유한 사용자 지정 시뮬레이션된 장치 모델을 만드는 방법을 설명했습니다. 이 문서에서는 다음을 수행하는 방법을 보여 주었습니다.

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * JSON 파일을 사용하여 시뮬레이션된 장치 모델 정의
> * 시뮬레이션된 장치의 속성 지정
> * 시뮬레이션된 장치가 전송하는 원격 분석 지정
> * 장치가 응답하는 클라우드-장치 메서드 지정

이제 JSON 스키마에 대해 학습했으므로 다음 단계에서는 [시뮬레이션된 장치의 동작을 구현](iot-accelerators-remote-monitoring-device-behavior.md)하는 방법을 알아보는 것이 좋습니다.

원격 모니터링 솔루션에 대한 자세한 개발자 정보는 다음을 참조하세요.

* [개발자 참조 가이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [개발자 문제 해결 가이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
