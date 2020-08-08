---
title: Windows 가상 데스크톱 (클래식)에 대 한 데이터 위치-Azure
description: Windows 가상 데스크톱 (클래식) 데이터와 메타 데이터가 저장 되는 위치에 대 한 간략 한 개요입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 869defde657c9cb4c8bea6bbacebb9458e5a2b96
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008817"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Windows 가상 데스크톱 (클래식)에 대 한 데이터 위치

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 지원하지 않는 Windows Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager Windows Virtual Desktop 개체를 관리하려는 경우 [이 문서](../data-locations.md)를 참조하세요.

Windows 가상 데스크톱은 현재 모든 지리적 위치에서 사용할 수 있습니다. 처음에는 서비스 메타 데이터를 미국 (미국) 지리에만 저장할 수 있습니다. 관리자는 호스트 풀 가상 컴퓨터 및 관련 서비스 (예: 파일 서버)를 만들 때 사용자 데이터를 저장할 위치를 선택할 수 있습니다. Azure [데이터 센터 맵에서](https://azuredatacentermap.azurewebsites.net/)azure 지역에 대해 자세히 알아보세요.

>[!NOTE]
>Microsoft는 사용자 또는 사용자가 사용자와 앱 특정 데이터에 액세스할 수 있는 지역을 제어 하거나 제한 하지 않습니다.

>[!IMPORTANT]
>Windows 가상 데스크톱은 미국에 있는 데이터 센터에 테 넌 트 이름, 호스트 풀 이름, 앱 그룹 이름 및 사용자 계정 이름과 같은 글로벌 메타 데이터 정보를 저장 합니다. 저장 된 메타 데이터는 미사용 상태에서 암호화 되 고 지역 중복 미러는 미국 내에서 유지 관리 됩니다. 앱 설정 및 사용자 데이터와 같은 모든 고객 데이터는 고객이 선택한 위치에 상주 하며 서비스에서 관리 되지 않습니다.

서비스 메타 데이터는 재해 복구를 위해 미국에서 복제 됩니다.