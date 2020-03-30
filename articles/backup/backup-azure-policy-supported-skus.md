---
title: Azure Policy에 지원되는 VM SKU
description: 백업에서 제공하는 기본 제공 Azure 정책에 대해 지원되는 지원되는 VM SKU(게시자, 이미지 제공 및 이미지 SKU)를 설명하는 문서입니다.
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1b6a94b0f57ecfea946654c6cae38ac335335e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980115"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Azure Policy에 지원되는 VM SKU

Azure Backup은 **구독 또는 리소스 그룹 내의 지정된 위치에 있는 모든 Azure VM에**할당할 수 있는 기본 제공 정책(Azure Policy 사용)을 제공합니다. 이 정책이 지정된 범위에 할당되면 해당 범위에서 생성된 모든 새 VM이 동일한 **위치 및 구독의 기존 자격 증명 모음에**백업하도록 자동으로 구성됩니다. 아래 표에는 이 정책에서 지원하는 모든 VM SCO가 나열되어 있습니다.

### <a name="supported-vms"></a>**지원되는 VM**

**정책 이름:** 위치의 VM에서 동일한 위치에 있는 기존 중앙 볼트로 백업 구성

이미지 게시자 | 이미지 제공 | 이미지 SKU입니다.
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | 윈도우 서버 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [스몰 디스크] 윈도우 서버 2008 R2 SP (2008-R2-SP1-스몰 디스크)
MicrosoftWindowsServer | WindowsServer | Windows 서버 2012 데이터 센터(2012-데이터 센터)
MicrosoftWindowsServer | WindowsServer | [스몰 디스크] Windows 서버 2012 데이터 센터(2012-데이터 센터-스몰디스크)
MicrosoftWindowsServer | WindowsServer | Windows 서버 2012 R2 데이터 센터(2012-R2-데이터 센터)
MicrosoftWindowsServer | WindowsServer | [스몰 디스크] Windows 서버 2012 R2 데이터 센터(2012-R2-데이터 센터-스몰디스크)
MicrosoftWindowsServer | WindowsServer | Windows 서버 2016 데이터 센터(2016-데이터 센터)
MicrosoftWindowsServer | WindowsServer | Windows 서버 2016 데이터 센터 - 서버 코어(2016-데이터 센터-서버 코어)
MicrosoftWindowsServer | WindowsServer | [스몰 디스크] Windows Server 2016 데이터 센터 - 서버 코어(2016-데이터 센터-서버-코어-스몰디스크)
MicrosoftWindowsServer | WindowsServer | [스몰 디스크] Windows 서버 2016 데이터 센터(2016-데이터 센터-스몰디스크)
MicrosoftWindowsServer | WindowsServer | 컨테이너가 있는 Windows Server 2019 데이터 센터 서버 코어(2016-데이터 센터-컨테이너 포함)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 원격 데스크톱 세션 호스트 2016(2016-데이터 센터-RDSH)
MicrosoftWindowsServer | WindowsServer | Windows 서버 2019 데이터 센터(2019-데이터 센터)
MicrosoftWindowsServer | WindowsServer | Windows 서버 2019 데이터 센터 서버 코어(2019-데이터 센터-코어)
MicrosoftWindowsServer | WindowsServer | [스몰 디스크] Windows Server 2019 데이터 센터 서버 코어(2019-데이터 센터-코어-스몰디스크)
MicrosoftWindowsServer | WindowsServer | 컨테이너가 있는 Windows Server 2019 데이터 센터 서버 코어(2019-데이터 센터 코어-컨테이너 포함)
MicrosoftWindowsServer | WindowsServer | [스몰 디스크] 컨테이너가 있는 Windows Server 2019 데이터 센터 서버 코어(2019-데이터 센터-코어-컨테이너-스몰 디스크)
MicrosoftWindowsServer | WindowsServer | [스몰 디스크] Windows 서버 2019 데이터 센터(2019-데이터 센터-스몰디스크)
MicrosoftWindowsServer | WindowsServer | 컨테이너가 있는 Windows Server 2019 데이터 센터(2019-데이터 센터-컨테이너 포함)
MicrosoftWindowsServer | WindowsServer | [스몰 디스크] 컨테이너가 있는 Windows Server 2019 데이터 센터(2019-데이터 센터-컨테이너-스몰 디스크)
MicrosoftWindowsServer | WindowsServer | Windows 서버 2019 데이터 센터(zh-cn) (2019-데이터 센터-zhcn)
MicrosoftWindowsServer | 윈도우서버세미연례 | 데이터 센터 코어-1709-스몰 디스크
MicrosoftWindowsServer | 윈도우서버세미연례 | 데이터 센터 코어-1709-컨테이너-스몰 디스크
MicrosoftWindowsServer | 윈도우서버세미연례 | 데이터 센터 코어-1803-컨테이너-스몰 디스크
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 모든 이미지 SUS
MicrosoftSQLServer | SQL2016SP1-WS2016 | 모든 이미지 SUS
MicrosoftSQLServer | SQL2016-WS2016  | 모든 이미지 SUS
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | 모든 이미지 SUS
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | 모든 이미지 SUS
MicrosoftSQLServer | SQL2016-WS2012R2 | 모든 이미지 SUS
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | 모든 이미지 SUS
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | 모든 이미지 SUS
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | 모든 이미지 SUS
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | 모든 이미지 SUS
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | 모든 이미지 SUS
MicrosoftRServer | MLServer-WS2016 | 모든 이미지 SUS
마이크로소프트비주얼스튜디오 | 비주얼 스튜디오 | 모든 이미지 SUS
마이크로소프트비주얼스튜디오 | Windows | 모든 이미지 SUS
마이크로소프트다이나믹AX | Dynamics  | 사전 Req-AX7-원박스-U8
microsoft-ads | 윈도우 데이터 과학 - VM | 모든 이미지 SUS
MicrosoftWindowsDesktop | Windows-10 | 모든 이미지 SUS
RedHat | RHEL | 6.7, 6.8, 6.9, 6.10, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7
RedHat | RHEL-SAP-HANA | 6.7, 7.2, 7.3
SUSE | SLES | 12.X
SUSE | SLES-HPC | 12.X
SUSE | SLES-HPC 우선 순위 | 12.X
SUSE | SLES-SAP | 12.X
SUSE | SLES-SAP-BYOS | 12.X
SUSE | SLES 우선 순위 | 12.X
SUSE | 슬레스 바이오스 | 12.X
SUSE | SLES-SAPCAL | 12.X
SUSE | SLES 표준 | 12.X
Canonical | UbuntuServer | 14.04.0-LTS
Canonical | UbuntuServer | 14.04.1-LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3-LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5-DAILY-LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16.04-DAILY-LTS
Canonical | UbuntuServer | 16.04-LTS
Canonical | UbuntuServer | 16.04.0-LTS
Canonical | UbuntuServer | 18.04-데일리 LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6.8, 6.9, 6.10, 7.3, 7.4, 7.5, 7.6
OpenLogic | CentOS | 6.X, 7.X
OpenLogic | 센트로스-LVM | 6.X, 7.X
OpenLogic | 센토스-스리브 | 6.X, 7.X
cloudera | cloudera-centos-os | 7.X
