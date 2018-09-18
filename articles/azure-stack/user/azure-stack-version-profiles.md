---
title: Azure Stack에서 API 버전 프로필 관리 | Microsoft Docs
description: Azure Stack에서 API 버전 프로필에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 270587f484216d130848ee3c7bdc8ae1c1df0afc
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982156"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Azure Stack에서 API 버전 프로필 관리

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

프로필 API는 Azure 리소스 공급자 및 Azure REST 끝점에 대 한 API 버전을 지정합니다. API 프로필을 사용 하 여 서로 다른 언어로 사용자 지정 클라이언트를 만들 수 있습니다. 각 클라이언트 API 프로필을 사용 하 여 Azure Stack에 대 한 올바른 리소스 공급자 및 API 버전을 문의 하십시오.

각 리소스 공급자 API의 버전은 Azure Stack 호환 정확 하 게 분류 하지 않고 Azure 리소스 공급자를 사용 하 여 작업 하는 앱을 만들 수 있습니다. 방금 프로필;에 응용 프로그램을 맞춤합니다 SDK는 올바른 API 버전으로 되돌립니다.

이 항목에서는 도와줍니다.

 - Azure Stack에 대 한 API 프로필을 이해 합니다.
 - 프로필 API를 사용 하 여 솔루션을 개발 하는 방법을 알아봅니다.
 - 코드 관련 지침을 찾을 수 있는 위치를 참조 하세요.

## <a name="summary-of-api-profiles"></a>프로필 API 요약

- API 프로필은 Azure 리소스 공급자의 집합 및 해당 API 버전을 나타내는 데 사용 됩니다.
- API 프로필은 생성 하 여 여러 Azure 클라우드에서 템플릿을 만들 수 있습니다. 프로필은 호환 되 고 안정적인 인터페이스에 대 한 요구 사항이 충족 하도록 설계 되었습니다.
- 프로필에는 1 년에 4 번이 릴리스됩니다.
- 세 개의 프로필 명명 규칙이 사용 됩니다.
    - **latest**  
        전역 Azure에서 릴리스된 최신 API 버전을 포함 합니다.
    - **yyyy-mm-dd-hybrid**  
    매기고 주기로 발표,이 이번에 중점을 둡니다 일관성과 안정성 여러 클라우드 간에. 이 프로필에는 최적의 Azure Stack 호환성 대상 으로합니다.
    - **yyyy mm-dd 프로필** 최적의 안정성 및 최신 기능 사이 위치 합니다.

## <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Azure API 프로필 및 Azure Stack 호환성

최신 Azure API 프로필은 Azure Stack을 사용 하 여 호환 되지 않습니다. Azure Stack 솔루션에 대해 사용 하도록 프로필을 식별 하는 다음 명명 규칙을 사용할 수 있습니다.

**최신**  
이 프로필에는 Azure Stack에서 작동 하지 않습니다는 전역 Azure에 있는 최신 API 버전에 있습니다. **최신** 에 가장 큰 수의 주요 변경 내용입니다. 안정성 및 다른 클라우드를 사용 하 여 호환성 프로필을 따로 배치합니다. 최신 API 버전을 사용 하고자 하는 경우 **최신** 프로필 사용 해야 합니다.

**Yyyy-mm-dd-하이브리드**  
이 프로필은 매년 3 월과 9 월에 해제 됩니다. 이 프로필에는 최적의 안정성 및 다양 한 클라우드를 사용 하 여 호환성에 있습니다. **Yyyy-mm-dd-하이브리드** 는 전역 Azure 및 Azure Stack을 대상으로 설계 되었습니다. 이 프로필에 나열 된 Azure API 버전 Azure Stack에 나열 된 것과 동일 합니다. 하이브리드 클라우드 솔루션에 대 한 코드를 개발 하려면이 프로필을 사용할 수 있습니다.

**yyyy-mm-dd-profile**  
이 프로필은 글로벌 Azure 용 년 6 월 및 12 월에 해제 됩니다. 이 프로필은 Azure Stack;에 대해 작동 하지 않습니다. 일반적으로 많은 주요 변경 됩니다. 최적의 안정성 및 최신 기능, 차이 사이 위치 하지만 **최신** 는이 프로필 이며 **최신** 시간과 상관 없이 최신 API 버전을 항상으로 구성 됩니다 API 출시 되었습니다. 예를 들어 내일 새 API 버전을 계산 API에 대 한 만들어지면 해당 API 버전에 나열 됩니다는 **최신**, 되지 않습니다는 **yyyy-mm-dd-프로필** 이 프로필에 이미 있기 때문에 있습니다.  **yyyy mm-dd 프로필** 년 6 월 또는 년 12 월 하기 전에 릴리스된 최신 버전을 설명 합니다.

## <a name="azure-resource-manager-api-profiles"></a>Azure Resource Manager API 프로필

Azure Stack에서 최신 버전의 글로벌 Azure에 API 버전을 사용 하지 않습니다. 솔루션을 만들 때 Azure Stack을 사용 하 여 호환 되는 각 Azure 리소스 공급자에 대 한 API 버전을 확인 해야 합니다.

대신 모든 리소스 공급자 및 Azure Stack에서 지 원하는 특정 버전을 연구, 보다 API 프로필을 사용할 수 있습니다. 프로필에는 리소스 공급자 및 API 버전 집합을 지정합니다. SDK 또는 SDK를 사용 하 여 빌드한 도구는 프로필에 지정 된 대상 api 버전으로 전환 됩니다. API 프로필을 사용 하 여 전체 템플릿을 적용 되는 프로필 버전을 지정할 수 있습니다 하 고 런타임 시 Azure Resource Manager 리소스의 올바른 버전을 선택 합니다.

프로필 API는 Azure Resource Manager, PowerShell, Azure CLI, SDK 및 Microsoft Visual Studio에서 제공 하는 코드를 사용 하는 도구를 사용 하 여 작동 합니다. 도구 및 Sdk 모듈 및 응용 프로그램을 빌드할 때 포함 하는 라이브러리의 버전을 읽을 프로필을 사용할 수 있습니다.

예를 들어, 사용 하 여 저장소 계정을 만드는 PowerShell을 사용 하는 경우는 **Microsoft.Storage** api-version 2016-03-30 및 api 버전을 사용 하 여 Microsoft.Compute 리소스 공급자를 사용 하는 VM을 지 원하는 리소스 공급자 2015-12-01, 조회 하는 PowerShell 모듈 지원 2016-03-30 저장용 해야 하 고는 모듈은 계산 2015-02-01을 지원 하 고 설치 합니다. 대신 프로필을 사용할 수 있습니다. Cmdlet을 사용 하 여 * * 설치 프로필 * profilename * 및 PowerShell 모듈의 올바른 버전을 로드 합니다.

마찬가지로, Python SDK를 사용 하 여 Python 기반 응용 프로그램 빌드를 프로필을 지정할 수 있습니다. SDK에서 스크립트에 지정 된 리소스 공급자에 대 한 올바른 모듈을 로드 합니다.

개발자 솔루션 작성에 집중할 수 있습니다. api 버전에서는 리소스 공급자를 연구 하는 대신 클라우드 함께 작동을 프로필을 사용 하 고 코드는 해당 프로필을 지 원하는 모든 클라우드에서 작동 하는지 알고 있어야 합니다.

## <a name="api-profile-code-samples"></a>API 코드 샘플을 프로필

프로필을 사용 하 여 Azure Stack을 사용 하 여 원하는 언어를 사용 하 여 솔루션을 통합할 수 있도록 코드 샘플을 찾을 수 있습니다. 현재 다음 언어에 대 한 지침 및 샘플을 찾을 수 있습니다.

- **PowerShell**  
사용할 수는 **AzureRM.Bootstrapper** 모듈 API 버전 프로필을 사용 하는 데 필요한 PowerShell cmdlet을 가져오는 PowerShell 갤러리를 통해 사용할 수 있습니다. 정보를 참조 하세요 [PowerShell 사용 하 여 API 버전 프로필](azure-stack-version-profiles-powershell.md)합니다.
- **Azure CLI 2.0**  
Azure Stack 특정 API 버전 프로필을 사용 하도록 사용자의 환경 구성을 업데이트할 수 있습니다. 정보를 참조 하세요 [Azure CLI 2.0을 사용 하 여 API 버전 프로필](azure-stack-version-profiles-azurecli2.md)합니다.
- **GO**  
GO SDK에서 프로필을 다른 서비스에서 다른 버전을 사용 하 여 다양 한 리소스 유형 조합입니다. 프로필은 프로필에서 사용할 수 있는 / 해당 버전을 사용 하 여 경로 **YYYY-월-일** 형식입니다. 정보를 참조 하세요 [GO 사용 하 여 API 버전 프로필](azure-stack-version-profiles-go.md)합니다.
- **Ruby**  
Ruby SDK는 Azure Stack Resource Manager에 대 한 빌드 및 인프라를 관리 하는 데 유용한 도구를 제공 합니다. SDK의 리소스 공급자에는 계산, 가상 네트워크 및 Ruby 언어를 사용 하 여 storage 포함 됩니다. 내용은 [Ruby 사용 하 여 사용 하 여 API 버전 프로필](azure-stack-version-profiles-ruby.md)
- **Python**  
- Python SDK는 Azure Stack 및 전역 Azure와 같은 다른 클라우드 플랫폼을 대상으로 하는 API 버전 프로필을 지원 합니다. 하이브리드 클라우드를 위한 솔루션을 만드는 API 프로필을 사용할 수 있습니다. 내용은 [Python과 함께 사용 하 여 API 버전 프로필](azure-stack-version-profiles-python.md)

## <a name="next-steps"></a>다음 단계

* [Azure Stack용 PowerShell 설치](azure-stack-powershell-install.md)
* [Azure Stack 사용자의 PowerShell 환경 구성](azure-stack-powershell-configure-user.md)
* [프로필에서 지 원하는 리소스 공급자 API 버전에 대 한 세부 정보를 검토](azure-stack-profiles-azure-resource-manager-versions.md)합니다.