---
title: 화성 에이전트에 대한 지원 매트릭스
description: 이 문서에서는 Microsoft Azure 복구 서비스(MARS) 에이전트를 실행 하는 컴퓨터를 백업할 때 Azure 백업 지원을 요약 합니다.
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 6085bc647c06b5907282460a2d8706b8549e1bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247866"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>MARS(Microsoft Azure Recovery Services) 에이전트를 통한 백업 매트릭스 지원

Azure Backup [서비스를](backup-overview.md) 사용하여 온-프레미스 컴퓨터 및 앱을 백업하고 Azure 가상 시스템(VM)을 백업할 수 있습니다. 이 문서에서는 Microsoft Azure 복구 서비스(MARS) 에이전트를 사용하여 컴퓨터를 백업할 때 지원 설정 및 제한 사항에 대해 요약합니다.

## <a name="the-mars-agent"></a>화성 요원

Azure Backup은 MARS 에이전트를 사용하여 온-프레미스 컴퓨터 및 Azure VM에서 Azure의 백업 복구 서비스 자격 증명 모음으로 데이터를 백업합니다. MARS 에이전트는 다음을 수행할 수 있습니다.

- 온-프레미스 Windows 컴퓨터에서 실행하여 Azure의 백업 복구 서비스 자격 증명 모음에 직접 백업할 수 있습니다.
- Windows VM에서 실행하여 볼트에 직접 백업할 수 있습니다.
- Microsoft Azure 백업 서버(MABS) 또는 시스템 센터 데이터 보호 관리자(DPM) 서버에서 실행합니다. 이 시나리오에서는 컴퓨터와 워크로드를 MABS 또는 DPM 서버로 백업합니다. 그런 다음 MARS 에이전트는 이 서버를 Azure의 볼트에 백업합니다.

> [!NOTE]
>Azure Backup은 일광 절약 시간제(DST)에 대한 시계 자동 조정을 지원하지 않습니다. 일광 절약 시간을 고려하여 실제 시간과 예약된 백업 시간 간의 불일치를 방지하기 위해 정책을 수정합니다.

백업 옵션은 에이전트가 설치된 위치에 따라 다릅니다. 자세한 내용은 [MARS 에이전트를 사용하는 Azure 백업 아키텍처를](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)참조하십시오. MABS 및 DPM 백업 아키텍처에 대한 자세한 내용은 [DPM 또는 MABS로 백업을](backup-architecture.md#architecture-back-up-to-dpmmabs)참조하십시오. 또한 백업 아키텍처에 대한 [요구 사항을](backup-support-matrix-mabs-dpm.md) 참조하십시오.

**설치** | **세부 정보**
--- | ---
최신 MARS 에이전트 다운로드 | 자격 증명 모음에서 최신 버전의 에이전트를 다운로드하거나 [직접 다운로드](https://aka.ms/azurebackup_agent)할 수 있습니다.
머신에 직접 설치 | MARS 에이전트를 온-프레미스 Windows 서버 또는 [지원되는 운영 체제를](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems)실행 중인 Windows VM에 직접 설치할 수 있습니다.
백업 서버에 설치 | Azure에 백업하도록 DPM 또는 MABS를 설정할 때 MARS 에이전트를 서버에 다운로드하여 설치합니다. 백업 서버 지원 매트릭스에서 [지원되는 운영 체제에](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) 에이전트를 설치할 수 있습니다.

> [!NOTE]
> 기본적으로 백업에 사용하도록 설정된 Azure VM에는 Azure 백업 확장 설치가 있습니다. 이 확장은 전체 VM을 백업합니다. 전체 VM이 아닌 특정 폴더와 파일을 백업하려면 MARS 에이전트를 확장과 함께 Azure VM에 설치하고 실행할 수 있습니다.
> Azure VM에서 MARS 에이전트를 실행하면 VM의 임시 저장소에 있는 파일이나 폴더가 백업됩니다. 파일 또는 폴더가 임시 저장소에서 제거되거나 임시 저장소가 제거된 경우 백업이 실패합니다.

## <a name="cache-folder-support"></a>캐시 폴더 지원

MARS 에이전트를 사용하여 데이터를 백업하면 에이전트는 데이터의 스냅숏을 만들고 Azure로 데이터를 보내기 전에 로컬 캐시 폴더에 저장합니다. 캐시 (스크래치) 폴더에는 몇 가지 요구 사항이 있습니다.

**캐시** | **세부 정보**
--- | ---
크기 |  캐시 폴더의 여유 공간은 백업 데이터의 전체 크기의 5~10% 이상이어야 합니다.
위치 | 캐시 폴더는 백업중인 컴퓨터에 로컬로 저장되어야 하며 온라인 이어야 합니다. 캐시 폴더는 네트워크 공유, 이동식 미디어 또는 오프라인 볼륨에 없어야 합니다.
폴더 | 캐시 폴더는 중복 제거된 볼륨이나 압축된 폴더, 희소성 또는 희소한 폴더에서 암호화해서는 안 됩니다.
위치 변경 | 백업 엔진()을`net stop bengine`중지하고 캐시 폴더를 새 드라이브에 복사하여 캐시 위치를 변경할 수 있습니다. 새 드라이브에 충분한 공간이 있는지 확인합니다. 그런 다음 **HKLM\SOFTWARE\Microsoft\Windows Azure** 백업(구성/스크래치 위치 및 **구성/클라우드백업제공자/스크래치 위치)에서**두 개의 레지스트리 항목을 새 위치로 업데이트하고 엔진을 다시 시작합니다.**Config/ScratchLocation**

## <a name="networking-and-access-support"></a>네트워킹 및 액세스 지원

### <a name="url-and-ip-access"></a>URL 및 IP 액세스

MARS 에이전트에서 액세스해야 하는 URL은 다음과 같습니다.

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net

그리고 이러한 IP 주소:

- 20.190.128.0/18
- 40.126.0.0/18

위에 나열된 모든 URL 및 IP 주소에 대한 액세스는 포트 443의 HTTPS 프로토콜을 사용합니다.

### <a name="azure-expressroute-support"></a>Azure 익스프레스루트 지원

공용 피어링(이전 회로에서 사용 가능) 및 Microsoft 피어링을 사용하여 Azure ExpressRoute를 통해 데이터를 백업할 수 있습니다. 개인 피어링을 통해 백업은 지원되지 않습니다.

공용 피어링: 다음 도메인/주소에 대한 액세스 확인:

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

Microsoft 피어링을 사용하면 다음 서비스/지역 및 관련 커뮤니티 값을 선택하십시오.

- Azure Active Directory (12076:5060)
- Microsoft Azure 지역(복구 서비스 볼트의 위치에 따라 다)
- Azure 저장소(복구 서비스 자격 증명 모음의 위치에 따라 다릅니다)

자세한 내용은 [ExpressRoute 라우팅 요구 사항을](https://docs.microsoft.com/azure/expressroute/expressroute-routing)참조하십시오.

>[!NOTE]
>새 회로의 경우 공용 피어링이 더 이상 사용되지 않습니다.

### <a name="throttling-support"></a>제한 지원

**기능** | **세부 정보**
--- | ---
대역폭 제어 | 지원됩니다. MARS 에이전트에서 **속성 변경을** 사용하여 대역폭을 조정합니다.
네트워크 제한 | Windows Server 2008 R2, Windows Server 2008 SP2 또는 Windows 7을 실행하는 백업 된 컴퓨터에서는 사용할 수 없습니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

>[!NOTE]
> MARS 에이전트는 Windows 서버 코어 SCO를 지원하지 않습니다.

MARS 에이전트를 사용하여 아래 실행 중인 운영 체제에서 Azure에 직접 백업할 수 있습니다.

1. 온-프레미스 윈도우 서버
2. Windows가 실행되는 Azure VM

운영 체제는 64비트여야 하며 최신 서비스 팩 및 업데이트를 실행해야 합니다. 다음 표에는 이러한 운영 체제가 요약되어 있습니다.

**운영 체제** | **파일/폴더** | **시스템 상태** | **소프트웨어/모듈 요구 사항**
--- | --- | --- | ---
Windows 10(Enterprise, Pro, Home) | yes | 예 |  소프트웨어/모듈 요구 사항에 대한 해당 서버 버전 확인
Windows 8.1(Enterprise, Pro)| yes |예 | 소프트웨어/모듈 요구 사항에 대한 해당 서버 버전 확인
Windows 8(Enterprise, Pro) | yes | 예 | 소프트웨어/모듈 요구 사항에 대한 해당 서버 버전 확인
Windows Server 2016(Standard, Datacenter, Essentials) | yes | yes | - .NET 4.5 <br> - 윈도우 파워 쉘 <br> - 최신 호환 마이크로 소프트 VC ++ 재배포 <br> - 마이크로 소프트 관리 콘솔 (MMC) 3.0
Windows Server 2012 R2(Standard, Datacenter, Foundation, Essentials) | yes | yes | - .NET 4.5 <br> - 윈도우 파워 쉘 <br> - 최신 호환 마이크로 소프트 VC ++ 재배포 <br> - 마이크로 소프트 관리 콘솔 (MMC) 3.0
Windows Server 2012(Standard, Datacenter, Foundation) | yes | yes |- .NET 4.5 <br> -Windows PowerShell <br> - 최신 호환 마이크로 소프트 VC ++ 재배포 <br> - 마이크로 소프트 관리 콘솔 (MMC) 3.0 <br> - 배포 이미지 서비스 및 관리 (DISM.exe)
Windows 저장소 서버 2016/2012 R2/2012(표준, 작업 그룹) | yes | 예 | - .NET 4.5 <br> - 윈도우 파워 쉘 <br> - 최신 호환 마이크로 소프트 VC ++ 재배포 <br> - 마이크로 소프트 관리 콘솔 (MMC) 3.0
Windows Server 2019(Standard, Datacenter, Essentials) | yes | yes | - .NET 4.5 <br> - 윈도우 파워 쉘 <br> - 최신 호환 마이크로 소프트 VC ++ 재배포 <br> - 마이크로 소프트 관리 콘솔 (MMC) 3.0

자세한 내용은 [지원되는 MABS 및 DPM 운영 체제를](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)참조하십시오.

### <a name="operating-systems-at-end-of-support"></a>지원 종료 시 운영 체제

다음 운영 체제는 지원이 끝나도 계속 보호상태를 유지하기 위해 운영 체제를 업그레이드하는 것이 좋습니다.

기존 약정으로 인해 운영 체제를 업그레이드할 수 있는 경우 Windows 서버를 Azure VM으로 마이그레이션하고 Azure VM 백업을 활용하여 보호 상태를 계속 유지하는 것이 좋습니다. Windows 서버 마이그레이션에 대한 자세한 내용은 [여기의 마이그레이션 페이지를](https://azure.microsoft.com/migration/windows-server/) 참조하십시오.

운영 체제를 업그레이드하거나 Azure로 마이그레이션할 수 없는 온-프레미스 또는 호스팅된 환경의 경우 컴퓨터가 보호되고 지원되는 상태를 계속 유지하도록 확장 보안 업데이트를 활성화합니다. 특정 버전만 확장 보안 업데이트를 받을 수 있습니다. 자세한 내용은 [FAQ 페이지를](https://www.microsoft.com/cloud-platform/extended-security-updates) 참조하십시오.

| **운영 체제**                                       | **파일/폴더** | **시스템 상태** | **소프트웨어/모듈 요구 사항**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| 윈도우 7 (궁극적 인, 기업, 프로, 홈 프리미엄 / 기본, 스타터) | yes               | 예                 | 소프트웨어/모듈 요구 사항에 대한 해당 서버 버전 확인 |
| Windows 서버 2008 R2(표준, 엔터프라이즈, 데이터 센터, 기반) | yes               | yes                | - .NET 3.5, .NET 4.5 <br>  - 윈도우 파워 쉘 <br>  - 호환 마이크로 소프트 VC ++ 재배포 <br>  - 마이크로 소프트 관리 콘솔 (MMC) 3.0 <br>  - 배포 이미지 서비스 및 관리 (DISM.exe) |
| Windows 서버 2008 SP2(표준, 데이터 센터, 기초)  | yes               | 예                 | - .NET 3.5, .NET 4.5 <br>  - 윈도우 파워 쉘 <br>  - 호환 마이크로 소프트 VC ++ 재배포 <br>  - 마이크로 소프트 관리 콘솔 (MMC) 3.0 <br>  - 배포 이미지 서비스 및 관리 (DISM.exe) <br>  - 가상 서버 2005 기본 + KBKB948515 |

## <a name="backup-limits"></a>Backup 제한

### <a name="size-limits"></a>크기 한도

Azure 백업은 백업할 수 있는 파일 또는 폴더 데이터 원본의 크기를 제한합니다. 단일 볼륨에서 백업한 항목은 이 표에 요약된 크기를 초과할 수 없습니다.

**운영 체제** | **크기 제한**
--- | ---
Windows Server 2012 이상 |54,400GB
Windows Server 2008 R2 SP1 |1,700GB
Windows Server 2008 SP2| 1,700GB
Windows 8 이상| 54,400GB
Windows 7| 1,700GB

### <a name="other-limitations"></a>기타 제한 사항

- MARS는 이름이 같은 여러 컴퓨터의 보호를 단일 볼트에 지원하지 않습니다.

## <a name="supported-file-types-for-backup"></a>백업에 지원되는 파일 형식

**유형** | **지원**
--- | ---
암호화<sup>*</sup>| 지원됩니다.
Compressed | 지원됩니다.
스파스 | 지원됩니다.
압축 및 스파스 |지원됩니다.
하드 링크| 지원되지 않습니다. 생략.
재분석 지점| 지원되지 않습니다. 생략.
암호화 및 스파스 |지원되지 않습니다. 생략.
압축된 스트림| 지원되지 않습니다. 생략.
스파스 스트림| 지원되지 않습니다. 생략.
OneDrive(동기화된 파일은 희소스트림)| 지원되지 않습니다.
DFS 복제가 활성화된 폴더 | 지원되지 않습니다.

\*MARS 에이전트가 암호화된 파일에 액세스하는 데 필요한 인증서에 액세스할 수 있는지 확인합니다. 액세스할 수 없는 파일은 건너뜁니다.

## <a name="supported-drives-or-volumes-for-backup"></a>백업을 위해 지원되는 드라이브 또는 볼륨

**드라이브/볼륨** | **지원** | **세부 정보**
--- | --- | ---
읽기 전용 볼륨| 지원되지 않음 | 볼륨 복사 그림자 서비스(VSS)는 볼륨을 쓰기 할 수 있는 경우에만 작동합니다.
오프라인 볼륨| 지원되지 않음 |VSS는 볼륨이 온라인 인 경우에만 작동합니다.
네트워크 공유| 지원되지 않음 |볼륨은 서버에서 로컬이어야 합니다.
비트로커 잠금 볼륨| 지원되지 않음 |백업을 시작하기 전에 볼륨을 잠금 해제해야 합니다.
파일 시스템 식별| 지원되지 않음 |NTFS만 지원됩니다.
이동식 미디어| 지원되지 않음 |모든 백업 항목 원본에는 *고정* 된 상태가 있어야 합니다.
중복 제거된 드라이브 | 지원됨 | Azure Backup에서 중복 제거된 데이터를 일반 데이터로 변환합니다. 데이터를 최적화, 암호화, 저장 및 볼트로 보냅니다.

## <a name="support-for-initial-offline-backup"></a>오프라인 초기 백업 지원

Azure Backup은 디스크를 사용하여 초기 백업 데이터를 Azure로 전송하는 *오프라인 시드를* 지원합니다. 이 지원은 초기 백업이 테라바이트(TB)의 크기 범위에 있을 가능성이 있는 경우에 유용합니다. 오프라인 백업이 지원되는 대상은 다음과 같습니다.

- MARS 에이전트를 실행하는 온-프레미스 컴퓨터에서 파일 및 폴더를 직접 백업합니다.
- DPM 서버 또는 MABS에서 파일 및 워크로드 백업

오프라인 백업은 시스템 상태 파일에 사용할 수 없습니다.

## <a name="support-for-data-restoration"></a>데이터 복원 지원

Azure Backup의 [인스턴트 복원](backup-instant-restore-capability.md) 기능을 사용하여 데이터를 볼트에 복사하기 전에 복원할 수 있습니다. 백업하는 컴퓨터가 .NET Framework 4.5.2 이상을 실행해야 합니다.

이전 버전의 운영 체제를 실행 중인 대상 컴퓨터로 백업을 복원할 수 없습니다. 예를 들어 Windows 7을 실행하는 컴퓨터에서 가져온 백업은 Windows 8 이상에서 복원할 수 있습니다. 그러나 Windows 8을 실행하는 컴퓨터에서 가져온 백업은 Windows 7을 실행하는 컴퓨터에서 복원 할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [MARS 에이전트를 사용하는 백업 아키텍처에](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)대해 자세히 알아봅니다.
- [MABS 또는 DPM 서버에서 MARS 에이전트를 실행할](backup-support-matrix-mabs-dpm.md)때 지원되는 작업에 대해 알아봅니다.
