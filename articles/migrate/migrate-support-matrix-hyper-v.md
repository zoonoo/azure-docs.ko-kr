---
title: Hyper-v 평가 및 마이그레이션에 대 한 Azure Migrate 지원 매트릭스
description: Azure Migrate 서비스를 사용 하 여 Hyper-v 평가 및 마이그레이션에 대 한 설정 및 제한 사항을 요약 합니다.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: raynew
ms.openlocfilehash: 97972be655a6a03cfe29d8589a144d1e027b86fc
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376089"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Hyper-V 평가 및 마이그레이션 지원 매트릭스

[Azure Migrate 서비스](migrate-overview.md) 를 사용 하 여 컴퓨터를 평가 하 고 Microsoft Azure 클라우드로 마이그레이션할 수 있습니다. 이 문서에서는 온-프레미스 Hyper-v Vm을 평가 하 고 마이그레이션하기 위한 지원 설정 및 제한 사항을 요약 합니다.



## <a name="hyper-v-scenarios"></a>Hyper-v 시나리오

이 표에는 Hyper-v Vm에 대해 지원 되는 시나리오가 요약 되어 있습니다.

**배포** | **대해***
--- | ---
**온-프레미스 Hyper-v Vm 평가** | 첫 번째 평가를 [설정](tutorial-prepare-hyper-v.md) 합니다.<br/><br/> 대규모 평가를 [실행](scale-hyper-v-assessment.md) 합니다.
**Hyper-V VM을 Azure로 마이그레이션** | Azure로의 마이그레이션을 [시도](tutorial-migrate-hyper-v.md) 합니다. 

System Center Virtual Machine Manager (VMM)로 관리 되는 Hyper-v 서버의 마이그레이션은 Azure Migrate Server Migration에서 지원 되지 않습니다. 

## <a name="azure-migrate-projects"></a>Azure Migrate 프로젝트

**지원** | **세부 정보**
--- | ---
Azure 권한 | Azure Migrate 프로젝트를 만들려면 구독에 대 한 참가자 또는 소유자 권한이 있어야 합니다.
Hyper-V VM | 단일 프로젝트에서 최대 35000 Hyper-v Vm을 평가 합니다. Azure 구독에 여러 프로젝트를 포함할 수 있습니다. 프로젝트에는 VMware Vm과 Hyper-v Vm이 모두 포함 될 수 있습니다 (평가 제한까지).
지리 | 여러 지역에서 Azure Migrate 프로젝트를 만들 수 있습니다. 특정 지역에 프로젝트를 만들 수 있지만 다른 대상 위치의 컴퓨터를 평가 하거나 마이그레이션할 수 있습니다. 프로젝트 지역은 검색된 메타데이터를 저장하는 데만 사용됩니다.

  **Geography** | **메타데이터 스토리지 위치**
  --- | ---
  Azure Government | US Gov 버지니아
  아시아 태평양 | 동아시아 또는 동남 아시아
  오스트레일리아 | 오스트레일리아 동부 또는 오스트레일리아 남동쪽
  캐나다 | 캐나다 중부 또는 캐나다 동부
  Europe | 북유럽 또는 유럽 서부
  인도 | 인도 중부 또는 인도 남부
  일본 |  일본 동부 또는 일본 서 부
  영국 | 영국 남부 또는 영국 서부
  미국 | 미국 중부 또는 미국 서 부 2


 > [!NOTE]
 > Azure Government에 대 한 지원은 현재 [이전 버전](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) 의 Azure Migrate 에서만 사용할 수 있습니다.


## <a name="assessment-hyper-v-host-requirements"></a>평가-Hyper-v 호스트 요구 사항

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **호스트 배포**       | Hyper-v 호스트는 독립 실행형 이거나 클러스터에 배포할 수 있습니다. |
| **사용 권한**           | Hyper-v 호스트에 대 한 관리자 권한이 필요 합니다. <br/> 또는 관리자 권한을 할당 하지 않으려는 경우 로컬 또는 도메인 사용자 계정을 만들고 이러한 그룹에 사용자를 추가 합니다. 원격 관리 사용자, Hyper-v 관리자 및 성능 모니터 사용자입니다. |
| **호스트 운영 체제** | Windows Server 2019, Windows Server 2016 또는 Windows Server 2012 R2.<br/> Windows Server 2012를 실행 하는 Hyper-v 호스트에 있는 Vm은 평가할 수 없습니다. |
| **PowerShell 원격 작업**   | 각 호스트에서를 사용 하도록 설정 해야 합니다. |
| **Hyper-v 복제본**       | Hyper-v 복제본을 사용 하는 경우 (또는 VM 식별자가 같은 vm이 여러 개 있는 경우) Azure Migrate를 사용 하 여 원래 vm과 복제 된 Vm을 모두 검색 하는 경우 Azure Migrate에서 생성 된 평가가 정확 하지 않을 수 있습니다. |


## <a name="assessment-hyper-v-vm-requirements"></a>평가-Hyper-v VM 요구 사항

| **지원**                  | **세부 정보**               
| :----------------------------- | :------------------- |
| **운영 체제** | Azure에서 지 원하는 모든 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 및 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) 운영 체제입니다. |
| **Integration Services**       | 운영 체제 정보를 캡처하기 위해 평가 하는 Vm에서 [hyper-v Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) 실행 중 이어야 합니다. |



## <a name="assessment-appliance-requirements"></a>평가-어플라이언스 요구 사항

평가를 위해 Azure Migrate 경량 어플라이언스를 실행 하 여 Hyper-v Vm을 검색 하 고 VM 메타 데이터 및 성능 데이터를 Azure Migrate 보냅니다. 어플라이언스는 Hyper-v VM에서 실행 되며, Azure Portal에서 다운로드 하는 압축 된 Hyper-v VHD를 사용 하 여 설정 합니다. 다음 표에는 어플라이언스 요구 사항이 요약 되어 있습니다.

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **어플라이언스 배포**   |  어플라이언스를 Hyper-v VM으로 배포 합니다.<br/> Azure Migrate에서 제공 하는 어플라이언스 VM은 Hyper-v VM 버전 5.0입니다.<br/> Hyper-v 호스트는 Windows Server 2012 R2 이상을 실행 해야 합니다.<br/> 호스트는 16gb RAM, 8 개 vCPUs, 80 GB의 저장소 공간 및 어플라이언스 VM에 대 한 외부 스위치를 할당 하는 데 충분 한 공간이 필요 합니다.<br/> 어플라이언스에는 정적 또는 동적 IP 주소와 인터넷 액세스가 필요 합니다.
| **Azure Migrate 프로젝트**  |  어플라이언스는 단일 프로젝트에 연결할 수 있습니다.<br/> 모든 수의 어플라이언스를 단일 프로젝트에 연결할 수 있습니다.<br/> 프로젝트에서 최대 35000 개의 Vm을 평가할 수 있습니다.
| **Hyper-V 호스트**          | 어플라이언스는 최대 300 Hyper-v 호스트에 연결할 수 있습니다.
| **조사**              | 단일 어플라이언스에서 최대 5000 Vm을 검색할 수 있습니다.
| **평가 그룹**       | 단일 그룹에 최대 35000 대의 컴퓨터를 추가할 수 있습니다.
| **평가**             | 단일 평가에서 최대 35000 Vm을 평가할 수 있습니다.



## <a name="assessment-appliance-url-access"></a>평가-어플라이언스 URL 액세스

Vm을 평가 하려면 Azure Migrate 어플라이언스에서 인터넷에 연결 해야 합니다.

- 어플라이언스를 배포 하는 경우 Azure Migrate 아래 표에 요약 된 Url에 대 한 연결 확인을 수행 합니다.
- URL 기반 프록시를 사용 하는 경우 테이블의 Url에 대 한 액세스를 허용 하 여 프록시가 Url을 조회 하는 동안 수신 된 CNAME 레코드를 확인 하는지 확인 합니다.
- 가로채기 프록시가 있는 경우 프록시 서버에서 어플라이언스로 서버 인증서를 가져와야 할 수 있습니다.


**URL** | **세부 정보**  
--- | ---
*.portal.azure.com | Azure Portal 탐색
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Azure 구독에 로그인합니다.
*.microsoftonline.com <br/> *.microsoftonline-p.com | 어플라이언스에서 서비스 통신에 대 한 Azure Active Directory 응용 프로그램 만들기
management.azure.com | 어플라이언스에서 서비스 통신에 대 한 Azure Active Directory 응용 프로그램 만들기
dc.services.visualstudio.com | 로깅 및 모니터링
*.vault.azure.net | 어플라이언스와 서비스 간에 통신 하는 경우 Azure Key Vault의 비밀을 관리 합니다.
aka.ms/* | 즉, 링크에 대 한 액세스를 허용 합니다.
https://download.microsoft.com/download/* | Microsoft 다운로드 사이트에서 다운로드할 수 있습니다.



## <a name="assessment-port-requirements"></a>평가-포트 요구 사항

다음 표에는 평가를 위한 포트 요구 사항이 요약 되어 있습니다.

**장치** | **연결**
--- | ---
**기기가** | 어플라이언스에 대 한 원격 데스크톱 연결을 허용 하기 위해 TCP 포트 3389에서 인바운드 연결<br/> URL을 사용 하 여 어플라이언스 관리 앱에 원격으로 액세스 하기 위해 포트 44368에서 인바운드 연결:``` https://<appliance-ip-or-name>:44368 ```<br/> Azure Migrate에 검색 및 성능 메타 데이터를 보내기 위한 포트 443, 5671 및 5672의 아웃 바운드 연결
**Hyper-v 호스트/클러스터** | CIM(Common Information Model) (CIM) 세션을 사용 하 여 Hyper-v Vm의 구성과 성능 메타 데이터를 끌어오기 위해 WinRM 포트 5985 (HTTP) 및 5986 (HTTPS)에서 인바운드 연결

## <a name="migration-limitations"></a>마이그레이션-제한 사항
복제를 위해 한 번에 최대 10 개의 Vm을 선택할 수 있습니다. 더 많은 컴퓨터를 마이그레이션하려는 경우 10 그룹으로 복제 합니다.

## <a name="migration-hyper-v-host-requirements"></a>마이그레이션-Hyper-v 호스트 요구 사항

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **호스트 배포**       | Hyper-v 호스트는 독립 실행형 이거나 클러스터에 배포할 수 있습니다. |
| **사용 권한**           | Hyper-v 호스트에 대 한 관리자 권한이 필요 합니다. |
| **호스트 운영 체제** | Windows Server 2019, Windows Server 2016 또는 Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>마이그레이션-Hyper-v VM 요구 사항

| **지원**                  | **세부 정보**               
| :----------------------------- | :------------------- |
| **운영 체제** | Azure에서 지 원하는 모든 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 및 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) 운영 체제입니다. |
| **사용 권한**           | 평가 하려는 각 Hyper-v VM에 대 한 관리자 권한이 필요 합니다. |
| **Integration Services**       | 운영 체제 정보를 캡처하기 위해 평가 하는 Vm에서 [hyper-v Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) 실행 중 이어야 합니다. |
| **Azure에 대 한 필수 변경 내용** | 일부 Vm은 Azure에서 실행할 수 있도록 변경 해야 할 수 있습니다. Azure Migrate는 다음 운영 체제에 대해 이러한 변경을 자동으로 수행 합니다.<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> -SUSE Linux Enterprise Server 12 SP1 이상<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7, 8<br/><br/> 다른 운영 체제의 경우 마이그레이션하기 전에 수동으로 조정 해야 합니다. 관련 문서에는이 작업을 수행 하는 방법에 대 한 지침이 포함 되어 있습니다. |
| **Linux 부팅**                 | /Boot는 전용 파티션에 있는 경우 OS 디스크에 상주해 야 하며 여러 디스크에 분산 되 면 안 됩니다.<br/> /Boot가 루트 (/) 파티션의 일부인 경우 '/' 파티션은 OS 디스크에 있어야 하며 다른 디스크에 걸쳐 있지 않아야 합니다. |
| **UEFI 부팅**                  | UEFI 부팅이 포함 된 Vm은 마이그레이션을 지원 하지 않습니다.  |
| **디스크 크기**                  | OS 디스크의 경우 2tb, 데이터 디스크의 경우 4tb
| **디스크 번호** | VM 당 최대 16 개의 디스크
| **암호화 된 디스크/볼륨**    | 마이그레이션에 지원 되지 않습니다. |
| **RDM/통과 디스크**      | 마이그레이션에 지원 되지 않습니다. |
| **공유 디스크** | 공유 디스크를 사용 하는 Vm은 마이그레이션을 지원 하지 않습니다.
| **NFS**                        | Vm에 볼륨으로 탑재 된 NFS 볼륨이 복제 되지 않습니다. |
| **ISCSI**                      | ISCSI 대상을 사용 하는 Vm은 마이그레이션을 지원 하지 않습니다.
| **대상 디스크**                | 관리 디스크를 사용 하 여 Azure Vm으로 마이그레이션할 수 있습니다. |
| **IPv6** | 지원되지 않습니다.
| **NIC 팀** | 지원되지 않습니다.
| **Azure Site Recovery** | Azure Site Recovery를 사용 하 여 복제에 대해 VM을 사용 하도록 설정한 경우 Azure Migrate 서버 마이그레이션을 사용 하 여 복제할 수 없습니다.





## <a name="migration-hyper-v-host-url-access"></a>마이그레이션-Hyper-v 호스트 URL 액세스

다음 표에는 Hyper-v 호스트에 대 한 URL 액세스 요구 사항이 요약 되어 있습니다.

**URL** | **세부 정보**  
--- | ---
login.microsoftonline.com | Active Directory를 사용 하 여 액세스 제어 및 id 관리.
*.backup.windowsazure.com | 복제 데이터 전송 및 조정.
\*.hypervrecoverymanager.windowsazure.com | Azure Migrate 서비스 Url에 연결 합니다.
\*.blob.core.windows.net | 저장소 계정에 데이터를 업로드 합니다.
dc.services.visualstudio.com | 내부 모니터링에 사용 되는 앱 로그를 업로드 합니다.
time.windows.com | 시스템 시간과 글로벌 시간 간의 시간 동기화를 확인 합니다.

## <a name="migration-port-access"></a>마이그레이션-포트 액세스

다음 표에는 VM 마이그레이션을 위한 Hyper-v 호스트 및 Vm에 대 한 포트 요구 사항이 요약 되어 있습니다.

**장치** | **연결**
--- | ---
Hyper-v 호스트/V m | Azure Migrate로 VM 복제 데이터를 보내기 위한 HTTPS 포트 443의 아웃 바운드 연결




## <a name="next-steps"></a>다음 단계

마이그레이션을 위한 [HYPER-V VM 평가를 준비](tutorial-prepare-hyper-v.md) 합니다.
