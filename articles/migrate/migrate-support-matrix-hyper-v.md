---
title: Hyper-v 평가 및 마이그레이션을 위한 azure 마이그레이션 지원 매트릭스
description: 설정 및 Hyper-v 평가 및 Azure Migrate 서비스를 사용 하 여 마이그레이션에 대 한 제한 사항이 요약 되어 있습니다.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2019
ms.author: raynew
ms.openlocfilehash: f6edbe19429b38d68aea1f1ecfe426c9b2d194d0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811350"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Hyper-v 평가 및 마이그레이션을 위한 지원 매트릭스

사용할 수는 [Azure Migrate 서비스](migrate-overview.md) 평가 하 고 Microsoft Azure 클라우드로 컴퓨터를 마이그레이션하려 합니다. 이 문서는 지원 설정 및 평가 하 고 온-프레미스 Hyper-v Vm을 마이그레이션에 대 한 제한 사항 요약 되어 있습니다.



## <a name="hyper-v-scenarios"></a>하이퍼-V 시나리오

Hyper-v Vm에 대 한 지원 되는 시나리오를 요약합니다.

**배포** | **세부 정보*** 
--- | --- 
**온-프레미스 Hyper-v Vm을 평가 합니다.** | [설정](tutorial-prepare-hyper-v.md) 첫 번째 평가 합니다.<br/><br/> [실행](scale-hyper-v-assessment.md) 대규모 평가 합니다.
**Hyper-v Vm을 Azure로 마이그레이션** | [사용해](tutorial-migrate-hyper-v.md) Azure로의 마이그레이션.

    

## <a name="azure-migrate-projects"></a>Azure Migrate 프로젝트

**지원** | **세부 정보**
--- | ---
Azure 사용 권한 | Azure Migrate 프로젝트를 만드는 구독의 참가자 또는 소유자 권한이 필요 합니다.
Hyper-V VM | 단일 프로젝트에서 최대 10,000 개의 Hyper-v Vm을 평가 합니다.

프로젝트 평가 한도까지 VMware Vm 및 Hyper-v Vm을 모두 포함할 수 있습니다.


## <a name="assessment-hyper-v-host-requirements"></a>평가-Hyper-v 호스트 요구 사항

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **호스트 배포**       | Hyper-v 호스트 독립 실행형 또는 클러스터에 배포. |
| **사용 권한**           | Hyper-v 호스트에서 관리자 권한이 필요합니다. |
| **호스트 운영 체제** | Windows Server 2016 또는 Windows Server 2012 R2입니다.<br/> Windows Server 2019를 실행 하는 Hyper-v 호스트에 있는 Vm을 평가할 수 없습니다. |
| **PowerShell 원격**   | 각 호스트에서 사용할 수 있어야 합니다. |
| **Hyper-v 복제본**       | 하는 경우 Hyper-v 복제본을 사용 하면, 동일한 VM 식별자를 사용 하 여 여러 Vm이 있는 두 원본 및 복제 된 Vm이 Azure Migrate를 사용 하 여 검색을 정확 하 게 생성 하 여 Azure Migrate 평가 설정 되지 않을 수 있습니다. |


## <a name="assessment-hyper-v-vm-requirements"></a>평가 Hyper-v VM 요구 사항

| **지원**                  | **세부 정보**               
| :----------------------------- | :------------------- |
| **운영 체제** | 모든 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 하 고 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Azure에서 지원 되는 운영 체제입니다. |
| **사용 권한**           | 평가 하려는 각 Hyper-v VM에 대 한 관리자 권한이 필요 합니다. |
| **Integration Services**       | [Hyper-v 통합 서비스](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) 운영 체제 정보를 캡처하려면를 평가 하는 Vm에서 실행 되어야 합니다. |
| **Azure에 대해 필요한 변경** | 일부 Vm을 Azure에서 실행할 수 있도록 변경 해야 합니다. Azure Migrate에는 다음 운영 체제에 대 한 자동으로 이러한 변경 작업:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7, 8<br/><br/> 다른 운영 체제 마이그레이션 시작 하기 전에 수동으로 조정 해야 합니다. 관련 문서를이 작업을 수행 하는 방법에 대 한 지침을 포함 합니다. |
| **Linux boot**                 | 전용 파티션에 /boot 인 경우 OS 디스크에 있어야 하 고 여러 디스크에 걸쳐 분산 하지 않습니다.<br/> /Boot 루트 (/) 파티션의 일부 이면 다음 '/' 파티션 OS 디스크에 있고 다른 디스크에 걸쳐 있지 않고 합니다. |
| **UEFI 부팅**                  | UEFI 부팅을 사용 하 여 Vm 마이그레이션에 대 한 지원 되지 않습니다. |
| **암호화 된 디스크/볼륨**    | 암호화 된 디스크/볼륨을 사용 하 여 Vm 마이그레이션에 대 한 지원 되지 않습니다. |
| **RDM/통과 디스크**      | Vm이 RDM 또는 통과 디스크에 있는 경우 이러한 디스크는 Azure에 복제할 수 없습니다. |
| **NFS**                        | Vm의 볼륨으로 탑재 하는 NFS 볼륨을 복제할 수 없습니다. |
| **대상 디스크**                | Azure Migrate 평가 관리 디스크를 사용 하 여 Azure Vm으로 마이그레이션을 권장합니다. |


## <a name="assessment-appliance-requirements"></a>평가 어플라이언스 요구 사항

평가 위해 Azure Migrate Azure Migrate에 VM 메타 데이터 및 성능 데이터를 보내고 Hyper-v Vm을 검색 하는 경량 어플라이언스를 실행 합니다. Hyper-v VM에서 실행 되는 어플라이언스 및 Azure portal에서 다운로드 하는 압축 된 Hyper-v VHD를 사용 하 여 설정 합니다. 다음 표에서 어플라이언스 요구 사항을 보여 줍니다.

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **Azure Migrate 프로젝트**  |  단일 프로젝트를 사용 하 여 어플라이언스에 연결할 수 있습니다.<br/> 단일 어플라이언스를 사용 하 여 최대 5000 개의 Hyper-v Vm을 검색할 수 있습니다.
| **Hyper-v 제한 사항**    |  Hyper-v VM으로 어플라이언스를 배포 합니다.<br/> 제공 하는 VM 어플라이언스는 Hyper-v VM 버전 5.0입니다.<br/> VM 호스트 Windows Server 2012 R2를 실행 해야 이상.<br/> 16GB RAM, 4 개의 가상 프로세서를 할당할 공간이 충분 하며 VM 어플라이언스에 대 한 1 외부 전환 합니다.<br/> 어플라이언스는 정적 또는 동적 IP 주소 및 인터넷 액세스가 필요합니다.
| **하이퍼-V 어플라이언스**      |  어플라이언스에는 Hyper-v VM으로 설정 됩니다.<br/> 다운로드를 위해 제공 하는 VHD는 Hyper-v VM 버전 5.0입니다.
| **Host**                   | 어플라이언스 VM을 실행 하는 VM 호스트 Windows Server 2012 R2를 실행 해야 합니다 이상.<br/> VM 어플라이언스에 대해 16GB RAM, 4 개의 가상 프로세서 및 외부 스위치를 두 개를 할당 하는 데 충분 한 공간이 필요 합니다.<br/> 어플라이언스는 정적 또는 동적 IP 주소 및 인터넷 액세스가 필요합니다. |
| **마이그레이션 지원**      | 컴퓨터 복제를 시작 하려면 어플라이언스에서 마이그레이션 게이트웨이가 서비스 1.18.7141.12919 해야 이상. 버전을 확인 하려면 어플라이언스 웹 앱에 로그인 합니다. |

## <a name="assessment-appliance-url-access"></a>평가 어플라이언스 URL 액세스

Vm을 평가 하기 위해 Azure Migrate 어플라이언스에 인터넷 연결이 필요 합니다.

- 어플라이언스를 배포할 때 Azure Migrate는 연결 검사를 아래 표에 요약 된 url입니다.
- URL 기반 firewall.proxy를 사용 하는 경우 프록시는 CNAME 레코드는 Url을 조회 하는 동안 수신 해결 되었는지 테이블의 Url에 대 한 액세스를 허용 합니다.
- 가로채는 프록시가 경우 기기에 프록시 서버에서 서버 인증서를 가져올 해야 합니다. 

    
**URL** | **세부 정보**  
--- | --- 
*.portal.azure.com | Azure portal 탐색
*.windows.net | Azure 구독에 로그인합니다.
*.microsoftonline.com | 서비스 통신 하는 어플라이언스에 대 한 만들기의 Azure Active Directory 응용 프로그램입니다.
management.azure.com | 서비스 통신 하는 어플라이언스에 대 한 만들기의 Azure Active Directory 응용 프로그램입니다.
dc.services.visualstudio.com | 로깅 및 모니터링 
*.vault.azure.net | 어플라이언스와 서비스 간에 통신 하는 경우 Azure Key Vault에서 비밀을 관리 합니다.


## <a name="assessment-port-requirements"></a>평가 포트 요구 사항

다음 표에서 평가 대 한 포트 요구 사항을 보여 줍니다.

**장치** | **연결**
--- | --- 
**어플라이언스** | 어플라이언스로 원격 데스크톱 연결을 허용 하도록 3389 TCP 포트에서 인바운드 연결 합니다.<br/> 인바운드 URL을 사용 하 여 어플라이언스 관리 앱을 원격으로 액세스 하려면 44368 포트로 연결: https://<appliance-ip-or-name>:44368<br/> Azure Migrate를 검색 및 성능 메타 데이터를 전송 하려면 포트 443에서 아웃 바운드 연결 합니다.
**Hyper-v 호스트/클러스터** | 인바운드 WinRM 포트 5985 (HTTP) 및 5986(https 모델 CIM (Common Information) 세션을 사용 하 여 Hyper-v Vm의 구성 및 성능 메타 데이터 끌어오기 (HTTPS)에 연결 합니다.

## <a name="migration-hyper-v-host-requirements"></a>마이그레이션-Hyper-v 호스트 요구 사항

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **호스트 배포**       | Hyper-v 호스트 독립 실행형 또는 클러스터에 배포. |
| **사용 권한**           | Hyper-v 호스트에서 관리자 권한이 필요합니다. |
| **호스트 운영 체제** | Windows Server 2019, Windows Server 2016 또는 Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>마이그레이션 Hyper-v VM 요구 사항

| **지원**                  | **세부 정보**               
| :----------------------------- | :------------------- |
| **운영 체제** | 모든 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 하 고 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Azure에서 지원 되는 운영 체제입니다. |
| **사용 권한**           | 평가 하려는 각 Hyper-v VM에 대 한 관리자 권한이 필요 합니다. |
| **Integration Services**       | [Hyper-v 통합 서비스](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) 운영 체제 정보를 캡처하려면를 평가 하는 Vm에서 실행 되어야 합니다. |
| **Azure에 대해 필요한 변경** | 일부 Vm을 Azure에서 실행할 수 있도록 변경 해야 합니다. Azure Migrate에는 다음 운영 체제에 대 한 자동으로 이러한 변경 작업:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7, 8<br/><br/> 다른 운영 체제 마이그레이션 시작 하기 전에 수동으로 조정 해야 합니다. 관련 문서를이 작업을 수행 하는 방법에 대 한 지침을 포함 합니다. |
| **Linux boot**                 | 전용 파티션에 /boot 인 경우 OS 디스크에 있어야 하 고 여러 디스크에 걸쳐 분산 하지 않습니다.<br/> /Boot 루트 (/) 파티션의 일부 이면 다음 '/' 파티션 OS 디스크에 있고 다른 디스크에 걸쳐 있지 않고 합니다. |
| **UEFI 부팅**                  | UEFI 부팅을 사용 하 여 Vm 마이그레이션에 대 한 지원 되지 않습니다. |
| **암호화 된 디스크/볼륨**    | 암호화 된 디스크/볼륨을 사용 하 여 Vm 마이그레이션에 대 한 지원 되지 않습니다. |
| **RDM/통과 디스크**      | Vm이 RDM 또는 통과 디스크에 있는 경우 이러한 디스크는 Azure에 복제할 수 없습니다. |
| **NFS**                        | Vm의 볼륨으로 탑재 하는 NFS 볼륨을 복제할 수 없습니다. |
| **대상 디스크**                | 관리 되는 디스크만 사용 하 여 Azure Vm에 마이그레이션할 수 있습니다. |




## <a name="migration-hyper-v-host-url-access"></a>마이그레이션-Hyper-v 호스트 URL 액세스

다음 표에서 Hyper-v 호스트에 대 한 URL 액세스 요구 사항을 보여 줍니다.

**URL** | **세부 정보**  
--- | ---
login.microsoftonline.com | 액세스 제어 및 id 관리 Active Directory를 사용 합니다.
*.backup.windowsazure.com | 복제 데이터 전송 및 조정 합니다.
\*.hypervrecoverymanager.windowsazure.com | Azure Migrate 서비스 Url에 연결 합니다.
\*.blob.core.windows.net | 저장소 계정에 데이터를 업로드 합니다.
dc.services.visualstudio.com | 내부 모니터링에 사용 되는 앱 로그를 업로드 합니다.
time.windows.com | 시스템 시간과 글로벌 시간 간의 시간 동기화를 확인합니다.

## <a name="migration-port-access"></a>마이그레이션 포트 액세스

다음 표에서 VM 마이그레이션에 대 한 Hyper-v 호스트 및 Vm에서 포트 요구 사항을 보여 줍니다.

**장치** | **연결**
--- | --- 
Hyper-v 호스트/v m | 아웃 바운드 연결에 HTTPS 포트 443 Azure Migrate에 VM 복제 데이터를 보낼 수 있습니다.

  
## <a name="migration-vm-disk-support"></a>마이그레이션 VM 디스크 지원 

**지원** | **세부 정보**
--- | ---
마이그레이션된 디스크 | Vm에서 Azure managed disks (HHD 표준, 프리미엄 SSD)로 마이그레이션할 수 있습니다.
   

## <a name="next-steps"></a>다음 단계

[Hyper-v VM 평가 준비](tutorial-prepare-hyper-v.md) 마이그레이션에 대 한 합니다.




 
