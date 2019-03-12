---
title: Azure Stack에 대 한 서비스와 유효성 검사 개요 | Microsoft Docs
description: 개요 서비스로 Azure Stack 유효성 검사입니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ca22f29597cb452d6d33338b8ba0367c9377f6fb
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772520"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Azure Stack에 대 한 서비스와 유효성 검사는 무엇입니까?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

유효성 검사 (VaaS) 서비스로 microsoft Azure Stack 제품 엔지니어링 배치 된 파트너 솔루션을 위한 네이티브 Azure 서비스입니다. 솔루션 파트너 솔루션 Microsoft의 요구 사항을 충족 하 고 Azure Stack을 사용 하 여 예상 대로 작동을 확인 하려면 서비스를 사용할 수 있습니다.

VaaS에 대 한 기본 사용 됩니다.

- 새 Azure Stack 솔루션의 유효성 검사
- Azure Stack 소프트웨어 변경 유효성 검사
- 배포 중에 사용 하는 솔루션 파트너 패키지에 디지털 서명
- 미리 보기 VaaS 테스트 참고 자료

## <a name="validate-a-new-azure-stack-solution"></a>새 Azure Stack 솔루션의 유효성을 검사합니다

사용 하 여 파트너를 **솔루션 유효성 검사** 워크플로를 새 Azure Stack 솔루션의 유효성을 검사 합니다. 솔루션에 필요한 하드웨어 랩 키트 (HLK) Azure Stack 구성 요소 테스트를 통과 해야 합니다. 하드웨어 구성의 범위를 인증 하려면 워크플로의 각 새 솔루션에 대 한 두 번 실행 해야 합니다: 최소 및 최대 구성에 대 한 번씩입니다.

자세한 내용은 [새 Azure Stack 솔루션의 유효성을 검사](azure-stack-vaas-validate-solution-new.md)합니다.

## <a name="validate-changes-to-the-azure-stack-software"></a>Azure Stack 소프트웨어 변경 내용 유효성 검사

사용 하 여 파트너를 **패키지 유효성 검사** 워크플로를 최신 Azure Stack 소프트웨어 업데이트를 사용 하 여 해당 솔루션 작동 하는지 확인 합니다. 패키지 유효성 검사 워크플로 업데이트를 적용 하려면 패치 및 업데이트 (& U)를 사용한 Microsoft 권장 하드웨어 환경에서 실행 되어야 합니다. 또한 초기 빌드 워크플로 실행 하는 것이 좋습니다.

자세한 내용은 [Microsoft에서 소프트웨어 업데이트의 유효성을 검사](azure-stack-vaas-validate-microsoft-updates.md)합니다.

## <a name="get-digitally-signed-solution-partner-packages"></a>디지털 서명 된 솔루션 파트너 패키지 가져오기

Azure Stack 업데이트의 유효성을 검사 하는 것 외에도 사용 하 여 파트너를 **패키지 유효성 검사** Azure Stack 파트너 관련 드라이버, 펌웨어 및 기타 소프트웨어를 포함 하는 OEM 사용자 지정 패키지에 대 한 업데이트의 유효성을 검사 하는 워크플로 Azure Stack 소프트웨어의 배포 중에 사용 합니다. 최소 지원 되는 최소 규모 솔루션을 사용 하는 Azure Stack 소프트웨어의 현재 버전의 유효성을 검사할 패키지를 배포 합니다. 패키지는 테스트를 실행 하기 전에 VaaS에 제출 됩니다. 테스트가 성공 하면 알립니다 [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) Azure Stack 디지털 서명을 사용 하 여 서명 된 패키지 테스트를 완료 하 고 디지털 이어야 합니다. Microsoft는 패키지를 서명 하 고 패키지 VaaS 포털에서 다운로드할 수는 Azure Stack 파트너에 게 알립니다.

자세한 내용은 [패키지의 유효성을 검사 하는 OEM](azure-stack-vaas-validate-oem-package.md)합니다.

## <a name="preview-vaas-test-collateral"></a>미리 보기 VaaS 테스트 참고 자료

Microsoft은 정기적으로 새로운 기능이 Azure Stack에서 사용할 수 있습니다. 시장에 이러한 기능을 제공 하기 위해 개발 프로세스의 일환으로, 새 테스트 참고 자료는에서 사용 가능 합니다 **테스트 통과** 워크플로. 테스트 통과 워크플로 비공식 테스트 실행에 대 한 허용 하도록 다른 워크플로에서 테스트 참고 자료가 포함 됩니다. 승인에 대 한 결과 제출 하는 테스트 통과 워크플로 사용 하지 마세요. 솔루션 유효성 검사 및 패키지 유효성 검사 워크플로 사용 하 여 솔루션에 대 한 공식 승인을 받습니다.

자세한 내용은 [빠른 시작: 서비스 포털로 유효성 검사를 사용 하 여 첫 번째 테스트를 예약 하려면](azure-stack-vaas-schedule-test-pass.md)합니다.

## <a name="validation-workflow-tests-summary"></a>유효성 검사 워크플로 테스트 요약

| 유효성 검사 워크플로 | 필요한 테스트 |
|----|------------|
| [새 솔루션 유효성 검사](azure-stack-vaas-validate-solution-new.md) | 클라우드 시뮬레이션 엔진<br>SDK Operational Suite를 계산 합니다.<br>디스크 식별 테스트<br>KeyVault 확장 SDK에 대 한 운영 Suite<br>KeyVault SDK 운영 도구 모음<br>네트워크 SDK 운영 도구 모음<br>저장소 계정 SDK Operational Suite<br> |
| [OEM 패키지 유효성 검사](azure-stack-vaas-validate-oem-package.md) | OEM 확장 패키지 확인<br>클라우드 시뮬레이션 엔진 |
| [월별 업데이트 유효성 검사](azure-stack-vaas-validate-microsoft-updates.md) | 월별 Azure Stack 업데이트 확인<br>클라우드 시뮬레이션 엔진<br> |

## <a name="next-steps"></a>다음 단계

- [서비스 리소스와 유효성 검사 설정](azure-stack-vaas-set-up-resources.md)
- 에 대 한 자세한 [서비스 핵심 개념으로 유효성 검사](azure-stack-vaas-key-concepts.md)
