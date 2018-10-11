---
title: Azure Stack의 지역 관리 | Microsoft Docs
description: Azure Stack의 지역 관리를 간략하게 설명합니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: sethm
ms.reviewer: efemmano
ms.openlocfilehash: 401b81ceb7ab71528a4ad11bc7d8944b4d732933
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078868"
---
# <a name="region-management-in-azure-stack"></a>Azure Stack의 지역 관리

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 영역을 Azure Stack 인프라를 구성 하는 하드웨어 리소스로 구성 된 논리적 엔터티입니다의 개념을 사용 합니다. 지역 관리 내에서 성공적으로 Azure Stack 인프라를 운영 하는 데 필요한 모든 리소스를 찾을 수 있습니다.

시스템 배포를 통합 하는 하나 (라고 하는 *Azure Stack 클라우드에*) 단일 영역 구성 합니다. 각 Azure Stack Development Kit 라는 한 지역에 **로컬**합니다. 에 두 번째 Azure Stack 통합 시스템을 배포 하거나 별도 하드웨어 개발 키트의 다른 인스턴스를 설정 하는 경우이 Azure Stack 클라우드는 서로 다른 영역입니다.

## <a name="information-available-through-the-region-management-tile"></a>지역 관리 타일을 통해 제공 되는 정보

Azure Stack에서 사용할 수 있는 지역 관리 기능 집합이 합니다 **하위 지역 관리** 바둑판식으로 배열 합니다. 이 타일은 관리자 포털에서 기본 대시보드에서 Azure Stack 운영자에 게 사용할 수 있습니다. 이 타일을 통해 모니터링 하 고 Azure Stack 지역 및 지역에 한정 되는 해당 구성 요소를 업데이트할 수 있습니다.

 ![지역 관리 타일](media/azure-stack-manage-region/image1.png)

 지역 관리 타일의 영역을 클릭 하면 다음 정보를 액세스할 수 있습니다.

  ![지역 관리 블레이드의 창 설명](media/azure-stack-manage-region/image2.png)

1. **리소스 메뉴**합니다. 여기에서 특정 인프라 관리 영역에 액세스할 수 있습니다 및 보기 및 저장소 계정 및 가상 네트워크와 같은 사용자 리소스를 관리 합니다.

2. **경고**. 이 시스템 수준 경고를 나열 하 고 각 경고에 자세히 설명 합니다.

3. **업데이트**. 여기 Azure Stack 인프라, 사용 가능한 업데이트 및 업데이트 기록의 현재 버전을 볼 수 있습니다. 또한 통합된 시스템을 업데이트할 수 있습니다.

4. **리소스 공급자**. 리소스 공급자는 Azure Stack을 실행 하는 데 필요한 구성 요소에서 제공 하는 사용자 기능을 관리할 수 있는 곳입니다. 각 리소스 공급자 관리 환경이 제공 됩니다. 이 환경에는 특정 공급자에 대한 경고, 메트릭 및 리소스 공급자에 한정된 기타 관리 기능이 포함될 수 있습니다.

5. **인프라 역할**. 인프라 역할은 Azure Stack을 실행하는 데 필요한 구성 요소입니다. 경고를 보고 하는 인프라 역할만 나열 됩니다. 역할을 선택 하면 역할 및이 역할이 실행 되 고 있는 역할 인스턴스를 사용 하 여 관련 된 경고를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack의 상태 및 경고 모니터링](azure-stack-monitor-health.md)
- [Azure Stack에서 업데이트 관리](azure-stack-updates.md)
