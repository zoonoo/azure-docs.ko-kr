---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 75aec9f3509881c35de9309fa1532b961fb2bc03
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875567"
---
Azure Portal에서 클래식 VNet을 만드는 경우 볼 수 있는 이름은 PowerShell에 사용한 전체 이름이 아닙니다. 예를 들어 포털에 **TestVNet1**이라는 이름으로 표시되는 VNet은 네트워크 구성 파일에서 훨씬 더 긴 이름이 있을 수 있습니다. 해당 이름은 **Group ClassicRG TestVNet1**과 같은 모양일 수 있습니다. 연결을 만드는 경우 네트워크 구성 파일에 있는 값을 사용하는 것이 중요합니다.

다음 단계에서는 연결에 필요한 값을 확보하기 위해 Azure 계정에 연결하고 네트워크 구성 파일을 다운로드하여 살펴봅니다.

1. 최신 버전의 Azure SM(서비스 관리) PowerShell cmdlet을 다운로드하여 설치합니다. 대부분의 사용자에 게는 로컬로 설치 된 리소스 관리자 모듈이 있지만 서비스 관리 모듈은 없습니다. 서비스 관리 모듈은 레거시 이며 별도로 설치 해야 합니다. 자세한 내용은 [서비스 관리 Cmdlet 설치](/powershell/azure/servicemanagement/install-azure-ps)를 참조 하세요.

1. 상승된 권한으로 PowerShell 콘솔을 열고 계정에 연결합니다. 다음 예제를 사용 하 여 연결을 지원 합니다. PowerShell 서비스 관리 모듈을 사용 하 여 이러한 명령을 로컬로 실행 해야 합니다. 계정에 연결합니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

   ```powershell
   Add-AzureAccount
   ```
1. 계정에 대한 구독을 확인합니다.

   ```powershell
   Get-AzureSubscription
   ```
1. 둘 이상의 구독이 있는 경우 사용할 구독을 선택합니다.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
1. 네트워크 구성 파일을 내보내고 및 살펴봅니다. 컴퓨터에 디렉터리를 만들고 디렉터리에 네트워크 구성 파일을 내보냅니다. 이 예제에서는 네트워크 구성 파일을 **C:\AzureNet**으로 내보냅니다.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
1. 텍스트 편집기에서 파일을 열고 VNet과 사이트의 이름을 확인합니다. 이러한 이름은 연결을 만들 때 사용 하는 이름이 됩니다.<br>VNet 이름은 **VirtualNetworkSite name =** 으로 나열됩니다.<br>사이트 이름은 **LocalNetworkSiteRef name =** 으로 나열됩니다.