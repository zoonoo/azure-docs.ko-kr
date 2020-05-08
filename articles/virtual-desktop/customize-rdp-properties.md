---
title: PowerShell을 사용 하 여 RDP 속성 사용자 지정-Azure
description: PowerShell cmdlet을 사용 하 여 Windows 가상 데스크톱에 대 한 RDP 속성을 사용자 지정 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 66b76fcdd9729b2a92ea2d561c740dbe148e0bbe
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611554"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>호스트 풀의 원격 데스크톱 프로토콜 속성 사용자 지정

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows 가상 데스크톱 개체를 사용 하 여 스프링 2020 업데이트에 적용 됩니다. Azure Resource Manager 개체 없이 Windows 가상 데스크톱 2019 릴리스를 사용 하는 경우 [이 문서](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md)를 참조 하세요.
>
> Windows 가상 데스크톱 스프링 2020 업데이트는 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에 사용 하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

다중 모니터 환경 및 오디오 리디렉션과 같은 RDP (호스트 풀의 원격 데스크톱 프로토콜) 속성을 사용자 지정 하면 요구 사항에 따라 사용자에 게 최적의 환경을 제공할 수 있습니다. Azure Portal를 사용 하거나 **AzWvdHostPool** cmdlet의 *-customrdpproperty* 매개 변수를 사용 하 여 WINDOWS 가상 데스크톱에서 RDP 속성을 사용자 지정할 수 있습니다.

지원 되는 속성 및 해당 기본값의 전체 목록은 [지원 되는 RDP 파일 설정](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) 을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작 하기 전에 PowerShell 모듈을 설정 하 고 Azure에 로그인 하려면 [Windows 가상 데스크톱 powershell 모듈 설정](powershell-module.md) 의 지침을 따르세요.

## <a name="default-rdp-properties"></a>기본 RDP 속성

기본적으로 게시 된 RDP 파일에는 다음 속성이 포함 됩니다.

|RDP 속성 | 데스크톱 | RemoteApp |
|---|---| --- |
| 다중 모니터 모드 | 사용 | 해당 없음 |
| 드라이브 리디렉션 사용 | 드라이브, 클립보드, 프린터, COM 포트, USB 장치 및 스마트 카드| 드라이브, 클립보드 및 프린터 |
| 원격 오디오 모드 | 로컬로 재생 | 로컬로 재생 |

호스트 풀에 대해 정의 하는 모든 사용자 지정 속성은 이러한 기본값을 재정의 합니다.

## <a name="configure-rdp-properties-in-the-azure-portal"></a>Azure Portal에서 RDP 속성 구성

Azure Portal에서 RDP 속성을 구성 하려면 다음을 수행 합니다.

1. <https://portal.azure.com> 에서 Azure에 로그인합니다.
2. 검색 창에 **windows 가상 데스크톱** 을 입력 합니다.
3. 서비스에서 **Windows 가상 데스크톱**을 선택 합니다.
4. Windows 가상 데스크톱 페이지의 화면 왼쪽에 있는 메뉴에서 **호스트 풀** 을 선택 합니다.
5. 업데이트 하려는 **호스트 풀의 이름을** 선택 합니다.
6. 화면 왼쪽의 메뉴에서 **속성** 을 선택 합니다.
7. Rdp **설정을** 선택 하 여 rdp 속성 편집을 시작 합니다.
8. 완료 되 면 **저장** 을 선택 하 여 변경 내용을 저장 합니다.

RDP 설정 메뉴에 표시 되지 않는 설정을 편집 하려는 경우 PowerShell에서 cmdlet을 실행 하 여 수동으로 편집 해야 합니다. 다음 섹션에서는 PowerShell에서 사용자 지정 RDP 속성을 수동으로 편집 하는 방법을 설명 합니다.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>단일 사용자 지정 RDP 속성 추가 또는 편집

단일 사용자 지정 RDP 속성을 추가 하거나 편집 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

방금 실행 한 cmdlet이 속성을 업데이트 했는지 확인 하려면 다음 cmdlet을 실행 합니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

예를 들어 0301HP 라는 호스트 풀의 "audiocapturemode" 속성을 확인 하는 경우이 cmdlet을 입력 합니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>여러 사용자 지정 RDP 속성 추가 또는 편집

여러 사용자 지정 RDP 속성을 추가 하거나 편집 하려면 사용자 지정 RDP 속성을 세미콜론으로 구분 된 문자열로 제공 하 여 다음 PowerShell cmdlet을 실행 합니다.

```powershell
$properties="<property1>;<property2>;<property3>" 
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties 
```

다음 cmdlet을 실행 하 여 RDP 속성이 추가 되었는지 확인할 수 있습니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty 

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

이전 cmdlet 예제에 따라 0301HP 호스트 풀에 여러 RDP 속성을 설정 하는 경우 cmdlet은 다음과 같이 표시 됩니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty 

Name              : 0301HP 
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>모든 사용자 지정 RDP 속성 다시 설정

[단일 사용자 지정 rdp 속성 추가 또는 편집](#add-or-edit-a-single-custom-rdp-property)의 지침에 따라 개별 사용자 지정 rdp 속성을 기본값으로 다시 설정 하거나, 다음 PowerShell cmdlet을 실행 하 여 호스트 풀의 모든 사용자 지정 rdp 속성을 다시 설정할 수 있습니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

설정이 성공적으로 제거 되었는지 확인 하려면 다음 cmdlet을 입력 합니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty 

Name              : <hostpoolname> 
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>다음 단계

이제 지정 된 호스트 풀의 RDP 속성을 사용자 지정 했으므로 Windows 가상 데스크톱 클라이언트에 로그인 하 여 사용자 세션의 일부로 테스트할 수 있습니다. 다음 방법 가이드에서는 사용자가 선택한 클라이언트를 사용 하 여 세션에 연결 하는 방법을 설명 합니다.

- [Windows Desktop 클라이언트와 연결](connect-windows-7-and-10.md)
- [웹 클라이언트를 사용하여 연결](connect-web.md)
- [Android 클라이언트와 연결](connect-android.md)
- [macOS 클라이언트와 연결](connect-macos.md)
- [iOS 클라이언트와 연결](connect-ios.md)