---
title: 위협 인텔리전스 & 보안 경고 맵 - Azure 보안 센터
description: Azure Security Center에서 보안 경고 맵 및 위협 인텔리전스 기능을 사용하여 VM 및 컴퓨터의 잠재적 위협을 식별하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 39835d9d73adcbe474d3b70dfced313e18d1a3b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603424"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>보안 경고 맵 및 위협 인텔리전스
이 문서에서는 Azure Security Center 보안 경고 맵과 보안 이벤트 기반 위협 인텔리전스 맵을 사용하여 보안 관련 문제를 해결하는 데 도움이 됩니다.

> [!NOTE]
> 보안 *이벤트* 맵 버튼은 2019년 7월 31일에 사용 중지되었습니다. 자세한 정보 및 대체 서비스는 [보안 센터 사용 중지 기능(2019년 7월)을](security-center-features-retirement-july2019.md#menu_securityeventsmap)참조하십시오.


## <a name="how-the-security-alerts-map-works"></a>보안 경고 맵이 작동하는 방법
Security Center는 환경에 대한 보안 위협을 식별하는 데 도움이 되는 맵을 제공합니다. 예를 들어 특정 컴퓨터가 봇네트에 속해 있는지 여부와 위협이 제공되는 위치를 확인할 수 있습니다. 공격자가 봇네트를 관리하는 명령 및 제어와 몰래 상호 작용하는 맬웨어를 불법적으로 설치하면 컴퓨터가 봇네트의 노드가 될 수 있습니다. 

Security Center는 이 맵을 작성하기 위해 Microsoft 내의 여러 소스에서 제공되는 데이터를 사용합니다. Security Center는 이 데이터를 사용하여 사용자 환경에 대한 잠재적 위협을 매핑합니다. 

[보안 인시던트 대응 프로세스](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) 단계 중 하나는 손상된 시스템의 심각도를 식별하는 것입니다. 이 단계에서는 다음 작업을 수행해야 합니다.

- 공격 특성 확인
- 공격 원점 확인
- 공격 의도 확인 특정 정보를 얻기 위해 사용자 조직을 노린 공격입니까, 아니면 무작위 공격입니까?
- 손상된 시스템 식별
- 액세스된 파일 식별 및 해당 파일의 중요도 확인

Security Center에서 보안 경고 맵을 사용하여 이러한 작업을 지원할 수 있습니다.

## <a name="access-the-security-alerts-map"></a>보안 경고 맵 액세스
환경의 현재 위협을 시각화하려면 보안 경고 맵을 엽니다.

1. **Security Center** 대시보드를 엽니다.
2. 왼쪽 창의 **위협 보호** 아래에서 **보안 경고 맵**을 선택합니다. 맵이 열립니다.
3. 경고에 대한 자세한 정보를 얻고 수정 단계를 받으려면 맵에서 경고 점을 클릭하고 지침을 따릅니다. 
 
보안 경고 맵은 경고를 기반으로 합니다. 이러한 경고는 IP 주소가 알려진 위험한 IP 주소(예: 알려진 암호 화폐 채굴기(cryptominer))인지 또는 이전에 위험한 것으로 인식되지 않은 IP 주소인지 여부에 관계없이 네트워크 통신이 성공적으로 확인된 IP 주소와 연결된 활동을 기반으로 합니다. 맵은 Azure에서 이전에 선택한 모든 구독에 대해 알림을 제공합니다. 

맵의 경고는 발생 원점으로 감지된 지리적 위치에 따라 표시되며, 심각도별 색 코드로 지정되어 있습니다. 
    ![위협 인텔리전스 보고서](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>참조
이 문서에서는 의심스러운 활동을 식별하기 위해 Security Center에서 위협 인텔리전스를 사용하는 방법을 살펴보았습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 보안 센터에서 보안 경고를 관리하고 응답합니다.](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Security Center에서 경고를 관리하고 보안 인시던트에 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md). Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). 다양한 유형의 보안 경고에 대해 알아봅니다.
* [Azure 보안 센터 문제 해결 가이드.](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide) Security Center의 일반적인 문제를 해결하는 방법에 대해 알아봅니다.