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
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 7949e764baa7a4e20eb988c78817b6b4f0045593
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333771"
---
# <a name="validate-a-new-azure-stack-solution"></a>새 Azure Stack 솔루션의 유효성을 검사합니다

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

사용 방법 알아보기 합니다 **솔루션 유효성 검사** 새 Azure Stack 솔루션을 인증 하는 워크플로.

Azure Stack 방법은 하드웨어 자재 명세서 정보 (BoM)의 공동 합의 Microsoft와 파트너 간에 Windows Server 로고 인증 요구 사항을 충족 한 후입니다. 솔루션을 BoM 하드웨어 변경 되었을 때 recertified 수 있어야 합니다. 솔루션을 다시 인증 하는 경우에 대 한 추가 질문에 대 한 팀에 문의 [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)합니다.

솔루션이 인증 하려면 솔루션 유효성 검사 워크플로 두 번 실행 합니다. 에 대해 한 번씩 실행 합니다 *최소* 지원 되는 구성 합니다. 에 대 한 두 번 실행 합니다 *최대로* 지원 되는 구성 합니다. Microsoft는 모든 테스트를 통과 하는 둘 다 구성 하는 경우 솔루션을 인증 합니다.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>솔루션의 유효성 검사 워크플로 만들기

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]
2. 선택 **시작** 에 **솔루션 유효성 검사** 바둑판식으로 배열 합니다.

    ![유효성 검사 워크플로 솔루션 타일](media/tile_validation-solution.png)

3. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
4. 선택 된 **솔루션 구성**합니다.
    - **최소**: 지원 되는 최소 노드 수를 사용 하 여 구성 된 솔루션입니다.
    - **최대**: 지원 되는 최대 노드 수를 사용 하 여 구성 된 솔루션입니다.
5. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![솔루션 유효성 검사 정보](media/workflow_validation-solution_info.png)

6. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > 워크플로 만든 후 환경 매개 변수를 수정할 수 없습니다.

7. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
8. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    테스트 요약 페이지로 리디렉션됩니다.

## <a name="execute-solution-validation-tests"></a>솔루션의 유효성 검사 테스트를 실행 합니다.

에 **솔루션 유효성 검사 테스트 요약** 페이지 유효성 검사를 완료 하는 데 필요한 테스트 목록이 표시 됩니다.

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

![일정 솔루션 유효성 검사 테스트](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>다음 단계

- [모니터링 및 VaaS 포털에서 테스트를 관리 합니다.](azure-stack-vaas-monitor-test.md)