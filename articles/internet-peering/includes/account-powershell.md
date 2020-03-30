---
title: 포함 파일
titleSuffix: Azure
description: 포함 파일
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3a5f7157ef8f3645dd03ec93684238dd8bbc067e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774225"
---
구성을 시작하기 전에 필요한 모듈을 설치하고 가져옵니다. PowerShell에 모듈을 설치하려면 관리자 권한이 필요합니다.

1. Az 모듈 설치 및 가져오기
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Az.피어링 모듈 설치 및 가져오기
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. 아래 명령을 사용하여 모듈을 잘 가져왔는지 확인하십시오.
    ```powershell
    Get-Module
    ```
1. 다음 명령을 사용하여 Azure 계정에 로그인합니다.
    ```powershell
    Connect-AzAccount
    ```
1. 계정의 구독을 확인하고 피어링을 만들 구독을 선택합니다.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. 리소스 그룹이 아직 없는 경우 피어링을 만들기 전에 리소스 그룹을 만들어야 합니다. 다음 명령을 실행하여 수행할 수 있습니다.

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> ASN 및 구독을 아직 연결하지 않은 경우 [동료 피어 ASN](../howto-subscription-association-powershell.md)에 대한 단계를 따릅니다. 피어링을 요청하려면 이 작업을 수행해야 합니다.

> [!NOTE]
> 리소스 그룹의 위치는 피어링을 설정하도록 선택한 위치와 독립적입니다.
&nbsp;
