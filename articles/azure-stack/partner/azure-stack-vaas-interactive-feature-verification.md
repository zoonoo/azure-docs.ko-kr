---
title: 대화형 기능 확인 서비스로 Azure Stack 유효성 검사 테스트 | Microsoft Docs
description: 서비스 유효성 검사를 사용 하 여 Azure Stack에 대 한 대화형 기능 유효성 검사 테스트를 만드는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d3db8ea8639f73f3522ddaa358195e7c9ef2f9a9
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766005"
---
# <a name="interactive-feature-verification-testing"></a>대화형 기능 확인 테스트  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

테스트 시스템에 대 한 요청에 대화형 기능이 확인 테스트 프레임 워크를 사용할 수 있습니다. 테스트를 요청할 때 Microsoft 대화형 수동 단계를 수행 해야 하는 테스트를 준비 하려면 프레임 워크를 사용 합니다. Microsoft은 프레임 워크를 사용 여러 독립 실행형 자동화 된 테스트를 함께 연결할 수 있습니다.

이 문서에서는 간단한 수동 시나리오를 설명 합니다. 테스트는 Azure Stack에서 디스크 교체를 확인 합니다. 프레임 워크는 각 단계에서 진단 로그를 수집합니다. 찾으면서 문제를 디버그할 수 있습니다. 프레임 워크는 또한 다른 도구 또는 프로세스에서 생성 된 로그를 공유할 수 있도록 허용 하 고 시나리오에 피드백을 제공할 수 있습니다.

> [!Important]  
> 이 문서에서는 디스크 식별을 수행 하는 단계를 참조 합니다. 이 단순히 데모를 테스트 통과 과정에서 수집 된 모든 결과 새 솔루션 확인을 위해 사용할 수 없습니다.

## <a name="overview-of-interactive-testing"></a>대화형 테스트 개요

디스크 교체에 대 한 테스트는 일반적인 시나리오입니다. 이 예제에서는 테스트에는 5 단계:

1. 새 **테스트 통과** 워크플로.
2. 선택 된 **식별 테스트 디스크**합니다.
3. 메시지가 표시 되 면 수동 단계를 완료 합니다.
4. 시나리오의 결과 확인 합니다.
5. Microsoft 테스트 결과를 보냅니다.

## <a name="create-a-new-test-pass"></a>새 테스트 패스를 만들기

에 대 한 지침을 따르세요는 기존 테스트를 통과 사용할 수 없으면 [테스트를 예약](azure-stack-vaas-schedule-test-pass.md)합니다.

## <a name="schedule-the-test"></a>테스트를 예약 합니다.

1. 선택 **식별 테스트 디스크**합니다.

    > [!Note]  
    > 테스트 참고 자료를 개선 하는 대로 버전의 테스트를 하나씩 늘립니다. Microsoft에서 다르게 지정 하지 않으면에 항상 가장 높은 버전을 사용 해야 합니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. 선택 하 여 도메인 관리자 사용자 이름 및 암호를 제공한 **편집**합니다.

1. 적절 한 테스트 실행 에이전트/dvm이에서 테스트를 시작 하려면 선택 합니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. 선택 **제출** 테스트를 시작 합니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. 이전 단계에서 선택한 에이전트에서 대화형 테스트에 대 한 UI에 액세스 합니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. 수행 합니다 **설명서** 및 **유효성 검사** 검토 하는 방법은 Microsoft에서이 시나리오를 수행 하는 방법에 대 한 링크.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. **다음**을 선택합니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. Precheck 스크립트를 실행 하는 지침을 따릅니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. Precheck 스크립트를 성공적으로 완료 되 면 수동 시나리오 (디스크 교체)를 기준으로 실행 합니다 **설명서** 하 고 **유효성 검사** 에서 링크를 **정보**탭 합니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > 수동 시나리오를 수행 하는 동안 대화 상자를 닫지 마세요.

1. 수동 시나리오를 수행 했으면 게시물 확인 스크립트를 실행 하려면 지침을 따릅니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. 수동 시나리오 (디스크 교체) 성공적으로 완료 되 면 선택 **다음**합니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > 창을 닫으면 그 전에 테스트는 중지 됩니다.

1. 테스트 환경에 대 한 피드백을 제공 합니다. 이러한 질문에는 시나리오의 성공 비율과 릴리스 품질을 평가 하는 Microsoft 데 도움이 됩니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. Microsoft에 제출 하려면 모든 로그 파일을 연결 합니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. 피드백 제출을 EULA를 수락 합니다.

1. 선택 **제출** 결과 Microsoft에 보냅니다.

## <a name="next-steps"></a>다음 단계

- [모니터링 및 VaaS 포털에서 테스트를 관리 합니다.](azure-stack-vaas-monitor-test.md)
