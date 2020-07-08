---
title: 파일 포함
titleSuffix: Azure
description: 포함 파일
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81678467"
---
구성을 시작 하기 전에 필요한 모듈을 설치 하 고 가져옵니다. PowerShell에서 모듈을 설치 하려면 관리자 권한이 필요 합니다.

1. Az module을 설치 하 고 가져옵니다.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Az. 피어 링 모듈을 설치 하 고 가져옵니다.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. 다음 명령을 사용 하 여 모듈을 제대로 가져왔는지 확인 합니다.
    ```powershell
    Get-Module
    ```
1. 다음 명령을 사용하여 Azure 계정에 로그인합니다.
    ```powershell
    Connect-AzAccount
    ```
1. 계정에 대 한 구독을 확인 하 고 피어 링을 만들려는 구독을 선택 합니다.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. 리소스 그룹이 아직 없는 경우 피어 링을 만들기 전에 먼저 리소스 그룹을 만들어야 합니다. 다음 명령을 실행하여 수행할 수 있습니다.

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> 이미 ASN 및 구독을 연결 하지 않은 경우 [피어 Asn 연결](../howto-subscription-association-powershell.md)의 단계를 따르세요. 피어 링을 요청 하려면이 작업이 필요 합니다.

> [!NOTE]
> 리소스 그룹의 위치는 피어 링을 설정 하도록 선택 하는 위치와는 독립적입니다.
&nbsp;
