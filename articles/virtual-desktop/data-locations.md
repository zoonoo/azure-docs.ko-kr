---
title: Windows 가상 데스크톱용 데이터 위치 - Azure
description: Windows 가상 데스크톱의 데이터 및 메타데이터가 저장되는 위치에 대한 간략한 개요입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5f48586f38c310c77c61f470d3e2fbc819c33f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128047"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Windows 가상 데스크톱용 데이터 위치

Windows 가상 데스크톱은 현재 모든 지리적 위치에 사용할 수 있습니다. 처음에는 서비스 메타데이터를 미국(미국) 지역에만 저장할 수 있습니다. 관리자는 호스트 풀 가상 시스템 및 파일 서버와 같은 관련 서비스를 만들 때 사용자 데이터를 저장할 위치를 선택할 수 있습니다. Azure [데이터 센터 맵에서](https://azuredatacentermap.azurewebsites.net/)Azure 지역에 대해 자세히 알아봅니다.

>[!NOTE]
>Microsoft는 사용자 또는 사용자가 사용자 및 앱 별 데이터에 액세스할 수 있는 지역을 제어하거나 제한하지 않습니다.

>[!IMPORTANT]
>Windows Virtual Desktop은 미국에 위치한 데이터 센터에 테넌트 이름, 호스트 풀 이름, 앱 그룹 이름 및 사용자 주체 이름과 같은 글로벌 메타데이터 정보를 저장합니다. 저장된 메타데이터는 미사용 으로 암호화되고 지리적 중복 미러는 미국 내에서 유지됩니다. 앱 설정 및 사용자 데이터와 같은 모든 고객 데이터는 고객이 선택한 위치에 상주하며 서비스에서 관리되지 않습니다.

서비스 메타데이터는 재해 복구를 위해 미국에서 복제됩니다.