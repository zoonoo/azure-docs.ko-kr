---
title: Azure Virtual Desktop(클래식)의 데이터 위치 - Azure
description: Azure Virtual Desktop(클래식) 데이터와 메타데이터가 저장되는 위치에 대한 간략한 개요입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9ddfc6cd8a18e9bdbee071d0c5fd64a0c14b2e95
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754478"
---
# <a name="data-locations-for-azure-virtual-desktop-classic"></a>Azure Virtual Desktop(클래식)의 데이터 위치

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 지원하지 않는 Azure Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager Azure Virtual Desktop 개체를 관리하려는 경우 [이 문서](../data-locations.md)를 참조하세요.

Azure Virtual Desktop은 현재 모든 지리적 위치에서 사용할 수 있습니다. 처음에는 서비스 메타데이터를 미국(US) 지리에만 저장할 수 있습니다. 관리자는 호스트 풀 가상 머신 및 파일 서버와 같은 관련 서비스를 만들 때 사용자 데이터를 저장할 위치를 선택할 수 있습니다. [Azure 데이터 센터 맵](https://azuredatacentermap.azurewebsites.net/)에서 Azure 지리에 대해 자세히 알아보세요.

>[!NOTE]
>Microsoft는 사용자가 사용자 및 앱 특정 데이터에 액세스할 수 있는 지역을 제어하거나 제한하지 않습니다.

>[!IMPORTANT]
>Azure Virtual Desktop은 미국에 있는 데이터 센터에 테넌트 이름, 호스트 풀 이름, 앱 그룹 이름 및 사용자 계정 이름과 같은 글로벌 메타데이터 정보를 저장합니다. 저장된 메타데이터는 미사용 상태에서 암호화되고 지역 중복 미러는 미국 내에서 유지 관리됩니다. 앱 설정 및 사용자 데이터와 같은 모든 고객 데이터는 고객이 선택한 위치에 상주하며 서비스에서 관리되지 않습니다.

서비스 메타데이터는 재해 복구를 위해 미국에서 복제됩니다.