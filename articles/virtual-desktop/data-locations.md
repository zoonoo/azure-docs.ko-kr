---
title: Windows 가상 데스크톱에 대 한 데이터 위치-Azure
description: Windows 가상 데스크톱의 데이터 및 메타 데이터가 저장 되는 위치에 대 한 간략 한 개요입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e626b7e729e394b1012848904f5ce12279c3ef24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010075"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Windows 가상 데스크톱에 대 한 데이터 및 메타 데이터 위치

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 통해 Windows Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/data-locations-2019.md)를 참조하세요.

Windows 가상 데스크톱은 현재 모든 지리적 위치에서 사용할 수 있습니다. 관리자는 호스트 풀 가상 컴퓨터 및 관련 서비스 (예: 파일 서버)를 만들 때 사용자 데이터를 저장할 위치를 선택할 수 있습니다. Azure [데이터 센터 맵에서](https://azuredatacentermap.azurewebsites.net/)azure 지역에 대해 자세히 알아보세요.

>[!NOTE]
>Microsoft는 사용자 또는 사용자가 사용자와 앱 특정 데이터에 액세스할 수 있는 지역을 제어 하거나 제한 하지 않습니다.

>[!IMPORTANT]
>Windows 가상 데스크톱은 데이터 센터에 테 넌 트 이름, 호스트 풀 이름, 앱 그룹 이름 및 사용자 계정 이름과 같은 전역 메타 데이터 정보를 저장 합니다. 고객이 서비스 개체를 만들 때마다 서비스 개체의 위치를 입력 해야 합니다. 입력 하는 위치에 따라 개체의 메타 데이터가 저장 되는 위치가 결정 됩니다. 고객은 Azure 지역을 선택 하 고 메타 데이터는 관련 지리에 저장 됩니다. 모든 Azure 지역 및 관련 된 지역 목록은 [azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/)을 참조 하세요.

현재 미국 (US) Azure 지리에만 메타 데이터를 저장 하도록 지원 합니다. 저장 된 메타 데이터는 미사용 시 암호화 되며 지역 중복 미러는 지리 내에서 유지 관리 됩니다. 앱 설정 및 사용자 데이터와 같은 모든 고객 데이터는 고객이 선택한 위치에 상주 하며 서비스에서 관리 되지 않습니다. 서비스가 증가 함에 따라 더 많은 지역을 사용할 수 있게 됩니다.

서비스 메타 데이터는 재해 복구를 위해 Azure 지리 내에 복제 됩니다.