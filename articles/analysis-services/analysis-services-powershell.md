---
title: "PowerShell을 사용하여 Azure Analysis Services 관리 | Microsoft Docs"
description: "PowerShell 사용한 Azure Analysis Services 관리입니다."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: c0dd85570f052c4a9651faecbaeb3eaa181f9017
ms.openlocfilehash: b00b2cf1d3a54bba716d7470643aa1a5e6a58633
ms.lasthandoff: 03/01/2017


---

# <a name="manage-azure-analysis-services-with-powershell"></a>PowerShell을 사용하여 Azure Analysis Services 관리

이 문서에서는 Azure Analysis Services 서버 및 데이터베이스 관리 작업을 수행하는 데 사용되는 PowerShell cmdlet에 대해 설명합니다. 

서버 만들기 또는 삭제, 서버 작업 일시 중단 또는 다시 시작 또는 서비스 수준(계층) 변경과 같은 서버 관리 작업은 AzureRM(Azure Resource Manager) cmdlet을 사용합니다. 역할 멤버 추가 또는 제거, 처리 또는 분할과 같은 기타 데이터베이스 관리 작업은 [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) 모듈에서 SQL Server Analysis Services와 동일한 cmdlet을 사용합니다.

## <a name="permissions"></a>권한
대부분의 PowerShell 작업에는 관리하는 Analysis Services 서버에 대해 관리자 권한이 있어야 합니다. 예약된 PowerShell 작업은 무인 작업입니다. 스케줄러를 실행하는 계정에는 Analysis Services 서버에 대해 관리자 권한이 있어야 합니다. 

AzureRm cmdlet을 사용하여 서버를 운영하려면 사용자의 계정 또는 스케줄러를 실행하는 계정이 [Azure 역할 기반 액세스 제어(RBAC)](../active-directory/role-based-access-control-what-is.md)의 리소스에 대한 소유자 역할에도 속해야 합니다. 

## <a name="server-operations"></a>서버 작업 
Azure Analysis Services cmdlet은 [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) 구성 요소 모듈에 포함되어 있습니다. AzureRM cmdlet 모듈을 설치하려면 PowerShell 갤러리의 [Azure Resource Manager cmdlet](https://docs.microsoft.com/powershell/resourcemanager/)을 참조하세요.

|Cmdlet|설명| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/get-azurermanalysisservicesserver)|서버 인스턴스에 대한 세부 정보를 가져옵니다.|  
|[New-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/new-azurermanalysisservicesserver)|새 서버 인스턴스를 만듭니다.|
|[Remove-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/remove-azurermanalysisservicesserver)|서버 인스턴스를 제거합니다.|  
|[Suspend-AzureRmAnalysisServicesServe](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/suspend-azurermanalysisservicesserver)|서버 인스턴스를 일시 중단합니다.| 
|[Resume-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/resume-azurermanalysisservicesserver)|서버 인스턴스를 다시 시작합니다.|  
|[Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/set-azurermanalysisservicesserver)|서버 인스턴스를 수정합니다.|   
|[Test-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/test-azurermanalysisservicesserver)|서버 인스턴스의 존재 여부를 테스트합니다.| 

## <a name="database-operations"></a>데이터베이스 작업
Azure Analysis Services 데이터베이스 작업은 SQL Server Analysis Services와 동일한 [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) 모듈을 사용합니다. 그러나 모든 cmdlet이 Azure Analysis Services 미리 보기에서 지원되는 것은 아닙니다. 

SQLASCMDLETS 모듈은 TMSL(테이블 형식 모델 스크립팅 언어) 쿼리 또는 스크립트를 허용하는 범용 Invoke-ASCmd cmdlet 뿐만 아니라 작업 관련 데이터베이스 관리 cmdlet도 제공합니다. SQLASCMDLETS 모듈의 다음 cmdlet은 Azure Analysis Services 미리 보기에서 지원됩니다.
  
|Cmdlet|설명|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|데이터베이스 역할에 구성원을 추가합니다.| 
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|데이터베이스 역할에서 구성원을 제거합니다.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|TMSL 스크립트를 실행합니다.|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|데이터베이스를 처리합니다.|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|파티션을 처리합니다.| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|테이블을 처리합니다.|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|파티션을 병합합니다.|  
  

## <a name="related-information"></a>관련 정보
* [Analysis Services의 PowerShell 스크립팅](https://msdn.microsoft.com/library/hh213141.aspx)
* [호환성 수준 1200에 대한 테이블 형식 모델 프로그래밍](https://msdn.microsoft.com/library/mt712541.aspx)
