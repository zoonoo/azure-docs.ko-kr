---
title: "여러 Azure 가상 컴퓨터에 대한 업데이트 관리 | Microsoft Docs"
description: "Azure 가상 컴퓨터를 등록하여 업데이트를 관리합니다."
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.openlocfilehash: 89bf87f27fdf276068cba261fc6ae1660307e0b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>여러 Azure 가상 컴퓨터에 대한 업데이트 관리

업데이트 관리를 사용하면 Azure 가상 컴퓨터에 대한 업데이트 및 패치를 관리할 수 있습니다.
[Azure Automation](automation-offering-get-started.md) 계정에서 가상 컴퓨터를 신속하게 등록하고, 사용 가능한 업데이트의 상태를 빠르게 평가하고, 필수 업데이트의 설치를 예약하고, 배포 결과를 검토하여 업데이트가 업데이트 관리가 활성화된 모든 가상 컴퓨터에 성공적으로 적용되었는지 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 가이드의 단계를 완료하려면 다음이 필요합니다.

* Azure 자동화 계정. Azure 자동화 실행 계정 만들기에 대한 지침은 [Azure 실행 계정](automation-sec-configure-azure-runas-account.md)을 참조하세요.
* Azure Resource Manager 가상 컴퓨터(클래식 아님). VM 만들기에 대한 지침은 [Azure 포털에서 첫 번째 Windows 가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="enable-update-management-for-azure-virtual-machines"></a>Azure 가상 컴퓨터에 대한 업데이트 관리 사용

1. Azure Portal에서 Automation 계정을 엽니다.
2. 화면 왼쪽에서 **업데이트 관리**를 선택합니다.
3. 화면 맨 위에서 **Azure VM 추가**를 클릭합니다.
    ![VM 등록](./media/manage-update-multi/update-onboard-vm.png)
4. 등록할 가상 컴퓨터를 선택합니다. **업데이트 관리 사용** 화면이 나타납니다.
5. **사용**을 클릭합니다.

   ![업데이트 관리 사용](./media/manage-update-multi/update-enable.png)

가상 컴퓨터에 대한 업데이트 관리를 사용합니다.

## <a name="view-update-assessment"></a>업데이트 평가 보기

**업데이트 관리**를 사용하도록 설정하면 **업데이트 관리** 화면이 나타납니다. **누락된 업데이트** 탭에서 누락된 업데이트 목록을 볼 수 있습니다.

## <a name="schedule-an-update-deployment"></a>업데이트 배포 예약

업데이트를 설치하려면 릴리스 일정 및 서비스 기간 이후로 배포를 예약합니다.
배포에 포함할 업데이트 형식을 선택할 수 있습니다. 예를 들어 중요 업데이트나 보안 업데이트를 포함하고 업데이트 롤업은 제외할 수 있습니다.

**업데이트 관리** 화면 맨 위에서 **업데이트 배포 예약**을 클릭하여 하나 이상의 가상 컴퓨터에 대한 새 업데이트 배포를 예약합니다. **새 배포 업데이트** 화면에서 다음을 지정합니다.

* **이름** - 업데이트 배포를 식별하는 고유 이름을 제공합니다.
* **OS 유형** - Windows 또는 Linux를 선택합니다.
* **업데이트할 컴퓨터** - 업데이트하려는 가상 컴퓨터를 선택합니다.

  ![업데이트할 가상 컴퓨터 선택](./media/manage-update-multi/update-select-computers.png)

* **업데이트 분류** - 배포에 포함되는 업데이트 배포 소프트웨어의 종류를 선택합니다. 분류 형식은 다음과 같습니다.
  * 중요 업데이트
  * 보안 업데이트
  * 업데이트 롤업
  * 기능 팩
  * 서비스 팩
  * 정의 업데이트
  * 도구
  * 업데이트
* **일정 설정** - 현재 시간부터 30분 이후에 해당하는 기본 날짜 및 시간을 그대로 적용하거나 다른 시간을 지정할 수 있습니다.
   배포가 한 번만 수행될지 여부를 지정하거나 되풀이 일정을 설정할 수도 있습니다. 되풀이 일정을 설정하려면 되풀이에서 되풀이 옵션을 클릭합니다.

   ![업데이트 일정 설정 화면](./media/manage-update-multi/update-set-schedule.png)

* **유지 관리 기간(분)** - 업데이트 배포가 수행될 기간을 지정합니다.  이 기간을 지정하면 정해진 서비스 기간 내에 변경이 수행됩니다.

일정 구성을 완료한 후 **만들기** 단추를 클릭하여 상태 대시보드로 돌아갑니다.
**예약됨** 표에는 만든 배포 일정이 표시됩니다.

> [!WARNING]
> 재부팅이 필요한 업데이트의 경우 가상 컴퓨터는 자동으로 다시 시작됩니다.

## <a name="view-results-of-an-update-deployment"></a>업데이트 배포의 결과 보기

예약된 배포가 시작된 후에 **업데이트 관리** 화면의 **업데이트 배포** 탭에서 해당 배포에 대한 상태를 확인할 수 있습니다.
현재 실행 중인 경우 상태가 **진행 중**으로 표시됩니다. 성공적으로 완료되면 **성공**으로 바뀝니다.
배포에서 하나 이상의 업데이트가 실패하면 상태는 **부분적으로 실패**로 나타납니다.

![배포 상태 업데이트 ](./media/manage-update-multi/update-view-results.png)

완료된 배포 업데이트를 클릭하여 해당 업데이트 배포에 대한 대시보드를 확인합니다.

**업데이트 결과** 타일에는 가상 컴퓨터의 총 업데이트 수 및 배포 결과 요약이 표시됩니다.
오른쪽의 표에는 각 업데이트에 대한 자세한 분석과 다음 값 중 하나로 표시되는 설치 결과가 제공됩니다.

* 시도 안 함 - 정의된 유지 관리 기간에 따라 사용할 수 있는 시간이 충분하지 않기 때문에 업데이트가 설치되지 않았습니다.
* 성공 - 업데이트했습니다.
* 실패 -업데이트하지 못했습니다.

배포를 통해 생성된 항목에 대한 모든 로그를 보려면 **모든 로그**를 클릭합니다.

**출력** 타일을 클릭하여 대상 가상 컴퓨터의 업데이트 배포를 관리하는 runbook의 작업 스트림을 확인합니다.

배포의 모든 오류에 대한 자세한 정보를 보려면 **오류**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

* 업데이트 관리에 대한 자세한 내용은 [업데이트 관리](../operations-management-suite/oms-solution-update-management.md)를 참조하세요.