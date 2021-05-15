---
title: Azure Policy에 지원되는 VM SKU
description: Backup에서 제공하는 기본 제공 Azure 정책에 지원되는 VM SKU(게시자, 이미지 제공, 이미지 SKU)를 설명하는 문서
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 4658d1beffe707682e173491edea1eac0db9c811
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "82183622"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Azure Policy에 지원되는 VM SKU

Azure Backup은 **구독 또는 리소스 그룹 내의 지정된 위치에 있는 모든 Azure VM** 에 할당될 수 있는 기본 제공 정책(Azure Policy 사용)을 제공합니다. 해당 정책이 지정된 범위에 할당되면 해당 범위에서 만들어진 모든 새 VM은 **동일한 위치 및 구독의 기존 자격 증명 모음** 에 백업하도록 자동으로 구성됩니다. 아래 테이블에는 해당 정책에서 지원하는 모든 VM SKU가 나열되어 있습니다.

## <a name="supported-vms"></a>지원되는 VM*

**정책 이름:** 특정 위치에 있는 VM을 동일한 위치에 있는 기존 중앙 자격 증명 모음으로 백업하는 작업 구성

이미지 게시자 | 이미지 제공 | 이미지 SKU입니다.
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1(2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP(2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 Datacenter(2012-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 Datacenter(2012-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Datacenter(2012-R2-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 R2 Datacenter(2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter(2016-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter - Server Core(2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter - Server Core(2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter(2016-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core with Containers(2016-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Remote Desktop Session Host 2016(2016-Datacenter-with-RDSH)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter(2019-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core(2019-Datacenter-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core(2019-Datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core with Containers(2019-Datacenter-Core-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core with Containers(2019-Datacenter-Core-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter(2019-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter with Containers(2019-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter with Containers(2019-Datacenter-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter(zh-cn)(2019-Datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-with-Containers-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1803-with-Containers-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 모든 이미지 SKU
MicrosoftSQLServer | SQL2016SP1-WS2016 | 모든 이미지 SKU
MicrosoftSQLServer | SQL2016-WS2016 | 모든 이미지 SKU
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | 모든 이미지 SKU
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | 모든 이미지 SKU
MicrosoftSQLServer | SQL2016-WS2012R2 | 모든 이미지 SKU
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | 모든 이미지 SKU
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | 모든 이미지 SKU
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | 모든 이미지 SKU
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | 모든 이미지 SKU
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | 모든 이미지 SKU
MicrosoftRServer | MLServer-WS2016 | 모든 이미지 SKU
MicrosoftVisualStudio | VisualStudio | 모든 이미지 SKU
MicrosoftVisualStudio | Windows | 모든 이미지 SKU
MicrosoftDynamicsAX | Dynamics | Pre-Req-AX7-Onebox-U8
microsoft-ads | windows-data-science-vm | 모든 이미지 SKU
MicrosoftWindowsDesktop | Windows-10 | 모든 이미지 SKU
RedHat | RHEL | 6.7, 6.8, 6.9, 6.10, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7
RedHat | RHEL-SAP-HANA | 6.7, 7.2, 7.3
SUSE | SLES | 12.X
SUSE | SLES-HPC | 12.X
SUSE | SLES-HPC-Priority | 12.X
SUSE | SLES-SAP | 12.X
SUSE | SLES-SAP-BYOS | 12.X
SUSE | SLES-Priority | 12.X
SUSE | SLES-BYOS | 12.X
SUSE | SLES-SAPCAL | 12.X
SUSE | SLES-Standard | 12.X
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
Canonical | UbuntuServer | 18.04-DAILY-LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6.8, 6.9, 6.10, 7.3, 7.4, 7.5, 7.6
OpenLogic | CentOS | 6.X, 7.X
OpenLogic | CentOS–LVM | 6.X, 7.X
OpenLogic | CentOS–SRIOV | 6.X, 7.X
cloudera | cloudera-centos-os | 7.X
