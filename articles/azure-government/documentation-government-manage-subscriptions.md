---
title: "Azure Government 구독 | Microsoft 문서"
description: "Azure Government에서 구독 관리에 대한 정보"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: d3375e84-a37d-4e44-9040-70dbe08eabfc
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/12/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 398abc29328adc179b860ab2cde5e6122f81779d


---
# <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>Azure Government에서 구독 관리 및 구독에 연결
Azure Government에는 환경 관리를 위한 고유한 URL 및 끝점이 있습니다. 올바른 연결을 사용하여 포털 또는 PowerShell을 통해 환경을 관리하는 것이 중요합니다. Azure Government 환경에 연결되면, 구성 요소가 배포된 경우 서비스를 관리하기 위한 일반 작업이 작동합니다.

## <a name="connecting-via-the-portal"></a>포털을 통해 연결
대부분의 사람들이 Azure Government에 연결할 때 사용하는 주요 방법은 포털입니다.  연결하려면 [https://portal.azure.us](https://portal.azure.us)에서 포털로 이동합니다.  Azure Portal의 이전 버전은 [https://manage.windowsazure.us](https://manage.windowsazure.us)를 통해 액세스할 수 있습니다.

[https://account.windowsazure.us](https://account.windowsazure.us)에 연결하여 계정에 대한 구독을 만들 수 있습니다.

## <a name="connecting-via-powershell"></a>PowerShell을 통해 연결
Azure PowerShell을 사용하여 현재 Azure Portal에 제공되지 않는 스크립트 또는 액세스 기능을 통해 대형 구독을 관리하는 경우 Azure Public 대신 Azure Government에 연결해야 합니다.  Azure Public에서 PowerShell을 사용한 경우 대부분 동일합니다.  Azure Government의 차이점은 다음과 같습니다.

* 계정 연결
* 지역 이름

> [!NOTE]
> 아직 PowerShell을 사용하지 않은 경우 [Azure PowerShell 소개](/powershell/azureps-cmdlets-docs)를 참조하세요.
> 
> 

PowerShell을 시작할 때 환경 매개 변수를 지정하여 Azure Government에 연결하라고 Azure PowerShell에 알려야 합니다.  이 매개 변수는 PowerShell이 올바른 끝점에 연결하도록 보장합니다.  끝점 컬렉션은 연결하여 계정에 로그인할 때 결정됩니다.  다양한 API만큼 다양한 환경 스위치 버전이 필요합니다.

| 연결 형식 | 명령 |
| --- | --- |
| [서비스 관리](https://msdn.microsoft.com/library/dn708504.aspx) 명령 |`Add-AzureAccount -Environment AzureUSGovernment` |
| [리소스 관리](https://msdn.microsoft.com/library/mt125356.aspx) 명령 |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) 명령 |`Connect-MsolService -AzureEnvironment UsGovernment` |
| [Azure Active Directory 명령 v2](https://msdn.microsoft.com/library/azure/mt757189.aspx) |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Azure CLI 명령줄](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

또한 New-AzureStorageContext를 사용하여 저장소 계정에 연결할 때 환경 스위치를 사용하고 AzureUSGovernment를 지정할 수 있습니다.

### <a name="determining-region"></a>지역 확인
연결된 후 추가적인 차이점이 하나 더 있습니다. 그것은 바로 서비스 대상을 결정할 때 사용하는 지역입니다.  Azure 클라우드마다 지역이 서로 다릅니다.  지역은 서비스 가용성 페이지에서 확인할 수 있습니다.  일반적으로 명령의 위치 매개 변수에 지역을 사용합니다.

한 가지 문제가 있습니다.  Azure Government 지역은 일반 이름과 다르게 형식을 지정해야 합니다.

| 일반 이름 | 명령 |
| --- | --- |
| 미국 정부 버지니아 |USGov 버지니아 |
| 미국 정부 아이오와 |미국 정부 아이오와 |

> [!NOTE]
> 위치 매개 변수를 사용할 때 US와 Gov 사이에 공백이 없습니다.
> 
> 

Azure Government에서 사용 가능한 지역을 확인하려면 다음 명령을 실행하고 현재 목록을 인쇄합니다.

    Get-AzureLocation

Azure에서 사용 가능한 환경을 알아보려면 다음 명령을 실행합니다.

    Get-AzureEnvironment

## <a name="connecting-via-visual-studio"></a>Visual Studio를 통해 연결
개발자는 솔루션을 구축하는 동안 Azure 구독을 쉽게 관리하기 위해 Visual Studio를 사용합니다.  Visual Studio의 현재 사용자 인터페이스에서는 Azure Government에 대한 연결을 구성할 수 없습니다.  

### <a name="updating-visual-studio-for-azure-government"></a>Azure Government를 위한 Visual Studio 업데이트
Visual Studio에서 Azure Government에 연결할 수 있게 하려면 레지스트리를 업데이트해야 합니다.

1. Visual Studio를 닫습니다.
2. **VisualStudioForAzureGov.reg**라는 텍스트 파일을 만듭니다.
3. **VisualStudioForAzureGov.reg**에 다음 텍스트를 복사하여 붙여넣습니다.
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login-us.microsoftonline.com/"
        "adaluri"="https://management.core.usgovcloudapi.net"
        "AzureRMEndpoint"="https://management.usgovcloudapi.net"
        "AzureRMAudienceEndpoint"="https://management.core.usgovcloudapi.net"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.windows.net"
4. 해당 파일을 저장한 다음 두 번 클릭하여 실행합니다.  파일을 레지스트리에 병합할지 묻는 메시지가 표시됩니다.
5. Visual Studio를 실행하고 [클라우드 탐색기](../vs-azure-tools-resources-managing-with-cloud-explorer.md) 사용을 시작합니다.

> [!NOTE]
> 이 레지스트리 키가 설정되면 Azure Government 구독에만 액세스할 수 있습니다.  현재 Visual Studio가 Azure 공용 대신 Azure Government에 연결되어 있기 때문에 이전에 구성했지만 작동하지 않는 구독이 계속 표시됩니다.  변경 사항을 되돌리려면 다음 섹션을 참조하세요.
> 
> 

### <a name="reverting-visual-studio-connection-to-azure-government"></a>Visual Studio 연결을 Azure Government로 되돌리기
Visual Studio에서 Azure 공용에 연결할 수 있게 하려면 Azure Government에 연결할 수 있는 레지스트리 설정을 제거해야 합니다.

1. Visual Studio를 닫습니다.
2. **VisualStudioForAzureGov_Remove.reg**라는 텍스트 파일을 만듭니다.
3. **VisualStudioForAzureGov_Remove.reg**에 다음 텍스트를 복사하여 붙여넣습니다.
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
4. 해당 파일을 저장한 다음 두 번 클릭하여 실행합니다.  파일을 레지스트리에 병합할지 묻는 메시지가 표시됩니다.
5. Visual Studio를 실행합니다.

> [!NOTE]
> 이 레지스트리 키가 되돌려지면 Azure Government 구독이 표시되지만 액세스할 수 없습니다.  그러면 해당 구독을 안전하게 제거할 수 있습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음을 참조하세요.

* [GitHub의 PowerShell 문서](https://github.com/Azure/azure-powershell)
* [리소스 관리에 연결하는 단계별 지침](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/)
* [MSDN의 Azure PowerShell 문서](https://msdn.microsoft.com/library/mt619274.aspx)

추가 정보 및 업데이트는 [Microsoft Azure Government 블로그](https://blogs.msdn.microsoft.com/azuregov/)를 구독하세요.




<!--HONumber=Dec16_HO2-->


