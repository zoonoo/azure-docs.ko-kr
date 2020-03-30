---
title: Azure 업데이트 관리와 함께 작동하도록 Windows 업데이트 설정 구성
description: 이 문서에서는 Azure 업데이트 관리에서 작동하도록 구성한 Windows 업데이트 설정에 대해 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279027"
---
# <a name="configure-windows-update-settings-for-update-management"></a>업데이트 관리를 위한 Windows 업데이트 설정 구성

Azure 업데이트 관리는 Windows 업데이트를 다운로드하고 설치하기 위해 [Windows 업데이트 클라이언트를](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) 기반으로 합니다. Windows 서버 업데이트 서비스(WSUS) 또는 Windows 업데이트에 연결할 때 Windows 업데이트 클라이언트에서 사용하는 특정 설정이 있습니다. 이러한 설정의 대부분은 다음과 함께 관리할 수 있습니다.

- 로컬 그룹 정책 편집기
- 그룹 정책
- PowerShell
- 레지스트리 직접 편집

업데이트 관리는 Windows Update 클라이언트를 제어하도록 지정된 많은 설정을 존중합니다. 설정을 사용하여 Windows 이외의 업데이트를 사용하도록 설정하는 경우 업데이트 관리도 해당 업데이트를 관리합니다. 업데이트 배포가 발생하기 전에 업데이트 다운로드를 사용하도록 설정하려는 경우 업데이트 배포가 더 빠르고 효율적이며 유지 관리 기간을 초과할 가능성이 낮아질 수 있습니다.

## <a name="pre-download-updates"></a>사전 다운로드 업데이트

업데이트자동 다운로드를 구성하지만 자동으로 설치하지 않으려면 그룹 정책을 사용하여 [자동 업데이트 구성 설정을](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) **3로**설정할 수 있습니다. 이 설정을 사용하면 백그라운드에서 필요한 업데이트를 다운로드할 수 있으며 업데이트를 설치할 준비가 되었는지 를 확인할 수 있습니다. 이러한 방식으로 업데이트 관리는 일정을 제어할 수 있지만 업데이트 관리 유지 관리 기간 외부에서 업데이트를 다운로드할 수 있습니다. 이 동작은 업데이트 관리에서 **유지 관리 기간이 초과된** 오류를 방지합니다.

다음 명령을 실행하여 PowerShell을 사용하여 이 설정을 활성화할 수 있습니다.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>재부팅 설정 구성

[다시 시작을 관리하는 데 사용되는](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) 레지스트리 및 레지스트리 [키를 편집하여 자동 업데이트 구성에](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) 나열된 레지스트리 키는 **업데이트 배포** 설정에서 다시 부팅 **안 됨을** 지정한 경우에도 컴퓨터를 다시 부팅할 수 있습니다. 사용자 환경에 가장 적합한 레지스트리 키를 구성합니다.

## <a name="enable-updates-for-other-microsoft-products"></a>다른 Microsoft 제품에 대한 업데이트 사용

기본적으로 Windows 업데이트 클라이언트는 Windows에 대해서만 업데이트를 제공하도록 구성됩니다. Windows 설정을 **업데이트할 때 다른 Microsoft 제품에 대한 업데이트 제공을** 사용하도록 설정하면 Microsoft SQL Server 및 기타 Microsoft 소프트웨어에 대한 보안 패치를 비롯한 다른 제품에 대한 업데이트도 받게 됩니다. 이 옵션은 Windows 2016 이상에서 사용할 수 있는 최신 [관리 템플릿 파일을](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) 다운로드하여 복사한 경우 구성할 수 있습니다.

Windows Server 2012 R2를 실행 하는 경우 그룹 정책에 의해이 설정을 구성할 수 없습니다. 해당 컴퓨터에서 다음 PowerShell 명령을 실행합니다. 업데이트 관리는 이 설정을 준수합니다.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS 구성 설정

업데이트 관리는 WSUS 설정을 지원합니다. 업데이트 관리 작업을 위해 구성할 수 있는 WSUS 설정은 다음과 같습니다.

### <a name="intranet-microsoft-update-service-location"></a>인트라넷 마이크로소프트 업데이트 서비스 위치

[인트라넷 Microsoft 업데이트 서비스 위치 지정에서](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)업데이트를 검색하고 다운로드할 수 있는 소스를 지정할 수 있습니다. 기본적으로 Windows 업데이트 클라이언트는 Windows 업데이트에서 업데이트를 다운로드하도록 구성됩니다. WSUS 서버를 컴퓨터의 소스로 지정하면 WSUS에서 업데이트가 승인되지 않으면 업데이트 배포가 실패합니다. 

컴퓨터를 해당 내부 업데이트 서비스로만 제한하려면 [Windows Update 인터넷 위치에 연결하지 않음을 구성합니다.](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations) 

## <a name="next-steps"></a>다음 단계

Windows 업데이트 설정을 구성한 후 [Azure VM에 대한 업데이트 및 패치 관리의](automation-tutorial-update-management.md)지침에 따라 업데이트 배포를 예약할 수 있습니다.
