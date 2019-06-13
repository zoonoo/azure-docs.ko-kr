---
title: Azure Site Recovery를 사용하여 보조 Azure 지역에 Azure VM의 재해 복구 설정
description: Azure Site Recovery 서비스를 사용하여 다른 Azure 지역에 Azure VM의 재해 복구를 설정하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5602298ffe60ad15d3daf52587c50357c310200c
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480086"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Azure VM에 대한 재해 복구 설정

[Azure Site Recovery](site-recovery-overview.md) 서비스는 온-프레미스 컴퓨터와 Azure VM(Virtual Machines)의 복제, 장애 조치(failover), 장애 복구(failback)를 관리 및 오케스트레이션하여 재해 복구 전략에 기여합니다.

이 자습서에서는 다른 Azure 지역으로 복제하여 Azure VM의 재해 복구를 설정하는 방법을 보여 줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Recovery Services 자격 증명 모음 만들기
> * 대상 리소스 설정 확인
> * VM에 대한 아웃바운드 네트워크 연결 설정
> * VM에 대한 복제 사용

> [!NOTE]
> 이 문서에서는 가장 간단한 설정을 사용하여 재해 복구를 배포하기 위한 지침을 제공합니다. 사용자 지정된 설정을 알아보려면 [방법 섹션](azure-to-azure-how-to-enable-replication.md)의 문서를 검토하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- [시나리오 아키텍처 및 구성 요소](concepts-azure-to-azure-architecture.md)를 검토합니다.
- 시작하기 전에 [지원 요구 사항](site-recovery-support-matrix-azure-to-azure.md)을 검토합니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

원본 지역을 제외한 모든 지역에 자격 증명 모음을 만듭니다.

1. [Azure Portal](https://portal.azure.com) > **Recovery Services**에 로그인합니다.
2. **리소스 만들기** > **관리 도구** > **Backup 및 Site Recovery**를 클릭합니다.
3. **이름**에 자격 증명 모음을 식별하기 위한 이름을 지정합니다. 구독이 두 개 이상인 경우 적절한 구독을 선택합니다.
4. 리소스 그룹을 만들거나 기존 그룹을 선택합니다. Azure 지역을 지정합니다. 지원되는 지역을 확인하려면 [Azure Site Recovery 가격 정보](https://azure.microsoft.com/pricing/details/site-recovery/)에서 지리적 가용성을 참조하세요.
5. 대시보드에서 자격 증명 모음에 빠르게 액세스하려면 **대시보드에 고정**을 클릭하고 **만들기**를 클릭합니다.

   ![새 자격 증명 모음](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   **대시보드**의 **모든 리소스** 아래와 주 **Recovery Services 자격 증명 모음** 페이지에 새 자격 증명 모음이 추가됩니다.

## <a name="verify-target-resource-settings"></a>대상 리소스 설정 확인

1. Azure 구독에서 대상 지역에 VM을 만들 수 있도록 허용하는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.
2. 구독에 원본 VM과 동일한 크기의 VM을 지원할 수 있을 만큼 충분한 리소스가 있는지 확인합니다. Site Recovery는 대상 VM에 대해 동일한 크기 또는 가장 가깝게 가능한 크기를 선택합니다.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>VM에 대한 아웃바운드 네트워크 연결 설정

Site Recovery가 예상대로 작동하려면 복제하려는 VM에서 아웃바운드 네트워크 연결을 수정해야 합니다.

> [!NOTE]
> Site Recovery는 인증 프록시를 사용하여 네트워크 연결을 제어하도록 지원하지 않습니다.


### <a name="outbound-connectivity-for-urls"></a>URL에 대한 아웃바운드 연결

URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 이러한 URL에 대한 액세스를 허용하세요.

| **URL** | **세부 정보** |
| ------- | ----------- |
| \*.blob.core.windows.net | VM에서 원본 지역의 캐시 저장소 계정에 데이터를 쓸 수 있도록 합니다. |
| login.microsoftonline.com | Site Recovery 서비스 URL에 대한 권한 부여 및 인증을 제공합니다. |
| \*.hypervrecoverymanager.windowsazure.com | VM이 Site Recovery 서비스와 통신할 수 있도록 합니다. |
| \*.servicebus.windows.net | VM이 Site Recovery 모니터링 및 진단 데이터를 쓸 수 있도록 합니다. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 주소 범위에 대한 아웃바운드 연결

URL 대신 IP 주소를 사용하여 아웃바운드 연결을 제어하려는 경우에 IP 기반 방화벽, 프록시 또는 NSG 규칙에 대해 이러한 주소를 허용합니다.

  - [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)
  - [Windows Azure 데이터 센터 IP 범위(독일)](https://www.microsoft.com/download/details.aspx?id=54770)
  - [Windows Azure 데이터 센터 IP 범위(중국)](https://www.microsoft.com/download/details.aspx?id=42064)
  - [Office 365 URL 및 IP 주소 범위](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Site Recovery 서비스 엔드포인트 IP 주소](https://aka.ms/site-recovery-public-ips)

NSG를 사용하는 경우 원본 지역에 대한 스토리지 서비스 태그 NSG 규칙을 만들 수 있습니다. [자세히 알아보기](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges).

## <a name="verify-azure-vm-certificates"></a>Azure VM 인증서 확인

복제하려는 VM에 최신 루트 인증서가 있는지 확인합니다. 없으면 보안 제약으로 인해 VM이 Site Recovery에 등록될 수 업습니다.

- Windows VM의 경우 VM에 최신 Windows 업데이트를 모두 설치하여 신뢰할 수 있는 모든 루트 인증서가 컴퓨터에 있도록 합니다. 연결이 끊어진 환경에서 조직의 표준 Windows 업데이트 및 인증서 업데이트 프로세스를 따릅니다.
- Linux VM의 경우 Linux 배포자가 제공한 지침에 따라 신뢰할 수 있는 최신 루트 인증서 및 인증서 해지 목록을 VM에 가져옵니다.

## <a name="set-permissions-on-the-account"></a>계정에 권한 설정

Azure Site Recovery는 Site Recovery 관리 작업을 제어하는 3가지 기본 제공 역할을 제공합니다.

- **Site Recovery 참가자** - 이 역할에는 Recovery Services 자격 증명 모음에서 Azure Site Recovery 작업을 관리하는 데 필요한 모든 사용 권한이 있습니다. 그러나 이 역할의 사용자는 Recovery Services 자격 증명 모음을 만들거나 삭제할 수 없고 액세스 권한을 다른 사용자에게 할당할 수 없습니다. 이 역할은 애플리케이션이나 전체 조직에 재해 복구를 사용하도록 설정하고 관리할 수 있는 재해 복구 관리자에게 가장 적합합니다.

- **Site Recovery 연산자** - 이 역할에는 장애 조치 및 장애 복구 작업을 실행하고 관리하는 사용 권한이 있습니다. 이 역할의 사용자는 복제를 활성화하거나 비활성화할 수 없고, 자격 증명 모음을 만들거나 삭제할 수 없으며, 새로운 인프라를 등록하거나 다른 사용자에게 액세스 권한을 할당할 수 없습니다. 이 역할은 애플리케이션 소유자 및 IT 관리자가 지시하는 경우 가상 머신 또는 애플리케이션을 장애 조치할 수 있는 재해 복구 운영자에게 가장 적합합니다. 재해를 해결한 후에 DR 운영자는 가상 머신을 다시 보호하고 장애 복구(failback)할 수 있습니다.

- **Site Recovery 읽기 권한자** - 이 역할은 모든 Site Recovery 관리 작업을 볼 수 있는 권한을 갖습니다. 이 역할은 현재 보호 상태를 모니터링하고 지원 티켓을 발행할 수 있는 IT 모니터링 임원에게 가장 적합합니다.

[Azure RBAC 기본 제공 역할](../role-based-access-control/built-in-roles.md)에 대해 자세히 알아봅니다.

## <a name="enable-replication-for-a-vm"></a>VM에 대한 복제 사용

### <a name="select-the-source"></a>원본 선택

1. Recovery Services 자격 증명 모음에서 자격 증명 모음 이름 > **+복제**를 클릭합니다.
2. **원본**에서 **Azure**를 선택합니다.
3. **원본 위치**에서 VM이 현재 실행 중인 원본 Azure 지역을 선택합니다.
4. 가상 머신이 실행되는 **구독 원본**을 선택합니다. 복구 서비스 자격 증명 모음이 있는 동일한 Azure Active Directory 테넌트 내에 있는 구독일 수 있습니다.
5. **원본 리소스 그룹**을 선택하고 **확인**을 클릭하여 설정을 저장합니다.

    ![원본 설정](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>VM 선택

Site Recovery는 구독 및 리소스 그룹/클라우드 서비스와 연결된 VM 목록을 검색합니다.

1. **Virtual Machines**에서 복제할 VM을 선택합니다.
2. **확인**을 클릭합니다.

### <a name="configure-replication-settings"></a>복제 설정 구성

Site Recovery는 대상 지역에 대한 기본 설정 및 복제 정책을 만듭니다. 필요에 따라 설정을 변경할 수 있습니다.

1. **설정**을 클릭하여 대상 및 복제 설정을 확인합니다.
2. 기본 대상 설정을 재정의하려면 **리소스 그룹, 네트워크, 스토리지 및 가용성** 옆의 **사용자 지정**을 클릭합니다.

   ![설정 구성](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. 표에 요약된 것처럼 대상 설정을 사용자 지정합니다.

    **설정** | **세부 정보**
    --- | ---
    **대상 구독** | 기본적으로 대상 구독은 원본 구독과 동일합니다. '사용자 지정'을 클릭하여 동일한 Azure Active Directory 테넌트 내에서 다른 대상 구독을 선택합니다.
    **대상 위치** | 재해 복구에 사용되는 대상 지역입니다.<br/><br/> 대상 위치가 Site Recovery 자격 증명 모음의 위치와 일치하는 것이 좋습니다.
    **대상 리소스 그룹** | 장애 조치 후 Azure VM을 보유하는 대상 지역의 리소스 그룹입니다.<br/><br/> 기본적으로 Site Recovery는 "asr" 접미사를 사용하여 대상 지역에 새 리소스 그룹을 만듭니다. 원본 가상 머신이 호스트되는 지역을 제외한 모든 지역이 대상 리소스 그룹의 위치가 될 수 있습니다.
    **대상 가상 네트워크** | 장애 조치 후 VM이 있는 대상 지역의 네트워크입니다.<br/><br/> 기본적으로 Site Recovery는 "asr" 접미사를 사용하여 대상 지역에 새 가상 네트워크(및 서브넷)를 만듭니다.
    **캐시 저장소 계정** | Site Recovery에서 원본 지역의 스토리지 계정을 사용합니다. 원본 VM의 변경 내용이 이 계정으로 전송된 후 대상 위치에 복제됩니다.<br/><br/> 방화벽 지원 캐시 스토리지 계정을 사용하는 경우 **신뢰할 수 있는 Microsoft 서비스 허용**을 사용하도록 설정해야 합니다. [자세한 정보](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
    **대상 스토리지 계정(원본 VM에서 비관리형 디스크를 사용하는 경우)** : | 기본적으로 Site Recovery는 대상 지역에 새 스토리지 계정을 만들어서 원본 VM 스토리지 계정을 미러링합니다.<br/><br/> 방화벽 지원 캐시 저장소 계정을 사용하는 경우 **신뢰할 수 있는 Microsoft 서비스 허용**을 사용하도록 설정합니다.
    **복제본 Managed Disks(원본 VM에서 Managed Disks를 사용하는 경우)** : | 기본적으로 Site Recovery는 대상 지역에 복제본 관리 디스크를 만들어 원본 VM의 관리 디스크를 이 관리 디스크와 동일한 스토리지 유형(표준 또는 프리미엄)으로 미러링합니다. 디스크 유형만 사용자 지정할 수 있습니다. 
    **대상 가용성 집합** | 기본적으로 Azure Site Recovery는 원본 지역에서 가용성 세트에 있는 VM 부분의 이름에 “asr” 접미사가 있는 대상 지역에 새 가용성 세트를 만듭니다. Azure Site Recovery에서 만든 가용성 집합이 이미 있는 경우 해당 가용성 집합이 재사용 됩니다.
    **대상 가용성 영역** | 기본적으로 Site Recovery는 대상 지역이 가용성 영역을 지원하는 경우 대상 지역의 원본 지역과 동일한 영역 번호를 할당합니다.<br/><br/> 대상 지역이 가용성 영역을 지원하지 않는 경우 대상 VM은 기본적으로 단일 인스턴스로 구성됩니다.<br/><br/> **사용자 지정**을 클릭하여 대상 지역에서 가용성 집합의 일부로 VM을 구성합니다.<br/><br/> 복제를 사용하도록 설정한 후에는 가용성 유형(단일 인스턴스, 가용성 집합 또는 가용성 영역)을 변경할 수 없습니다. 가용성 유형을 변경하려면 복제를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다.

4. 복제 정책 설정을 사용자 지정하려면 **복제 정책** 옆의 **사용자 지정**을 클릭하고 필요에 따라 설정을 수정합니다.

    **설정** | **세부 정보**
    --- | ---
    **복제 정책 이름** | 정책 이름입니다.
    **복구 지점 보존** | 기본적으로 Site Recovery는 복구 지점을 24시간 동안 유지합니다. 이 값을 1-72시간 사이에서 구성할 수 있습니다.
    **앱 일치 스냅샷 빈도** | 기본적으로 Site Recovery는 4시간마다 앱 일치 스냅샷을 만듭니다. 이 값을 1-12시간 사이에서 구성할 수 있습니다.<br/><br/> 앱 일치 스냅샷은 VM 내 애플리케이션 데이터의 지정 시간 스냅샷입니다. VSS(볼륨 섀도 복사본 서비스)는 스냅샷을 만들 때 VM의 앱이 일관된 상태가 되도록 합니다.
    **복제 그룹** | 애플리케이션에서 여러 VM 간에 다중 VM 일관성을 유지해야 하는 경우 해당 VM에 대한 복제 그룹을 만들 수 있습니다. 선택한 VM은 기본적으로 복제 그룹에 포함되지 않습니다.

5. 새 또는 기존 복제 그룹에 VM을 추가하려는 경우  **사용자 지정**에서 다중 VM 일관성에 대해 **예**를 선택합니다. 그런 후 **OK**를 클릭합니다. 

    >[!NOTE]
    >- 복제 그룹의 모든 컴퓨터는 장애 조치(failover) 시에 충돌 일치/앱 일치 복구 지점을 공유합니다.
    >- 다중 VM 일관성을 사용하면 워크로드 성능에 영향을 줄 수 있습니다(CPU 집약적임). 머신이 동일한 워크로드를 실행 중이며 다중 머신에서 일관성이 필요한 경우에만 사용해야 합니다.
    >- 복제 그룹에는 최대 16개의 VM이 있을 수 있습니다.
    >- 다중 VM 일관성을 사용하도록 설정하면 복제 그룹의 컴퓨터는 20004 포트를 통해 서로 통신하게 됩니다. 이 포트를 통한 VM 간의 내부 통신을 차단하는 방화벽이 없는지 확인합니다.
    >- 복제 그룹에 있는 Linux VM의 경우, Linux 버전에 대한 지침에 따라 20004 포트의 아웃바운드 트래픽을 수동으로 열어야 합니다.



### <a name="configure-encryption-settings"></a>암호화 설정 구성

원본 VM에서 ADE(Azure Disk Encryption)가 사용하도록 설정되면 설정을 검토합니다.

1. 설정을 확인합니다.
    - **디스크 암호화 Key Vault**: 기본적으로 Site Recovery는 "asr" 접미사를 사용하여 원본 VM 디스크 암호화 키에 새 Key Vault를 만듭니다. Key Vault가 이미 있으면 다시 사용됩니다.
    - **키 암호화 Key Vault**: 기본적으로 Site Recovery는 대상 지역에 새 Key Vault를 만듭니다. 이름은 "asr" 접미사를 포함하며, 원본 VM 키 암호화 키를 기준으로 합니다. Site Recovery에서 만든 Key Vault가 이미 있으면 다시 사용됩니다.

2. **사용자 지정**을 클릭하여 사용자 지정 Key Vault를 선택합니다.

>[!NOTE]
>Azure Site Recovery에서는 Windows 운영 체제에서 실행 중인 Azure VM과 [Azure AD 앱을 통한 암호화가 가능](https://aka.ms/ade-aad-app)한 Azure VM만 지원됩니다.
>

### <a name="track-replication-status"></a>복제 상태 추적

1. **설정**에서 **새로 고침**을 클릭하여 최신 상태를 가져옵니다.
2. 다음과 같이 진행률 및 상태를 추적합니다.
    - **설정** > **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적합니다.
    - **설정** > **복제된 항목**에서 VM의 상태 및 초기 복제 진행률을 볼 수 있습니다. VM을 클릭하여 해당 설정으로 드릴다운합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure VM의 재해 복구를 구성해 보았습니다. 이제 재해 복구 훈련을 시작하여 장애 조치(Failover)가 예상대로 작동하는지 확인할 수 있습니다.

> [!div class="nextstepaction"]
> [재해 복구 드릴 실행](azure-to-azure-tutorial-dr-drill.md)
