---
title: "Azure Machine Learning 배포 전개 문제 해결 가이드 | Microsoft Docs"
description: "배포 및 서비스 만들기에 대한 문제 해결 가이드"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/05/2017
ms.openlocfilehash: 066a6a223692055c7855abc63667e345ee8dc2d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="model-management-troubleshooting"></a>모델 관리 문제 해결
다음 정보는 배포 중 또는 웹 서비스 호출 시 오류의 원인을 파악하는 데 도움이 될 수 있습니다.
 
## <a name="1-service-logs"></a>1. 서비스 로그
서비스 CLI의 `logs` 옵션은 Docker 및 Kubernetes의 로그 데이터를 제공합니다.

```
az ml service logs realtime -i <web service id>
```

추가로 로그 설정을 하려면 `--help`(또는 `-h`) 옵션을 사용합니다.

```
az ml service logs realtime -h
```

## <a name="2-debug-and-verbose-options"></a>2. 디버그 및 자세한 정보 표시 옵션
서비스가 전개 될 때 디버그 로그를 표시하려면 `--debug` 플래그를 사용합니다.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

서비스가 전개 될 때 추가적인 세부 정보를 표시하려면 `--verbose` 플래그를 사용합니다.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

## <a name="3-app-insights"></a>3. App Insights
요청 수준 로깅을 사용하도록 웹 서비스를 만들 때 `-l` 플래그를 true로 설정합니다. 요청 로그는 Azure에서 사용자 환경에 대한 App Insights 인스턴스에 기록됩니다. 이 인스턴스는 `az ml env setup` 명령을 사용할 때 사용한 환경 이름을 사용하여 검색합니다.

- 서비스를 만들 때 `-l`을 true로 설정합니다.
- Azure Portal에서 App Insights를 엽니다. 환경 이름을 사용하여 App Insights 인스턴스를 찾습니다.
- App Insights에서 최상위 메뉴의 검색을 클릭하여 결과를 확인합니다.
- 또는 `Analytics` > `Exceptions` > `exceptions take | 10`으로 이동합니다.


## <a name="4-error-handling-in-script"></a>4. 스크립트의 오류 처리
`scoring.py` 스크립트의 **run** 함수에서 예외 처리를 사용하여 웹 서비스 출력의 일부로 오류 메시지를 반환합니다.

Python 예제:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="5-other-common-problems"></a>5. 다른 일반적인 문제
- `env setup` 명령이 실패하면 구독에 사용 가능한 코어가 충분한 지 확인합니다.
- 웹 서비스 이름에 밑줄(_)을 사용하지 마십시오(예: *my_webservice*).
- 웹 서비스를 호출 할 때 **502 Bad Gateway** 오류가 발생하면 다시 시도합니다. 이 오류는 일반적으로 컨테이너가 클러스터에 아직 배포되지 않았음을 의미합니다.
- 서비스를 만들 때 **CrashLoopBackOff** 오류가 발생하면 로그를 확인합니다. 일반적으로 **init** 함수에 종속성이 누락된 결과입니다.