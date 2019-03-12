---
title: 새 Azure Stack 솔루션의 유효성을 검사 | Microsoft Docs
description: 서비스 유효성 검사를 사용 하 여 새 Azure Stack 솔루션의 유효성을 검사 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: fb7b98a5307606f7335e76ada42d583b975730f5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759666"
---
# <a name="validate-a-new-azure-stack-solution"></a>새 Azure Stack 솔루션의 유효성을 검사합니다

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

사용 방법 알아보기 합니다 **솔루션 유효성 검사** 새 Azure Stack 솔루션을 인증 하는 워크플로.

Azure Stack 방법은 하드웨어 자재 명세서 정보 (BoM)의 공동 합의 Microsoft와 파트너 간에 Windows Server 로고 인증 요구 사항을 충족 한 후입니다. 솔루션을 BoM 하드웨어 변경 되었을 때 recertified 수 있어야 합니다. 솔루션을 다시 인증 하는 경우에 대 한 추가 질문에 대 한 팀에 문의 [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)합니다.

솔루션이 인증 하려면 솔루션 유효성 검사 워크플로 두 번 실행 합니다. 에 대해 한 번씩 실행 합니다 *최소* 지원 되는 구성 합니다. 에 대 한 두 번 실행 합니다 *최대로* 지원 되는 구성 합니다. Microsoft는 모든 테스트를 통과 하는 둘 다 구성 하는 경우 솔루션을 인증 합니다.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>솔루션의 유효성 검사 워크플로 만들기

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. 선택 **시작** 에 **솔루션 유효성 검사** 바둑판식으로 배열 합니다.

    ![유효성 검사 워크플로 솔루션 타일](media/tile_validation-solution.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. 선택 된 **솔루션 구성**합니다.
    - **최소**: 지원 되는 최소 노드 수를 사용 하 여 구성 된 솔루션입니다.
    - **최대**: 지원 되는 최대 노드 수를 사용 하 여 구성 된 솔루션입니다.
6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![솔루션 유효성 검사 정보](media/workflow_validation-solution_info.png)

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > 워크플로 만든 후 환경 매개 변수를 수정할 수 없습니다.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    테스트 요약 페이지로 리디렉션됩니다.

## <a name="run-solution-validation-tests"></a>솔루션의 유효성 검사 테스트를 실행 합니다.

에 **솔루션 유효성 검사 테스트 요약** 페이지 유효성 검사를 완료 하는 데 필요한 테스트 목록이 표시 됩니다.

유효성 검사 워크플로에서 **예약** 워크플로 만드는 동안 지정 된 워크플로 수준 일반적인 매개 변수를 사용 하는 테스트 (참조 [서비스로AzureStack유효성검사에대한워크플로일반매개변수](azure-stack-vaas-parameters.md)). 테스트 매개 변수 값에 유효 하지 않게 됩니다, 경우 있습니다 해야 재충전할 하에 설명 된 대로 [워크플로 매개 변수를 수정](azure-stack-vaas-monitor-test.md#change-workflow-parameters)합니다.

> [!NOTE]
> 기존 인스턴스에 대해 유효성 검사 테스트를 예약 이전 인스턴스 대신 새 인스턴스를 포털에서 만들어집니다. 이전 인스턴스에 대 한 로그는 보존할 되지만 포털에서 액세스할 수 없습니다.  
테스트를 성공적으로 완료 되 면 합니다 **일정** 작업 비활성화 합니다.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

2. 다음 테스트를 선택 합니다.
    - 클라우드 시뮬레이션 엔진
    - SDK Operational Suite를 계산 합니다.
    - 디스크 식별 테스트
    - KeyVault 확장 SDK에 대 한 운영 Suite
    - KeyVault SDK 운영 도구 모음
    - 네트워크 SDK 운영 도구 모음
    - 저장소 계정 SDK Operational Suite

3. 선택 **일정** 테스트 인스턴스 예약에 대 한 프롬프트를 열려면 상황에 맞는 메뉴에서입니다.

4. 테스트 매개 변수를 검토 하 고 선택한 **제출** 실행에 대 한 테스트를 예약 합니다.

![일정 솔루션 유효성 검사 테스트](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>다음 단계

- [모니터링 및 VaaS 포털에서 테스트를 관리 합니다.](azure-stack-vaas-monitor-test.md)