---
title: Azure Security Center (인시던트)의 클라우드 스마트 경고 상관 관계 | Microsoft Docs
description: 이 항목에서는 fusion Azure Security Center에서 클라우드 스마트 경고 상관 관계를 사용 하 여 보안 인시던트를 생성 하는 방법에 대해 설명 합니다.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 7ba2cf14d9fac100f44a1ef23997b27ba062bee0
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295856"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Azure Security Center의 클라우드 스마트 경고 상관 관계 (인시던트)

Security Center는 고급 분석 및 위협 인텔리전스를 사용 하 여 하이브리드 클라우드 워크 로드를 지속적으로 분석 하 여 악의적인 활동을 경고 합니다.

위협 범위 수가 증가 하 고 손상 된 slightest 표시도 검색 해야 하는 경우 보안 분석가가 다른 경고를 심사 하 고 실제 공격을 식별 하는 것이 어려울 수 있습니다. Security Center는 다른 경고와 낮은 충실도 신호를 보안 인시던트에 상관 관계를 사용 하 여 경고 피로 및 진단 공격에 대처 하는 데 도움이 됩니다.

Fusion은 인시던트를 Security Center 하 여 서로 다른 경고와 상황별 신호를 서로 연결할 수 있도록 하는 기술 및 분석 백 엔드입니다. 퓨전은 리소스의 구독에 대해 보고 된 여러 신호를 확인 하 고, 공격 진행 상황을 표시 하는 널리 사용 되는 패턴 또는 통합 응답 절차를 나타내는 공유 컨텍스트 정보를 사용 하는 신호를 찾는 방식으로 작동 합니다. 사용 됩니다.

Fusion analytics는 보안 도메인 지식을 AI와 결합 하 여 경고를 분석 하 고 발생 하는 새로운 공격 패턴을 검색 합니다. 

Security Center는 MITRE 공격 매트릭스를 활용 하 여 경고를 인식 된 도와 연결 하 고 보안 도메인 지식을 공식화 하는 데 도움을 줍니다. 또한 공격의 각 단계에 대해 수집 된 정보를 사용 하 여 공격의 단계로 표시 되는 활동을 규칙 할 수 Security Center 있지만이는 그렇지 않습니다.  

공격은 종종 서로 다른 테 Security Center 넌 트에서 발생 하므로 AI 알고리즘을 결합 하 여 각 구독에 대해 보고 되는 공격 순서를 분석 하 여 각 구독에 대 한 일반적인 경고 패턴으로 확인할 수 있습니다. 다른.

인시던트를 조사 하는 동안 분석가는 위협에 대 한 특성 및이를 완화 하는 방법에 대 한 verdict에 도달 하는 데 추가 컨텍스트가 필요 하기도 합니다. 예를 들어 네트워크에서 문제가 감지 되어도 네트워크에서 발생 하는 다른 작업을 이해 하거나 대상 리소스와 관련 하 여 다른 작업을 수행 하는 작업은 이해 하기 어렵습니다. 이를 돕기 위해 보안 인시던트에는 아티팩트, 관련 이벤트 및 정보가 포함 될 수 있습니다. 보안 인시던트에 사용할 수 있는 추가 정보는 검색 된 위협 유형 및 사용자 환경의 구성에 따라 달라 집니다. 

![보안 인시던트 정보](./media/security-center-alerts-cloud-smart/security-incident.png)

보안 인시던트를 더 잘 이해 하려면 [Azure Security Center에서 보안 인시던트를 처리 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-incident)을 참조 하세요.

