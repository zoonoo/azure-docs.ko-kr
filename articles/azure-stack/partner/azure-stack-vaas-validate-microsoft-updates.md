---
title: 서비스로 Azure Stack 유효성 검사에는 Microsoft의 소프트웨어 업데이트의 유효성을 검사 | Microsoft Docs
description: 서비스 유효성 검사를 사용 하 여 Microsoft의 소프트웨어 업데이트의 유효성을 검사 하는 방법에 알아봅니다.
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
ms.openlocfilehash: ad9d5057c18d316dcf3254dc57a3184c1b75fc50
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780843"
---
# <a name="validate-software-updates-from-microsoft"></a>Microsoft의 소프트웨어 업데이트의 유효성을 검사합니다

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft은 Azure Stack 소프트웨어 업데이트를 주기적으로 해제 됩니다. 이러한 업데이트는 Azure stack 파트너 coengineering 제공 됩니다. 업데이트를 공개적으로 사용할 수 있는 사전에 제공 됩니다. 솔루션에 대 한 업데이트를 확인 하 고 Microsoft에 피드백을 제공할 수 있습니다.

Azure Stack에 Microsoft 소프트웨어 업데이트는 명명 규칙을 사용 하 여 지정 된, 2018 년 3 월에 대 한 예를 들어은 1803 나타내는 업데이트 합니다. 주기 및 릴리스 정보는 사용할 수 있는 Azure Stack update 정책에 대 한 자세한 내용은 [Azure Stack 서비스 정책](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy)합니다.

## <a name="prerequisites"></a>필수 조건

VaaS 월별 업데이트 프로세스를 행사 하기 전에 다음 항목을 잘 알고 있어야 합니다.

- [서비스 키 개념으로 유효성 검사](azure-stack-vaas-key-concepts.md)
- [대화형 기능 확인 테스트](azure-stack-vaas-interactive-feature-verification.md)

## <a name="required-tests"></a>필요한 테스트

다음 테스트는 월별 소프트웨어 유효성 검사를 위해 다음 순서 대로 실행할지:

1. 월별 Azure Stack 업데이트 확인
2. 클라우드 시뮬레이션 엔진

## <a name="validating-software-updates"></a>소프트웨어 업데이트 유효성 검사

1. 새 **패키지 유효성 검사** 워크플로.
1. 위의 필수 테스트에서 지침을 따릅니다 [패키지 유효성 검사 실행 테스트](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests)합니다. 에 대 한 추가 지침은 아래 섹션을 참조 합니다 **월별 Azure Stack 업데이트 확인** 테스트 합니다.

### <a name="apply-the-monthly-update"></a>월별 업데이트를 적용 합니다.

1. 에 대 한 테스트를 실행 하는 에이전트를 선택 합니다.
1. 일정 **월별 Azure Stack 업데이트 확인**합니다.
1. 스탬프에 현재 배포 된 확장 패키지를 OEM 위치 및 위치를 업데이트 하는 동안 적용 되는 OEM 확장 패키지를 제공 합니다. 이러한 패키지에 대 한 Url을 구성 하려면 참조 [유효성 검사에 대 한 패키지 관리](azure-stack-vaas-validate-oem-package.md#managing-packages-for-validation)합니다.
1. 선택한 에이전트에서 UI의 단계를 따릅니다.

질문이 나 궁금한 내용이 있으면 문의 [VaaS 도움말](mailto:vaashelp@microsoft.com)합니다.

## <a name="next-steps"></a>다음 단계

- [모니터링 및 VaaS 포털에서 테스트를 관리 합니다.](azure-stack-vaas-monitor-test.md)