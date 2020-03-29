---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73484101"
---
컨테이너에는 다음과 같은 구성 설정이 있습니다.

|필수|설정|목적|
|--|--|--|
|yes|[ApiKey](#apikey-configuration-setting)|청구 정보를 추적합니다.|
|예|[애플리케이션 인사이트](#applicationinsights-setting)|컨테이너에 [Azure 응용 프로그램 인사이트](https://docs.microsoft.com/azure/application-insights) 원격 분석 지원을 추가할 수 있습니다.|
|yes|[청구](#billing-configuration-setting)|Azure에서 서비스 리소스의 엔드포인트 URI를 지정합니다.|
|yes|[Eula](#eula-setting)| 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.|
|예|[Fluentd](#fluentd-settings)|선택적으로 메트릭 데이터를 Fluentd 서버에 기록합니다.|
|예|HTTP 프록시|아웃바운드 요청을 하기 위해 HTTP 프록시를 구성합니다.|
|예|[로깅](#logging-settings)|컨테이너에 대한 ASP.NET Core 로깅 지원을 제공합니다. |
|예|[Mounts](#mount-settings)|호스트 컴퓨터에서 컨테이너로, 컨테이너에서 호스트 컴퓨터로 데이터를 다시 읽고 씁니다.|
