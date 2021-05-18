---
title: 분석 Azure Monitor 로그에 대한 ETW(Windows용 이벤트 추적) 이벤트 수집
description: Azure Monitor 로그에서 분석을 위해 ETW(Windows용 이벤트 추적)를 수집하는 방법에 대해 알아봅니다.
services: azure-monitor
ms.topic: conceptual
ms.author: jamesfit
author: jimmyfit
ms.date: 01/29/2021
ms.openlocfilehash: 73135d95a56dc03790b3b7368a276ebfc99275fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025070"
---
# <a name="collecting-event-tracing-for-windows-etw-events-for-analysis-azure-monitor-logs"></a>분석 Azure Monitor 로그에 대한 ETW(Windows용 이벤트 추적) 이벤트 수집

*ETW(Windows용 이벤트 추적)* 는 사용자 모드 애플리케이션 및 커널 모드 드라이버를 계측하는 메커니즘을 제공합니다. Log Analytics 에이전트는 관리 및 운영 [ETW 채널](/windows/win32/wes/eventmanifestschema-channeltype-complextype)에 기록된 [Windows 이벤트를 수집](./data-sources-windows-events.md)하는 데 사용됩니다. 그러나 분석 채널에 기록된 것과 같은 다른 이벤트를 캡처 및 분석해야 하는 경우도 있습니다.  

## <a name="event-flow"></a>이벤트 흐름

Azure Monitor 로그에서 분석할 수 있도록 [매니페스트 기반 ETW 이벤트](/windows/win32/etw/about-event-tracing#types-of-providers)를 성공적으로 수집하려면 WAD(Windows용 [Azure 진단 확장](./diagnostics-extension-overview.md))를 사용해야 합니다. 이 시나리오에서 진단 확장은 ETW 소비자 역할을 하며 Azure Storage(테이블)에 이벤트를 중간 스토리지로 작성합니다. 여기서 **WADETWEventTable** 이라는 테이블에 저장됩니다. 그런 다음 Log Analytics는 Azure 스토리지에서 테이블 데이터를 수집하여 **ETWEvent** 라는 테이블로 표시합니다.

![이벤트 흐름](./media/data-sources-event-tracing-windows/event-flow.png)

## <a name="configuring-etw-log-collection"></a>ETW 로그 수집 구성

### <a name="step-1-locate-the-correct-etw-provider"></a>1단계: 올바른 ETW 공급자 찾기

다음 명령 중 하나를 사용하여 원본 Windows 시스템에서 ETW 공급자를 열거합니다.

명령줄:

```
logman query providers
```

PowerShell:
```
Get-NetEventProvider -ShowInstalled | Select-Object Name, Guid
```
필요에 따라 탐색을 지원하기 위해 Out-Gridview에 이 PowerShell 출력을 파이프하도록 선택할 수 있습니다.

이벤트 뷰어에 표시되는 분석 또는 디버그 로그 또는 이벤트 데이터를 수집할 모듈에 맞는 ETW 공급자 이름 및 GUID를 기록합니다.

### <a name="step-2-diagnostics-extension"></a>2단계: 진단 확장

모든 원본 시스템에 *Windows 진단 확장* 이 [설치](./diagnostics-extension-windows-install.md#install-with-azure-portal)되어 있는지 확인합니다.

### <a name="step-3-configure-etw-log-collection"></a>3단계: ETW 로그 컬렉션 구성

1. 가상 머신의 **진단 설정** 블레이드로 이동합니다.

2. **로그** 탭을 선택합니다.

3. 아래로 스크롤하여 **ETW(Windows용 이벤트 추적) 이벤트** 옵션을 사용하도록 설정합니다.![진단 설정의 스크린샷](./media/data-sources-event-tracing-windows/enable-event-tracing-windows-collection.png)

4. 컬렉션의 구성 대상인 공급자에 따라 공급자 GUID 또는 공급자 클래스를 설정합니다.

5. [**로그 수준**](/windows/win32/etw/configuring-and-starting-an-event-tracing-session)을 적절하게 설정합니다.

6. 제공된 공급자 옆에 있는 줄임표를 클릭하고 **구성** 을 클릭합니다.

7. **기본 대상 테이블** 이 **etweventtable** 로 설정되어 있는지 확인합니다.

8. 필요한 경우 [**키워드 필터**](/windows/win32/wes/defining-keywords-used-to-classify-types-of-events)를 설정합니다.

9. 공급자 및 로그 설정을 저장합니다.

일치하는 이벤트가 생성되면 Azure Storage의 **WADetweventtable** 테이블에 표시되는 ETW 이벤트가 표시되기 시작해야 합니다. Azure Storage Explorer를 사용하여 이를 확인할 수 있습니다.

### <a name="step-4-configure-log-analytics-storage-account-collection"></a>4단계: Log Analytics 스토리지 계정 컬렉션 구성

Azure Storage에서 로그를 수집하려면 [다음 지침](./diagnostics-extension-logs.md#collect-logs-from-azure-storage)을 따르세요. 구성된 후에는 **ETWEvent** 테이블 아래 Log Analytics에 ETW 이벤트 데이터가 표시되어야 합니다.

## <a name="next-steps"></a>다음 단계
- [사용자 지정 필드](../logs/custom-fields.md)를 사용하여 ETW 이벤트에서 구조 만들기
- 데이터 원본 및 솔루션에서 수집한 데이터를 분석하는 [로그 쿼리](../logs/log-query-overview.md)에 대해 알아봅니다.