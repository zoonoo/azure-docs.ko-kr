---
title: Azure 업데이트 관리에서 작동 하도록 Windows 업데이트 설정 구성
description: 이 문서에서는 Azure 업데이트 관리에서 작동 하도록 구성 하는 Windows 업데이트 설정에 대해 설명 합니다.
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: b9b5f2b19b29eae0132ec01a9f3fb7e8355361f5
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779453"
---
# <a name="configure-windows-update-settings-for-update-management"></a>업데이트 관리에 대 한 Windows 업데이트 설정 구성

Azure 업데이트 관리는 [Windows 업데이트 클라이언트](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) 를 사용 하 여 Windows 업데이트를 다운로드 하 고 설치 합니다. WSUS (Windows Server Update Services) 또는 Windows 업데이트에 연결할 때 Windows 업데이트 클라이언트에서 사용 하는 특정 설정이 있습니다. 이러한 설정의 대부분은 다음을 통해 관리할 수 있습니다.

- 로컬 그룹 정책 편집기
- 그룹 정책
- PowerShell
- 레지스트리 직접 편집

업데이트 관리은 Windows 업데이트 클라이언트를 제어 하기 위해 지정 된 대부분의 설정을 따릅니다. 설정을 사용 하 여 비 Windows 업데이트를 사용 하는 경우에도 업데이트 관리에서 해당 업데이트를 관리 합니다. 업데이트 배포를 수행 하기 전에 업데이트 다운로드를 사용 하도록 설정 하려는 경우 업데이트 배포는 더 빠르고 효율적이 고 유지 관리 기간을 초과할 가능성이 줄어듭니다.

Azure 구독에서 WSUS를 설정 하 고 Windows 가상 머신을 최신 상태로 안전 하 게 유지 하는 방법에 대 한 추가 권장 사항은 [wsus를 사용 하 여 azure에서 windows 가상 머신 업데이트를 위한 배포 계획](https://docs.microsoft.com/azure/architecture/example-scenario/wsus/)을 참조 하세요.

## <a name="pre-download-updates"></a>다운로드 전 업데이트

업데이트 자동 다운로드를 구성 하지만 자동으로 설치 하지 않으려면 그룹 정책를 사용 하 여 [자동 업데이트 구성 설정을](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) **3**으로 설정할 수 있습니다. 이 설정은 백그라운드에서 필요한 업데이트를 다운로드할 수 있도록 하며 업데이트를 설치할 준비가 되었음을 알립니다. 이러한 방식으로 업데이트 관리 일정의 제어를 유지 하지만 업데이트 관리 유지 관리 기간 외에 업데이트를 다운로드할 수 있습니다. 이 동작은 업데이트 관리에서 **유지 관리 기간을 초과** 하지 않도록 합니다.

다음 명령을 실행 하 여 PowerShell을 사용 하 여이 설정 설정을 사용 하도록 설정할 수 있습니다.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>다시 부팅 설정 구성

**업데이트 배포** 설정에서 **다시 부팅 안 함** 을 지정한 경우에도 [다시 시작을 관리 하는 데 사용 되](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) 는 레지스트리 및 레지스트리 키를 [편집 하 여 자동 업데이트 구성](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) 에 나열 된 레지스트리 키로 인해 컴퓨터가 다시 부팅 될 수 있습니다. 사용자 환경에 가장 적합 하도록 이러한 레지스트리 키를 구성 합니다.

## <a name="enable-updates-for-other-microsoft-products"></a>다른 Microsoft 제품에 대한 업데이트 사용

기본적으로 Windows 업데이트 클라이언트는 Windows에 대 한 업데이트만 제공 하도록 구성 됩니다. **Windows 업데이트를 업데이트할 때 다른 Microsoft 제품에 대 한 업데이트 제공** 설정을 사용 하도록 설정 하면 Microsoft SQL Server 및 기타 microsoft 소프트웨어에 대 한 보안 패치를 비롯 한 다른 제품에 대 한 업데이트도 수신 됩니다. Windows 2016 이상에서 사용할 수 있는 최신 [관리 템플릿 파일](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) 을 다운로드 하 고 복사한 경우이 옵션을 구성할 수 있습니다.

Windows Server 2012 r 2를 실행 하는 경우 그룹 정책에 의해이 설정을 구성할 수 없습니다. 해당 컴퓨터에서 다음 PowerShell 명령을 실행 합니다. 업데이트 관리이 설정을 준수 합니다.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS 구성 설정

업데이트 관리 WSUS 설정을 지원 합니다. 업데이트 관리 사용 하도록 구성할 수 있는 WSUS 설정은 아래에 나열 되어 있습니다.

### <a name="intranet-microsoft-update-service-location"></a>인트라넷 Microsoft 업데이트 서비스 위치

[인트라넷 Microsoft 업데이트 서비스 위치 지정](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)에서 업데이트를 검색 하 고 다운로드 하기 위한 원본을 지정할 수 있습니다. 기본적으로 Windows 업데이트 클라이언트는 Windows 업데이트에서 업데이트를 다운로드 하도록 구성 됩니다. WSUS 서버를 컴퓨터의 원본으로 지정 하는 경우 업데이트가 WSUS에서 승인 되지 않으면 업데이트 배포가 실패 합니다. 

해당 내부 업데이트 서비스로만 컴퓨터를 제한 하려면 [Windows 업데이트 인터넷 위치에 연결 안 함](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations)을 구성 합니다. 

## <a name="next-steps"></a>다음 단계

Windows 업데이트 설정을 구성한 후에는 [Azure vm에 대 한 업데이트 및 패치 관리](automation-tutorial-update-management.md)의 지침에 따라 업데이트 배포를 예약할 수 있습니다.
