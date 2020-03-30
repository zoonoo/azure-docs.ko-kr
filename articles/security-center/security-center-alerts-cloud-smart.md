---
title: Azure 보안 센터 인시던트 - 경고의 스마트 상관 관계
description: 이 항목에서는 Fusion이 클라우드 스마트 경고 상관 관계를 사용하여 Azure 보안 센터에서 보안 인시던트를 생성하는 방법을 설명합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: b26f0bab073ce248ca23bb8a815fa3e293ddba51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73686482"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Azure 보안 센터의 클라우드 스마트 경고 상관 관계(인시던트)

Azure Security Center는 고급 분석 및 위협 인텔리전스를 사용하여 하이브리드 클라우드 워크로드를 지속적으로 분석하여 악의적인 활동에 대해 경고합니다.

위협 범위의 폭이 넓어지고 있습니다. 사소한 손상도 감지해야 하는 것이 중요하며 보안 분석가가 다양한 경고를 분류하고 실제 공격을 식별하는 것이 어려울 수 있습니다. 보안 센터는 분석가가 이러한 경고 피로에 대처할 수 있도록 지원합니다. 다양한 경고와 낮은 충실도 신호를 보안 인시던트와 상호 연관시켜 공격이 발생할 때 이를 진단하는 데 도움이 됩니다.

Fusion 분석은 보안 센터 인시던트를 지원하는 기술 및 분석 백 엔드로, 서로 다른 경고 및 컨텍스트 신호를 함께 상호 연관시킬 수 있습니다. Fusion은 리소스 전체의 구독에 보고된 다양한 신호를 살펴봅니다. Fusion은 공유 컨텍스트 정보가 있는 공격 진행 또는 신호를 표시하는 패턴을 찾아 통합 응답 프로시저를 사용해야 한다는 것을 나타냅니다.

Fusion 분석은 보안 도메인 지식과 AI를 결합하여 경고를 분석하여 새로운 공격 패턴이 발생할 때 발견합니다. 

보안 센터는 MITRE Attack Matrix를 활용하여 경고를 인식된 의도와 연결하여 보안 도메인 지식을 공식화하는 데 도움을 줍니다. 또한 보안 센터는 공격의 각 단계에 대해 수집된 정보를 사용하여 공격의 단계로 보이지만 실제로는 그렇지 않은 활동을 배제할 수 있습니다.

보안 센터는 여러 테넌에서 공격이 자주 발생하기 때문에 AI 알고리즘을 결합하여 각 구독에 보고된 공격 시퀀스를 분석할 수 있습니다. 이 기술은 공격 시퀀스를 부수적으로 서로 연관되는 것이 아니라 널리 사용되는 경고 패턴으로 식별합니다.

분석가는 사고를 조사하는 동안 위협의 특성과 이를 완화하는 방법에 대한 판결에 도달하기 위해 추가 컨텍스트가 필요한 경우가 많습니다. 예를 들어 네트워크 변칙이 감지되더라도 네트워크에서 어떤 일이 일어나고 있는지 또는 대상 리소스와 관련하여 어떤 일이 발생하는지 이해하지 못하면 다음에 수행할 작업을 이해하기가 어렵습니다. 보안 인시던트에는 아티팩트, 관련 이벤트 및 정보가 포함될 수 있습니다. 보안 인시던트에 사용할 수 있는 추가 정보는 검색된 위협 유형 및 사용자 환경의 구성에 따라 다릅니다. 

![보안 인시던트 감지 보고서 스크린샷](./media/security-center-alerts-cloud-smart/security-incident.png)

보안 인시던트를 더 잘 이해하려면 [Azure 보안 센터에서 보안 인시던트를 처리하는 방법을](https://docs.microsoft.com/azure/security-center/security-center-incident)참조하세요.

