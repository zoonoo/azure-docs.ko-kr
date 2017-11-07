---
title: "Azure Site Recovery에서 장애 조치 및 복구의 복구 계획 만들기 | Microsoft Docs"
description: "Azure Site Recovery에서 복구 계획을 만들고 사용자 지정하여 VM 및 물리적 서버를 장애 조치(failover)하고 복구하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/25/2017
ms.author: raynew
ms.openlocfilehash: 202e0ac8be36e9156ec16fadc1b722f4eb3d1432
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2017
---
# <a name="create-recovery-plans"></a>복구 계획 만들기


이 문서는 [Azure Site Recovery](site-recovery-overview.md)에서 복구 계획을 만들고 사용자 지정하는 정보를 제공합니다.

이 문서의 하단 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

 다음을 수행하는 복구 계획을 만듭니다.

* 장애 조치(failover)된 다음 함께 시작하는 컴퓨터의 그룹을 정의합니다.
* 복구 계획 그룹에서 함께 그룹화하여 컴퓨터 간의 종속성을 모델링합니다. 예를 들어, 특정 응용프로그램을 장애 조치하고 표시하기 위해 동일한 복구 계획 그룹에서 해당 응용 프로그램에 대한 VM을 그룹화합니다.
* 장애 조치(Failover)를 실행합니다. 복구 계획에서 테스트를 실행하고 계획되거나 계획되지 않은 장애 조치를 실행할 수 있습니다.


## <a name="create-a-recovery-plan"></a>복구 계획 만들기

1. **복구 계획** > **복구 계획 만들기**를 클릭합니다.
   복구 계획 이름, 원본 및 대상을 지정합니다. 원본 위치에는 장애 조치(Failover) 및 복구를 사용하도록 설정한 가상 컴퓨터가 있어야 합니다.

    - VMM에서 VMM으로 복제하는 경우 **원본 유형** > **VMM**과 원본 및 대상 VMM 서버를 차례로 선택합니다. **Hyper-V**를 클릭하여 보호되는 클라우드를 확인합니다.
    - VMM에서 Azure까지의 경우 **원본 형식** > **VMM**을 선택합니다.  원본 VMM 서버 및 **Azure**를 대상으로 선택합니다.
    - Hyper-V 복제에서 VMM 없는 Azure까지의 경우 **원본 형식** > **Hyper-V 사이트**를 선택합니다. 이 사이트를 원본으로, **Azure**를 대상으로 선택합니다.
    - VMware 또는 물리적 온-프레미스 서버에서 Azure까지의 경우 구성 서버를 원본으로, **Azure**를 대상으로 선택합니다.
    - Azure에서 Azure 복구 계획까지의 경우 원본으로 Azure 지역을 선택하고 대상으로 보조 Azure 지역을 선택합니다. 가상 컴퓨터에 해당하는 보조 Azure 지역만 보호됩니다.
2. **가상 컴퓨터 선택**에서 복구 계획 내 기본 그룹(그룹 1)에 추가하고자 하는 가상 컴퓨터(또는 복제 그룹)를 선택합니다.

## <a name="customize-and-extend-recovery-plans"></a>복구 계획 사용자 지정 및 확장

복구 계획을 사용자 지정 및 확장할 수 있습니다.

- **새 그룹 추가**—기본 그룹에 추가 복구 계획 그룹(최대 7개)을 추가한 다음 해당 복구 계획 그룹에 컴퓨터나 복제 그룹을 추가합니다. 그룹은 추가한 순서대로 번호가 지정됩니다. 가상 컴퓨터나 복제 그룹은 1개의 복구 계획 그룹에만 포함할 수 있습니다.
- **수동 작업 추가**—복구 계획 그룹의 앞 또는 뒤에 실행되는 수동 작업을 추가할 수 있습니다. 복구 계획을 실행하는 경우 수동 작업을 삽입하는 지점에서 중지됩니다. 대화 상자는 수동 작업이 완료되도록 지정하라는 메시지를 표시합니다.
- **스크립트 추가**—복구 계획 그룹 앞뒤로 실행되는 스크립트를 추가할 수 있습니다. 스크립트를 추가하면 해당 그룹에 대해 새로운 작업 집합이 추가됩니다. 예를 들어, 그룹 1에 대한 사전 단계 집합이 Group 1: Pre-steps라는 이름으로 생성됩니다. 모든 사전 단계가 이 집합 내에 나열됩니다. 배포된 VMM 서버를 보유한 경우 주 사이트에만 스크립트를 추가할 수 있습니다.
- **Azure runbook 추가**—Azure runbook을 사용하여 복구 계획을 확장할 수 있습니다. 예를 들어, 작업을 자동화하거나 단일 단계 복구를 만듭니다. [자세히 알아보세요](site-recovery-runbook-automation.md)을 확인하세요.

## <a name="add-scripts"></a>스크립트 추가

복구 계획에서 PowerShell 스크립트를 사용할 수 있습니다.

 - 예외를 정상적으로 처리할 수 있도록 스크립트에서 try-catch 블록을 사용하는지 확인합니다.
    - 스크립트에 예외가 있다면 실행이 중지하고 작업에 실패한 것으로 표시합니다.
    - 오류가 발생한 경우 스크립트의 모든 남은 부분이 실행되지 않습니다.
    - 계획되지 않은 장애 조치를 실행할 때 오류가 발생하면 복구 계획이 계속됩니다.
    - 계획된 장애 조치를 실행할 때 오류가 발생하면 복구 계획이 중단됩니다. 스크립트를 수정하고 예상된 대로 실행되는지 확인한 다음 복구 계획을 다시 실행해야 합니다.
- Write-host 명령이 복구 계획 스크립트에서 작동 하지 않으며 스크립트가 실패합니다. 출력을 만들려면 기본 스크립트를 실행하는 프록시 스크립트를 만듭니다. >> 명령을 사용하여 모든 출력이 표시되는지 확인합니다.
  * 600초 이내에 반환하지 않는 경우 스크립트 시간이 초과됩니다.
  * 모든 항목이 STDERR에 기록된다면 스크립트가 실패로 분류됩니다. 이 정보는 스크립트 실행 세부 정보에 표시됩니다.

배포에서 VMM을 사용하는 경우:

* 복구 계획 내 스크립트는 VMM 서비스 계정의 컨텍스트에서 실행됩니다. 이 계정에 스크립트가 위치한 원격 공유에 대한 읽기 권한이 있는지 확인합니다. VMM 서비스 계정 권한 수준에서 실행하는 스크립트를 테스트합니다.
* VMM cmdlet은 Windows PowerShell 모듈로 배달됩니다. 모듈은 VMM 콘솔을 설치할 때 설치됩니다. 스크립트에서 다음 명령을 사용하여 스크립트에 로드될 수 있습니다.
   - Import-Module -Name virtualmachinemanager. [자세히 알아보세요](https://technet.microsoft.com/library/hh875013.aspx)을 확인하세요.
* VMM 배포에 최소 1개의 라이브러리 서버가 있는지 확인합니다. 기본적으로 라이브러리는 VMM 서버에 대한 경로를 공유하며, MSCVMMLibrary라는 폴더 이름으로 VMM 서버에 로컬로 위치해 있습니다.
    * 라이브러리 공유 경로가 원격인 경우(또는 로컬이지만 MSCVMMLibrary와 공유하지 않는 경우), 공유를 다음처럼 구성합니다(\\libserver2.contoso.com\share\를 예제로 사용).
      * 레지스트리 편집기를 열고 **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**으로 이동합니다.
      * **ScriptLibraryPath** 값을 편집하고 \\libserver2.contoso.com\share\.로 입력합니다. 전체 FQDN을 지정합니다. 공유 위치에 대한 사용 권한을 제공합니다. 이 노드는 공유의 루트 노드입니다. **VMM의 루트 노드에서 라이브러리 찾아이것을 확인할 수 있습니다. 열리는 경로가 경로의 루트이며 변수에서 사용해야 할 경로입니다**.
      * VMM 서비스 계정과 동일한 권한을 가진 사용자 계정을 사용하여 스크립트를 테스트해야 합니다. 테스트된 독립 실행형 스크립트가 복구 계획과 동일한 방식으로 실행되는지 확인합니다. VMM 서버에서 실행 정책을 다음과 같이 우회하도록 설정합니다.
        * 상승된 권한을 사용하여 **64비트 Windows PowerShell** 콘솔을 엽니다.
        * 유형: **Set-executionpolicy bypass**. [자세히 알아봅니다](https://technet.microsoft.com/library/ee176961.aspx).

> [!IMPORTANT]
> 64비트 PowerShell에서만 실행 정책을 바이패스로 설정해야 합니다. 32비트 PowerShell에 설정하면 스크립트가 실행되지 않습니다.

## <a name="add-a-script-or-manual-action-to-a-plan"></a>계획에 스크립트 또는 수동 작업 추가

VM이나 복제 그룹을 기본 복구 계획 그룹에 추가하고 계획을 만든 후에 여기에 스크립트를 추가할 수 있습니다.

1. 복구 계획을 엽니다.
2. **단계** 목록에서 아무 항목이나 클릭하고 **스크립트** 또는 **수동 작업**을 클릭합니다.
3. 스크립트나 작업을 선택한 항목의 앞 또는 뒤에 추가할 것인지 지정합니다. **위로 이동** 및 **아래로 이동** 단추를 사용하여 스크립트의 위치를 위/아래로 이동합니다.
4. VMM 스크립트를 추가하면 **VMM 스크립트에 대한 장애 조치**를 선택합니다. **스크립트 경로**에서 공유에 상대 경로를 입력합니다. 아래의 VMM 예제에서 **\RPScripts\RPScript.PS1** 경로를 지정합니다.
5. Azure Automation Runbook을 추가하는 경우 Runbook이 위치한 Azure Automation 계정을 지정하고 적절한 Azure Runbook 스크립트를 선택합니다.
6. 복구 계획의 장애 조치를 실행하여 스크립트가 예상대로 작동하는지 확인합니다.


### <a name="add-a-vmm-script"></a>VMM 스크립트 추가

VMM 원본 사이트가 있는 경우 VMM 서버에 스크립트를 생성하고 복구 계획에 포함할 수 있습니다.

1. 라이브러리 공유에서 새 폴더를 만듭니다. 예를 들어, \<VMMServerName>\MSSCVMMLibrary\RPScripts.입니다. 원본 및 대상 VMM 서버에 놓습니다.
2. 스크립트(예: RPScript)를 만들고 예상한 대로 작동하는지 확인합니다.
3. 원본 및 대상 VMM 서버에서 \<VMMServerName>\MSSCVMMLibrary 위치에 스크립트를 배치합니다.


## <a name="next-steps"></a>다음 단계

장애 조치를 실행하는 방법에 대해 [자세히 알아보세요](site-recovery-failover.md).
