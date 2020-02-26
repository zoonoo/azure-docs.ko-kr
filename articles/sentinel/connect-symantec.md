---
title: Azure 센티널에 Symantec ICDx 데이터 연결 | Microsoft Docs
description: Azure 센티널에 Symantec ICDx 데이터를 연결 하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588096"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Symantec ICDx 어플라이언스 연결 



Symantec ICDx 커넥터를 사용 하면 Azure 센티널에 모든 Symantec 보안 솔루션 로그를 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 이렇게 하면 조직의 네트워크에 대 한 자세한 정보를 제공 하 고 보안 작업 기능을 향상 시킬 수 있습니다. Symantec ICDx와 Azure 센티널을 통합 하면 REST API를 사용 합니다.


> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-symantec-icdx"></a>Symantec ICDx 구성 및 연결 

Symantec ICDx는 로그를 Azure 센티널로 직접 통합 하 고 내보낼 수 있습니다.

1. ICDx 관리 콘솔을 열어 Microsoft Azure 센티널 (Log Analytics) 전달자를 추가 합니다.
2. ICDx 탐색 모음에서 **구성**을 클릭 합니다. 
3. **구성** 화면 맨 위에서 **전달자**를 클릭 합니다.
4. **전달자**에서 Microsoft Azure 센티널 (Log Analytics) 옆에 있는 **추가**를 클릭 합니다. 
4. **Microsoft Azure 센티널 (Log Analytics)** 창에서 **고급 표시**를 클릭 합니다. 
5. Microsoft Azure 센티널 (Log Analytics) 창의 위쪽에서 다음을 수행 합니다.
    -   **이름**: 전달 자의 이름을 30 자 미만으로 입력 합니다. 고유 하 고 의미 있는 이름을 선택 합니다. 이 이름은 **구성** 화면의 전달자 목록 및 **대시보드** 화면에 표시 됩니다. 예: Microsoft Azure Log Analytics 동부. 이 필드는 필수입니다.
    -   **설명**: 전달자에 대 한 설명을 입력 합니다. 이 설명은 **구성** 화면의 전달자 목록에도 표시 됩니다. 전달 되는 이벤트 유형과 데이터를 검사 해야 하는 그룹 등의 세부 정보를 포함 합니다.
    -   **시작 유형**: 전달자 구성의 시작 방법을 선택 합니다. 옵션은 수동 및 자동입니다.<br>기본값은 자동입니다. 
6. **이벤트**에서 다음을 수행 합니다. 
    - **원본**: 이벤트를 전달 하는 보관 파일을 하나 이상 선택 합니다. 활성 수집기 보관 파일 (일반적인 보관 파일 포함), 분리 된 수집기 보관 파일 (삭제 한 수집기에 대 한 보관 파일), ICDx 수신기 보관 또는 시스템 보관을 선택할 수 있습니다. <br>기본값은 일반적인 보관 파일입니다.
      > [!NOTE]
      > ICDx 받는 사람 보관 파일은 이름별로 별도로 나열 됩니다. 
 
    - **필터**: 전달할 이벤트의 하위 집합을 지정 하는 필터를 추가 합니다. 다음 중 하나를 수행합니다.
        - 필터 조건을 선택 하려면 유형, 특성, 연산자 및 값을 클릭 합니다. 
        - 필터 필드에서 필터 조건을 검토 합니다. 필드에서 직접 편집 하거나 필요에 따라 삭제할 수 있습니다.
        - AND 또는 OR를 클릭 하 여 필터 조건에 추가 합니다.
        - 저장 된 쿼리를 클릭 하 여 저장 된 쿼리를 적용할 수도 있습니다.
    - **포함 된 특성**: 전달 된 데이터에 포함할 쉼표로 구분 된 특성 목록을 입력 합니다. 포함 된 특성은 제외 된 특성 보다 우선적으로 적용 됩니다.
    - **제외 된 특성**: 전달 된 데이터에서 제외할 특성의 쉼표로 구분 된 목록을 입력 합니다.
    - **일괄 처리 크기**: 일괄 처리당 전송할 이벤트 수를 선택 합니다. 옵션은 10, 50, 100, 500 및 1000입니다.<br>기본값은 100입니다. 
    - **속도 제한**: 이벤트를 전달 하는 속도를 선택 하 여 초당 이벤트로 표시 합니다. 옵션은 무제한, 500, 1000, 5000, 1만입니다. <br> 기본값은 5000입니다. 
7. **Azure Destination**에서 다음을 수행 합니다. 
    - **작업 영역 id**: 아래에서 작업 영역 id를 붙여 넣습니다. 이 필드는 필수입니다.
    - **기본 키**: 아래에서 기본 키를 붙여넣습니다. 이 필드는 필수입니다.
    - **사용자 지정 로그 이름**: 이벤트를 전달 하려는 Microsoft Azure portal Log Analytics 작업 영역에 사용자 지정 로그 이름을 입력 합니다. 기본값은 SymantecICDx입니다. 이 필드는 필수입니다.
8. *저장* 을 클릭 하 여 전달자 구성을 완료 합니다. 
9. 전달자를 시작 하려면 **옵션**에서 **추가** 를 클릭 한 다음 **시작**을 클릭 합니다.
10. Symantec ICDx 이벤트의 Log Analytics에서 관련 스키마를 사용 하려면 **SymantecICDx_CL**를 검색 합니다.


## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics 표시 되기 시작할 때까지 20 분 정도 걸릴 수 있습니다. 



## <a name="next-steps"></a>다음 단계
이 문서에서는 Symantec ICDx를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md) 하 여 데이터를 모니터링 합니다.


