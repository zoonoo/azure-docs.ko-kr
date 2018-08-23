---
title: 서비스 및 앱 빌드를 사용 하는 경우 Azure 및 Azure Stack 간의 주요 차이점을 이해 | Microsoft Docs
description: 필요한 서비스를 사용 하거나 Azure Stack에 대 한 앱을 작성 하기 위해 알아야 합니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/15/2018
ms.author: sethm
ms.openlocfilehash: a8d211992f52c9719cad76f16133e23eba24d422
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139668"
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>주요 고려 사항: 서비스를 사용 하 여 또는 Azure Stack에 대 한 앱 빌드

서비스를 사용 하거나 Azure Stack에 대 한 앱을 작성 하기 전에 Azure Stack 및 Azure 간의 차이점을 이해 해야 합니다. 이 문서에서는 하이브리드 클라우드 개발 환경으로 Azure Stack을 사용 하는 경우 중요 한 고려 사항을 식별 합니다.

## <a name="overview"></a>개요

Azure Stack은 회사 또는 서비스 공급자의 데이터 센터에서 Azure 서비스를 사용할 수 있게 하는 하이브리드 클라우드 플랫폼입니다. Azure Stack에서 앱을 빌드할 수 있으며 그런 다음 Azure Stack, Azure 또는 Azure 하이브리드 클라우드 배포.

귀하가 Azure Stack 운영자를 사용 하 여 사용할 수 있는 서비스 및 지원을 받는 방법을 알 수 있습니다. 이 서비스는 사용자 지정 된 계획 및 제품은를 제공합니다.

Azure 기술 콘텐츠는 Azure Stack 대신 Azure 서비스에 대 한 앱이 개발 되는 가정 합니다. 를 빌드 및 Azure Stack에 앱 배포와 같은 몇 가지 주요 차이점을 이해 해야 합니다.

* Azure Stack 서비스 및 Azure에서 사용할 수 있는 기능의 하위 집합을 제공 합니다.
* 회사 또는 서비스 공급자를 제공 하고자 하는 서비스를 선택할 수 있습니다. 사용 가능한 옵션은 사용자 지정된 서비스 또는 응용 프로그램에 포함 될 수 있습니다. 자체 사용자 지정 된 설명서를 제공할 수 있습니다.
* 올바른 사용 해야 합니다 (예: 포털 주소 및 Azure Resource Manager 끝점의 Url) Azure Stack 별 끝점입니다.
* Azure Stack에서 지원 되는 PowerShell 및 API 버전을 사용 해야 합니다. 지원 되는 버전을 사용 하 여 Azure Stack 및 Azure 둘 다에서 앱은 작동 하는지 확인 합니다.

## <a name="cheat-sheet-high-level-differences"></a>치트 시트: 고급 차이점

다음 표에서 Azure Stack 및 Azure 간의 대략적인 차이 설명 합니다. Azure Stack 용 개발 하거나 Azure Stack 서비스를 사용할 때 이러한 차이점에 주의 유지 합니다.
*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

| 영역 | Azure (전역) | Azure Stack |
| -------- | ------------- | ----------|
| 작동 하기? | Microsoft | 조직 또는 서비스 공급자입니다.|
| 지원 담당자가 누구 | Microsoft | 통합된 시스템을 지원 (조직 또는 서비스 공급자)에서 귀하가 Azure Stack 운영자에 게 문의 합니다.<br><br>Azure Stack 개발 키트 지원에 대 한 참조를 [Microsoft 포럼](https://social.msdn.microsoft.com/Forums/home?forum=azurestack)합니다. 개발 키트는 평가 환경, 이므로 통해 Microsoft 고객 지원 서비스 (CSS) 제공 하는 공식 지원 되지 않습니다.
| 사용 가능한 서비스 | 목록은 [Azure 제품](https://azure.microsoft.com/services/?b=17.04b)합니다. 사용 가능한 서비스는 Azure 지역에 따라 달라 집니다. | Azure Stack Azure 서비스의 하위 집합을 지원 합니다. 실제 서비스 제공 하기로 하는 조직 또는 서비스 공급자에 따라 달라 집니다.
| Azure Resource Manager 끝점 * | https://management.azure.com | Azure Stack 통합 시스템, 귀하가 Azure Stack 운영자가 제공 하는 끝점을 사용 합니다.<br><br>개발 키트를 사용 합니다. https://management.local.azurestack.external
| 포털 URL * | [https://portal.azure.com](https://portal.azure.com) | Azure Stack 통합 시스템, 귀하가 Azure Stack 운영자가 제공한 URL로 이동 합니다.<br><br>개발 키트를 사용 합니다. https://portal.local.azurestack.external
| 지역 | 배포할 지역을 선택할 수 있습니다. | Azure Stack 통합 시스템, 시스템에서 사용할 수 있는 영역을 사용 합니다.<br><br>개발 키트에 대 한 지역은 항상 **로컬**합니다.
| 리소스 그룹 | 리소스 그룹을 여러 지역에 걸쳐 있습니다. | 통합된 시스템 및 개발 키트 모두에 대 한 지역 하나만 있습니다.
|지원 되는 네임 스페이스, 리소스 유형 및 API 버전 | 최신 (또는 이전 버전을 아직 사용 되지 됩니다). | Azure Stack 특정 버전을 지원합니다. 이 문서의 "버전 요구 사항" 섹션을 참조 하세요.
| | |

* Azure Stack 운영자 인 경우 참조 [관리자 포털을 사용 하 여](../azure-stack-manage-portals.md) 하 고 [관리 기본 사항](../azure-stack-manage-basics.md) 자세한 내용은 합니다.

## <a name="helpful-tools-and-best-practices"></a>유용한 도구 및 모범 사례
 
 Microsoft은 Azure Stack 용 개발 하는 여러 가지 도구와 도움이 되는 지침을 제공 합니다.

| 권장 사항 | 참조 | 
| -------- | ------------- | 
| 개발자 워크스테이션에서 올바른 도구를 설치 합니다. | - [PowerShell 설치](azure-stack-powershell-install.md)<br>- [도구 다운로드](azure-stack-powershell-download.md)<br>- [PowerShell 구성](azure-stack-powershell-configure-user.md)<br>- [Visual Studio 설치](azure-stack-install-visual-studio.md) 
| 다음 항목에 대 한 정보를 검토 합니다.<br>-Azure Resource Manager 템플릿 고려 사항<br>-빠른 시작 템플릿을 찾는 방법<br>-Azure를 사용 하 여 Azure Stack 용으로 개발할 수 있도록 정책 모듈을 사용 합니다. | [Azure Stack에 대한 개발](azure-stack-developer.md) | 
| 검토 하 고 템플릿에 대 한 모범 사례를 따르세요. | [Resource Manager 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>버전 요구 사항

Azure Stack 특정 버전의 Azure PowerShell 및 Azure 서비스 Api 지원합니다. 두 Azure Stack 및 Azure 앱에 배포할 수 있도록 지원 되는 버전을 사용 합니다.

사용 하 여 올바른 버전의 Azure PowerShell 사용 [API 버전 프로필](azure-stack-version-profiles.md)합니다. 사용할 수 있는 최신 API 버전 프로필을 확인 하려면 사용 하는 Azure Stack의 빌드에 대해 알아봅니다. Azure Stack 관리자에 게이 정보를 가져올 수 있습니다.

>[!NOTE]
 Azure Stack Development Kit를 사용 하는 경우 관리자 권한이의 "현재 버전 확인" 섹션을 참조 하세요 [업데이트 관리](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#determine-the-current-version) Azure Stack 빌드를 확인 하려면.

다른 Api에 대 한 네임 스페이스, 리소스 유형 및 Azure Stack 구독에 지원 되는 API 버전을 출력 하려면 다음 PowerShell 명령을 실행 합니다. 여기에 메모 속성 수준에서 차이 수 있습니다. (이 명령이 작동 하려면 해야 이미 [설치](azure-stack-powershell-install.md) 하 고 [구성](azure-stack-powershell-configure-user.md) Azure Stack 환경에 대 한 PowerShell. 또한 해야 Azure Stack 제품을 구독 합니다.)

 ```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

예제 출력 (잘림): ![Get-azurermresourceprovider 명령의 예제 출력](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>다음 단계

서비스 수준 차이점에 대 한 자세한 내용은 다음을 참조 하세요.

* [Azure Stack에서 Virtual Machines에 대 한 고려 사항](azure-stack-vm-considerations.md)
* [Azure Stack의 Storage에 대 한 고려 사항](azure-stack-acs-differences.md)
* [Azure Stack 네트워킹에 대 한 고려 사항](azure-stack-network-differences.md)
