---
title: 서비스 키 개념으로 azure Stack 유효성 검사 | Microsoft Docs
description: 서비스로 Azure Stack 유효성 검사의 주요 개념을 설명합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 6a14a806b48b30d84b021a84a1ef0a2f4318ea94
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106476"
---
# <a name="validation-as-a-service-key-concepts"></a>서비스 키 개념으로 유효성 검사

이 문서에서는 유효성 검사 (VaaS) 서비스로에서 주요 개념을 설명 합니다.

## <a name="solutions"></a>솔루션

VaaS 솔루션을는 특정 하드웨어 자재 명세서 정보 (BoM)를 사용 하 여 Azure Stack 솔루션을 나타냅니다. VaaS 솔루션은 Azure Stack 솔루션에 대해 실행 되는 워크플로에 대 한 컨테이너로 작동 합니다.

### <a name="create-a-solution-in-the-vaas-portal"></a>VaaS 포털에서 솔루션 만들기

1. 에 로그인 합니다 [VaaS 포털](https://azurestackvalidation.com)합니다.
2. 솔루션 대시보드에서 선택 **새 솔루션**합니다.
3. 솔루션에 대 한 이름을 입력 합니다. 명명 제안에 대해서 [VaaS 솔루션에 대 한 명명 규칙](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions)합니다.
4. 선택 **저장할** 솔루션을 만들려고 합니다.

## <a name="workflows"></a>워크플로

VaaS 워크플로 VaaS 솔루션의 컨텍스트 내에서 작동합니다. Azure Stack 배포의 기능을 실행 하는 테스트 도구 모음 집합을 나타냅니다. Azure Stack 솔루션의 모든 배포 또는 소프트웨어 업데이트 워크플로 작성 해야 합니다.

워크플로 시나리오 형식 테스트로 분류 됩니다. 비공식 테스트에 **테스트 통과** 워크플로 사용 하면 모든 사용 가능한 VaaS 자료에서 테스트를 선택할 수 있습니다. 공식 테스트에 **유효성 검사** 워크플로 대상 테스트 시나리오에 맞는 Microsoft에서 선정한 합니다.

![VaaS 워크플로 타일](media/tile_all-workflows.png)

> [!NOTE]
> 합니다 **솔루션 유효성 검사** 워크플로 현재 두 가지 시나리오를 지원 합니다. [OEM 패키지 유효성 검사](azure-stack-vaas-validate-oem-package.md) 하 고 [Microsoft의 소프트웨어 업데이트의 유효성을 검사](azure-stack-vaas-validate-microsoft-updates.md)합니다.

워크플로 형식에 대 한 자세한 내용은 참조 하세요. [서비스로 Azure Stack에 대 한 유효성 검사를 란?](azure-stack-vaas-overview.md)합니다.

### <a name="getting-started-with-vaas-workflows"></a>VaaS 워크플로 시작 하기

1. 솔루션 대시보드에서 새 솔루션을 만들거나 기존 항목을 선택 합니다. 이 새로 고침과 워크플로 타일 수 있습니다.
2. 새 워크플로 만들려면에서 선택 **시작** 타일입니다. 각 워크플로 관련 내용은 다음 문서를 참조 합니다.
    - 테스트 통과: [빠른 시작: 서비스 포털로 유효성 검사를 사용 하 여 첫 번째 테스트를 예약 하려면](azure-stack-vaas-schedule-test-pass.md)
    - 솔루션의 유효성 검사: [새 Azure Stack 솔루션의 유효성을 검사합니다](azure-stack-vaas-validate-solution-new.md)
    - 솔루션의 유효성 검사: [Microsoft의 소프트웨어 업데이트의 유효성을 검사합니다](azure-stack-vaas-validate-microsoft-updates.md)
    - 솔루션의 유효성 검사: [OEM 패키지 유효성 검사](azure-stack-vaas-validate-oem-package.md)

3. 를 관리 하거나 기존 워크플로 모니터링에서 선택 **관리** 워크플로 타일입니다. 이름을 사용 하 여 워크플로 선택 합니다 **편집** 단추 속성을 보거나 일반적인 테스트 매개 변수를 변경 합니다.

워크플로 속성 및 매개 변수에 대 한 자세한 내용은 참조 하세요. [서비스로 Azure Stack 유효성 검사에 대 한 워크플로 일반 매개 변수](azure-stack-vaas-parameters.md)합니다.

## <a name="tests"></a>테스트

VaaS에서 테스트 하는 Azure Stack 솔루션에 대해 실행 되는 작업의 모음으로 구성 됩니다. 테스트에 다른 용도 범주별으로 식별 된 기능 같은 또는 안정성 및 대상 Azure Stack의 하나 이상의 서비스입니다. 각 테스트 매개 변수를 포함 하는 워크플로 일반 매개 변수로 지정 된 일부는 자체 집합을 정의 합니다.

관리 및 테스트를 모니터링 하는 방법에 대 한 자세한 내용은 참조 하세요. [모니터 VaaS 포털에서 테스트를 관리 하 고](azure-stack-vaas-monitor-test.md)입니다.

테스트 매개 변수에 대 한 자세한 내용은 참조 하세요. [서비스로 Azure Stack 유효성 검사에 대 한 워크플로 일반 매개 변수](azure-stack-vaas-parameters.md)합니다.

## <a name="agents"></a>에이전트

VaaS 에이전트 테스트 실행을 구동합니다. 두 가지 유형의 에이전트 VaaS 테스트를 실행합니다.

- 합니다 **클라우드 에이전트**합니다. 이 기본 에이전트 VaaS에서 사용할 수 있습니다. 설치 하지 않고도 필요 하지만 이렇게 하려면 사용자 환경에 대 한 인바운드 연결 및 Azure Stack 끝점을 인터넷에서 확인할 수 있어야 합니다. 일부 테스트 클라우드 에이전트와 호환 되지 않습니다.
- A **로컬 에이전트**합니다. 이 옵션을 사용 하면 사용자 환경에 대 한 인바운드 연결 수 없는 시나리오에서 유효성 검사를 실행할 수 있습니다. 일부 테스트는 로컬 에이전트를 통해 실행이 필요합니다.

로컬 에이전트는 특정 Azure Stack 또는 VaaS 솔루션에 연결 되지 않습니다. 모범 사례로 Azure Stack 환경 외부에서 실행 되도록 합니다.

로컬 에이전트를 추가 하는 방법에 지침은 [로컬 에이전트를 배포](azure-stack-vaas-local-agent.md)합니다.

## <a name="next-steps"></a>다음 단계

- [서비스 유효성 검사에 대 한 모범 사례](azure-stack-vaas-best-practice.md)