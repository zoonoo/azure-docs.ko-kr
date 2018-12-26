---
title: Azure Stack에서 Azure Resource Manager 템플릿 사용 | Microsoft Docs
description: Azure Stack에서 Azure Resource Manager 템플릿을 사용 하 여 리소스를 프로 비전 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: 38e85ede1e984aa3d26fe509d68b4582d11b6c26
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636223"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Azure Stack에서 Azure Resource Manager 템플릿 사용

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Resource Manager 템플릿을 배포 하 고 조정 된 단일 작업에서 응용 프로그램에 대 한 모든 리소스를 프로 비전을 사용할 수 있습니다. 또한 리소스 그룹에서 리소스를 변경 하려면 템플릿을 다시 배포할 수 있습니다.

이러한 템플릿은 Microsoft Azure Stack 포털, PowerShell, 명령줄 및 Visual Studio에서 배포할 수 있습니다.

다음 빠른 시작 템플릿은 [GitHub](https://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>SharePoint Server (비-고가용성 배포) 배포

PowerShell DSC 확장을 사용 하 여 [SharePoint Server 2013 팜을 만들려면](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha) 다음 리소스를 포함 하는:

* 가상 네트워크
* 저장소 계정 3개
* 외부 부하 분산 장치 2개
* 단일 도메인을 사용 하 여 새 포리스트의 도메인 컨트롤러로 구성 된 하나의 가상 머신 (VM)
* SQL Server 2014 독립 실행형 서버로 하나의 VM이 구성됨
* 하나의 컴퓨터 SharePoint Server 2013 팜을 구성 하는 하나의 VM

## <a name="deploy-ad-non-high-availability-deployment"></a>AD (비-고가용성-가용성-배포) 배포

PowerShell DSC 확장을 사용 하 여 [AD 도메인 컨트롤러 서버 만들기](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha) 다음 리소스를 포함 합니다.

* 가상 네트워크
* 저장소 계정 1개
* 외부 부하 분산 장치 1개
* 단일 도메인을 사용 하 여 새 포리스트의 도메인 컨트롤러로 구성 된 하나의 가상 머신 (VM)

## <a name="deploy-adsql-non-high-availability-deployment"></a>AD/SQL (비-고가용성-가용성-배포) 배포

PowerShell DSC 확장을 사용 하 여 [SQL Server 2014 독립 실행형 서버를 만들](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha) 다음 리소스를 포함 하는:

* 가상 네트워크
* 두 저장소 계정
* 외부 부하 분산 장치 1개
* 단일 도메인을 사용 하 여 새 포리스트의 도메인 컨트롤러로 구성 된 하나의 가상 머신 (VM)
* SQL Server 2014 독립 실행형 서버로 하나의 VM이 구성됨

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

PowerShell DSC 확장을 사용하여 기존 가상 머신 로컬 구성 관리자(LCM)를 구성하고 Azure Automation 계정 DSC 끌어오기 서버에 등록합니다.

## <a name="create-a-virtual-machine-from-a-user-image"></a>사용자 이미지에서 가상 머신 만들기

[사용자 지정 사용자 이미지에서 가상 컴퓨터를 만들](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-from-user-image)합니다. 이 템플릿은 가상 네트워크(DNS 사용), 공용 IP 주소 및 네트워크 인터페이스도 배포합니다.

## <a name="basic-virtual-machine"></a>기본 가상 머신

[Windows VM 배포](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm) (DNS)이 있는 가상 네트워크, 공용 IP 주소 및 네트워크 인터페이스를 포함 합니다.

## <a name="cancel-a-running-template-deployment"></a>실행 중인 템플릿 배포 취소

실행 중인 템플릿 배포를 취소 하려면 사용 합니다 [중지 AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment) PowerShell cmdlet.

## <a name="next-steps"></a>다음 단계

* [포털을 사용하여 템플릿 배포](azure-stack-deploy-template-portal.md)
* [Azure 리소스 관리자 개요](../../azure-resource-manager/resource-group-overview.md)
