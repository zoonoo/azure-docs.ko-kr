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
ms.date: 03/21/2018
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2018
ms.openlocfilehash: 29e4bb23b4b80f6e472d2dcb27fe5e5279a35890
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337717"
---
# <a name="region-management-in-azure-stack"></a>Azure Stack의 지역 관리

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

개념을 사용 하는 azure Stack *지역*에 Azure Stack 인프라를 구성 하는 하드웨어 리소스로 구성 하는 논리적 엔터티입니다. 지역 관리에서 Azure Stack 인프라를 성공적으로 작동 하는 데 필요한 모든 리소스를 찾을 수 있습니다.

시스템 배포를 통합 하는 하나 (라고 하는 *Azure Stack 클라우드에*) 단일 영역 구성 합니다. 각 Azure Stack 개발 키트 (ASDK) 라는 한 지역에 **로컬**합니다. 에 두 번째 Azure Stack 통합 시스템을 배포 하거나 별도 하드웨어 개발 키트의 다른 인스턴스를 설정 하는 경우이 Azure Stack 클라우드는 서로 다른 영역입니다.

## <a name="information-available-through-the-region-management-tile"></a>지역 관리 타일을 통해 제공 되는 정보

Azure Stack에서 사용할 수 있는 지역 관리 기능 집합이 합니다 **하위 지역 관리** 바둑판식으로 배열 합니다. 이 타일은 관리자 포털에서 기본 대시보드에서 Azure Stack 운영자에 게 사용할 수 있습니다. 이 타일을 통해 모니터링 하 고 Azure Stack 지역 및 지역에 한정 되는 해당 구성 요소를 업데이트할 수 있습니다.

![지역 관리 타일](media/azure-stack-region-management/image1.png)

영역을 클릭 하는 경우는 **지역 관리** 타일을 다음 정보에 액세스할 수 있습니다.

[![지역 관리 블레이드의 창 설명](media/azure-stack-region-management/regionssm.png "지역 관리 블레이드")](media/azure-stack-region-management/regions.png#lightbox)

1. **리소스 메뉴**합니다. 특정 인프라 관리 영역에 액세스 하 고 보려면 저장소 계정 및 가상 네트워크와 같은 사용자 리소스를 관리 합니다.

2. **경고**. 시스템 수준 경고를 나열 하 고 각 경고의 세부 정보를 제공 합니다.

3. **업데이트**. 업데이트 기록 및 사용 가능한 업데이트를 사용 하는, Azure Stack 인프라의 현재 버전을 봅니다. 또한 통합된 시스템을 업데이트할 수 있습니다.

4. **리소스 공급자**. Azure Stack을 실행 하는 데 필요한 구성 요소에서 제공 하는 사용자 기능을 관리 합니다. 각 리소스 공급자 관리 환경이 제공 됩니다. 이 환경에는 특정 공급자에 대한 경고, 메트릭 및 리소스 공급자에 한정된 기타 관리 기능이 포함될 수 있습니다.

5. **인프라 역할**. Azure Stack을 실행 하는 데 필요한 구성 요소 경고를 보고 하는 인프라 역할만 나열 됩니다. 역할을 선택 하면 역할 및이 역할이 실행 되 고 있는 역할 인스턴스를 사용 하 여 관련 된 경고를 볼 수 있습니다.

6. **Properties**. 등록 상태 및 지역 관리 블레이드에서 환경의 세부 정보입니다. 상태 수 있습니다 **Registered** 또는 **등록 되지 않은**합니다. 등록 하는 경우 또한 보여줍니다 Azure 구독 ID를 사용 하 여 Azure Stack에 등록 리소스 그룹 및 이름과 함께 등록를.

## <a name="next-steps"></a>다음 단계

- [Azure Stack에서 상태 및 경고 모니터링](azure-stack-monitor-health.md)
- [Azure Stack에서 업데이트 관리](azure-stack-updates.md)
