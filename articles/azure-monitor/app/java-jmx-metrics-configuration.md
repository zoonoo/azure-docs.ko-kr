---
title: JMX 메트릭을 구성 하는 방법-Java 용 application insights Azure Monitor
description: Azure Monitor application insights Java 에이전트에 대 한 추가 JMX 메트릭 수집 구성
ms.topic: conceptual
ms.date: 03/16/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 020278bf6e5a823f6b3caa22d03f4b5dd003c0d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609611"
---
# <a name="configuring-jmx-metrics"></a>JMX 메트릭 구성

Application insights Java 3.0 에이전트는 기본적으로 일부 JMX 메트릭을 수집 하지만 대부분의 경우이는 충분 하지 않습니다. 이 문서에서는 JMX 구성 옵션에 대해 자세히 설명 합니다.

## <a name="how-do-i-collect-additional-jmx-metrics"></a>추가 JMX 메트릭을 수집 어떻게 할까요?

JMX 메트릭 컬렉션은 파일의 applicationinsights.js에 섹션을 추가 하 여 구성할 수 있습니다 ```"jmxMetrics"``` . Application insights 리소스의 Azure Portal에 표시 하려는 메트릭의 이름을 지정할 수 있습니다. 수집 하려는 각 메트릭에 대해 개체 이름과 특성을 정의 해야 합니다.

## <a name="how-do-i-know-what-metrics-are-available-to-configure"></a>구성에 사용할 수 있는 메트릭을 알고 어떻게 할까요??

이죠이는 개체 이름 및 특성을 알아야 하며, 이러한 속성은 다양 한 라이브러리, 프레임 워크 및 응용 프로그램 서버에서 서로 다르며 문서화 되지 않는 경우가 많습니다. 개체 이름 및 특성을 가져오려면 MBean 트리를 확인 해야 합니다. MBean은 장치, 응용 프로그램 또는 리소스를 나타낼 수 있고 특성 집합을 포함 하는 관리 되는 Java 개체입니다. 

사용 가능한 메트릭을 확인 하 고 사용 가능한 메트릭을 찾아보려면 [Java 임무 제어](https://www.oracle.com/java/technologies/jdk-mission-control.html)를 사용 하는 것이 좋습니다.

### <a name="how-to-navigate-the-java-mission-control-to-get-to-the-right-metrics"></a>올바른 메트릭을 얻기 위해 Java 임무 제어를 탐색 하는 방법

Java 임무 제어 도구를 실행할 때 왼쪽에서 사용할 수 있는 Jvm를 선택 하 고 ' JVM 브라우저 ' 탭에서 관련 프로세스를 클릭 합니다. JMC가 프로세스에 대 한 대시보드를 로드할 때까지 기다린 후 아래쪽의 ' MBean 브라우저 ' 탭을 선택 합니다 (아래 참조). JMC는 JVM과 동일한 폴더에 위치 해야 하며 프로세스/앱이 실행 중 이어야 합니다.

![JMC MBean 브라우저 스크린샷](media/java-ipa/jmx/jmc-mbean-browser.png)

### <a name="how-to-get-to-the-metrics-i-want-and-the-necessary-attributes"></a>원하는 메트릭 및 필요한 특성을 가져오는 방법

MBean 브라우저가 확장할 수 있는 범주 목록으로 MBean 트리를 엽니다. 왼쪽의 범주를 선택 하면 오른쪽에 있는 특성 목록이 열립니다. 다음은 메트릭, 해당 개체 이름 및 특성의 예입니다. 특성은 아래 예제와 같이 중첩 될 수 있습니다.

![JMC MBean 트리의 스크린샷](media/java-ipa/jmx/jmc-metric-sample.png)

### <a name="configuration-example"></a>구성 예

위의 이미지에 표시 된 것 처럼 선택 항목에서 몇 가지 메트릭을 구성할 수 있습니다. 첫 번째는 `LastGcInfo` 여러 속성을 포함 하는 중첩 된 메트릭의 예제 이며를 캡처해야 합니다 `GcThreadCount` .

```json
"jmxMetrics": [
      {
        "name": "Demo - GC Thread Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "LastGcInfo.GcThreadCount"
      },
      {
        "name": "Demo - GC Collection Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "CollectionCount"
      },
      {
        "name": "Demo - Thread Count",
        "objectName": "java.lang:type=Threading",
        "attribute": "ThreadCount"
      }
],
```

### <a name="types-of-collected-metrics-and-available-configuration-options"></a>수집 된 메트릭 유형 및 사용 가능한 구성 옵션

숫자 및 부울 JMX 메트릭을 지원 하지만 다른 형식은 지원 되지 않으며 무시 됩니다. 

현재는 와일드 카드 및 집계 된 특성이 지원 되지 않으므로 모든 특성 ' 개체 이름 '/' 특성 ' 쌍을 별도로 구성 해야 합니다. 


## <a name="where-do-i-find-the-jmx-metrics-in-application-insights"></a>Application insights에서 JMX 메트릭은 어디에서 찾을 까 요?

응용 프로그램이 실행 중이 고 JMX 메트릭이 수집 되 면 Azure Portal로 이동 하 여 application insights 리소스로 이동 하 여 볼 수 있습니다. 메트릭 탭에서 아래와 같이 드롭다운을 선택 하 여 메트릭을 확인 합니다.

![포털의 메트릭 스크린샷](media/java-ipa/jmx/jmx-portal.png)
