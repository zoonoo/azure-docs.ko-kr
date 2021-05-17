---
title: MARS 에이전트 지원 매트릭스
description: 이 문서에는 MARS(Microsoft Azure Recovery Services) 에이전트를 실행하는 컴퓨터를 백업할 때의 Azure Backup 지원이 요약되어 있습니다.
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 2170440b7b47861b75801b8dbd334686b4cabc8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98985617"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>MARS(Microsoft Azure Recovery Services) 에이전트를 통한 백업 매트릭스 지원

[Azure Backup 서비스](backup-overview.md)를 사용하여 온-프레미스 컴퓨터 및 워크로드, Azure VM(가상 머신)을 백업할 수 있습니다. 이 문서에는 MARS(Microsoft Azure Recovery Services) 에이전트를 사용하여 컴퓨터를 백업할 때의 지원 설정 및 제한 사항이 요약되어 있습니다.

## <a name="the-mars-agent"></a>MARS 에이전트

Azure Backup은 MARS 에이전트를 사용하여 온-프레미스 컴퓨터와 Azure VM의 데이터를 Azure의 백업 Recovery Services 자격 증명 모음에 백업합니다. MARS 에이전트는 다음을 수행할 수 있습니다.

- 온-프레미스 Windows 컴퓨터에서 실행되어 Azure의 백업 Recovery Services 자격 증명 모음에 직접 백업할 수 있습니다.
- Windows VM에서 실행되어 자격 증명 모음에 직접 백업할 수 있습니다.
- MABS(Microsoft Azure Backup Server) 또는 System Center DPM(Data Protection Manager) 서버에서 실행됩니다. 이 시나리오에서 컴퓨터와 워크로드는 MABS 또는 DPM 서버에 백업됩니다. 그러면 MARS 에이전트가 이 서버를 Azure의 자격 증명 모음에 백업합니다.

> [!NOTE]
>Azure Backup은 DST(일광 절약 시간제)를 위한 시간 자동 조정을 지원하지 않습니다. 실제 시간과 예약된 백업 시간의 불일치를 방지하려면 일광 절약 시간제를 고려하도록 정책을 수정합니다.

백업 옵션은 에이전트가 설치된 위치에 따라 달라집니다. 자세한 내용은 [MARS 에이전트를 사용하는 Azure Backup 아키텍처](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)를 참조하세요. MABS 및 DPM 백업 아키텍처에 대한 자세한 내용은 [DPM 또는 MABS에 백업](backup-architecture.md#architecture-back-up-to-dpmmabs)을 참조하세요. 백업 아키텍처의 [요구 사항](backup-support-matrix-mabs-dpm.md)도 참조하세요.

**설치** | **세부 정보**
--- | ---
최신 MARS 에이전트 다운로드 | 자격 증명 모음에서 최신 버전의 에이전트를 다운로드하거나 [직접 다운로드](https://aka.ms/azurebackup_agent)할 수 있습니다.
머신에 직접 설치 | MARS 에이전트를 온-프레미스 Windows 서버 또는 [지원되는 운영 체제](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) 중 하나를 실행하는 Windows VM에 직접 설치할 수 있습니다.
백업 서버에 설치 | Azure에 백업하도록 DPM 또는 MABS를 설정할 때 MARS 에이전트를 서버에 다운로드하여 설치합니다. 백업 서버 지원 매트릭스의 [지원되는 운영 체제](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)에 에이전트를 설치할 수 있습니다.

> [!NOTE]
> 백업에 사용하도록 설정된 Azure VM에는 기본적으로 Azure Backup 확장 설치가 있습니다. 이 확장은 전체 VM을 백업합니다. 전체 VM이 아닌 특정 폴더와 파일을 백업하려면 MARS 에이전트를 확장과 함께 Azure VM에 설치하고 실행할 수 있습니다.
> Azure VM에서 MARS 에이전트를 실행하면 VM의 임시 스토리지에 있는 파일 또는 폴더가 백업됩니다. 파일 또는 폴더가 임시 스토리지에서 제거되거나 임시 스토리지가 제거되면 백업이 실패합니다.

## <a name="cache-folder-support"></a>캐시 폴더 지원

MARS 에이전트를 사용하여 데이터를 백업하면 에이전트에서 데이터의 스냅샷을 만들어 로컬 캐시 폴더에 저장한 후에 데이터를 Azure로 보냅니다. 캐시(스크래치) 폴더에는 몇 가지 요구 사항이 있습니다.

**Cache** | **세부 정보**
--- | ---
크기 |  캐시 폴더의 사용 가능한 공간은 전체 백업 데이터 크기의 5~10% 이상이어야 합니다.
Location | 캐시 폴더는 백업되는 컴퓨터에 로컬로 저장되어야 하며, 온라인 상태여야 합니다. 캐시 폴더는 네트워크 공유, 이동식 미디어 또는 오프라인 볼륨에 있으면 안 됩니다.
폴더 | 중복 제거된 볼륨 또는 압축되거나 스파스이거나 재분석 지점이 있는 폴더에서 캐시 폴더를 암호화해서는 안 됩니다.
위치 변경 | 백업 엔진(`net stop bengine`)을 중지하고 캐시 폴더를 새 드라이브에 복사하여 캐시 위치를 변경할 수 있습니다. (공간이 충분한지 확인). 그런 다음 **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** 아래의 두 레지스트리 항목(**Config/ScratchLocation** 및 **Config/CloudBackupProvider/ScratchLocation**)을 새 위치로 업데이트하고 엔진을 다시 시작합니다.

## <a name="networking-and-access-support"></a>네트워킹 및 액세스 지원

### <a name="url-and-ip-access"></a>URL 및 IP 액세스

MARS 에이전트에서 액세스해야 하는 URL은 다음과 같습니다.

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net
- `www.msftconnecttest.com`

또한 다음 IP 주소에 액세스해야 합니다.

- 20.190.128.0/18
- 40.126.0.0/18

위에 나열된 모든 URL 및 IP 주소에 대한 액세스는 포트 443에서 HTTPS 프로토콜을 사용합니다.

MARS 에이전트를 사용하여 Azure VM에서 파일 및 폴더를 백업하는 경우 Azure 가상 네트워크도 액세스를 허용하도록 구성해야 합니다. NSG(네트워크 보안 그룹)를 사용하는 경우 *AzureBackup* 서비스 태그를 사용하여 Azure Backup에 대한 아웃바운드 액세스를 허용하세요. Azure Backup 태그 외에도 Azure AD(*AzureActiveDirectory*) 및 Azure Storage(*Storage*)에 대해 유사한 [NSG 규칙](../virtual-network/network-security-groups-overview.md#service-tags)을 만들어 인증 및 데이터 전송에 대한 연결을 허용해야 합니다. 다음 단계에서는 Azure Backup 태그에 대한 규칙을 만드는 프로세스에 대해 설명합니다.

1. **모든 서비스** 에서 **네트워크 보안 그룹** 으로 이동하여 네트워크 보안 그룹을 선택합니다.
2. **설정** 아래에서 **아웃바운드 보안 규칙** 을 선택합니다.
3. **추가** 를 선택합니다. [보안 규칙 설정](../virtual-network/manage-network-security-group.md#security-rule-settings)에 설명된 대로 새 규칙을 만드는 데 필요한 세부 정보를 모두 입력합니다. **대상** 옵션이 *서비스 태그* 로 설정되고 **대상 서비스 태그** 가 *AzureBackup* 으로 설정되어 있는지 확인합니다.
4. **추가** 를 선택하여 새로 만든 아웃바운드 보안 규칙을 저장합니다.

마찬가지로, Azure Storage 및 Azure AD에 대한 NSG 아웃바운드 보안 규칙을 만들 수 있습니다. 서비스 태그에 대한 자세한 내용은 이 [문서](../virtual-network/service-tags-overview.md)를 참조하세요.

### <a name="azure-expressroute-support"></a>Azure ExpressRoute 지원

공용 피어링(이전 회로에 사용 가능) 및 Microsoft 피어링을 사용하여 Azure ExpressRoute를 통해 데이터를 백업할 수 있습니다. 개인 피어링에 대한 백업은 지원되지 않습니다.

공용 피어링 사용: 다음 도메인/주소에 대한 액세스를 확인합니다.

* URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP 주소
  * 20.190.128.0/18
  * 40.126.0.0/18

Microsoft 피어링을 사용하여 다음 서비스/지역 및 관련 커뮤니티 값을 선택합니다.

- Azure Backup(Recovery Services 자격 증명 모음의 위치에 따름)
- Azure Active Directory(12076:5060)
- Azure Storage(Recovery Services 자격 증명 모음의 위치에 따름)

자세한 내용은 [ExpressRoute 라우팅 요구 사항](../expressroute/expressroute-routing.md#bgp)을 참조하세요.

>[!NOTE]
>공용 피어링은 새 회로에 사용되지 않습니다.

### <a name="private-endpoint-support"></a>프라이빗 엔드포인트 지원

이제 프라이빗 엔드포인트를 사용하여 데이터를 서버에서 Recovery Services 자격 증명 모음으로 안전하게 백업할 수 있습니다. Azure Active Directory는 현재 프라이빗 엔드포인트를 지원하지 않으므로 Azure Active Directory에 필요한 IP 및 FQDN은 별도의 아웃바운드 액세스가 허용되어야 합니다.

MARS 에이전트를 사용하여 온-프레미스 리소스를 백업하는 경우 (백업할 리소스가 포함된) 온-프레미스 네트워크는 자격 증명 모음의 프라이빗 엔드포인트를 포함하는 Azure VNet과 피어링됩니다. 그러면 MARS 에이전트를 계속 설치하고 백업을 구성할 수 있습니다. 다만 백업을 위한 모든 통신은 피어링된 네트워크를 통해 수행되어야 합니다.

MARS 에이전트를 등록한 후에 자격 증명 모음의 프라이빗 엔드포인트를 제거하는 경우 컨테이너를 자격 증명 모음에 다시 등록해야 합니다. 보호를 중지할 필요는 없습니다.

[Azure Backup의 프라이빗 엔드포인트](private-endpoints.md)에 대해 자세히 알아보세요.

### <a name="throttling-support"></a>제한 지원

**기능** | **세부 정보**
--- | ---
대역폭 제어 | 지원됨. MARS 에이전트에서 **속성 변경** 을 사용하여 대역폭을 조정합니다.
네트워크 제한 | Windows Server 2008 R2, Windows Server 2008 SP2 또는 Windows 7을 실행하는 백업된 컴퓨터에는 사용할 수 없습니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

>[!NOTE]
> MARS 에이전트는 Windows Server Core SKU를 지원하지 않습니다.

MARS 에이전트를 사용하면 다음에서 실행되는 아래 나열된 운영 체제에서 Azure로 직접 백업할 수 있습니다.

1. 온-프레미스 Windows Server
2. Windows가 실행되는 Azure VM

운영 체제는 64비트여야 하며, 최신 서비스 팩 및 업데이트를 실행하고 있어야 합니다. 다음 표에는 이러한 운영 체제가 요약되어 있습니다.

**운영 체제** | **파일/폴더** | **시스템 상태** | **소프트웨어/모듈 요구 사항**
--- | --- | --- | ---
Windows 10(Enterprise, Pro, Home) | 예 | 예 |  소프트웨어/모듈 요구 사항에 해당하는 서버 버전 확인
Windows 8.1(Enterprise, Pro)| 예 |예 | 소프트웨어/모듈 요구 사항에 해당하는 서버 버전 확인
Windows 8(Enterprise, Pro) | 예 | 예 | 소프트웨어/모듈 요구 사항에 해당하는 서버 버전 확인
Windows Server 2016(Standard, Datacenter, Essentials) | 예 | 예 | - .NET 4.5 <br> - Windows PowerShell <br> - 호환되는 최신 Microsoft VC++ 재배포 가능 패키지 <br> - MMC(Microsoft Management Console) 3.0
Windows Server 2012 R2(Standard, Datacenter, Foundation, Essentials) | 예 | 예 | - .NET 4.5 <br> - Windows PowerShell <br> - 호환되는 최신 Microsoft VC++ 재배포 가능 패키지 <br> - MMC(Microsoft Management Console) 3.0
Windows Server 2012(Standard, Datacenter, Foundation) | 예 | 예 |- .NET 4.5 <br> -Windows PowerShell <br> - 호환되는 최신 Microsoft VC++ 재배포 가능 패키지 <br> - MMC(Microsoft Management Console) 3.0 <br> - 배포 이미지 서비스 및 관리(DISM.exe)
Windows Storage Server 2016/2012 R2/2012(Standard, Workgroup) | 예 | 예 | - .NET 4.5 <br> - Windows PowerShell <br> - 호환되는 최신 Microsoft VC++ 재배포 가능 패키지 <br> - MMC(Microsoft Management Console) 3.0
Windows Server 2019(Standard, Datacenter, Essentials) | 예 | 예 | - .NET 4.5 <br> - Windows PowerShell <br> - 호환되는 최신 Microsoft VC++ 재배포 가능 패키지 <br> - MMC(Microsoft Management Console) 3.0

자세한 내용은 [지원되는 MABS 및 DPM 운영 체제](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)를 참조하세요.

### <a name="operating-systems-at-end-of-support"></a>지원 종료 운영 체제

다음 운영 체제는 지원이 종료되며, 보호된 상태로 계속 유지하려면 운영 체제를 업그레이드하는 것이 좋습니다.

기존 약정으로 인해 운영 체제를 업그레이드할 수 없는 경우 Windows 서버를 Azure VM으로 마이그레이션하고 Azure VM 백업을 활용하여 보호된 상태를 유지하는 것이 좋습니다. Windows 서버 마이그레이션에 대한 자세한 내용은 [여기의 마이그레이션 페이지](https://azure.microsoft.com/migration/windows-server/)를 참조하세요.

온-프레미스 또는 호스트된 환경에서 운영 체제를 업그레이드하거나 Azure로 마이그레이션할 수 없는 경우 계속 보호 및 지원을 받으려면 컴퓨터의 확장된 보안 업데이트를 활성화합니다. 확장된 보안 업데이트는 특정 버전만 받을 수 있습니다. 자세한 내용은 [FAQ 페이지](https://www.microsoft.com/windows-server/extended-security-updates)를 참조하세요.

| **운영 체제**                                       | **파일/폴더** | **시스템 상태** | **소프트웨어/모듈 요구 사항**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7(Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | 예               | 예                 | 소프트웨어/모듈 요구 사항에 해당하는 서버 버전 확인 |
| Windows Server 2008 R2(Standard, Enterprise, Datacenter, Foundation) | 예               | 예                | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - 호환되는 Microsoft VC++ 재배포 가능 패키지 <br>  - MMC(Microsoft Management Console) 3.0 <br>  - 배포 이미지 서비스 및 관리(DISM.exe) |
| Windows Server 2008 SP2(Standard, Datacenter, Foundation)  | 예               | 예                 | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - 호환되는 Microsoft VC++ 재배포 가능 패키지 <br>  - MMC(Microsoft Management Console) 3.0 <br>  - 배포 이미지 서비스 및 관리(DISM.exe) <br>  - Virtual Server 2005 기본 + KB KB948515 |

## <a name="backup-limits"></a>Backup 제한

### <a name="size-limits"></a>크기 한도

Azure Backup은 백업할 수 있는 파일 또는 폴더 데이터 원본의 크기를 제한합니다. 단일 볼륨에서 백업하는 항목은 이 표에 요약된 크기를 초과할 수 없습니다.

**운영 체제** | **크기 제한**
--- | ---
Windows Server 2012 이상 |54,400GB
Windows Server 2008 R2 SP1 |1,700GB
Windows Server 2008 SP2| 1,700GB
Windows 8 이상| 54,400GB
Windows 7| 1,700GB

### <a name="minimum-retention-limits"></a>최소 보존 한도

다음은 다양한 복구 시점에 대해 설정할 수 있는 최소 보존 기간입니다.

|복구 지점 |Duration  |
|---------|---------|
|일간 복구 지점    |   7 일      |
|주간 복구 지점     |    4주     |
|월간 복구 지점    |   3개월      |
|연간 복구 지점  |      1년   |

### <a name="other-limitations"></a>기타 제한 사항

- MARS는 단일 자격 증명 모음에 대한 이름이 동일한 여러 컴퓨터 보호를 지원하지 않습니다.

## <a name="supported-file-types-for-backup"></a>백업에 지원되는 파일 형식

**유형** | **지원**
--- | ---
암호화됨<sup>*</sup>| 지원됨.
Compressed | 지원됨.
스파스 | 지원됨.
압축 및 스파스 |지원됨.
하드 링크| 지원되지 않습니다. 건너뜀.
재분석 지점| 지원되지 않습니다. 건너뜀.
암호화 및 스파스 |지원되지 않습니다. 건너뜀.
압축된 스트림| 지원되지 않습니다. 건너뜀.
스파스 스트림| 지원되지 않습니다. 건너뜀.
OneDrive(동기화된 파일은 스파스 스트림)| 지원되지 않습니다.
DFS 복제를 사용하도록 설정된 폴더 | 지원되지 않습니다.

\* MARS 에이전트가 암호화된 파일에 액세스하는 데 필요한 인증서에 액세스할 수 있는지 확인합니다. 액세스할 수 없는 파일은 건너뜁니다.

## <a name="supported-drives-or-volumes-for-backup"></a>백업에 지원되는 드라이브 또는 볼륨

**드라이브/볼륨** | **지원** | **세부 정보**
--- | --- | ---
읽기 전용 볼륨| 지원되지 않음 | VSS(볼륨 섀도 복사본 서비스)는 쓰기 가능한 볼륨에서만 사용할 수 있습니다.
오프라인 볼륨| 지원되지 않음 |VSS는 온라인 상태인 볼륨에서만 사용할 수 있습니다.
네트워크 공유| 지원되지 않음 |볼륨이 서버에서 로컬이어야 합니다.
BitLocker 잠김 볼륨| 지원되지 않음 |볼륨의 잠금을 해제해야 백업을 시작할 수 있습니다.
파일 시스템 식별| 지원되지 않음 |NTFS만 지원됩니다.
이동식 미디어| 지원되지 않음 |모든 백업 항목 원본의 상태가 *고정* 이어야 합니다.
중복 제거된 드라이브 | 지원됨 | Azure Backup에서 중복 제거된 데이터를 일반 데이터로 변환합니다. 데이터를 최적화, 암호화, 저장하고, 자격 증명 모음으로 보냅니다.

## <a name="support-for-initial-offline-backup"></a>오프라인 초기 백업 지원

Azure Backup은 디스크를 사용하여 초기 백업 데이터를 Azure로 보내는 *오프라인 시딩* 을 지원합니다. 이 지원은 초기 백업이 TB(테라바이트) 크기 범위에 있을 가능성이 높은 경우에 유용합니다. 오프라인 백업이 지원되는 대상은 다음과 같습니다.

- MARS 에이전트를 실행하는 온-프레미스 컴퓨터의 파일 및 폴더 직접 백업
- DPM 서버 또는 MABS에서 파일 및 워크로드 백업

시스템 상태 파일에는 오프라인 백업을 사용할 수 없습니다.

## <a name="support-for-data-restoration"></a>데이터 복원 지원

Azure Backup의 [즉시 복원](backup-instant-restore-capability.md) 기능을 사용하면 자격 증명 모음에 복사하기 전에 데이터를 복원할 수 있습니다. 백업하는 컴퓨터가 .NET Framework 4.5.2 이상을 실행해야 합니다.

이전 버전의 운영 체제를 실행 중인 대상 컴퓨터에는 백업을 복원할 수 없습니다. 예를 들어 Windows 7를 실행하는 컴퓨터에서 만든 백업은 Windows 8 이상에서 복원할 수 있습니다. 그러나 Windows 8을 실행하는 컴퓨터에서 만든 백업은 Windows 7을 실행하는 컴퓨터에서 복원할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [MARS 에이전트를 사용하는 백업 아키텍처](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)에 대해 자세히 알아봅니다.
- [MABS 또는 DPM 서버에서 MARS 에이전트를 실행](backup-support-matrix-mabs-dpm.md)할 때 지원되는 항목을 알아봅니다.
