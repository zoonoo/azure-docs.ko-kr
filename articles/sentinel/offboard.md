---
title: Offboard Azure 센티널 | Microsoft Docs
description: Azure 센티널 인스턴스를 삭제 하는 방법
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: rkarlin
ms.openlocfilehash: 4c0c415235fd290bc47ac402a6b81a1afa7af903
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74777437"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>작업 영역에서 Azure 센티널 제거

Azure 센티널을 더 이상 사용 하지 않으려는 경우이 문서에서는 작업 영역에서 제거 하는 방법을 설명 합니다.

## <a name="how-to-delete-azure-sentinel"></a>Azure 센티널을 삭제 하는 방법

백그라운드에서 Azure 센티널을 설치 하면 **Securityinsights** 솔루션이 선택한 작업 영역에 설치 됩니다. 따라서 먼저 **Securityinsights** 솔루션을 제거 해야 합니다.

1.  **Azure 센티널**로 이동한 후 **구성**, **작업 영역 설정**, **솔루션**을 차례로 클릭 합니다.

2.  `SecurityInsights`를 선택 하 고 클릭 합니다.

    ![SecurityInsights 솔루션 찾기](media/offboard/find-solution.png)

3.  페이지 위쪽에서 **삭제**를 선택 합니다.

    > [!IMPORTANT]
    > 작업 영역을 제거 하면 Azure Monitor를 포함 하 여이 작업 영역을 사용 하는 다른 솔루션 및 데이터 원본에 영향을 줄 수 있습니다. 이 작업 영역을 사용 하는 솔루션을 확인 하려면 [설치 된 모니터링 솔루션 나열](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)을 참조 하세요. 작업 영역에 수집 되는 솔루션의 데이터를 확인 하려면 [수집 데이터 볼륨 이해](../azure-monitor/platform/manage-cost-storage.md#understanding-ingested-data-volume)를 참조 하세요.

    ![SecurityInsights 솔루션을 삭제 합니다.](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>백그라운드에서 수행 되는 작업은 무엇 인가요?

솔루션을 삭제 하면 Azure 센티널에서 삭제 프로세스의 첫 번째 단계를 완료 하는 데 최대 48 시간이 소요 됩니다.

연결이 끊어진 후에는 오프 보 딩 프로세스가 시작 됩니다.

**이러한 커넥터의 구성이 삭제 됩니다.**
-   PowerApps

-   AWS

-   Microsoft 서비스 보안 경고 (Cloud Discovery 섀도 IT 보고, Azure AD ID 보호, Microsoft Defender ATP, Azure Security Center)를 포함 한 Microsoft Cloud App Security Azure ATP

-   위협 인텔리전스

-   일반적인 보안 로그 (CEF 기반 로그, Barracuda 및 Syslog 포함) (Azure Security Center 경우 이러한 로그는 계속 수집 됩니다.)

-   Windows 보안 이벤트 (Azure Security Center 경우 이러한 로그는 계속 수집 됩니다.)

처음 48 시간 이내에 데이터 및 경고 규칙 (실시간 자동화 구성 포함)은 더 이상 Azure 센티널에서 액세스 하거나 쿼리할 수 없습니다.

**30 일 후에는 다음 리소스를 삭제 합니다.**

-   인시던트 (조사 메타 데이터 포함)

-   경고 규칙

-   내부의

플레이 북, 저장 된 통합 문서, 저장 된 구하기 쿼리 및 노트북은 삭제 되지 않습니다. **제거 된 데이터로 인해 일부는 중단 될 수 있습니다. 수동으로 제거할 수 있습니다.**

서비스를 제거 하 고 나면 30 일간의 유예 기간이 표시 됩니다 .이 기간 동안에는 솔루션을 다시 사용 하도록 설정 하 고 데이터 및 경고 규칙이 복원 되지만, 연결이 끊어진 구성 된 커넥터는 다시 연결 해야 합니다.

> [!NOTE]
> 솔루션을 제거 하는 경우 구독은 Azure 센티널 리소스 공급자에 계속 등록 됩니다. **수동으로 제거할 수 있습니다.**




## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 센티널 서비스를 제거 하는 방법을 배웠습니다. 사용자의 점을 변경 하 고 다시 설치 하려면 다음을 수행 합니다.
- [Azure 센티널을 온 보 딩](quickstart-onboard.md)하기 시작 합니다.

