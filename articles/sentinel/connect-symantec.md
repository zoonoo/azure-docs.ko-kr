---
title: 시만텍 ICDx 데이터를 Azure 센티넬에 연결| 마이크로 소프트 문서
description: 시만텍 ICDx 데이터를 Azure 센티넬에 연결하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cac63aee5f9ebf3859b138e6444e40b1e2dd30f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588096"
---
# <a name="connect-your-symantec-icdx-appliance"></a>시만텍 ICDx 어플라이언스 연결 



시만텍 ICDx 커넥터를 사용하면 모든 시만텍 보안 솔루션 로그를 Azure Sentinel에 쉽게 연결하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이를 통해 조직의 네트워크에 대한 더 많은 통찰력을 얻고 보안 운영 기능을 향상시킬 수 있습니다. 시만텍 ICDx와 Azure 센티넬 간의 통합은 REST API를 사용합니다.


> [!NOTE]
> 데이터는 Azure Sentinel을 실행 중인 작업 영역의 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-symantec-icdx"></a>시만텍 ICDx 구성 및 연결 

시만텍 ICDx는 Azure Sentinel에 직접 로그를 통합하고 내보낼 수 있습니다.

1. ICDx 관리 콘솔을 열어 Microsoft Azure Sentinel(로그 분석) 전달자를 추가합니다.
2. ICDx 탐색 모음에서 **구성**을 클릭합니다. 
3. **구성** 화면 상단에서 **전달자를 클릭합니다.**
4. **전달자**아래에서 Microsoft Azure Sentinel(로그 분석) 옆의 **추가를**클릭합니다. 
4. Microsoft **Azure Sentinel(로그 분석)** 창에서 **고급 표시를**클릭합니다. 
5. Microsoft Azure Sentinel(로그 분석) 창으로 확장된 창 의 맨 위에 다음을 수행합니다.
    -   **이름**: 30자 이하의 전달자의 이름을 입력합니다. 독특하고 의미 있는 이름을 선택합니다. 이 이름은 **구성** 화면의 전달자 목록과 **대시보드** 화면의 대시보드에 나타납니다. 예: 마이크로소프트 Azure 로그 분석 동쪽. 이 필드는 필수입니다.
    -   **설명**: 전달자에 대한 설명을 입력합니다. 이 설명은 **구성** 화면의 전달자 목록에도 나타납니다. 전달중인 이벤트 유형 및 데이터를 검사해야 하는 그룹과 같은 세부 정보를 포함합니다.
    -   **시작 유형**: 전달자 구성에 대한 시작 방법을 선택합니다. 옵션은 수동 및 자동입니다.<br>기본값은 자동입니다. 
6. **이벤트에서**다음을 수행합니다. 
    - **출처**: 이벤트를 전달할 하나 이상의 아카이브를 선택합니다. 활성 컬렉터 아카이브(공통 아카이브 포함), 고아 컬렉터 아카이브(즉, 삭제한 컬렉터의 아카이브), ICDx 수신기 아카이브 또는 시스템 아카이브를 선택할 수 있습니다. <br>기본값은 일반 아카이브입니다.
      > [!NOTE]
      > ICDx 수신기 아카이브는 이름으로 별도로 나열됩니다. 
 
    - **필터**: 전달할 이벤트의 하위 집합을 지정하는 필터를 추가합니다. 다음 중 하나를 수행합니다.
        - 필터 조건을 선택하려면 유형, 특성, 연산자 및 값을 클릭합니다. 
        - 필터 필드에서 필터 조건을 검토합니다. 필드에서 직접 편집하거나 필요에 따라 삭제할 수 있습니다.
        - AND 또는 OR을 클릭하여 필터 조건에 추가합니다.
        - 저장된 쿼리를 클릭하여 저장된 쿼리를 적용할 수도 있습니다.
    - **포함된 특성**: 전달된 데이터에 포함할 쉼표 구분된 특성 목록을 입력합니다. 포함된 특성이 제외된 특성보다 우선합니다.
    - **제외된 특성**: 전달된 데이터에서 제외할 쉼표 구분된 특성 목록을 입력합니다.
    - **일괄 처리 크기**: 일괄 처리당 보낼 이벤트 수를 선택합니다. 옵션은 10, 50, 100, 500 및 1000입니다.<br>기본값은 100입니다. 
    - **속도 제한**: 이벤트가 전달되는 속도를 선택하여 초당 이벤트로 표현합니다. 옵션은 무제한, 500, 1000, 5000, 10000입니다. <br> 기본값은 5000입니다. 
7. **Azure 대상에서**다음을 수행합니다. 
    - **작업 영역 ID**: 아래에서 작업 영역 ID를 붙여넣습니다. 이 필드는 필수입니다.
    - **기본 키**: 아래에서 기본 키를 붙여 넣습니다. 이 필드는 필수입니다.
    - **사용자 지정 로그 이름:** 이벤트를 전달할 Microsoft Azure 포털 로그 분석 작업 영역에 사용자 지정 로그 이름을 입력합니다. 기본값은 시만텍IcDx입니다. 이 필드는 필수입니다.
8. 전달자 구성을 완료하려면 *저장을* 클릭합니다. 
9. 전달기를 시작하려면 **옵션**에서 **더를** 클릭한 다음 **을 시작합니다.**
10. 시만텍 ICDx 이벤트에 대한 로그 분석에서 관련 스키마를 사용하려면 **SymantecICDx_CL**검색합니다.


## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 로그 분석에 표시되기 시작할 때까지 20분 이상이 걸릴 수 있습니다. 



## <a name="next-steps"></a>다음 단계
이 문서에서는 시만텍 ICDx를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.


