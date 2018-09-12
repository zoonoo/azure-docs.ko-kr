---
title: Azure Stack 개요에 대 한 업데이트 관리 | Microsoft Docs
description: Azure Stack 통합 시스템에 대 한 업데이트 관리에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9b0781f4-2cd5-4619-a9b1-59182b4a6e43
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.openlocfilehash: 4d8a79862dac53429acda14f81818f92a96df529
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44376853"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Azure Stack 개요에 대 한 업데이트 관리

*적용 대상: Azure Stack 통합 시스템*

Microsoft update 패키지 일반적으로 Azure Stack 통합 시스템에 대 한 각 월의 네 번째 화요일 주위를 놓습니다. 업데이트 알림 조직에 도달 하는 특정 알림 프로세스에 대 한 OEM을 요청 합니다. 이 문서 라이브러리에서 확인할 수 있습니다 **개요** > **릴리스** 현재 지원 되는 릴리스에 대 한 정보에 대 한 합니다. 

각 릴리스의 Microsoft 소프트웨어 업데이트는 단일 업데이트 패키지로 제공 됩니다. Azure Stack 운영자로 가져올 수 있습니다, 설치 및 이러한의 설치 진행률을 모니터링 관리자 포털에서 패키지를 업데이트 합니다. 

원래 장비 제조업체 (OEM) 하드웨어 공급 업체는 또한 드라이버 및 펌웨어 업데이트 등의 업데이트를 릴리스 했습니다. 이러한 업데이트, OEM 하드웨어 공급 업체에서 별도 패키지로 전달 되는 동안 가져온, 설치 하며 업데이트 패키지 가져온, 설치 및 관리 되는 Microsoft에서 동일한 방식으로 업데이트 패키지를 관리 합니다.

지원 시스템을 유지 하려면 특정 버전 수준으로 업데이트 하는 Azure Stack을 유지 해야 합니다. 검토 하 고 있는지 확인 합니다 [Azure Stack 서비스 정책](azure-stack-servicing-policy.md)합니다.

> [!NOTE]
> Azure Stack 개발 키트 Azure Stack 업데이트 패키지에 적용할 수 없습니다. 업데이트 패키지는 통합된 시스템을 위한 설계 되었습니다. 정보를 참조 하세요 [재배포는 ASDK](https://docs.microsoft.com/en-us/azure/azure-stack/asdk)합니다.

## <a name="the-update-resource-provider"></a>업데이트 리소스 공급자

Azure Stack에는 Microsoft 소프트웨어 업데이트의 응용 프로그램을 오케스트레이션 하는 업데이트 리소스 공급자를 포함 합니다. 이 리소스 공급자는 모든 물리적 호스트, Service Fabric 응용 프로그램 및 런타임 및 모든 인프라 가상 컴퓨터 및 연결 된 서비스에서 업데이트에 적용 되었는지 확인 합니다.

업데이트 설치에 업데이트 프로세스 대상 Azure Stack (예를 들어, 물리적 호스트 및 인프라 virtual machines)에서 다양 한 하위 시스템으로 상위 수준 상태를 볼 수 있습니다.

## <a name="plan-for-updates"></a>업데이트에 대 한 계획

유지 관리 작업을 사용자에 게 알림 및 예약 하는 일반적인 유지 관리 기간 업무 외 시간 동안 가능한 경우 것이 좋습니다. 유지 관리 작업 테 넌 트 워크 로드와 포털 작업에 영향을 줄 수 있습니다.

## <a name="using-the-update-tile-to-manage-updates"></a>타일 업데이트를 사용 하 여 업데이트를 관리 하려면
관리자 포털에서 업데이트를 관리합니다. Azure Stack 운영자로 서는 대시보드에서 타일 업데이트를 사용할 수 있습니다.

- 현재 버전 같은 중요 한 정보를 봅니다.
- 업데이트를 설치 하 고 진행률을 모니터링 합니다.
- 이전에 설치 된 업데이트에 대 한 업데이트 기록을 검토 합니다.
 
## <a name="determine-the-current-version"></a>현재 버전 확인

타일 업데이트 Azure Stack의 현재 버전을 보여 줍니다. 관리자 포털에서 다음 방법 중 하나를 사용 하 여 타일 업데이트를 가져올 수 있습니다.

- 현재 버전을 보려면 대시보드에서 합니다 **업데이트** 바둑판식으로 배열 합니다.
 
   ![기본 대시보드에서 타일을 업데이트](./media/azure-stack-updates/image1.png)
 
- 에 **하위 지역 관리** 타일 영역 이름을 클릭 합니다. 현재 버전을 보려면 합니다 **업데이트** 바둑판식으로 배열 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack 서비스 정책](azure-stack-servicing-policy.md) 
- [Azure Stack의 지역 관리](azure-stack-region-management.md)     


