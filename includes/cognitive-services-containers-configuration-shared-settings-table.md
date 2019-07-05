---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2019
ms.openlocfilehash: 4f9300ab1d688e1f5043f5b919e70c5a36c7c0e7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063997"
---
컨테이너는 다음 구성 설정은 다음과 같습니다.

|필수|설정|목적|
|--|--|--|
|예|[ApiKey](#apikey-configuration-setting)|청구 정보를 추적 합니다.|
|아닙니다.|[ApplicationInsights](#applicationinsights-setting)|추가 사용 하도록 설정 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 컨테이너에 원격 분석 지원.|
|예|[결제](#billing-configuration-setting)|Azure에서 서비스 리소스의 엔드포인트 URI를 지정합니다.|
|예|[Eula](#eula-setting)| 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.|
|아니요|[Fluentd](#fluentd-settings)|로그 쓰기 및 필요에 따라 Fluentd 서버로 메트릭 데이터입니다.|
|아닙니다.|Http 프록시|아웃 바운드 요청에 대 한 HTTP 프록시를 구성 합니다.|
|아닙니다.|[로깅](#logging-settings)|컨테이너에 대한 ASP.NET Core 로깅 지원을 제공합니다. |
|아니요|[탑재](#mount-settings)|읽기 및 쓰기 데이터 컨테이너 및 컨테이너에 호스트 컴퓨터에서 다시 호스트 컴퓨터에 있습니다.|
