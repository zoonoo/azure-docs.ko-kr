---
title: Azure 마이그레이션에서 하이퍼 V 마이그레이션 지원
description: Azure 마이그레이션을 통해 Hyper-V 마이그레이션에 대한 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 8ec0b72cac75518ac938faa202b28d055409e8dc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538191"
---
# <a name="support-matrix-for-hyper-v-migration"></a>하이퍼 V 마이그레이션을 위한 지원 매트릭스

이 문서에서는 [Azure 마이그레이션: 서버 마이그레이션을](migrate-services-overview.md#azure-migrate-server-migration-tool) 사용 하 고 Hyper-V VM 마이그레이션에 대 한 지원 설정 및 제한 을 요약 합니다. Azure로 마이그레이션할 하이퍼 VM 평가에 대한 정보를 찾고 있는 경우 [평가 지원 매트릭스를](migrate-support-matrix-hyper-v.md)검토하십시오.

## <a name="migration-limitations"></a>마이그레이션 제한 사항

복제를 위해 한 번에 최대 10개의 VM을 선택할 수 있습니다. 더 많은 컴퓨터를 마이그레이션하려면 10개의 그룹으로 복제합니다.


## <a name="hyper-v-hosts"></a>Hyper-V 호스트

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **배포**       | Hyper-V 호스트는 독립 실행형이거나 클러스터에 배포될 수 있습니다. <br/>Azure 마이그레이션 복제 소프트웨어(Hyper-V 복제 공급자)가 Hyper-V 호스트에 설치됩니다.|
| **사용 권한**           | Hyper-V 호스트에 대한 관리자 권한이 필요합니다. |
| **호스트 운영 체제** | 윈도우 서버 2019, 윈도우 서버 2016, 또는 윈도우 서버 2012 R2. |
| **포트 액세스** |  VM 복제 데이터를 전송하기 위해 HTTPS 포트 443의 아웃바운드 연결입니다.

### <a name="url-access-public-cloud"></a>URL 액세스(퍼블릭 클라우드)

Hyper-V 호스트의 복제 공급자 소프트웨어는 이러한 URL에 액세스해야 합니다.

**URL** | **세부 정보**
--- | ---
login.microsoftonline.com | Active Directory를 사용하여 액세스 제어 및 ID 관리.
*.backup.windowsazure.com | 복제 데이터 전송 및 조정.
\*.hypervrecoverymanager.windowsazure.com | 마이그레이션에 사용됩니다.
\*.blob.core.windows.net | 저장소 계정에 데이터를 업로드합니다. 
dc.services.visualstudio.com | 내부 모니터링에 사용되는 앱 로그를 업로드합니다.
time.windows.com | 시스템과 전역 시간 간의 시간 동기화를 확인합니다.

### <a name="url-access-azure-government"></a>URL 액세스(Azure 정부)

Hyper-V 호스트의 복제 공급자 소프트웨어는 이러한 URL에 액세스해야 합니다.

**URL** | **세부 정보**
--- | ---
login.microsoftonline.us | Active Directory를 사용하여 액세스 제어 및 ID 관리.
backup.windowsazure.us | 복제 데이터 전송 및 조정.
*. hypervrecoverymanager.windowsazure.us | 마이그레이션에 사용됩니다.
*.blob.core.usgovcloudapi.net | 저장소 계정에 데이터를 업로드합니다.
dc.services.visualstudio.com | 내부 모니터링에 사용되는 앱 로그를 업로드합니다.
time.nist.gov | 시스템과 전역 시간 간의 시간 동기화를 확인합니다.


## <a name="hyper-v-vms"></a>Hyper-V VM

| **지원**                  | **세부 정보**               
| :----------------------------- | :------------------- |
| **운영 체제** | Azure에서 지원하는 모든 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 및 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) 운영 체제입니다. |
| **Azure에 필요한 변경 사항** | 일부 VM은 Azure에서 실행될 수 있도록 변경해야 할 수 있습니다. 마이그레이션하기 전에 수동으로 조정합니다. 관련 문서에는 이 작업을 수행하는 방법에 대한 지침이 포함되어 있습니다. |
| **리눅스 부팅**                 | /boot가 전용 파티션에 있는 경우 OS 디스크에 상주해야 하며 여러 디스크에 분산되지 않아야 합니다.<br/> /boot가 루트(/) 파티션의 일부인 경우 '/' 파티션은 OS 디스크에 있어야 하며 다른 디스크에 걸쳐 서는 안 됩니다. |
| **UEFI 부츠**                  | Azure에서 마이그레이션된 VM은 BIOS 부팅 VM으로 자동으로 변환됩니다. VM은 Windows Server 2012 이상에서만 실행되어야 합니다. OS 디스크에는 최대 5개의 파티션이 있어야 하며 OS 디스크의 크기는 300GB 미만이어야 합니다.
  |
| **디스크 크기**                  | OS 디스크의 경우 2TB, 데이터 디스크의 경우 4TB입니다.
| **디스크 번호** | VM당 최대 16개의 디스크입니다.
| **암호화된 디스크/볼륨**    | 마이그레이션에 대 한 지원 되지 않습니다. |
| **RDM/패스스루 디스크**      | 마이그레이션에 대 한 지원 되지 않습니다. |
| **공유 디스크** | 공유 디스크를 사용하는 VM은 마이그레이션에 지원되지 않습니다.
| **NFS**                        | VM의 볼륨으로 탑재된 NFS 볼륨은 복제되지 않습니다. |
| **Iscsi**                      | iSCSI 대상이 있는 VM은 마이그레이션에 지원되지 않습니다.
| **대상 디스크**                | 관리 디스크만 있는 Azure VM으로 마이그레이션할 수 있습니다. |
| **IPv6** | 지원되지 않습니다.
| **NIC 팀 구성** | 지원되지 않습니다.
| **Azure Site Recovery** | Azure 사이트 복구를 사용하여 복제할 때 VM이 활성화된 경우 Azure 마이그레이션 서버 마이그레이션을 사용하여 복제할 수 없습니다.
| **포트** | VM 복제 데이터를 전송하기 위해 HTTPS 포트 443의 아웃바운드 연결입니다.

## <a name="azure-vm-requirements"></a>Azure VM 요구 사항

Azure에 복제된 모든 온-프레미스 VM은 이 테이블에 요약된 Azure VM 요구 사항을 충족해야 합니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
운영 체제 디스크 크기 | 최대 2,048GB. | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 수 | 1 | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 수 | 16 이하. | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 크기 | 최대 4,095GB | 지원되지 않는 경우 확인이 실패합니다.
네트워크 어댑터 | 여러 어댑터가 지원됩니다. |
공유 VHD | 지원되지 않습니다. | 지원되지 않는 경우 확인이 실패합니다.
FC 디스크 | 지원되지 않습니다. | 지원되지 않는 경우 확인이 실패합니다.
BitLocker | 지원되지 않습니다. | 컴퓨터의 복제를 사용하도록 설정하기 전에 Bitlocker를 사용하지 않도록 설정해야 합니다.
VM 이름 | 1~63자 사이입니다.<br/> 문자, 숫자 및 하이픈으로 제한됩니다.<br/><br/> 컴퓨터 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. |  Site Recovery에서 컴퓨터 속성의 값을 업데이트합니다.
마이그레이션 후 연결-Windows | 마이그레이션 후 Windows를 실행하는 Azure VM에 연결하려면 다음을 수행하십시오.<br/> - 마이그레이션 전에 온-프레미스 VM에서 RDP를 사용할 수 있습니다. **공용** 프로필에 대한 TCP 및 UDP 규칙이 추가되었는지와 해당 RDP가 **Windows 방화벽** > **허용되는 앱**에서 모든 프로필에 대해 허용되는지 확인합니다.<br/> 사이트 간 VPN 액세스를 위해 RDP를 사용하도록 설정하고 **Windows 방화벽에서** -> RDP허용**앱 및** 도메인 및 **개인** 네트워크에 대한 기능을 허용합니다. 또한 운영 체제의 SAN 정책이 **OnlineAll로**설정되어 있는지 확인합니다. [자세히 알아보기](prepare-for-migration.md). |
마이그레이션 후 연결-리눅스 | SSH를 사용하여 마이그레이션한 후 Azure VM에 연결하려면 다음을 수행합니다.<br/> 온-프레미스 컴퓨터에서 마이그레이션하기 전에 보안 셸 서비스가 시작으로 설정되어 있는지, 방화벽 규칙에서 SSH 연결을 허용하는지 확인합니다.<br/> 장애 조치 후 Azure VM에서 VM을 통해 실패한 네트워크 보안 그룹 규칙및 연결된 Azure 서브넷에 대한 SSH 포트에 대한 들어오는 연결을 허용합니다. 또한 VM에 대한 공용 IP 주소를 추가합니다. |  

## <a name="next-steps"></a>다음 단계

마이그레이션을 위해 [하이퍼 V VM을 마이그레이션합니다.](tutorial-migrate-hyper-v.md)
