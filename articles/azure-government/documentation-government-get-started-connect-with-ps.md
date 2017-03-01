---
title: "PowerShell을 사용하는 Azure Government Connect | Microsoft Docs"
description: "Azure Government에서 PowerShell을 사용하여 구독을 연결하는 방법에 대한 정보"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: 47e5e535-baa0-457e-8c41-f9fd65478b38
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 3a0594fa639a2ac34c53b2263dbf7dd2410cf16a
ms.lasthandoff: 02/21/2017


---



# <a name="connect-to-azure-government-with-powershell"></a>PowerShell을 사용하여 Azure Government에 연결
Azure CLI를 사용하려면 공용 Azure가 아닌 Azure Government에 연결해야 합니다. Azure CLI를 사용하여 스크립트를 통해 많은 구독을 관리하거나 Azure Portal에서 현재 사용할 수 없는 기능에 액세스할 수 있습니다. Azure Public에서 PowerShell을 사용한 경우 대부분 동일합니다.  Azure Government의 차이점은 다음과 같습니다.

* 계정 연결
* 지역 이름

> [!NOTE]
> 아직 PowerShell을 사용하지 않은 경우 [Azure PowerShell 소개](/powershell/azureps-cmdlets-docs)를 참조하세요.
> 
> 

PowerShell을 시작할 때 환경 매개 변수를 지정하여 Azure Government에 연결하라고 Azure PowerShell에 알려야 합니다.  이 매개 변수는 PowerShell이 올바른 끝점에 연결하도록 보장합니다.  끝점 컬렉션은 연결하여 계정에 로그인할 때 결정됩니다.  다양한 API만큼 다양한 환경 스위치 버전이 필요합니다.

| 연결 형식 | 명령 |
| --- | --- |
| [Azure Active Directory(클래식 배포 모델)](https://msdn.microsoft.com/library/dn708504.aspx) 명령 |`Add-AzureAccount -Environment AzureUSGovernment` |
| [리소스 관리](https://msdn.microsoft.com/library/mt125356.aspx) 명령 |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) 명령 |`Connect-MsolService -AzureEnvironment UsGovernment` |
| [Azure Active Directory(Resource Manager 배포 모델)](https://msdn.microsoft.com/library/azure/mt757189.aspx) 명령 |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Azure CLI 명령줄](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

또한 New-AzureStorageContext를 사용하여 저장소 계정에 연결할 때 환경 스위치를 사용하고 AzureUSGovernment를 지정할 수 있습니다.

### <a name="determining-region"></a>지역 확인
연결된 후 추가적인 차이점이 하나 더 있습니다. 그것은 바로 서비스 대상을 결정할 때 사용하는 지역입니다.  Azure 클라우드마다 지역이 서로 다릅니다.  지역은 서비스 가용성 페이지에서 확인할 수 있습니다.  일반적으로 명령의 위치 매개 변수에 지역을 사용합니다.

한 가지 문제가 있습니다.  Azure Government 지역은 해당하는 일반 이름과 다르게 형식을 지정해야 합니다.

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

