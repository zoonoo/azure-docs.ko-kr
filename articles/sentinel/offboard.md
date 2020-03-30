---
title: Azure 파수꾼 제거 | 마이크로 소프트 문서
description: Azure Sentinel 인스턴스를 삭제하는 방법.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: yelevin
ms.openlocfilehash: 4042f7b9d0f6fdf293f338c89d5783fe266c4edf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581687"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>작업 영역에서 Azure 센티넬 제거

Azure Sentinel을 더 이상 사용하지 않으려는 경우 이 문서에서는 작업 영역에서 제거하는 방법을 설명합니다.

## <a name="how-to-remove-azure-sentinel"></a>Azure 센티넬을 제거하는 방법

이 프로세스를 수행하여 작업 영역에서 Azure Sentinel을 제거합니다.

1. Azure **Sentinel로**이동한 다음 **설정**다음에 Azure **Sentinel 제거**탭을 선택합니다.

1. Azure Sentinel을 제거하기 전에 확인란을 사용하여 제거하는 이유를 알려주세요.

1. **작업 영역에서 Azure Sentinel 제거를 선택합니다.**
    
    ![보안인사이트 솔루션 삭제](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>무대 뒤에서 무슨 일이 일어나는가?

솔루션을 제거하면 Azure Sentinel이 삭제 프로세스의 첫 번째 단계를 완료하는 데 최대 48시간이 걸립니다.

연결이 끊어지면 오프보딩 프로세스가 시작됩니다.

**이러한 커넥터의 구성이 제거됩니다.**
-   Office 365

-   AWS

-   마이크로소프트 서비스 보안 경고 (Azure ATP, 클라우드 검색 그림자 IT 보고, Azure AD ID 보호, 마이크로소프트 수비수 ATP, Azure 보안 센터를 포함 하 여 마이크로소프트 클라우드 앱 보안)

-   위협 인텔리전스

-   일반적인 보안 로그(CEF 기반 로그, 바라쿠다 및 Syslog 포함)(Azure 보안 센터가 있는 경우 이러한 로그는 계속 수집됩니다.)

-   Windows 보안 이벤트(Azure 보안 센터가 있는 경우 이러한 로그는 계속 수집됩니다.)

처음 48시간 내에 Azure Sentinel에서 데이터 및 분석 규칙(실시간 자동화 구성 포함)에 더 이상 액세스하거나 쿼리할 수 없습니다.

**30일이 지나면 이러한 리소스가 제거됩니다.**

-   인시던트(조사 메타데이터 포함)

-   분석 규칙

-   책갈피

플레이북, 저장된 통합 문서, 저장된 사냥 쿼리 및 전자 필기장이 제거되지 않습니다. **일부는 제거된 데이터로 인해 파손될 수 있습니다. 수동으로 제거할 수 있습니다.**

서비스를 제거한 후 30일의 유예 기간이 있어 솔루션을 다시 활성화할 수 있으며 데이터 및 분석 규칙이 복원되지만 연결이 끊어진 구성된 커넥터를 다시 연결해야 합니다.

> [!NOTE]
> 솔루션을 제거하면 구독이 Azure Sentinel 리소스 공급자에 계속 등록됩니다. **수동으로 제거할 수 있습니다.**




## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel 서비스를 제거하는 방법을 배웠습니다. 마음이 바뀌어 다시 설치하려는 경우:
- Azure [Sentinel 온보딩을](quickstart-onboard.md)시작하십시오.
