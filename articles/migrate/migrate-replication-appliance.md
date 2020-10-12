---
title: Azure Migrate 복제 어플라이언스
description: 에이전트 기반 VMWare 마이그레이션의 Azure Migrate 복제 어플라이언스에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: af9e45e47c2f0645d81a571161f15f7d69cfec61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532123"
---
# <a name="replication-appliance"></a>복제 어플라이언스

이 문서에서는 에이전트 기반 마이그레이션을 사용 하 여 VMware Vm, 물리적 컴퓨터 및 사설/공용 클라우드 Vm을 Azure로 마이그레이션할 때 [Azure Migrate: 서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool) 도구에서 사용 하는 복제 어플라이언스를 설명 합니다. 


## <a name="overview"></a>개요

복제 어플라이언스는 VMware Vm 또는 물리적 서버의 에이전트 기반 마이그레이션을 설정할 때 배포 됩니다. VMware VM 또는 물리적 서버로 단일 온-프레미스 컴퓨터에 배포 됩니다. 다음을 실행 합니다.

- **복제 어플라이언스**: 복제 어플라이언스는 통신을 조정 하 고 온-프레미스 VMware Vm 및 Azure로 복제 되는 물리적 서버에 대 한 데이터 복제를 관리 합니다.
- **프로세스 서버**: 복제 어플라이언스에 기본적으로 설치 되는 프로세스 서버 이며, 다음을 수행 합니다.
    - **복제 게이트웨이**: 복제 게이트웨이 역할을 합니다. 복제를 사용 하도록 설정 된 컴퓨터에서 복제 데이터를 수신 합니다. 또한 캐싱, 압축 및 암호화를 사용 하 여 복제 데이터를 최적화 하 고 Azure에 보냅니다.
    - **에이전트 설치 관리자**: 모바일 서비스의 강제 설치를 수행 합니다. 이 서비스는 마이그레이션을 위해 복제 하려는 각 온-프레미스 컴퓨터에 설치 되 고 실행 되어야 합니다.

## <a name="appliance-deployment"></a>어플라이언스 배포

**용도** | **세부 정보**
--- |  ---
**VMware VM 에이전트 기반 마이그레이션** | Azure Migrate 허브에서 OVA 템플릿을 다운로드 하 고 vCenter Server으로 가져와 어플라이언스 VM을 만듭니다.
**물리적 컴퓨터 에이전트 기반 마이그레이션** | VMware 인프라가 없거나 OVA 템플릿을 사용 하 여 VMware VM을 만들 수 없는 경우 Azure Migrate 허브에서 소프트웨어 설치 관리자를 다운로드 하 고 실행 하 여 어플라이언스 컴퓨터를 설정 합니다.

> [!NOTE]
> Azure Government에서 배포 하는 경우 설치 파일을 사용 하 여 복제 어플라이언스를 배포 합니다.

## <a name="appliance-requirements"></a>어플라이언스 요구 사항

Azure Migrate 허브에 제공 된 OVA 템플릿을 사용 하 여 복제 어플라이언스를 설정 하는 경우 어플라이언스는 Windows Server 2016를 실행 하 고 지원 요구 사항을 준수 합니다. 물리적 서버에서 복제 어플라이언스를 수동으로 설정 하는 경우 요구 사항에 부합 하는지 확인 합니다.

**구성 요소** | **요구 사항**
--- | ---
 | **VMware VM 어플라이언스**
PowerCLI | VMware VM에서 복제 어플라이언스를 실행 하는 경우에는 [Powercli 버전 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) 을 설치 해야 합니다.
NIC 유형 | VMXNET3 (기기가 VMware VM 인 경우)
 | **하드웨어 설정**
CPU 코어 | 8
RAM | 16GB
디스크 수 | 3: OS 디스크, 프로세스 서버 캐시 디스크, 보존 드라이브입니다.
사용 가능한 디스크 공간 (캐시) | 600GB
사용 가능한 디스크 공간(보존 디스크) | 600GB
**소프트웨어 설정** |
운영 체제 | Windows Server 2016 또는 Windows Server 2012 R2
라이선스 | 어플라이언스는 180 일 동안 유효한 Windows Server 2016 evaluation 라이선스와 함께 제공 됩니다.<br/><br/> 평가 기간이 곧 만료되는 경우 새 어플라이언스를 다운로드하여 배포하거나 어플라이언스 VM의 운영 체제 라이선스에 대한 정품 인증을 수행하는 것이 좋습니다.
운영 체제 로케일 | 미국 영어(en-us)
TLS | TLS 1.2를 사용 하도록 설정 해야 합니다.
.NET Framework | .NET Framework 4.6 이상이 컴퓨터에 설치 되어 있어야 합니다 (강력한 암호화를 사용 하도록 설정 됨).
MySQL | MySQL을 어플라이언스에 설치 해야 합니다.<br/> MySQL을 설치해야 합니다. 수동으로 설치 하거나 어플라이언스를 배포 하는 동안 설치할 수 Site Recovery.
다른 앱 | 복제 어플라이언스에서 다른 앱을 실행 하지 마세요.
Windows Server 역할 | 다음 역할을 사용하지 않도록 설정함: <br> - Active Directory Domain Services <br>- 인터넷 정보 서비스 <br> - Hyper-V
그룹 정책 | 다음 그룹 정책을 사용하지 않도록 설정함: <br> - 명령 프롬프트에 대한 액세스 방지 <br> - 레지스트리 편집 도구에 대한 액세스 방지 <br> - 파일 첨부를 위한 트러스트 논리 <br> - 스크립트 실행 켜기 <br> [자세한 정보](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - 기존의 기본 웹 사이트 없음 <br> - 포트 443에서 수신 대기하는 기존의 웹 사이트/애플리케이션 없음 <br>- [익명 인증](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) 사용 <br> - [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 설정 사용
**네트워크 설정** |
IP 주소 유형 | 정적
포트 | 443(컨트롤 채널 오케스트레이션)<br>9443(데이터 전송)
NIC 유형 | VMXNET3

## <a name="mysql-installation"></a>MySQL 설치 

MySQL이 복제 어플라이언스 컴퓨터에 설치 되어 있어야 합니다. 이러한 방법 중 하나를 사용 하 여 설치할 수 있습니다.

**메서드** | **세부 정보**
--- | ---
수동으로 다운로드 및 설치 | MySQL 응용 프로그램을 다운로드 하 & C:\Temp\ASRSetup 폴더에 배치한 다음 수동으로 설치 합니다.<br/> 기기를 설정 하는 경우 MySQL은 이미 설치 된 것으로 표시 됩니다.
온라인 다운로드 하지 않음 | C:\Temp\ASRSetup. 폴더에 MySQL 설치 관리자 응용 프로그램을 추가 합니다. 어플라이언스를 설치 하 고를 클릭 하 여 MySQL을 다운로드 하 고 설치 하면 추가 된 설치 관리자가 사용 됩니다.
Azure Migrate에서 다운로드 및 설치 | 어플라이언스를 설치할 때 MySQL을 입력 하 라는 메시지가 표시 되 면 **다운로드 및 설치**를 선택 합니다.

## <a name="url-access"></a>URL 액세스

복제 어플라이언스는 Azure 공용 클라우드에서 이러한 Url에 대 한 액세스 권한이 필요 합니다.

**URL** | **세부 정보**
--- | ---
\*.backup.windowsazure.com | 복제된 데이터 전송 및 조정에 사용됩니다.
\*.store.core.windows.net | 복제된 데이터 전송 및 조정에 사용됩니다.
\*.blob.core.windows.net | 복제된 데이터를 저장하는 스토리지 계정에 액세스하는 데 사용됩니다.
\*.hypervrecoverymanager.windowsazure.com | 복제 관리 작업 및 조정에 사용됩니다.
https:\//management.azure.com | 복제 관리 작업 및 조정에 사용됩니다.
*.services.visualstudio.com | 원격 분석 용도로 사용(선택 사항)
time.windows.com | 시스템 시간과 글로벌 시간 사이의 시간 동기화를 확인하는 데 사용됩니다.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | 어플라이언스 설정에 이러한 Url에 대 한 액세스 권한이 필요 합니다. Azure Active Directory에서 액세스 제어 및 ID 관리에 사용됩니다.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | MySQL 다운로드를 완료하려면 일부 지역에서는 다운로드가 CDN URL로 리디렉션될 수 있습니다. 필요한 경우 CDN URL도 허용 되는지 확인 합니다.


## <a name="azure-government-url-access"></a>Azure Government URL 액세스

복제 어플라이언스는 Azure Government의 이러한 Url에 대 한 액세스 권한이 필요 합니다.

**URL** | **세부 정보**
--- | ---
\*.backup.windowsazure.us | 복제된 데이터 전송 및 조정에 사용됩니다.
\*.store.core.windows.net | 복제된 데이터 전송 및 조정에 사용됩니다.
\*.blob.core.windows.net | 복제된 데이터를 저장하는 스토리지 계정에 액세스하는 데 사용됩니다.
\*.hypervrecoverymanager.windowsazure.us | 복제 관리 작업 및 조정에 사용됩니다.
https:\//management.usgovcloudapi.net | 복제 관리 작업 및 조정에 사용됩니다.
*.services.visualstudio.com | 원격 분석 용도로 사용(선택 사항)
time.nist.gov | 시스템 시간과 글로벌 시간 사이의 시간 동기화를 확인하는 데 사용됩니다.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | OVA로 어플라이언스를 설정 하려면 이러한 Url에 액세스 해야 합니다. Azure Active Directory 하 여 액세스 제어 및 id 관리에 사용 됩니다.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | MySQL 다운로드를 완료하려면 일부 지역에서는 다운로드가 CDN URL로 리디렉션될 수 있습니다. 필요한 경우 CDN URL도 허용 되는지 확인 합니다.

## <a name="port-access"></a>포트 액세스

**디바이스** | **연결**
--- | ---
VM | Vm에서 실행 되는 모바일 서비스는 복제 관리를 위해 HTTPS 443 인바운드 포트의 온-프레미스 복제 어플라이언스 (구성 서버)와 통신 합니다.<br/><br/> VM은 HTTPS 9443 인바운드 포트의 프로세스 서버(구성 서버 컴퓨터에서 실행)로 복제 데이터를 전송합니다. 이 포트는 수정할 수 있습니다.
복제 어플라이언스 | 복제 어플라이언스는 HTTPS 443 아웃 바운드 포트를 통해 Azure를 사용 하 여 복제를 오케스트레이션 합니다.
프로세스 서버 | 프로세스 서버는 복제 데이터를 수신 하 고, 최적화 하 고, 암호화 하 고 포트 443 아웃 바운드를 통해 Azure storage로 보냅니다.<br/> 기본적으로 프로세스 서버는 복제 어플라이언스에서 실행 됩니다.


## <a name="replication-process"></a>복제 프로세스

1. VM에 대해 복제를 사용하도록 설정하면 지정된 복제 정책을 사용하여 Azure Storage에 초기 복제가 시작됩니다. 
2. 트래픽은 인터넷을 통해 Azure Storage 공용 엔드포인트에 복제됩니다. 온-프레미스 사이트에서 Azure로의 사이트 간 VPN(가상 사설망)을 통한 트래픽 복제는 지원되지 않습니다.
3. 초기 복제가 완료되면 델타 복제가 시작됩니다. 컴퓨터에 대 한 추적 된 변경 내용이 기록 됩니다.
4. 다음과 같이 통신이 발생합니다.
    - Vm은 복제 관리를 위해 HTTPS 443 인바운드 포트의 복제 어플라이언스와 통신 합니다.
    - 복제 어플라이언스는 HTTPS 443 아웃 바운드 포트를 통해 Azure를 사용 하 여 복제를 오케스트레이션 합니다.
    - Vm은 복제 데이터를 HTTPS 9443 인바운드 포트에서 프로세스 서버 (복제 어플라이언스에서 실행)로 보냅니다. 이 포트는 수정할 수 있습니다.
    - 프로세스 서버는 복제 데이터를 수신 하 고, 최적화 하 고, 암호화 하 고 포트 443 아웃 바운드를 통해 Azure storage로 보냅니다.
5. 복제 데이터 로그는 먼저 Azure의 캐시 저장소 계정에 저장 됩니다. 이러한 로그는 처리 되 고 데이터는 Azure 관리 디스크에 저장 됩니다.

![다이어그램은 복제 프로세스의 아키텍처를 보여 줍니다.](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>어플라이언스 업그레이드

어플라이언스는 Azure Migrate 허브에서 수동으로 업그레이드 됩니다. 항상 최신 버전을 실행 하는 것이 좋습니다.

1. Azure Migrate > 서버 > Azure Migrate: 서버 평가, 인프라 서버에서 **구성 서버**를 클릭 합니다.
2. **구성 서버**에서 새 버전의 복제 어플라이언스를 사용할 수 있는 경우 **에이전트 버전** 에 링크가 표시 됩니다. 
3. 설치 관리자를 복제 어플라이언스 컴퓨터에 다운로드 하 고 업그레이드를 설치 합니다. 설치 관리자가 어플라이언스에서 현재 실행 중인 버전을 검색 합니다.
 
## <a name="next-steps"></a>다음 단계

- 에이전트 기반 VMware VM 마이그레이션을 위한 복제 어플라이언스를 설정 하 [는 방법에 대해 알아봅니다](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) .
- 물리적 서버에 대 한 복제 어플라이언스를 설정 하 [는 방법을 알아봅니다](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) .
