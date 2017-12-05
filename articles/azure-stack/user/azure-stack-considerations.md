---
title: "서비스를 사용 하 고 앱을 빌드할 때의 주요 Azure 및 Azure 스택 차이점 | Microsoft Docs"
description: "필요한 서비스를 사용 하거나 Azure 스택 앱을 빌드할 때 알아야 합니다."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/25/2017
ms.author: twooley
ms.custom: mvc
ms.openlocfilehash: 7a385ae50ad3403f41bd31f0676e019ee42d181f
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2017
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>주요 고려 사항: Azure 스택에 대 한 앱을 빌드 또는 서비스를 사용 하 여

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

서비스를 사용 하거나 Azure 스택 앱을 빌드할 때에 Azure 스택와 Azure 간에 차이가 있습니다 이해 해야 합니다. 이 문서에서는 Azure 스택 하이브리드 클라우드 개발 환경으로 대상으로 지정할 경우 주요 고려 사항에 대 한 개요를 제공 합니다.

## <a name="overview"></a>개요

Azure Stack은 회사 또는 서비스 공급자의 데이터 센터에서 Azure 서비스를 사용할 수 있게 하는 하이브리드 클라우드 플랫폼입니다. 개발자는 Azure 스택에 실행 되는 앱을 빌드할 수 있습니다. 다음 azure에 Azure 스택에 이러한 앱을 배포할 수 또는 Azure 스택 클라우드와 Azure 간의 연결을 활용 하는 하이브리드 앱 진정으로 빌드할 수 있습니다.

Azure 스택 연산자를 사용 하는 서비스를 사용 하는 데 사용할 수는 및 지원을 받는 방법 알 수 있습니다. 가 사용자 지정 된 계획 및 제공을 통해 이러한 서비스를 제공합니다.

Azure 기술적 내용 Azure 스택 대신 Azure 서비스에 대 한 응용 프로그램으로 개발 되 고 있는지를 가정 합니다. 를 빌드 및 Azure 스택 앱 배포와 같은 몇 가지 주요 차이점이 이해 해야:

* Azure 스택 서비스 및 Azure에서 사용할 수 있는 기능 하위 집합을 제공 합니다.
* 회사 또는 서비스 공급자는 서비스 제공에 선택할 수 있습니다. 사용자 지정 된 서비스 또는 응용 프로그램이 포함 됩니다. 사용자 지정 된 문서를 제공할 수 있습니다.
* 올바른 사용 해야 합니다 (예: 포털 주소와 Azure 리소스 관리자 끝점의 Url) Azure 스택 별 끝점입니다.
* Azure 스택에서 지원 되는 PowerShell 및 API 버전을 사용 해야 합니다. 이렇게 하면 앱 Azure 스택와 Azure 모두에서 작동 합니다.

## <a name="cheat-sheet-high-level-differences"></a>치트 시트: 고급 차이점

다음 표에서 Azure 스택와 Azure 간에 높은 수준의 차이점을 설명 합니다. Azure 스택에 대 한 개발 하거나 Azure 스택 서비스를 사용할 때 유념를 계속 합니다.

| 영역 | Azure (global) | Azure Stack |
| -------- | ------------- | ----------|
| 가 작동 하기? | Microsoft | 조직 또는 서비스 공급자입니다.|
| 에 지원 문의 | Microsoft | 통합된 된 시스템에 대 한 지원에 대 한 조직 또는 서비스 공급자) (에서 귀하가 Azure 스택 운영자에 게 문의 합니다.<br><br>Azure 스택 개발 키트 지원에 대 한 참조는 [Microsoft 포럼](https://social.msdn.microsoft.com/Forums/home?forum=azurestack)합니다. 개발 키트 평가 환경 이기 때문에 지원은 없습니다 공식를 통해 Microsoft 지원 CSS (고객 서비스)를 제공 합니다.
| 사용 가능한 서비스 | 목록을 보려면 [Azure 제품](https://azure.microsoft.com/services/?b=17.04b)합니다. 사용 가능한 서비스는 Azure 지역에 따라 다릅니다. | Azure 스택 Azure 서비스의 하위 집합을 지원 합니다. 실제 서비스는 조직 또는 서비스 공급자가 제공 하도록 선택에 따라 달라 집니다.
| Azure 리소스 관리자 끝점 * | https://management.azure.com | Azure 스택 통합 하는 시스템에 대 한 Azure 스택 연산자를 제공 하는 끝점을 사용 합니다.<br><br>개발 키트에 대 한 사용: https://management.local.azurestack.external
| 포털 URL * | [https://portal.azure.com/](https://portal.azure.com) | Azure 스택 통합 하는 시스템에 대 한 Azure 스택 연산자를 제공 하는 URL로 이동 합니다.<br><br>개발 키트에 대 한 사용: https://portal.local.azurestack.external
| 지역 | 배포할 지역을 선택할 수 있습니다. | Azure 스택 통합 하는 시스템에 대 한 시스템에서 사용할 수 있는 영역을 사용 합니다.<br><br>개발 키트에 대 한 지역은 항상 **로컬**합니다.
| 리소스 그룹 | 리소스 그룹 영역을 확장할 수 있습니다. | 통합된 시스템와 개발 키트에 대 한 하나의 영역 집합이 있습니다.
|지원 되는 네임 스페이스, 리소스 종류 및 API 버전 | 최신 (또는 아직 사용 되지 않는 이전 버전). | Azure 스택 특정 버전을 지원합니다. 이 문서의 "버전 요구 사항" 섹션을 참조 하십시오.
| | |

* Azure 스택 운영자 인 경우 참조 [관리자 포털을 사용 하 여](../azure-stack-manage-portals.md) 및 [관리 기본 사항](../azure-stack-manage-basics.md) 자세한 정보에 대 한 합니다.

## <a name="helpful-tools-and-best-practices"></a>유용한 도구 및 모범 사례
 
 Microsoft은 Azure 스택을 개발 하는 여러 가지 도구와 때 도움이 되는 지침을 제공 합니다.

| 권장 사항 | 참조 | 
| -------- | ------------- | 
| 개발자 워크스테이션에 올바른 도구를 설치 합니다. | - [PowerShell 설치](azure-stack-powershell-install.md)<br>- [도구 다운로드](azure-stack-powershell-download.md)<br>- [PowerShell 구성](azure-stack-powershell-configure-user.md)<br>- [Visual Studio 설치](azure-stack-install-visual-studio.md) 
| 다음에 대 한 정보를 검토 합니다.<br>-Azure 리소스 관리자 템플릿 고려 사항<br>-빠른 시작 서식 파일을 찾을 방법<br>-Azure를 사용 하 여 Azure 스택 용으로 개발할 수 있도록 정책 모듈을 사용 합니다. | [Azure Stack에 대한 개발](azure-stack-developer.md) | 
| 검토 하 고 서식 파일에 대 한 모범 사례를 따릅니다. | [리소스 관리자 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>버전 요구 사항

Azure 스택 특정 버전의 Azure PowerShell 및 Azure 서비스 Api 지원합니다. 지원 되는 버전을 사용 하 여 두 Azure 스택 및 Azure 앱에 배포할 수 있음을 확인 해야 합니다.

을 올바른 버전의 Azure PowerShell을 사용 하는 확인 하기 위해 사용 하 여 [API 버전 프로필](azure-stack-version-profiles.md)합니다. 사용할 수 있는 최신 API 버전 프로필을 확인 하려면 사용 중인 Azure 스택 하는 빌드를 알고 있어야 합니다. Azure 스택 관리자 로부터이 정보를 가져올 수 있습니다.

>[!NOTE]
 Azure 스택 개발 키트를 사용 하는 경우 관리 권한이의 "최신 버전을 확인 하려면" 섹션을 참조 [업데이트 관리](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#determine-the-current-version) Azure 스택 빌드를 확인 하려면.

다른 Api에 대 한 네임 스페이스, 리소스 종류 및 스택 Azure 구독에서 지원 되는 API 버전을 출력 하려면 다음 PowerShell 명령을 실행 합니다. 여기에 메모 속성 수준에서 차이점을 수 있습니다. (이 명령이 작동 되려면 있어야 이미 [설치](azure-stack-powershell-install.md) 및 [구성](azure-stack-powershell-configure-user.md) Azure 스택 환경에 대 한 PowerShell 합니다. 또한 있어야 Azure 스택 제공 하는 서비스에 대 한 구독.)

 ```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

예제 출력 (잘림): ![Get AzureRmResourceProvider 명령의 예제 출력](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>다음 단계

서비스 수준에서 차이점에 대 한 자세한 내용을 보려면 다음을 참조 하세요.

* [Azure 스택의 가상 컴퓨터에 대 한 고려 사항](azure-stack-vm-considerations.md)
* [Azure 스택에서 저장소에 대 한 고려 사항](azure-stack-acs-differences.md)
* [Azure 스택 네트워킹에 대 한 고려 사항](azure-stack-network-differences.md)
