---
title: Azure Stack 포털에 대 한 서비스로 유효성 검사를 사용 하 여 첫 번째 테스트를 예약 하려면 | Microsoft Docs
description: 서비스로 Azure Stack 포털에 대 한 유효성 검사를 사용 하 여 첫 번째 테스트를 예약 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 3fb5c3deeddb6f3ee381ca45df76feebf3405b21
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766576"
---
# <a name="scheduling-a-test"></a>테스트를 예약합니다.

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Azure Stack 솔루션에 대 한 유효성 검사 서비스 (VaaS) 포털에서 테스트를 예약 합니다. VaaS 솔루션을는 특정 하드웨어 자재 명세서 정보 (BoM)를 사용 하 여 Azure Stack 솔루션을 나타냅니다. 하드웨어가 Azure Stack을 실행할 수 있는지 확인 하는 테스트를 예약할 수 있습니다.

솔루션을 확인 하려면 테스트에 대 한 워크플로 만듭니다. VaaS 워크플로 VaaS 솔루션의 컨텍스트 내에서 작동합니다. 하드웨어에서 Azure Stack 배포의 기능을 실행 하는 테스트 도구 모음 집합을 나타냅니다. 솔루션의 환경 매개 변수를 추가 하 고 솔루션에서 실행할 하나 이상의 테스트를 선택 합니다.

테스트 통과 워크플로에서 결과 VaaS, 유효성 검사 워크플로에서 테스트를 포함 하 여 제공한 모든 테스트를 실행 하려면 테스트 통과 워크플로 사용할 수 있지만 간주 되지 않습니다 *공식*합니다. 공식 유효성 검사 워크플로에 대 한 정보를 참조 하세요 [워크플로](azure-stack-vaas-key-concepts.md#workflows)합니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 수행 하기 전에 다음 항목이 완료 해야 합니다.

- [서비스 리소스와 유효성 검사 설정](azure-stack-vaas-set-up-resources.md)
- [로컬 에이전트를 배포](azure-stack-vaas-local-agent.md) (필수)
- [서비스 키 개념으로 유효성 검사](azure-stack-vaas-key-concepts.md) (필수)

## <a name="start-a-workflow"></a>워크플로 시작

![VaaS 포털에 로그인](media/vaas_portalsignin.png)

포털을 선택 하거나 솔루션을 만들기에 로그인 하 고 솔루션을 선택 합니다.

1. 에 로그인 합니다 [VaaS 포털](https://azurestackvalidation.com)합니다.
2. 기존 솔루션의 이름을 입력 하거나 선택 **새 솔루션** 새 솔루션을 만듭니다. 자세한 내용은 [VaaS 포털에서 솔루션을 만들어](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-vaas-portal)합니다.
3. 선택 **시작** 에 **테스트 통과** 바둑판식으로 배열 합니다.

## <a name="specify-parameters"></a>매개 변수 지정

![대체 텍스트](media/vaas_test_pass_parameters.png)

워크플로 내에서 모든 테스트에 적용 되는 매개 변수를 제공 합니다.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. 선택 **다음** 예약 하는 테스트를 선택 합니다.

## <a name="select-tests-to-run"></a>실행할 테스트 선택

선택한 테스트 워크플로가 만들어지면 예약 됩니다.

1. 워크플로에서 실행 하려면 테스트를 선택 합니다.

    모든 테스트에 대 한 일반 매개 변수 (즉, 매개 변수는 이전 섹션에서 제공)를 재정의 하려는 경우에 선택 합니다 **편집** 새 값을 지정 하려면 다음을 링크 합니다.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

1. 선택 **다음** 워크플로 검토 합니다.

## <a name="review-and-submit"></a>검토 및 제출

워크플로 만들기를 완료 합니다.

1. 표시 되는 정보를 검토 합니다.

    서비스 제공된 된 정보를 사용 하 여 워크플로 만듭니다 및 선택한 테스트를 예약 합니다.

    아무 것도 나타나는 잘못 된 경우 사용 합니다 **이전** 는 이전 섹션으로 이동 하는 단추입니다.

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>다음 단계

- [모니터링 및 VaaS 포털에서 테스트를 관리 합니다.](azure-stack-vaas-monitor-test.md)
