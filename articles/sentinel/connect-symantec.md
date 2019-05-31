---
title: Azure 미리 보기 Sentinel Symantec ICDX 데이터 연결할 | Microsoft Docs
description: Symantec ICDX 데이터 Azure Sentinel를 연결 하는 방법에 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2019
ms.author: rkarlin
ms.openlocfilehash: 3b21371d6321b208b19ca8b2524308736c3ceca9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244350"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Symantec ICDX 어플라이언스에서 연결 

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Symantec ICDX 커넥터를 사용 하면 프로그램 Azure Sentinel, 대시보드 보기, 사용자 지정 경고를 만들고, 조사가 향상으로 모든 Symantec 보안 솔루션 로그를 쉽게 연결할 수 있습니다. 이 조직의 네트워크에 대 한 자세한 정보를 제공 하며 사용자 보안 작업 기능을 향상 시킵니다. Symantec ICDX와 Azure Sentinel 간의 통합을 사용 하면 REST API를 사용 합니다.


> [!NOTE]
> 데이터 작업 영역의 Azure Sentinel 실행 하는 지리적 위치에 저장 됩니다.

## <a name="configure-and-connect-symantec-icdx"></a>구성 하 고 Symantec ICDX 연결 

Symantec ICDX 통합 하 고 Azure Sentinel에 직접 로그를 내보낼 수 있습니다.

1. Microsoft Azure Sentinel (Log Analytics) 전달자를 추가 하려면 ICDX 관리 콘솔을 엽니다.
2. ICDx 탐색 모음에서 클릭 **구성**합니다. 
3. 맨 위에 있는 합니다 **구성** 화면에서 클릭 **전달자**합니다.
4. 아래 **전달자**, Microsoft Azure Sentinel (Log Analytics), 옆에 있는 **추가**합니다. 
4. 에 **Microsoft Azure (Log Analytics) Sentinel** 창 클릭 **고급 표시**합니다. 
5. 확장된 된 맨 위에 있는 Microsoft Azure Sentinel (Log Analytics) 창에 다음을 수행 합니다.
    -   **이름**: 30 개 이상의 문자가 포함 된 전달자에 대 한 이름을 입력 합니다. 고유 하 고 의미 있는 이름을 선택 합니다. 이 이름은의 전달자 목록에 표시 됩니다는 **구성** 화면에서 대시보드 및 합니다 **대시보드** 화면. 예를 들면 다음과 같습니다. Microsoft Azure Log Analytics East. 이 필드는 필수입니다.
    -   **설명**: 전달자에 대 한 설명을 입력 합니다. 이 설명은 나타나는 전달자 목록에는 **구성** 화면. 전달 중인 이벤트 유형과 같은 정보와 데이터를 검사 해야 하는 그룹을 포함 합니다.
    -   **시작 유형**: 전달자 구성에 대 한 시작 방법을 선택 합니다. 옵션은 수동 및 자동입니다.<br>기본값은 자동입니다. 
6. 아래 **이벤트**, 다음을 수행 합니다. 
    - **원본**: 이벤트를 전달 하는 하나 이상의 보관 파일을 선택 합니다. 분리 된 활성 수집기 보관 (일반적인 보관 파일 포함)를 선택할 수 있습니다 수집기 보관 파일 (즉, 보관 파일을 삭제 하는 수집기에 대 한), ICDx 수신기 보관, 또는 시스템 보관 합니다. <br>기본값은 일반적인 보관 합니다.
      > [!NOTE]
      > ICDx 수신기 보관 파일 이름으로 개별적으로 나열 됩니다. 
 
    - **필터**: 전달할 이벤트의 하위 집합을 지정 하는 필터를 추가 합니다. 다음 중 하나를 수행합니다.
        - 필터 조건을 선택한 형식, 특성, 연산자 및 값을 클릭 합니다. 
        - 필터 필드를 필터 조건을 검토 합니다. 필드에 직접 편집 하거나 필요에 따라 삭제할 수 있습니다.
        - 클릭 또는 및 또는 필터 조건에 추가 합니다.
        - 저장된 된 쿼리를 적용 하려면 저장 된 쿼리를 클릭할 수도 있습니다.
    - **특성을 포함**: 전달 된 데이터에 포함할 특성을 쉼표로 구분 된 목록을 입력 합니다. 포함 된 특성 제외 되는 특성 보다 우선합니다.
    - **제외 된 특성**: 전달 된 데이터에서 제외 하려면 특성의 쉼표로 구분 된 목록을 입력 합니다.
    - **일괄 처리 크기**: 일괄 처리당 보낼 이벤트의 수를 선택 합니다. 옵션에는 10, 50, 100, 500 및 1000 됩니다.<br>기본값은 100입니다. 
    - **속도 제한**: 이벤트 전달 되는, 초당 이벤트로 표현 되는 속도 선택 합니다. 옵션은 무제한, 500, 1000, 5000, 10000입니다. <br> 기본값은 5000입니다. 
7. 아래 **Azure 대상**, 다음을 수행 합니다. 
    - **작업 영역 ID**: 아래에서 작업 영역 ID를 붙여 넣습니다. 이 필드는 필수입니다.
    - **기본 키**: 아래에서 기본 키를 붙여 넣습니다. 이 필드는 필수입니다.
    - **사용자 지정 로그 이름을**: Microsoft Azure 포털 Log Analytics 작업 영역에서 이벤트를 전달 하려는는 사용자 지정 로그 이름을 입력 합니다. 기본값은 SymantecICDx 합니다. 이 필드는 필수입니다.
8. 클릭 *저장할* 전달자 구성을 완료 합니다. 
9. 아래에 있는 전달자를 시작 하려면 **옵션**, 클릭 **자세한** 차례로 **시작**합니다.
10. Log Analytics에서 관련 스키마를 사용 하 여 Symantec ICDX 이벤트를 검색할 **SymantecICDX_CL**합니다.


## <a name="validate-connectivity"></a>연결 유효성 검사

수준도 로그를 Log Analytics에 나타나기 시작 될 때까지 20 분 정도 걸릴 수 있습니다. 



## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel Symantec ICDX 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.

