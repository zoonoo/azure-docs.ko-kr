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
ms.date: 11/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: dc0c6a37e11574cef857a7af773a9d90bea57357
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52972068"
---
# <a name="interactive-feature-verification-testing"></a>대화형 기능 확인 테스트  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

테스트 시스템에 대 한 요청에 대화형 기능이 확인 테스트 프레임 워크를 사용할 수 있습니다. 테스트를 요청할 때 Microsoft 대화형 수동 단계를 수행 해야 하는 테스트를 준비 하려면 프레임 워크를 사용 합니다. Microsoft은 프레임 워크를 사용 여러 독립 실행형 자동화 된 테스트를 함께 연결할 수 있습니다.

이 문서에서는 간단한 수동 시나리오를 설명 합니다. 테스트는 Azure Stack에서 디스크 교체를 확인 합니다. 프레임 워크는 각 단계에서 진단 로그를 수집합니다. 찾으면서 문제를 디버그할 수 있습니다. 프레임 워크는 또한 다른 도구 또는 프로세스에서 생성 된 로그를 공유할 수 있도록 허용 하 고 시나리오에 피드백을 제공할 수 있습니다.

## <a name="overview-of-a-test-pass"></a>테스트 과정의 개요

디스크 교체에 대 한 테스트는 일반적인 시나리오입니다. 이 예제에서는 테스트에는 7 단계:

1.  새 **테스트 통과** 워크플로.
2.  선택 된 **식별 테스트 디스크**합니다.
3.  테스트를 시작 합니다.
4.  대화형 인증 시나리오에서 작업을 선택 합니다.
5.  시나리오의 결과 확인 합니다.
6.  Microsoft 테스트 결과를 보냅니다.
7.  VaaS 포털에서 상태를 확인 합니다.

## <a name="create-a-new-test-pass"></a>새 테스트 패스를 만들기

1.  로 이동 합니다 [Azure Stack 유효성 검사 포털](https://www.azurestackvalidation.com) 에 로그인 합니다.

2.  새 솔루션을 만들거나 기존 계정을 선택 합니다.

3.  선택 **시작** 에 **테스트 통과** 바둑판식으로 배열 합니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image1.png)

4.  에 대 한 이름을 입력 합니다 **테스트 통과** 워크플로.

5.  지침에 따라 환경 및 일반 테스트 매개 변수를 입력 합니다 [서비스로 Azure Stack 유효성 검사에 대 한 워크플로 일반 매개 변수](azure-stack-vaas-parameters.md) 문서.

6.  진단 연결 문자열에 지정 된 형식을 따라야 합니다.는 [테스트 매개 변수](azure-stack-vaas-parameters.md#test-parameters) 섹션을 [워크플로 일반 매개 변수](azure-stack-vaas-parameters.md) 문서.

7.  테스트에 대 한 필수 매개 변수를 입력 합니다.

8.  대화형 테스트 실행을 실행 하려면 에이전트를 선택 합니다.

> [!Note]  
> 디스크 식별 대화형 기능이 확인 테스트에 대 한 도메인 관리자 사용자 이름 및 암호를 지정 해야 합니다.

![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image2.png)

## <a name="select-the-test"></a>테스트를 선택 합니다.

1.  선택 **디스크 식별 테스트\<버전 >** 합니다.

    > [!Note]  
    > 테스트 참고 자료를 개선 하는 대로 버전의 테스트를 하나씩 늘립니다. Microsoft에서 다르게 지정 하지 않으면에 항상 가장 높은 버전을 사용 해야 합니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2.  선택 하 여 도메인 관리자 사용자 이름 및 암호를 제공한 **편집**합니다.

3.  적절 한 테스트 실행 에이전트/dvm이에서 테스트를 시작 하려면 선택 합니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4.  선택 **제출** 테스트를 시작 합니다.

![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image6.png)

## <a name="start-the-test"></a>테스트 시작

디스크 식별 테스트 프롬프트 VaaS 에이전트를 실행 하는 컴퓨터에 표시 합니다. 일반적으로 이것이 Jumpbox를 dvm이 Azure Stack 인스턴스에 대 한 합니다.

![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image8.png)

## <a name="choose-the-actions"></a>작업 선택

1.  수행 합니다 **설명서** 및 **유효성 검사** 검토 하는 방법은 Microsoft에서이 시나리오를 수행 하는 방법에 대 한 링크.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image9.png)

2.  **다음**을 선택합니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image10.png)

3.  Precheck 스크립트를 실행 하는 지침을 따릅니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image11.png)

4.  Precheck 스크립트를 성공적으로 완료 되 면 수동 시나리오 (디스크 교체)를 기준으로 실행 합니다 **설명서** 하 고 **유효성 검사** 에서 링크를 **정보**탭 합니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image12.png)

5.  수동 시나리오를 수행 하는 동안 대화 상자를 닫지 마세요.

6.  수동 시나리오를 수행 했으면 게시물 확인 스크립트를 실행 하려면 지침을 따릅니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image13.png)

7.  수동 시나리오 (디스크 교체) 성공적으로 완료 되 면 선택 **다음**합니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image14.png)

> [!Important]  
> 창을 닫으면 그 전에 테스트는 중지 됩니다.

## <a name="check-the-status"></a>상태 확인

1.  테스트가 완료 되 면 피드백을 보내도록 묻는 메시지가 나타납니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image15.png)

2.  이러한 질문에는 시나리오의 성공 비율과 릴리스 품질을 평가 하는 Microsoft 데 도움이 됩니다.

## <a name="send-the-test-result"></a>테스트 결과 전송 합니다.

1.  Microsoft에 제출 하려면 모든 로그 파일을 연결 합니다.

    ![대체 텍스트](media/azure-stack-vaas-interactive-feature-verification/image16.png)

2.  피드백 제출을 EULA를 수락 합니다.

3.  선택 **제출** 결과 Microsoft에 보냅니다.

## <a name="next-steps"></a>다음 단계

- [모니터링 및 VaaS 포털에서 테스트를 관리 합니다.](azure-stack-vaas-monitor-test.md)
