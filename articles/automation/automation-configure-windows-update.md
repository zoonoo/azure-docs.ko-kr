---
title: Azure 업데이트 관리에서 작동 하도록 Windows 업데이트 설정 구성
description: 이 문서에서는를 사용 하도록 구성 하는 Windows 업데이트 설정에 대해 설명 업데이트 관리
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f50ca9515f12e8c9b5943904c4d0226f2ca3353c
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377565"
---
# <a name="configure-windows-update-settings-for-update-management"></a>업데이트 관리에 대 한 Windows 업데이트 설정 구성

업데이트 관리는 Windows 업데이트를 활용하여 Windows 업데이트를 다운로드 및 설치합니다. 따라서 Windows 업데이트에서 사용되는 많은 설정을 적용하고 있습니다. 설정을 사용하여 Windows 이외 업데이트를 사용하도록 설정하는 경우 업데이트 관리는 해당 업데이트도 관리합니다. 업데이트 배포를 수행하기 전에 업데이트 다운로드를 사용하도록 설정하면 업데이트 배포가 더 빠르게 진행되고 유지 관리 기간을 초과하지 않을 수 있습니다.

## <a name="pre-download-updates"></a>업데이트 사전 다운로드

그룹 정책에서 업데이트를 자동으로 다운로드하도록 구성하려면 [자동 업데이트 설정 구성](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates)을 **3**으로 설정할 수 있습니다. 이 경우 필요한 업데이트가 백그라운드에서 다운로드되지만 설치되지는 않습니다. 이를 통해 업데이트 관리 일정을 제어할 수 있지만, 업데이트 관리 유지 관리 기간 이후에도 업데이트를 다운로드할 수 있게 됩니다. 이를 통해 업데이트 관리에서 **유지 관리 기간을 초과함** 오류가 발생하지 않을 수 있습니다.

PowerShell을 사용하여 설정할 수도 있습니다. 이를 위해 업데이트를 자동으로 다운로드하려는 시스템에서 다음 PowerShell을 실행합니다.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>자동 설치 사용 안 함

Azure Vm은 기본적으로 사용 하도록 설정 된 업데이트를 자동으로 설치 합니다. 이렇게 하면 업데이트 관리에 의해 설치 되도록 예약 하기 전에 업데이트를 설치할 수 있습니다. @No__t-0 레지스트리 키를 `1`로 설정 하 여이 동작을 사용 하지 않도록 설정할 수 있습니다. 다음 PowerShell 코드 조각은이 작업을 수행 하는 한 가지 방법을 보여 줍니다.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>다시 부팅 설정 구성

업데이트 배포 설정에서 **다시 부팅 안 함** 을 지정한 경우에도 [다시 시작을 관리 하는 데 사용](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) 된 레지스트리 및 레지스트리 키를 [편집 하 여 자동 업데이트 구성](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry) 에 나열 된 레지스트리 키로 인해 컴퓨터를 다시 부팅할 수 있습니다. . 사용자 환경에 맞게 필요에 따라 이러한 레지스트리 키를 구성 해야 합니다.

## <a name="enable-updates-for-other-microsoft-products"></a>다른 Microsoft 제품에 대한 업데이트 사용

기본적으로 Windows 업데이트는 Windows용 업데이트만 제공합니다. **Windows를 업데이트할 때 다른 Microsoft 제품에 대 한 업데이트 제공**을 사용 하도록 설정 하면 SQL Server 또는 다른 자사 소프트웨어에 대 한 보안 패치를 비롯 한 다른 제품에 대 한 업데이트가 제공 됩니다. 이 옵션은 그룹 정책을 통해 구성할 수 없습니다. 다른 타사 패치를 사용하도록 설정하려는 시스템에서 다음 PowerShell을 실행하면 업데이트 관리에 이 설정이 적용됩니다.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS 구성 설정

**업데이트 관리** WSUS 구성 설정을 고려 합니다. 업데이트 관리 사용 하도록 구성할 수 있는 WSUS 설정 목록은 아래에 나열 되어 있습니다.

### <a name="intranet-microsoft-update-service-location"></a>인트라넷 Microsoft 업데이트 서비스 위치

[인트라넷 Microsoft 업데이트 서비스 위치](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)에서 업데이트를 검색 하 고 다운로드 하기 위한 원본을 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Windows 업데이트 설정을 구성한 후에는 [Azure vm에 대 한 업데이트 및 패치 관리의](automation-tutorial-update-management.md) 지침에 따라 업데이트 배포를 예약할 수 있습니다.