---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 46376477aae9f94c1c8f6e1dd6bc718d213cc373
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712594"
---
컨테이너는 다음 구성 설정은 다음과 같습니다.

|필수|설정|용도|
|--|--|--|
|예|[ApiKey](#apikey-configuration-setting)|청구 정보를 추적 합니다.|
|아니요|[ApplicationInsights](#applicationinsights-setting)|추가 사용 하도록 설정 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 컨테이너에 원격 분석 지원.|
|예|[Billing](#billing-configuration-setting)|Azure에서 서비스 리소스의 엔드포인트 URI를 지정합니다.|
|예|[Eula](#eula-setting)| 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.|
|아니요|[Fluentd](#fluentd-settings)|로그 쓰기 및 필요에 따라 Fluentd 서버로 메트릭 데이터입니다.|
|아니요|Http 프록시|아웃 바운드 요청에 대 한 HTTP 프록시를 구성 합니다.|
|아니요|[Logging](#logging-settings)|컨테이너에 대한 ASP.NET Core 로깅 지원을 제공합니다. |
|예|[Mounts](#mount-settings)|읽기 및 쓰기 데이터 컨테이너 및 컨테이너에 호스트 컴퓨터에서 다시 호스트 컴퓨터에 있습니다.|
