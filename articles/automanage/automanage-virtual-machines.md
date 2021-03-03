---
title: 가상 컴퓨터에 대 한 Azure Automanage
description: 가상 컴퓨터에 대 한 Azure Automanage에 대해 알아봅니다.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 1d3b2174df5dd83852ce120ec6693ae187a3e795
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101643537"
---
# <a name="azure-automanage-for-virtual-machines"></a>가상 컴퓨터에 대 한 Azure Automanage

이 문서에서는 다음과 같은 이점을 제공 하는 가상 머신에 대 한 Azure Automanage에 대해 설명 합니다.

- 가상 머신을 지능적으로 선택 하 여 모범 사례 Azure 서비스 선택
- Azure 모범 사례 마다 자동으로 각 서비스를 구성 합니다.
- 검색 된 경우 드리프트를 모니터링 하 고 수정 합니다.
- 간단한 환경을 제공 합니다 (점, 클릭, 설정, 잊어버린).


## <a name="overview"></a>개요

가상 컴퓨터에 대 한 azure Automanage는 Azure에서 가상 컴퓨터에 유용한 특정 서비스를 검색 하 고, 등록 하는 방법을 파악 하 고, Azure에서 특정 서비스를 구성 하는 방법을 파악 하지 않아도 되는 서비스입니다. 이러한 서비스는 Azure 모범 사례 서비스로 간주 되며, 가상 컴퓨터에 대 한 안정성, 보안 및 관리를 개선 하는 데 도움이 됩니다. 예제 서비스는 [Azure 업데이트 관리](../automation/update-management/overview.md) 및 [Azure Backup](../backup/backup-overview.md)를 포함 합니다.

Azure Automanage에 가상 머신을 온 보 딩 한 후에는 각 모범 사례 서비스가 권장 설정으로 구성 됩니다. 모범 사례는 각 서비스 마다 다릅니다. Azure Backup 예를 들어, 하루에 한 번 가상 머신을 백업 하 고 6 개월의 보존 기간을 설정 하는 것이 가장 좋습니다.

또한 Azure Automanage는 검색 된 경우 자동으로 드리프트를 모니터링 하 고 수정 합니다. 즉, 가상 머신이 Azure Automanage에 등록 Azure 모범 사례에 따라 구성할 수는 없지만, 컴퓨터를 모니터링 하 여 전체 수명 주기 동안 이러한 모범 사례를 계속 준수 하는지 확인 합니다. 가상 머신이 이러한 사례를 드리프트 하거나 offboarded 하는 경우 (예: 서비스가 다시 시작 되는 경우),이를 수정 하 고 컴퓨터를 원하는 상태로 다시 끌어옵니다.

## <a name="prerequisites"></a>필수 구성 요소

가상 머신에서 Azure Automanage를 사용 하도록 설정 하기 전에 고려해 야 할 몇 가지 필수 구성 요소가 있습니다.

- 지원 되는 [Windows Server 버전](automanage-windows-server.md#supported-windows-server-versions) 및 [Linux](automanage-linux.md#supported-linux-distributions-and-versions) 배포판
- Vm은 지원 되는 지역에 있어야 합니다 (아래 참조).
- 사용자에 게 올바른 사용 권한이 있어야 합니다 (아래 참조).
- 지금은 automanage에서 샌드박스 구독을 지원 하지 않습니다.

### <a name="supported-regions"></a>지원되는 지역
Automanage는 다음 지역에 있는 Vm만 지원 합니다.
* 서유럽
* 북유럽
* 미국 중부
* 미국 동부
* 미국 동부 2
* 미국 서부
* 미국 서부 2
* 캐나다 중부
* 미국 중서부
* 미국 중남부
* 일본 동부
* 영국 남부
* 오스트레일리아 동부
* 오스트레일리아 남동부

### <a name="required-rbac-permissions"></a>필요한 RBAC 권한
새 Automanage 계정으로 Automanage를 사용 하도록 설정할지 여부에 따라 계정에는 약간 다른 RBAC 역할이 필요 합니다.

새 Automanage 계정으로 Automanage를 사용 하도록 설정 하는 경우:
* Vm을 포함 하는 구독에 대 한 **소유자** 역할 _**또는**_
* Vm을 포함 하는 구독에 대 한 **참가자** 및 **사용자 액세스 관리자** 역할

기존 Automanage 계정으로 Automanage를 사용 하도록 설정 하는 경우:
* Vm을 포함 하는 리소스 그룹의 **참가자** 역할

> [!NOTE]
> 다른 구독의 작업 영역에 연결 된 VM에서 Automanage를 사용 하려면 각 구독에서 위에 설명 된 사용 권한이 있어야 합니다.

## <a name="participating-services"></a>참여 하는 서비스

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="지능적으로 서비스를 등록 합니다.":::

지원 되는 환경 뿐만 아니라 참여 하는 Azure 서비스의 전체 목록은 다음을 참조 하세요.
- [Linux 용 automanage](automanage-linux.md)
- [Windows Server에 대 한 automanage](automanage-windows-server.md)

 이러한 참여 하는 서비스에 자동으로 등록 됩니다. [클라우드 채택 프레임 워크](/azure/cloud-adoption-framework/manage/azure-server-management)에서 찾을 수 있는 모범 사례 백서에 반드시 필요 합니다.

이러한 모든 서비스에 대해 자동으로 등록 하 고, 드리프트를 자동으로 구성 하 고, 드리프트가 검색 되 면 중재를 모니터링 합니다.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Azure Portal에서 Vm에 Automanage 사용

Azure Portal에서 기존 가상 컴퓨터에 대해 자동 관리를 사용 하도록 설정 하거나 새 가상 컴퓨터를 만들 수 있습니다. 이 프로세스에 대 한 간결한 단계는 [virtual machines의 Automanage 빠른](quick-create-virtual-machines-portal.md)시작을 참조 하세요.

VM에 대해 Automanage를 처음 사용 하는 경우 **automanage – Azure virtual machines 모범 사례** 에 대 한 Azure Portal에서 검색할 수 있습니다. **기존 vm에서 사용** 을 클릭 하 고, 등록 하려는 vm을 선택 하 고, **선택**, **사용** 을 차례로 클릭 한 후 완료 합니다.

이러한 서비스를 관리 하기 위해이 VM과 상호 작용 해야 하는 경우에는 VM을 수정 하려고 했지만 그렇게 하지 못한 이벤트에 있습니다. VM을 성공적으로 재구성 한 경우 경고를 표시 하지 않고 다시 준수 상태로 전환 합니다. 자세한 내용은 [vm의 상태](#status-of-vms)를 참조 하세요.


## <a name="environment-configuration"></a>환경 구성

가상 컴퓨터에 대해 Automanage를 사용 하도록 설정 하는 경우 환경이 필요 합니다. 환경은이 서비스의 기반이 됩니다. 컴퓨터를 등록 하는 서비스와 해당 서비스의 구성에 대 한 범위를 정의 합니다.

### <a name="default-environments"></a>기본 환경

현재 사용할 수 있는 두 가지 환경이 있습니다.

- **개발/테스트 환경은 개발** /테스트 컴퓨터용으로 설계 되었습니다.
- **프로덕션 환경은 프로덕션** 환경입니다.

이러한 차이점은를 실행 하는 작업에 따라 특정 서비스가 권장 되기 때문입니다. 예를 들어 프로덕션 컴퓨터에서 자동으로 Azure Backup으로 등록 됩니다. 그러나 개발/테스트 컴퓨터는 일반적으로 비즈니스에 영향을 미치기 때문에 개발/테스트 컴퓨터의 경우에는 불필요 한 비용을 줄일 수 있습니다.

### <a name="customizing-an-environment-using-preferences"></a>기본 설정을 사용 하 여 환경 사용자 지정

사용자가 등록 하는 표준 서비스 외에도 기본 설정의 특정 하위 집합을 구성할 수 있습니다. 이러한 기본 설정은 구성 옵션의 범위 내에서 허용 됩니다. 예를 들어 Azure Backup의 경우 백업 빈도와 백업 빈도를 정의 하 고이를 수행 하는 요일을 지정할 수 있습니다.

> [!NOTE]
> 개발/테스트 환경에서는 VM을 전혀 백업 하지 않습니다.

기본 설정에 따라 기본 환경의 설정을 조정할 수 있습니다. [여기](virtual-machines-custom-preferences.md)에서 기본 설정을 만드는 방법을 알아보세요.

> [!NOTE]
> Automanage를 사용 하는 동안에는 VM에서 enivonrment 구성을 변경할 수 없습니다. 해당 VM에 대해 Automanage를 사용 하지 않도록 설정한 다음 원하는 환경 및 기본 설정으로 Automanage를 다시 사용 하도록 설정 해야 합니다.

참여 하는 Azure 서비스의 전체 목록과 기본 설정이 지원 되는 경우 여기를 참조 하세요.
- [Linux 용 automanage](automanage-windows-server.md)
- [Windows Server에 대 한 automanage](automanage-windows-server.md)


## <a name="automanage-account"></a>계정 자동 관리

Automanage 계정은 자동화 된 작업이 수행 되는 보안 컨텍스트 또는 id입니다. 일반적으로 Automanage 계정 옵션을 선택 하는 것은 필수는 아니지만 리소스의 자동화 된 관리를 나누려면 (두 시스템 관리자)이 옵션을 사용 하면 각 관리자에 대해 Azure id를 정의할 수 있습니다.

Azure Portal 환경에서 Vm에 대해 Automanage를 사용 하도록 설정 하는 경우 Automanage 계정을 할당 하거나 수동으로 만들 수 있는 **AZURE VM 모범 사례 사용** 블레이드에서 고급 드롭다운이 있습니다.

Automanage 계정에는 Automanage에 등록 한 컴퓨터를 포함 하는 구독에 대 한 **참가자** 및 **리소스 정책 참가자** 역할이 모두 부여 됩니다. 여러 구독에서 컴퓨터에 동일한 Automanage 계정을 사용할 수 있습니다. 그러면 모든 구독에 대 한 자동 관리 계정 **참여자** 및 **리소스 정책 참가자** 권한이 부여 됩니다.

VM이 다른 구독의 Log Analytics 작업 영역에 연결 되어 있는 경우 Automanage 계정에는 **참가자** 와 **리소스 정책 참여자** 가 다른 구독에도 모두 부여 됩니다.

새 Automanage 계정으로 Automanage를 사용 하도록 설정 하는 경우 **사용자 액세스 관리자** 역할과 함께 **소유자** 역할 또는 **참가자** 에 대 한 다음 권한이 필요 합니다.

기존 Automanage 계정으로 Automanage를 사용 하도록 설정 하는 경우 Vm이 포함 된 리소스 그룹에 대 한 **참가자** 역할이 있어야 합니다.

> [!NOTE]
> Automanage 모범 사례를 사용 하지 않도록 설정 하면 연결 된 구독에 대 한 Automanage 계정의 사용 권한이 유지 됩니다. 구독의 IAM 페이지로 이동 하 여 사용 권한을 수동으로 제거 하거나 Automanage 계정을 삭제 합니다. 컴퓨터를 계속 관리 하는 경우 Automanage 계정을 삭제할 수 없습니다.


## <a name="status-of-vms"></a>Vm의 상태

Azure Portal에서 자동 관리 되는 모든 Vm을 나열 하는 **Automanage – Azure virtual machines 모범 사례** 페이지로 이동 합니다. 여기에서 각 가상 머신의 전반적인 상태를 확인할 수 있습니다.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="구성 된 가상 컴퓨터 목록입니다.":::

나열 된 각 VM에 대해 이름, 환경, 구성 기본 설정, 상태, 운영 체제, 계정, 구독 및 리소스 그룹 세부 정보가 표시 됩니다.

**상태** 열에는 다음 상태가 표시 될 수 있습니다.
- *진행 중* -VM을 사용 하도록 설정 하 고 구성 하는 중입니다.
- *구성* 됨-VM이 구성 되 고 드리프트가 검색 되지 않습니다.
- *실패* -VM이 데이터베이스가 드리프트을 수정할 수 없습니다.
- *보류 중* -vm은 현재 실행 되 고 있지 않으며 automanage는 다음에 실행 될 때 vm을 등록 하거나 재구성 하려고 합니다.

**상태가** *실패* 로 표시 되는 경우 VM이 있는 리소스 그룹을 통해 배포 문제를 해결할 수 있습니다. **리소스 그룹** 으로 이동 하 여 리소스 그룹을 선택 하 고 **배포** 를 클릭 한 다음 오류 정보와 함께 *실패* 상태를 확인 합니다.


## <a name="disabling-automanage-for-vms"></a>Vm에 대해 Automanage를 사용 하지 않도록 설정

특정 Vm에서 Automanage를 사용 하지 않도록 설정할 수 있습니다. 예를 들어, 컴퓨터에서 매우 중요 한 보안 워크 로드를 실행 하 고 있으므로 Azure 보다 더 많이 잠가야 합니다. 따라서 Azure 모범 사례 외부에서 컴퓨터를 구성 해야 합니다.

Azure Portal에서이 작업을 수행 하려면 자동 관리 되는 모든 Vm을 나열 하는 **Automanage – Azure virtual machines 모범 사례** 페이지로 이동 합니다. Automanage에서 사용 하지 않도록 설정할 가상 컴퓨터 옆의 확인란을 선택 하 고 **Automanage 사용 안 함** 단추를 클릭 합니다.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="가상 머신에서 Automanage 사용 안 함":::

**사용 안 함** 에 동의하기 전에 결과 팝업의 메시지를 자세히 읽어 보세요.

> [!NOTE]
> VM에서 automanagement를 사용 하지 않도록 설정 하면 다음과 같은 동작이 발생 합니다.
>
> - VM의 구성과 서비스 등록 변경 되지 않습니다.
> - 이러한 서비스에 의해 발생 하는 요금은 청구 가능 하 게 유지 되 고 계속 해 서 발생 합니다.
> - 모든 Automanage 동작은 즉시 중지 됩니다.


무엇 보다도, 우리가 등록 구성 된 서비스에서 가상 머신을 오프 보드 하지 않습니다. 따라서 해당 서비스에 의해 발생 하는 모든 요금은 계속 청구 가능 상태로 유지 됩니다. 필요한 경우에는 보드를 꺼야 합니다. 모든 Automanage 동작은 즉시 중지 됩니다. 예를 들어 더 이상 VM에서 드리프트를 모니터링 하지 않습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 머신에 대 한 Automanage에서 모범 사례 Azure 서비스를 알고, 등록 및 구성 하지 않아도 되는 경우를 제거할 수 있는 방법을 배웠습니다. 또한 가상 컴퓨터에 대해 Automanage 등록 컴퓨터가 환경 설정에서 상태가 하는 경우 자동으로 다시 준수 상태로 전환 됩니다.

Azure Portal에서 가상 컴퓨터에 대해 Automanage를 사용 하도록 설정 하세요.

> [!div class="nextstepaction"]
> [Azure Portal에서 가상 컴퓨터에 대해 Automanage를 사용 하도록 설정](quick-create-virtual-machines-portal.md)