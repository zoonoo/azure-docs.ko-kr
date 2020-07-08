---
title: Azure Site Recovery에서 모바일 서비스의 자동 업데이트
description: Azure Site Recovery를 사용 하 여 Azure Vm을 복제 하는 경우 모바일 서비스의 자동 업데이트 개요입니다.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: b2f5faea3df695500ea245d1dc71cb96a84c3643
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985604"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Azure 간 복제에서 모바일 서비스의 자동 업데이트

Azure Site Recovery는 월별 릴리스 주기를 사용 하 여 모든 문제를 해결 하 고 기존 기능을 향상 시키거나 새 기능을 추가 합니다. 서비스의 최신 상태를 유지 하려면 매월 패치 배포를 계획 해야 합니다. 각 업그레이드와 관련 된 오버 헤드를 방지 하기 위해 Site Recovery 구성 요소 업데이트를 관리 하도록 허용할 수 있습니다.

[Azure-azure 재해 복구 아키텍처](azure-to-azure-architecture.md)에 설명 된 것 처럼 모바일 서비스는 한 azure 지역에서 다른 azure 지역으로 복제를 사용 하도록 설정한 모든 azure vm (가상 머신)에 설치 됩니다. 자동 업데이트를 사용 하는 경우 각 새 릴리스는 모바일 서비스 확장을 업데이트 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>자동 업데이트 작동 방법

Site Recovery를 사용 하 여 업데이트를 관리 하는 경우 자격 증명 모음과 동일한 구독에서 만든 automation 계정을 통해 Azure 서비스에서 사용 되는 글로벌 runbook을 배포 합니다. 각 자격 증명 모음은 하나의 automation 계정을 사용 합니다. Runbook은 자격 증명 모음의 각 VM에 대해 활성 자동 업데이트를 확인 합니다. 최신 버전의 모바일 서비스 확장을 사용할 수 있는 경우 업데이트가 설치 됩니다.

기본 runbook 일정은 복제 된 VM의 지리 표준 시간대에 매일 오전 12:00에 발생 합니다. Automation 계정을 통해 runbook 일정을 변경할 수도 있습니다.

> [!NOTE]
> [업데이트 롤업 35](site-recovery-whats-new.md#updates-march-2019)부터 업데이트에 사용할 기존 automation 계정을 선택할 수 있습니다. 업데이트 롤업 35 이전에는 Site Recovery automation 계정을 기본적으로 만들었습니다. VM에 대 한 복제를 사용 하도록 설정한 경우에만이 옵션을 선택할 수 있습니다. 이미 복제를 사용 하도록 설정한 VM에 대해서는 사용할 수 없습니다. 선택한 설정은 동일한 자격 증명 모음에서 보호 되는 모든 Azure Vm에 적용 됩니다.

자동 업데이트를 설정 하려면 Azure Vm을 다시 시작 하거나 진행 중인 복제에 영향을 주지 않습니다.

Automation 계정의 작업 요금은 한 달에 사용 된 작업 런타임 시간 (분)을 기반으로 합니다. 작업 실행은 하루에 약 1 분 정도 걸리며 무료 단위로 적용 됩니다. 기본적으로 다음 표에 나와 있는 것 처럼 500 분은 automation 계정에 대 한 사용 가능한 단위로 포함 됩니다.

| 포함 된 무료 단위 (매월) | Price |
|---|---|
| 작업 런타임 500 분 | ₹ 0.14/분

## <a name="enable-automatic-updates"></a>자동 업데이트 사용

Site Recovery는 확장 업데이트를 관리할 수 있는 여러 가지 방법이 있습니다.

- [복제 사용 단계의 일부로 관리](#manage-as-part-of-the-enable-replication-step)
- [자격 증명 모음 내에서 확장 업데이트 설정 전환](#toggle-the-extension-update-settings-inside-the-vault)
- [수동으로 업데이트 관리](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>복제 사용 단계의 일부로 관리

[Vm 보기](azure-to-azure-quickstart.md) 또는 [recovery services 자격 증명 모음](azure-to-azure-how-to-enable-replication.md)에서 시작 하 여 vm에 대 한 복제를 사용 하도록 설정 하는 경우 Site Recovery Site Recovery 확장에 대 한 업데이트를 관리 하거나 수동으로 관리 하도록 허용할 수 있습니다.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="확장 프로그램 설정":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>자격 증명 모음 내에서 확장 업데이트 설정 전환

1. Recovery Services 자격 증명 모음에서 **관리**  >  **Site Recovery 인프라**로 이동 합니다.
1. **Azure Virtual Machines**  >  **확장 업데이트 설정**에서  >  **관리 Site Recovery 허용**을 선택 합니다. **On**

   확장을 수동으로 관리 하려면 **끄기**를 선택 합니다.

1. **저장**을 선택합니다.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="확장 업데이트 설정":::

> [!IMPORTANT]
> **Site Recovery에서 관리 하도록 허용**을 선택 하면 해당 설정이 자격 증명 모음에 있는 모든 vm에 적용 됩니다.

> [!NOTE]
> 두 옵션은 업데이트를 관리 하는 데 사용 되는 automation 계정을 알려 줍니다. 자격 증명 모음에서이 기능을 처음 사용 하는 경우에는 기본적으로 새 automation 계정이 생성 됩니다. 또는 설정을 사용자 지정 하 고 기존 automation 계정을 선택할 수 있습니다. 이후에 동일한 자격 증명 모음에서 복제를 사용 하도록 설정 하면 이전에 만든 automation 계정이 사용 됩니다. 현재 드롭다운 메뉴에는 자격 증명 모음과 동일한 리소스 그룹에 있는 automation 계정만 나열 됩니다.

### <a name="manage-updates-manually"></a>수동으로 업데이트 관리

1. Vm에 설치 된 모바일 서비스에 대 한 새 업데이트가 있는 경우 **새 Site Recovery 복제 에이전트 업데이트를 사용할 수 있습니다. 클릭 하 여 설치 합니다.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="복제된 항목 창":::

1. 알림을 선택 하 여 VM 선택 페이지를 엽니다.
1. 업그레이드할 Vm을 선택 하 고 **확인**을 선택 합니다. 선택한 각 VM에 대해 모바일 서비스 업데이트를 시작 합니다.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="복제된 항목 VM 목록":::

## <a name="common-issues-and-troubleshooting"></a>일반적인 문제 및 문제 해결

자동 업데이트에 문제가 있는 경우 자격 증명 모음 대시보드의 **구성 문제** 에 오류 알림이 표시 됩니다.

자동 업데이트를 사용할 수 없는 경우 다음과 같은 일반적인 오류 및 권장 되는 작업을 참조 하세요.

- **오류**: Azure 실행 계정(서비스 사용자)을 만들고 서비스 사용자에게 기여자 역할을 부여할 권한이 없습니다.

  **권장 조치**: 로그인 된 계정이 참가자로 할당 되었는지 확인 하 고 다시 시도 하세요. 권한 할당에 대 한 자세한 내용은 [방법: 포털을 사용 하 여 리소스에 액세스할 수 있는 AZURE AD 응용 프로그램 및 서비스 주체 만들기](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)의 필수 권한 섹션을 참조 하세요.

  자동 업데이트를 사용 하도록 설정한 후 대부분의 문제를 해결 하려면 **복구**를 선택 합니다. 복구 단추를 사용할 수 없는 경우 확장 업데이트 설정 창에 표시 되는 오류 메시지를 참조 하세요.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="확장 업데이트 설정의 Site Recovery 서비스 복구 단추":::

- **오류**: 복구 서비스 리소스에 액세스할 수 있는 권한이 실행 계정에 없습니다.

  **권장 작업**: 실행 계정을 삭제 하 고 [다시 만듭니다](/azure/automation/automation-create-runas-account). 또는 Automation 실행 계정의 Azure Active Directory 응용 프로그램이 recovery services 리소스에 액세스할 수 있는지 확인 합니다.

- **오류**: 실행 계정을 찾을 수 없습니다. Azure Active Directory 애플리케이션, 서비스 사용자, 역할, Automation 인증서 자산, Automation 연결 자산 중 하나가 삭제되었거나 생성되지 않았습니다. 또는 인증서와 연결 사이에서 지문이 일치하지 않습니다.

  **권장 작업**: 실행 계정을 삭제 하 고 [다시 만듭니다](/azure/automation/automation-create-runas-account).

- **오류**: automation 계정에서 사용 하는 Azure 실행 인증서가 곧 만료 됩니다.

  실행 계정에 대해 생성 된 자체 서명 된 인증서는 만든 날짜 로부터 1 년 후에 만료 됩니다. 만료되기 전에 언제든지 갱신할 수 있습니다. 전자 메일 알림을 등록 한 경우 사용자 측에서 조치가 필요한 경우에도 전자 메일을 받게 됩니다. 이 오류는 만료 날짜 전에 2 개월 후에 표시 되며, 인증서가 만료 되 면 심각한 오류로 변경 됩니다. 인증서가 만료 된 후에는 동일한을 갱신할 때까지 자동 업데이트가 작동 하지 않습니다.

  **권장 조치**:이 문제를 해결 하려면 **복구** 를 선택한 다음 **인증서 갱신**을 선택 합니다.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="갱신-인증서":::

  > [!NOTE]
  > 인증서를 갱신 한 후 페이지를 새로 고쳐 현재 상태를 표시 합니다.
