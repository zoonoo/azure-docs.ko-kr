---
title: 인벤토리 수집을 사용하여 Azure 가상 머신 관리 | Microsoft Docs
description: 인벤토리 수집을 사용하여 가상 컴퓨터 관리
services: automation
ms.subservice: change-inventory-management
keywords: 인벤토리, 자동화, 변경, 추적
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0627d2daa70c276535dc43b722e22e1d73b0c8d2
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617367"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>인벤토리 수집을 사용하여 Azure 가상 컴퓨터 관리

가상 컴퓨터의 리소스 페이지에서 Azure 가상 컴퓨터에 대한 인벤토리 추적을 활성화할 수 있습니다. 컴퓨터에서 다음 인벤토리 정보를 수집하고 볼 수 있습니다.

- Windows 소프트웨어(Windows 응용 프로그램 및 Windows 업데이트), 서비스, 파일 및 레지스트리 키
- 리눅스 소프트웨어 (패키지) 데몬, 파일

이 방법은 인벤토리 수집을 설정 및 구성하는 브라우저 기반 사용자 인터페이스를 제공합니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

Azure 구독이 없는 경우 [무료 계정을 만듭니다.](https://azure.microsoft.com/free/)

이 문서에서는 솔루션을 구성할 VM이 있다고 가정합니다. Azure 가상 머신이 없는 경우 [가상 머신을 만듭니다](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>가상 머신 리소스 페이지에서 인벤토리 수집 활성화

1. Azure Portal의 왼쪽 창에서 **가상 머신**를 선택합니다.
2. 가상 머신 목록에서 가상 머신을 선택합니다.
3. **작업** 아래의 **리소스** 메뉴에서 **인벤토리**를 선택합니다.
4. 데이터 로그를 저장하기 위해 로그 분석 작업 영역을 선택합니다.
    해당 영역에 대해 사용할 수 있는 작업 영역이 없는 경우 기본 작업 영역 및 자동화 계정을 만들라는 메시지가 표시됩니다.
5. 컴퓨터 온보딩을 시작하려면 **활성화**를 선택합니다.

   ![온보딩 옵션 보기](./media/automation-vm-inventory/inventory-onboarding-options.png)

    상태 표시줄에 솔루션이 활성화했다는 메시지가 표시됩니다. 이 프로세스는 15분까지 걸릴 수 있습니다. 이 시간 동안 창을 닫을 수 있습니다. 또는 창을 열어 두면 솔루션이 활성화될 때 알림 메시지가 표시됩니다. 알림 창에서 배포 상태를 모니터링할 수 있습니다.

   ![온보딩 직후 인벤토리 솔루션 보기](./media/automation-vm-inventory/inventory-onboarded.png)

배포가 완료되면 상태 표시줄이 사라집니다. 이때 시스템은 여전히 인벤토리 데이터를 수집하며, 아직 데이터가 표시되지 않을 수 있습니다. 전체 데이터 수집에 24시간이 걸릴 수 있습니다.

## <a name="configure-your-inventory-settings"></a>인벤토리 설정 구성

기본적으로 수집을 위해 소프트웨어, Windows 서비스 및 Linux 데몬을 구성합니다. Windows 레지스트리 및 파일 인벤토리를 수집하기 위해 인벤토리 컬렉션 설정을 구성합니다.

1. 인벤토리 페이지에서 페이지 상단의 **설정 편집을** 클릭합니다.
2. 새 컬렉션 설정을 추가하려면 **Windows 레지스트리,** **Windows 파일**또는 **Linux 파일** 탭을 선택하여 추가할 설정 범주로 이동합니다.
3. 적절한 범주를 선택하고 페이지 상단에서 **추가를** 클릭합니다.

다음 표는 다양한 범주에 대해 구성할 수 있는 각 속성에 대한 정보를 제공합니다.

### <a name="windows-registry"></a>Windows 레지스트리

|속성  |Description  |
|---------|---------|
|사용     | 설정 적용 여부 결정        |
|Item Name     | 추적할 파일의 이름        |
|그룹     | 파일을 논리적으로 그룹화하는 그룹 이름        |
|Windows 레지스트리 키   | 파일을 검사할 경로. 예: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="windows-files"></a>Windows 파일

|속성  |Description  |
|---------|---------|
|사용     | 설정이 적용된 경우 true이고 False는 그렇지 않습니다.        |
|Item Name     | 추적할 파일의 친숙한 이름입니다.        |
|그룹     | 논리적으로 파일을 그룹화하는 그룹 이름입니다.       |
|경로 입력     | 파일을 확인하는 경로(예: **c:\temp\myfile.txt)**

### <a name="linux-files"></a>Linux 파일

|속성  |Description  |
|---------|---------|
|사용     | 설정이 적용된 경우 true이고 False는 그렇지 않습니다.        |
|Item Name     | 추적할 파일의 친숙한 이름입니다.        |
|그룹     | 논리적으로 파일을 그룹화하는 그룹 이름입니다.        |
|경로 입력     | 파일을 확인하는 경로(예: **/etc/*.conf)**       |
|경로 유형     | 추적할 항목의 유형입니다. 값은 파일 및 디렉터리입니다.        |
|재귀     | 추적할 항목을 검색할 때 재귀가 사용되는 경우 True이고 그렇지 않으면 False입니다.        |
|sudo 사용     | 항목을 확인할 때 sudo를 사용하는 경우 true, 그렇지 않으면 False.         |
|링크     | 디렉터리를 탐색할 때 기호 링크가 처리되는 방법을 나타내는 값입니다. 가능한 값은 다음과 같습니다. <br> Ignore - 심볼 링크를 무시하고 참조된 파일/디렉터리를 포함하지 않음<br>Follow - 재귀 중에 심볼 링크를 따르고 참조된 파일/디렉터리도 포함<br>Manage - 심볼 링크를 따르고 반환된 콘텐츠의 처리를 변경하도록 허용      |

## <a name="manage-machine-groups"></a>머신 그룹 관리

인벤토리를 사용하면 Azure Monitor 로그에서 컴퓨터 그룹을 만들고 볼 수 있습니다. 컴퓨터 그룹은 Azure Monitor 로그의 쿼리에 의해 정의된 컴퓨터의 컬렉션입니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

머신 그룹을 보려면 인벤토리 페이지에서 **머신 그룹** 탭을 선택합니다.

![인벤토리 페이지에서 머신 그룹 보기](./media/automation-vm-inventory/inventory-machine-groups.png)

목록에서 머신 그룹을 선택하면 머신 그룹 페이지가 열립니다. 이 페이지에서는 머신 그룹에 대한 정보를 보여 줍니다. 이러한 세부 정보에는 그룹을 정의하는 데 사용되는 로그 분석 쿼리가 포함됩니다. 페이지의 맨 아래에서 해당 그룹의 일부인 머신의 페이징된 목록입니다.

![머신 그룹 페이지 보기](./media/automation-vm-inventory/machine-group-page.png)

**+ 복제를** 클릭하여 컴퓨터 그룹을 복제합니다. 그룹에 그룹에 새 이름과 별칭을 지정해야 합니다. 이번에는 정의를 변경할 수 있습니다. 쿼리를 변경한 후 **쿼리 유효성 검사를** 클릭하여 선택될 컴퓨터를 미리 봅니다. 그룹에 만족하면 **만들기를** 클릭하여 컴퓨터 그룹을 만듭니다.

새 컴퓨터 그룹을 만들려면 + **컴퓨터 그룹 만들기를**클릭합니다. 이 단추는 새 그룹을 정의할 수 있는 **컴퓨터 그룹 만들기** 페이지를 엽니다. **만들기**를 클릭하여 그룹을 만듭니다.

![새 머신 그룹 만들기](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>관리에서 가상 머신 분리

인벤토리 관리에서 가상 머신을 제거하려면 다음을 수행합니다.

1. Azure Portal의 왼쪽 창에서 **Log Analytics**를 선택한 다음 가상 머신을 온보딩할 때 사용한 작업 영역을 선택합니다.
2. 로그 분석 페이지에서 **리소스** 메뉴를 엽니다.
3. 작업 영역 **데이터 원본에서** **가상 컴퓨터를** 선택합니다.
4. 목록에서 분리할 가상 머신을 선택합니다. 가상 머신은 **OMS 연결** 열의 **이 작업 영역** 옆에 녹색 확인 표시가 있습니다.

   >[!NOTE]
   >OMS(운영 관리 제품군)를 이제 Azure 모니터 로그라고 합니다.
   
5. 다음 페이지 상단에서 **연결을**클릭합니다.
6. 확인 창에서 **예를** 클릭하여 관리에서 기기를 분리합니다.

## <a name="next-steps"></a>다음 단계

* 가상 머신의 파일 및 레지스트리 설정에서 변경 관리에 대해 알아보려면 [변경 내용 추적 솔루션으로 사용자 환경에서 소프트웨어 변경 추적](../log-analytics/log-analytics-change-tracking.md)을 참조하세요.
* 가상 머신에서 Windows 및 패키지 업데이트 관리에 대해 알아보려면 [Azure의 업데이트 관리 솔루션](../operations-management-suite/oms-solution-update-management.md)을 참조하세요.