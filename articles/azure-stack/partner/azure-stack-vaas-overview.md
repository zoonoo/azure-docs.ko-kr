---
title: Azure Stack에 대 한 서비스와 유효성 검사 개요 | Microsoft Docs
description: 알려진 문제 서비스로 Azure Stack 유효성 검사를 간략하게 설명 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: c96e7385356354d398108ad69492603d38667e46
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235307"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Azure Stack에 대 한 서비스와 유효성 검사는 무엇입니까?

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

유효성 검사 (VaaS) 서비스로 microsoft Azure Stack 제품 엔지니어링 배치 된 파트너 솔루션을 위한 네이티브 Azure 서비스입니다. 솔루션 파트너 솔루션 Microsoft의 요구 사항을 충족 하 고 Azure Stack을 사용 하 여 예상 대로 작동을 확인 하려면 서비스를 사용할 수 있습니다.

VaaS 주된 용도 다음과 같습니다.

- 새 Azure Stack 솔루션의 유효성을 검사합니다
- Azure Stack 소프트웨어 변경 내용 유효성 검사
- 디지털 서명 된 솔루션 파트너에 배포 하는 동안 사용 되는 패키지 가져오기
- 미리 보기 Azure Stack에 대 한 유효성 검사 참고 자료

## <a name="validate-new-azure-stack-solution"></a>새 Azure Stack 솔루션의 유효성 검사

파트너는 솔루션 유효성 검사 워크플로 사용 하 여 새 Azure Stack 솔루션을 확인 합니다. 솔루션에 필요한 하드웨어 랩 키트 (HKL) Azure Stack 테스트 구성 요소 테스트를 전달 해야 합니다. 각 새 솔루션에 대 한 두 번 워크플로 실행 해야 합니다: 최소 및 최대 구성에 한 번씩입니다.

자세한 내용은 [새 Azure Stack 솔루션의 유효성을 검사](azure-stack-vaas-validate-solution-new.md)합니다.

## <a name="validate-changes-to-the-azure-stack-software"></a>Azure Stack 소프트웨어 변경 내용 유효성 검사

파트너는 솔루션 최근 Azure Stack 소프트웨어 업데이트를 사용 하 여 작동 하는지 확인 하려면 패키지 유효성 검사 워크플로 사용 합니다. 최소한 패키지 유효성 검사 워크플로 실행 되어야 합니다 Microsoft에서 권장 하드웨어 환경에 솔루션을 패치 및 업데이트 (& U) 업데이트를 적용할 사용 되었습니다. 기준 빌드 패키지 유효성 검사를 실행 해야 합니다.

자세한 내용은 [Microsoft에서 소프트웨어 업데이트의 유효성을 검사](azure-stack-vaas-validate-microsoft-updates.md)합니다.

## <a name="get-digitally-signed-solution-partner-packages"></a>디지털 서명 된 솔루션 파트너 패키지 가져오기

Azure Stack 업데이트의 유효성을 검사 하는 것 외에도 Azure Stack 파트너 관련 드라이버, 펌웨어 및 Azure Stack 배포 중 사용 되는 다른 소프트웨어를 포함 하는 OEM 사용자 지정 패키지에 대 한 업데이트를 확인 하려면 패키지 유효성 검사 워크플로 사용할 수 있습니다. 소프트웨어입니다. Azure Stack 소프트웨어 사용 하 여 최소의 최소 크기의 솔루션 지원 되는 현재 버전에서 확인 하는 패키지를 배포 합니다. 업데이트 된 패키지 테스트를 시작 하기 전에 서비스에 업로드 해야 합니다. 테스트 성공 알림 vaashelp@microsoft.com합니다. 패키지 테스트를 완료 하 고 Azure Stack 디지털 서명을 사용 하 여 디지털로 서명 해야 Azure Stack 파트너를 알려 줍니다. Microsoft는 패키지를 서명 하 고 패키지는 포털에서 다운로드할 수는 Azure Stack 파트너에 게 알립니다.

자세한 내용은 [패키지의 유효성을 검사 하는 OEM](azure-stack-vaas-validate-oem-package.md)합니다.

## <a name="preview-azure-stack-validation-collateral"></a>미리 보기 Azure Stack에 대 한 유효성 검사 참고 자료

Microsoft은 정기적으로 새로운 기능이 Azure Stack에서 사용할 수 있습니다. 시장에 이러한 기능을 제공 하기 위해 개발 프로세스의 일부로 테스트 통과 워크플로에서 새 테스트 참고 자료는 가능 합니다. 테스트 통과 워크플로 비공식 테스트 실행에 대 한 허용 하도록 다른 워크플로에서 테스트 참고 자료가 포함 됩니다. 승인에 대 한 결과 제출 하는 테스트 통과 워크플로 사용 하지 마세요. 솔루션 유효성 검사 및 패키지 유효성 검사 워크플로 사용 하 여 솔루션에 대 한 공식 승인을 받습니다.

## <a name="next-steps"></a>다음 단계

- 시작 및 [서비스 계정으로 유효성 검사 설정](azure-stack-vaas-validate-solution-new.md)
