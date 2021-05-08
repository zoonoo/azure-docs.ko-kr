---
title: Symantec ICDx 데이터를 Azure Sentinel에 연결| Microsoft Docs
description: Symantec ICDx 커넥터를 사용하여 모든 Symantec 보안 솔루션 로그를 Azure Sentinel에 쉽게 연결하는 방법 알아보기.
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
ms.openlocfilehash: 7b32baa23a31342095e542d60745a503b81e89a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100092709"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Symantec ICDx 어플라이언스 연결 



Symantec ICDx 커넥터를 사용하면 Azure Sentinel에 모든 Symantec 보안 솔루션 로그를 쉽게 연결하여, 대시보드를 보고, 사용자 지정 경고를 만들어, 조사 기능을 개선할 수 있습니다. 이를 통해 조직의 네트워크에 대한 인사이트를 얻어, 보안 작업 기능이 향상됩니다. Symantec ICDx와 Azure Sentinel 간의 통합에는 REST API가 사용됩니다.


> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역이 있는 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-symantec-icdx"></a>Symantec ICDx 구성 및 연결 

Symantec ICDx는 로그를 Azure Sentinel로 직접 통합하고 내보낼 수 있습니다.

1. ICDx 관리 콘솔을 열어 Microsoft Azure Sentinel(Log Analytics) 전달자를 추가합니다.
2. ICDx 탐색 모음에서 **구성** 을 클릭합니다. 
3. **구성** 화면 맨 위에서 **전달자** 를 클릭합니다.
4. **전달자** 아래 Microsoft Azure Sentinel(Log Analytics) 옆에 있는 **추가** 를 클릭합니다. 
4. **Microsoft Azure Sentinel(Log Analytics)** 창에서 **고급 기능 표시** 를 클릭합니다. 
5. 확장된 Microsoft Azure Sentinel(Log Analytics) 창의 위쪽에서 다음을 수행합니다.
    -   **이름**: 전달자의 이름을 30자 미만으로 입력합니다. 고유하고 의미 있는 이름을 선택합니다. 해당 이름은 **구성** 화면의 전달자 목록 및 **대시보드** 화면의 대시보드에 표시됩니다. 예: Microsoft Azure Log Analytics 동부. 이 필드는 필수 필드입니다.
    -   **설명**: 전달자에 대한 설명을 입력합니다. 이 설명은 **구성** 화면의 전달자 목록에도 표시됩니다. 전달되는 이벤트 유형과 데이터를 검사해야 하는 그룹 등의 세부 정보를 포함합니다.
    -   **시작 유형**: 전달자 구성의 시작 방법을 선택합니다. 수동과 자동 중에서 선택할 수 있습니다.<br>기본값은 자동입니다. 
6. **이벤트** 아래에서 다음을 수행합니다. 
    - **원본**: 이벤트를 전달할 보관함을 하나 이상 선택합니다. 활성 수집기 보관함(일반 보관함 포함), 분리된 수집기 보관함(삭제한 수집기에 대한 보관함), ICDx 수신기 보관함 또는 시스템 보관함을 선택할 수 있습니다. <br>기본값은 일반 보관함입니다.
      > [!NOTE]
      > ICDx 수신기 보관함은 이름별로 나열됩니다. 
 
    - **필터**: 전달할 이벤트의 하위 집합을 지정하는 필터를 추가합니다. 다음 중 하나를 수행합니다.
        - 필터 조건을 선택하려면 유형, 특성, 연산자 및 값을 클릭합니다. 
        - 필터 필드에서 필터 조건을 검토합니다. 필드에서 직접 편집하거나 필요에 따라 삭제할 수 있습니다.
        - AND 또는 OR를 클릭하여 필터 조건에 추가합니다.
        - 저장된 쿼리를 클릭하여 저장된 쿼리를 적용할 수도 있습니다.
    - **포함된 특성**: 전달된 데이터에 포함할 쉼표로 구분된 특성 목록을 입력합니다. 포함된 특성은 제외된 특성보다 우선적으로 적용됩니다.
    - **제외된 특성**: 전달된 데이터에서 제외할 특성의 쉼표로 구분된 목록을 입력합니다.
    - **일괄 처리 크기**: 일괄 처리당 전송할 이벤트 수를 선택합니다. 옵션은 10, 50, 100, 500, 1000입니다.<br>기본값은 100입니다. 
    - **속도 제한**: 이벤트를 전달하는 속도를 선택하여 초당 이벤트로 표시합니다. 옵션은 무제한, 500, 1000, 5000, 10000입니다. <br> 기본값은 5000입니다. 
7. **Azure Destination** 에서 다음을 수행합니다. 
    - **작업 영역 ID**: 아래의 작업 영역 ID를 붙여넣습니다. 이 필드는 필수 필드입니다.
    - **기본 키**: 아래의 기본 키를 붙여넣습니다. 이 필드는 필수 필드입니다.
    - **사용자 지정 로그 이름**: 이벤트를 전달하려는 Microsoft Azure portal Log Analytics 작업 영역에 사용자 지정 로그 이름을 입력합니다. 기본값은 SymantecICDx입니다. 이 필드는 필수 필드입니다.
8. *저장* 을 클릭하여 전달자 구성을 완료합니다. 
9. 전달자를 시작하려면 **옵션** 에서 **더 보기** 를 클릭한 다음 **시작** 을 클릭합니다.
10. Symantec ICDx 이벤트에 대한 Log Analytics에서 관련 스키마를 사용하려면 **SymantecICDx_CL** 을 검색합니다.


## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시되는 데는 최대 20분이 걸립니다. 



## <a name="next-steps"></a>다음 단계
이상 Symantec ICDx를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.


