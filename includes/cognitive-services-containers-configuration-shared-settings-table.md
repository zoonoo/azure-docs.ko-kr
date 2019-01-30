---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: 1a3de57446c5c92afbc5dd5901e284bc3580f9db
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479014"
---
이 컨테이너에는 다음 구성 설정을 사용합니다.

|필수|설정|목적|
|--|--|--|
|예|[ApiKey](#apikey-setting)|청구 정보를 추적하는 데 사용됩니다.|
|아니요|[ApplicationInsights](#applicationinsights-setting)|[Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 원격 분석 지원을 컨테이너에 추가할 수 있습니다.|
|예|[결제](#billing-setting)|Azure에서 서비스 리소스의 엔드포인트 URI를 지정합니다.|
|예|[Eula](#eula-setting)| 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.|
|아니요|[Fluentd](#fluentd-settings)|로그 및 메트릭 데이터(선택 사항)를 Fluentd 서버에 씁니다.|
|아니요|[Http 프록시](#http-proxy-credentials-settings)|아웃바운드 요청을 만들기 위한 HTTP 프록시를 구성합니다.|
|아니요|[로깅](#logging-settings)|컨테이너에 대한 ASP.NET Core 로깅 지원을 제공합니다. |
|아니요|[탑재](#mount-settings)|호스트 컴퓨터에서 컨테이너로 데이터를 읽고 쓰고, 컨테이너에서 호스트 컴퓨터로 다시 데이터를 읽고 씁니다.|
