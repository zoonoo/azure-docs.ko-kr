---
title: 모니터링 및 관리는 Azure Stack VaaS 포털에서 테스트 | Microsoft Docs
description: 모니터링 하 고 Azure Stack VaaS 포털에서 테스트를 관리 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 8784acc0180be1c3e0ac277b7c2a21d422ebccd0
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341054"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>모니터링 및 VaaS 포털에서 테스트를 관리 합니다.

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Azure Stack 솔루션에 대 한 테스트를 예약, 후 (VaaS) 서비스로 유효성 검사 테스트 실행 상태를 보고 시작 됩니다. 이 정보는 디스플레이 창 및 테스트를 취소 하는 등의 작업 함께 VaaS 포털에서 사용할 수 있습니다.

## <a name="navigate-to-the-workflow-tests-summary-page"></a>워크플로 테스트 요약 페이지로 이동

1. 솔루션 대시보드에서 워크플로가 하나 이상 있는 기존 솔루션을 선택 합니다.

    ![워크플로 타일](media/tile_all-workflows.png)

1. 선택 **관리** 워크플로 타일입니다. 다음 페이지는 선택한 솔루션에 대해 만든 워크플로 나열 합니다.

1. 해당 테스트 요약을 열려면 워크플로 이름을 선택 합니다.

## <a name="change-workflow-parameters"></a>워크플로 매개 변수를 변경 합니다.

각 워크플로 유형에 편집할 수 있습니다 합니다 [매개 변수를 테스트](azure-stack-vaas-parameters.md#test-parameters) 워크플로 만드는 동안 지정 합니다.

1. 테스트 요약 페이지에서 선택 합니다 **편집** 단추입니다.

1. 에 따라 새 값을 제공할 [서비스로 Azure Stack 유효성 검사에 대 한 워크플로 일반 매개 변수](azure-stack-vaas-parameters.md)합니다.

1. 선택 **제출** 값을 저장 합니다.

> [!NOTE]
> 에 **테스트 통과** 워크플로 해야 선택한 테스트를 완료 하 고 새 매개 변수 값을 저장 하기 전에 검토 페이지로 이동 합니다.

### <a name="add-tests-test-pass-only"></a>테스트 (테스트 통과만 해당) 추가

**테스트 통과** 워크플로 모두를 **테스트를 추가** 및 **편집** 단추를 사용 하는 워크플로의 새 테스트를 예약할 수 있습니다.

> [!TIP]
> 선택 **테스트를 추가** 새 테스트를 예약 하려면 하 고 경우에 대 한 매개 변수를 편집할 필요 하지는 **테스트 통과** 워크플로.

## <a name="managing-test-instances"></a>테스트 인스턴스 관리

비공식 실행에 대 한 (즉, 합니다 **테스트 통과** 워크플로), 테스트 요약 페이지에서는 Azure Stack 솔루션에 대해 예약 된 테스트를 나열 합니다.

공식 실행에 대 한 (즉, 합니다 **유효성 검사** 워크플로), 테스트 요약 페이지에서는 Azure Stack 솔루션의 유효성 검사를 완료 하는 데 필요한 테스트를 나열 합니다. 유효성 검사 테스트는이 페이지에서 예약 됩니다.

각 예약 된 테스트 인스턴스는 다음 정보를 보여줍니다.

| 열 | 설명 |
| --- | --- |
| 테스트 이름 | 이름 및 버전의 테스트 합니다. |
| Category | 테스트의 용도입니다. |
| 생성일 | 테스트는 예약 된 시간입니다. |
| Started | 테스트 실행 시작 된 시간입니다. |
| 기간 | 테스트를 실행 하는 시간의 길이입니다. |
| 상태 | 상태 또는 테스트의 결과입니다. 실행 전 또는 진행 중인 상태: `Pending`, `Running`합니다. 터미널 상태는: `Cancelled`, `Failed`하십시오 `Aborted`, `Succeeded`합니다. |
| 에이전트 이름 | 테스트를 실행 하는 에이전트의 이름입니다. |
| 총작업 수 | 테스트 중에 시도 하는 작업의 총 수입니다. |
| 전달 된 작업 | 테스트 하는 동안 성공한 작업의 수입니다. |
|  실패 한 작업 | 테스트 하는 동안 실패 한 작업의 수입니다. |

### <a name="actions"></a>작업

상황에 맞는 메뉴를 클릭할 때 수행할 수 있는 사용 가능한 작업을 나열 하는 각 테스트 인스턴스에 **[...]**  테스트 인스턴스 테이블에 있습니다.

#### <a name="view-information-about-the-test-definition"></a>테스트 정의 대 한 정보 보기

선택 **정보를 보려면** 테스트 정의 대 한 일반 정보를 보려면 상황에 맞는 메뉴에서입니다. 이 동일한 이름 및 버전을 사용 하 여 각 테스트 인스턴스에서 공유 됩니다.

| 테스트 속성 | 설명 |
| -- | -- |
| 테스트 이름 | 테스트의 이름입니다. |
| 테스트 버전 | 테스트의 버전입니다. |
| 게시자 | 게시자 테스트입니다. |
| Category |  테스트의 용도입니다. |
| 대상 서비스 | Azure Stack 테스트할 서비스입니다. |
| 설명 | 테스트의 설명입니다. |
| 예상된 기간 (분) | 테스트의 예상된 런타임입니다. |
| 링크 | 테스트 또는 연락처의 지점에 대 한 모든 관련 정보입니다. |

#### <a name="view-test-instance-parameters"></a>테스트 인스턴스 매개 변수 보기

선택 **매개 변수를 보려면** 테스트 인스턴스에 예약 된 시간에 제공 된 매개 변수를 보려면 상황에 맞는 메뉴에서입니다. 암호와 같은 중요 한 문자열 표시 되지 않습니다. 이 작업은 예정 된 테스트에 대해서만 사용할 수 있습니다.

이 창에는 모든 테스트 인스턴스에 대 한 다음과 같은 메타 데이터가 포함 됩니다.

| 테스트 인스턴스 속성 | 설명 |
| -- | -- |
| 테스트 이름 | 테스트의 이름입니다. |
| 테스트 버전 | 테스트의 버전입니다. |
| 테스트 인스턴스 ID | 테스트의 특정 인스턴스를 식별 하는 GUID입니다. |

#### <a name="view-test-instance-operations"></a>테스트 인스턴스 작업 보기

선택 **작업 보기** 컨텍스트에서 메뉴 작업의 자세한 상태를 테스트 하는 동안 수행 합니다. 이 작업은 예정 된 테스트에 대해서만 사용할 수 있습니다.

![작업 보기](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>완료 된 테스트 인스턴스에 대 한 로그를 다운로드 합니다.

선택 **로그를 다운로드할** 다운로드 하려면 상황에 맞는 메뉴에서을 `.zip` 테스트 실행 하는 동안 로그 출력의 파일입니다. 이 작업은 예 중 상태를 사용 하 여 테스트를 완료 하는 테스트에만 사용 가능 `Cancelled`, `Failed`, `Aborted`, 또는 `Succeeded`합니다.

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>테스트 인스턴스를 다시 예약 하거나 테스트를 예약 합니다.

관리 페이지에서 테스트를 예약 하는 작업은 테스트 실행에서 워크플로의 유형에 따라 다릅니다.

##### <a name="test-pass-workflow"></a>테스트 통과 워크플로

테스트 통과 워크플로에서 **디스플레이 창** 테스트 인스턴스를 다시 원래 테스트 인스턴스와 동일한 매개 변수 집합을 사용 하 고 *대체* 해당 로그를 포함 하 여 원래 결과입니다. 일정을 재조정 하면 암호와 같은 중요 한 문자열을 다시 입력 해야 합니다.

1. 선택 **일정 변경** 테스트 인스턴스 디스플레이 창에 대 한 프롬프트를 열려면 상황에 맞는 메뉴에서입니다.

1. 해당 매개 변수를 입력 합니다.

1. 선택 **제출** 기존 인스턴스를 바꾸고 테스트 인스턴스를 다시 예약 합니다.

##### <a name="validation-workflows"></a>유효성 검사 워크플로

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>테스트 인스턴스를 취소 합니다.

예약 된 테스트를 해당 상태가 취소 될 수 있습니다 `Pending` 또는 `Running`합니다.  

1. 선택 **취소** 테스트 인스턴스를 취소 하는 것에 대 한 프롬프트를 열려면 상황에 맞는 메뉴에서입니다.

1. 선택 **제출** 테스트 인스턴스를 취소 합니다.

## <a name="next-steps"></a>다음 단계

- [서비스 유효성 검사 문제 해결](azure-stack-vaas-troubleshoot.md)
