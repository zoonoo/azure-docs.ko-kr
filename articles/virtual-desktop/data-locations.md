---
title: Windows Virtual Desktop의 데이터 위치 - Azure
description: Windows Virtual Desktop의 데이터와 메타데이터가 저장되는 위치에 대한 간략한 개요입니다.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a4c63cc686b08d179e20e6f3e3a7aa1efa69a5f8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447083"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Windows Virtual Desktop의 데이터 및 메타데이터 위치

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 통해 Windows Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/data-locations-2019.md)를 참조하세요.

Windows Virtual Desktop은 현재 모든 지리적 위치에서 사용할 수 있습니다. 관리자는 호스트 풀 가상 머신 및 파일 서버와 같은 관련 서비스를 만들 때 사용자 데이터를 저장할 위치를 선택할 수 있습니다. [Azure 데이터 센터 맵](https://azuredatacentermap.azurewebsites.net/)에서 Azure 지리에 대해 자세히 알아보세요.

>[!NOTE]
>Microsoft는 사용자가 사용자 및 앱별 데이터에 액세스할 수 있는 지역을 제어하거나 제한하지 않습니다.

>[!IMPORTANT]
>Windows Virtual Desktop은 테넌트 이름, 호스트 풀 이름, 앱 그룹 이름 및 사용자 계정 이름과 같은 글로벌 메타데이터 정보를 데이터 센터에 저장합니다. 고객이 서비스 개체를 만들 때마다 서비스 개체의 위치를 입력해야 합니다. 입력한 위치에 따라 개체의 메타데이터가 저장되는 위치가 결정됩니다. 고객은 Azure 지역을 선택하고 메타데이터는 관련 지리에 저장됩니다. 모든 Azure 지역 및 관련 지역 목록은 [Azure 지리](https://azure.microsoft.com/global-infrastructure/geographies/)를 참조하세요.

현재 다음 지리에 메타데이터를 저장할 수 있습니다.

- 미국(US)(일반적으로 사용 가능)
- 유럽(EU)(공개 미리 보기) 

>[!NOTE]
> Windows Virtual Desktop 서비스 개체를 만들 지역을 선택하면 미국과 유럽 지리 모두에 지역이 표시됩니다. 배포에 가장 적합한 지역을 파악하려면 [Azure 전역 인프라 맵](https://azure.microsoft.com/global-infrastructure/geographies/#geographies)을 살펴보세요.

저장된 메타데이터는 미사용 상태에서 암호화되고 지역 중복 미러는 해당 지리 내에서 유지 관리됩니다. 앱 설정 및 사용자 데이터와 같은 모든 고객 데이터는 고객이 선택한 위치에 상주하며 서비스에서 관리되지 않습니다. 서비스가 증가함에 따라 더 많은 지역을 사용할 수 있게 됩니다.

서비스 메타데이터는 재해 복구를 위해 Azure 지리 내에서 복제됩니다.