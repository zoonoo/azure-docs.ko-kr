---
title: VM에서 Azure Automation 인벤토리 수집 관리 | Microsoft Docs
description: 이 문서에서는 VM에서 인벤토리 수집을 관리하는 방법을 설명합니다.
services: automation
ms.subservice: change-inventory-management
keywords: 인벤토리, 자동화, 변경, 추적
ms.date: 06/30/2020
ms.topic: conceptual
ms.openlocfilehash: 32d3c17a5f3d152f32b19ffbfd5c9793a7a34b80
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185724"
---
# <a name="manage-inventory-collection-from-vms"></a>VM에서 인벤토리 컬렉션 관리

Azure VM의 리소스 페이지에서 해당 VM의 인벤토리 추적을 사용하도록 설정할 수 있습니다. 컴퓨터에 대한 다음과 같은 인벤토리 정보를 수집하고 볼 수 있습니다.

- Windows 업데이트, Windows 애플리케이션, 서비스, 파일, 레지스트리 키
- Linux 소프트웨어 패키지, 디먼, 파일

Azure Automation 변경 내용 추적 및 인벤토리는 인벤토리 수집의 설정 및 구성을 위한 브라우저 기반 사용자 인터페이스를 제공합니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/).

이 문서에서는 변경 내용 추적 및 인벤토리를 사용하도록 설정할 VM이 있다고 가정합니다. Azure VM이 없으면 [VM 만들기](../virtual-machines/windows/quick-create-portal.md)를 진행할 수 있습니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="enable-inventory-collection-from-the-vm-resource-page"></a>VM 리소스 페이지에서 인벤토리 수집을 사용하도록 설정

1. Azure Portal의 왼쪽 창에서 **가상 머신**를 선택합니다.
2. VM 목록에서 머신을 선택합니다.
3. **작업** 아래의 **리소스** 메뉴에서 **인벤토리**를 선택합니다.
4. 데이터 로그를 저장할 Log Analytics 작업 영역을 선택합니다.
    해당 영역에 대해 사용할 수 있는 작업 영역이 없는 경우 기본 작업 영역 및 자동화 계정을 만들라는 메시지가 표시됩니다.
5. 컴퓨터 사용 설정을 시작하려면 **사용**을 선택합니다.

   ![온보딩 옵션 보기](./media/automation-vm-inventory/inventory-onboarding-options.png)

    상태 표시줄에 변경 내용 추적 및 인벤토리 기능을 사용하도록 설정하고 있다는 메시지가 표시됩니다. 이 프로세스는 15분까지 걸릴 수 있습니다. 이 시간 동안 창을 닫을 수 있습니다. 또는 창을 열어 두고 이 기능이 사용하도록 설정되면 알림 메시지를 받을 수 있습니다. 알림 창에서 배포 상태를 모니터링할 수 있습니다.

   ![인벤토리 보기](./media/automation-vm-inventory/inventory-onboarded.png)

배포가 완료되면 상태 표시줄이 사라집니다. 이때 시스템은 여전히 인벤토리 데이터를 수집하며, 아직 데이터가 표시되지 않을 수 있습니다. 전체 데이터 수집에 24시간이 걸릴 수 있습니다.

## <a name="configure-your-inventory-settings"></a>인벤토리 설정 구성

기본적으로 수집을 위해 소프트웨어, Windows 서비스 및 Linux 데몬을 구성합니다. Windows 레지스트리 및 파일 인벤토리를 수집하기 위해 인벤토리 컬렉션 설정을 구성합니다.

1. 인벤토리 페이지 위쪽에서 **설정 편집**을 클릭합니다.
2. 새 수집 설정을 추가하려면 **Windows 레지스트리**, **Windows 파일** 또는 **Linux 파일** 탭을 선택하여 추가할 설정 범주로 이동합니다.
3. 적절한 범주를 선택하고 페이지 위쪽에서 **추가**를 클릭합니다.

이어지는 섹션에서는 다양한 범주에 대해 구성할 수 있는 각 속성에 대한 정보를 제공합니다.

### <a name="windows-registry"></a>Windows 레지스트리

|속성  |Description  |
|---------|---------|
|사용     | 설정 적용 여부 결정        |
|Item Name     | 추적할 파일의 이름        |
|그룹     | 파일을 논리적으로 그룹화하는 그룹 이름        |
|Windows 레지스트리 키   | 파일을 확인할 경로입니다(예: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="windows-files"></a>Windows 파일

|속성  |Description  |
|---------|---------|
|사용     | 설정이 적용되면 True이고, 그렇지 않으면 False입니다.        |
|Item Name     | 추적할 파일의 식별 이름입니다.        |
|그룹     | 논리적으로 파일을 그룹화하는 그룹 이름입니다.       |
|경로 입력     | 파일을 확인할 경로입니다(예: **c:\temp\myfile.txt**).

### <a name="linux-files"></a>Linux 파일

|속성  |Description  |
|---------|---------|
|사용     | 설정이 적용되면 True이고, 그렇지 않으면 False입니다.        |
|Item Name     | 추적할 파일의 식별 이름입니다.        |
|그룹     | 논리적으로 파일을 그룹화하는 그룹 이름입니다.        |
|경로 입력     | 파일을 확인할 경로입니다(예: **/etc/*.conf**).       |
|경로 유형     | 추적할 항목의 유형입니다. 값은 파일 및 디렉터리입니다.        |
|재귀     | 추적할 항목을 찾을 때 재귀가 사용되면 True이고, 그렇지 않으면 False입니다.        |
|Sudo 사용     | 항목을 확인할 때 sudo가 사용되었으면 True이고, 그렇지 않으면 False입니다.         |
|링크     | 디렉터리를 트래버스할 때 바로 가기 링크가 처리되는 방식을 나타내는 값입니다. 가능한 값은 다음과 같습니다. <br> 무시 - 바로 가기 링크가 무시되고 참조된 파일/디렉터리가 포함되지 않습니다.<br>팔로우 - 재귀 중에 바로 가기 링크를 따르고 참조된 파일/디렉터리도 포함합니다.<br>관리 - 바로 가기 링크를 따르고 반환된 콘텐츠의 취급을 변경할 수 있도록 허용합니다.      |

## <a name="manage-machine-groups"></a>머신 그룹 관리

인벤토리를 사용하면 Azure Monitor 로그에서 머신 그룹을 만들고 볼 수 있습니다. 머신 그룹은 Azure Monitor 로그에서 쿼리에 의해 정의된 머신의 컬렉션입니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

머신 그룹을 보려면 인벤토리 페이지에서 **머신 그룹** 탭을 선택합니다.

![인벤토리 페이지에서 머신 그룹 보기](./media/automation-vm-inventory/inventory-machine-groups.png)

목록에서 머신 그룹을 선택하면 머신 그룹 페이지가 열립니다. 이 페이지에서는 머신 그룹에 대한 정보를 보여 줍니다. 이러한 세부 정보에는 그룹을 정의 하는 데 사용 되는 Azure Monitor 로그 쿼리가 포함 됩니다. 페이지의 맨 아래에서 해당 그룹의 일부인 머신의 페이징된 목록입니다.

![머신 그룹 페이지 보기](./media/automation-vm-inventory/machine-group-page.png)

**+ 복제**를 클릭하여 머신 그룹을 복제합니다. 그룹에 새 이름과 별칭을 지정해야 합니다. 이번에는 정의를 변경할 수 있습니다. 쿼리를 변경한 후에는 **쿼리 유효성 검사**를 클릭하여 선택할 머신을 미리 봅니다. 이 그룹에 만족하면 **만들기**를 클릭하여 머신 그룹을 만듭니다.

새 머신 그룹을 만들려면 **+ 머신 그룹 만들기**를 클릭합니다. 이 단추를 누르면 새 그룹을 정의할 수 있는 **머신 그룹 만들기 페이지**가 열립니다. **만들기**를 클릭하여 그룹을 만듭니다.

![새 머신 그룹 만들기](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-vm-from-management"></a>관리에서 VM 연결 끊기

변경 내용 추적 및 인벤토리 관리에서 VM을 제거하려면:

1. Azure Portal의 왼쪽 창에서 **Log Analytics**를 선택한 다음 VM에서 변경 내용 추적 및 인벤토리를 사용하도록 설정할 때 사용한 작업 영역을 선택합니다.
2. **Log Analytics** 페이지에서 **리소스** 메뉴를 엽니다.
3. **작업 영역 데이터 원본** 아래에서 **가상 머신**을 선택합니다.
4. 목록에서 연결을 끊을 VM을 선택합니다. 머신에는 **OMS 연결** 열의 **이 작업 영역** 옆에 녹색 확인 표시가 있습니다.

   >[!NOTE]
   >OMS(Operations Management Suite)가 이제 ‘Azure Monitor 로그’로 지칭됩니다.

5. 다음 페이지 맨 위에서 **연결 끊기**를 클릭합니다.
6. 확인 창에서 **예**를 클릭하여 관리에서 머신의 연결을 끊습니다.

>[!NOTE]
>최근 24 시간 동안 인벤토리에 포함 된 모든 컴퓨터를 보고 하기 때문에 컴퓨터가 등록 취소 된 후에도 계속 표시 됩니다. 컴퓨터 연결을 끊은 후에는 더 이상 나열 되지 않도록 24 시간 동안 기다려야 합니다.

## <a name="next-steps"></a>다음 단계

* 이 기능을 사용하는 방법에 대한 자세한 내용은 [변경 내용 추적 및 인벤토리 관리](change-tracking-file-contents.md)를 참조하세요.
* 소프트웨어 변경 내용 추적에 대한 자세한 내용은 [변경 내용 추적으로 사용자 환경에서 소프트웨어 변경 내용 추적](./change-tracking.md)을 참조하세요.
* 이 기능의 일반적인 문제를 해결하려면 [변경 내용 추적 및 인벤토리 문제 해결](troubleshoot/change-tracking.md)을 참조하세요.
