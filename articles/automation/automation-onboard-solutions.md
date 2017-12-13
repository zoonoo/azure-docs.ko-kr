---
title: "Azure Automation에 업데이트 및 변경 내용 추적 솔루션 등록 | Microsoft Docs"
description: "Azure Automation에 업데이트 및 변경 내용 추적 솔루션을 등록하는 방법을 알아봅니다."
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: edae1156-2dc7-4dab-9e5c-bf253d3971d0
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: eamono
ms.openlocfilehash: 9ae5e9ebcbcf3af61318eac98defc6b249417f41
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2017
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Azure Automation에 업데이트 및 변경 내용 추적 솔루션 등록

이 자습서에서는 다음과 같이 VM에 대한 업데이트, 변경 내용 추적 및 인벤토리 솔루션을 Azure Automation에 자동으로 등록하는 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Virtual Machine을 수동으로 등록합니다.
> * 필요한 Azure 모듈을 설치 및 업데이트합니다.
> * Azure VM을 등록하는 Runbook을 가져옵니다.
> * Azure VM을 자동으로 등록하는 Runbook을 시작합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.
+ 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
+ 컴퓨터를 관리하기 위한 [Automation 계정](automation-offering-get-started.md)

## <a name="onboard-an-azure-virtual-machine-manually"></a>Azure Virtual Machine을 수동으로 등록

1.  Automation 계정을 엽니다.
2.  인벤토리 페이지를 클릭합니다.
![인벤토리 솔루션 등록](media/automation-onboard-solutions/inventory-onboard.png)
3.  기존 Log Analytics 작업 영역을 선택하거나 작업 영역을 새로 만듭니다. 사용 단추를 클릭합니다.
4.  변경 내용 추적 및 인벤토리 솔루션 등록 알림이 완료되면 업데이트 관리 페이지를 클릭합니다.
![업데이트 솔루션 등록](media/automation-onboard-solutions/update-onboard.png)
4.  업데이트 솔루션을 등록하는 사용 단추를 클릭합니다.
5.  업데이트 솔루션 등록 알림이 완료되면 변경 내용 추적 페이지를 클릭합니다.
![변경 내용 추적 등록](media/automation-onboard-solutions/change-tracking-onboard-vm.png)
6.  Azure VM 추가 단추를 클릭합니다.
![변경 내용 추적을 위한 VM 선택](media/automation-onboard-solutions/enable-change-tracking.png)
7.  Azure VM을 선택하고 사용 단추를 클릭하여 변경 내용 추적 및 인벤토리 솔루션에 등록합니다.
8.  VM 등록 알림이 완료되면 업데이트 관리 페이지를 클릭합니다.
![업데이트 관리를 위한 VM 등록](media/automation-onboard-solutions/update-onboard-vm.png)
9.  Azure VM 추가 단추를 클릭합니다.
![업데이트 관리를 위한 VM 선택](media/automation-onboard-solutions/enable-update.png)
10.  Azure VM을 선택하고 사용 단추를 클릭하여 업데이트 관리 솔루션에 등록합니다.

## <a name="install-and-update-required-azure-modules"></a>필요한 Azure 모듈 설치 및 업데이트

솔루션 등록을 자동화하려면 최신 Azure 모듈로 업데이트하고 AzureRM.OperationalInsights를 가져와야 합니다.
1.  모듈 페이지를 클릭합니다.
![모듈 업데이트](media/automation-onboard-solutions/update-modules.png)
2.  Azure 모듈 업데이트 단추를 클릭하여 최신 버전으로 업데이트합니다. 업데이트가 완료될 때까지 기다립니다.
3.  갤러리 찾아보기 단추를 클릭하여 모듈 갤러리를 엽니다.
![OperationalInsights 모듈 가져오기](media/automation-onboard-solutions/import-operational-insights-module.png)
4.  AzureRM.OperationalInsights를 검색하고 Automation 계정으로 이 모듈을 가져옵니다.

## <a name="import-a-runbook-that-onboards-solutions-to-azure-vms"></a>Azure VM에 솔루션을 등록하는 Runbook 가져오기

1.  Runbook 페이지에서 "프로세스 Automation" 범주 아래를 클릭합니다.
2.  "갤러리 찾아보기" 단추를 클릭합니다.
3.  "업데이트 및 변경 내용 추적"을 검색하고 Runbook을 Automation 계정으로 가져옵니다.
![온보딩 Runbook 가져오기](media/automation-onboard-solutions/import-from-gallery.png)
4.  "편집"을 클릭하여 Runbook 원본을 보고 "게시" 단추를 클릭합니다.
![온보딩 Runbook 가져오기](media/automation-onboard-solutions/publish-runbook.png)

## <a name="start-the-runbook-that-onboards-azure-vms-automatically"></a>Azure VM을 자동으로 등록하는 Runbook 시작

이 runbook을 시작하려면 변경 내용 추적 또는 업데이트 솔루션을 Azure VM에 등록했어야 합니다. 매개 변수로 솔루션이 등록된 기존 가상 머신 및 리소스 그룹이 필요합니다.
1.  Enable-MultipleSolution Runbook을 엽니다.
![여러 솔루션 Runbook](media/automation-onboard-solutions/runbook-overview.png)
2.  시작 단추를 클릭하고 매개 변수에 대해 다음 값을 입력합니다.
    *   VMNAME. 업데이트 또는 변경 내용 추적 솔루션에 등록할 기존 VM의 이름입니다. 이 항목을 비워 두면 리소스 그룹에 있는 모든 VM이 등록됩니다.
    *   VMRESOURCEGROUP. VM이 구성원인 리소스 그룹의 이름입니다.
    *   SUBSCRIPTIONID. 등록할 새 VM이 있는 구독 ID입니다. 이 항목을 비워 두면 작업 영역의 구독이 사용됩니다. 다른 구독 ID를 지정하는 경우 이 Automation 계정에 대한 실행 계정을 이 구독에 대한 참가자로도 추가해야 합니다.
    *   ALREADYONBOARDEDVM. 업데이트 또는 변경 내용 추적 솔루션에 수동으로 등록한 VM의 이름입니다.
    *   ALREADYONBOARDEDVMRESOURCEGROUP. VM이 구성원인 리소스 그룹의 이름입니다.
    *   SOLUTIONTYPE. "업데이트" 또는 "변경 내용 추적"을 입력합니다.
    
    ![여러 솔루션 Runbook 매개 변수](media/automation-onboard-solutions/runbook-parameters.png)
3.  확인을 클릭하여 Runbook 작업을 시작합니다.
4.  runbook 작업 페이지에서 진행률 및 오류를 모니터링합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Runbook 예약](automation-schedules.md)을 참조하세요.