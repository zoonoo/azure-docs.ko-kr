---
title: Azure Security Center (인시던트)에서 스마트 경고 상관 관계 클라우드 | Microsoft Docs
description: 이 항목에서는 Azure Security Center에서 fusion 사용 하 여 클라우드 스마트 경고 상관 관계 보안 인시던트를 생성 하는 방법을 설명 합니다.
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
ms.author: monhaber
ms.openlocfilehash: 2ab4dab8cb7729b0c2ca023f22066f7b5d166a02
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571778"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Azure Security Center (인시던트)에서 스마트 경고 상관 관계 클라우드

Security Center는 악의적인 활동에 대 한 고급 분석 및 위협 인텔리전스를 사용 하 여 하이브리드 클라우드 워크 로드를 지속적으로 분석 합니다.

광범위 한 위협 검사 증가 한 갖지 표시도 검색 필요가 증가 손상 다양 한 경고를 심사 하 고 실제 공격을 식별 하려면 보안 분석가에 게 어려운 일일 수 있습니다. Security Center 경고 피로 발생 했을 때 공격을 진단 하 고 대처 하는 분석가 통해 보안 인시던트를 다양 한 경고 및 낮은 충실도 신호를 상호 연결 하 여 합니다.

Fusion은 기술 및 분석 백 엔드 요구 사항은 보안 센터 인시던트를 다른 경고와 함께 하는 상황에 맞는 신호를 서로 연결할 수 있도록 합니다. 구독, 리소스에서 서로 다른 신호를 살펴보면 융해 작동 보고 및 공격 진행을 표시 하거나 통합된 응답 프로시저를 나타내는 공유 컨텍스트 정보를 사용 하 여 신호는 널리 알려진 패턴을 찾고 해야 에 가져옵니다.

경고를 발생 하는 새로운 공격 패턴을 검색 분석에 AI를 사용 하 여 보안 도메인 지식을 결합 하는 fusion 분석 합니다. 

Security Center는 보안 도메인 기술 자료를 공식화 하는 데 도움이 인식된 의도 사용 하 여 경고를 연결할 MITRE 공격 행렬을 활용 합니다. 또한 공격의 각 단계에 대해 수집 된 정보를 사용 하 여 Security Center 제외할 수는 공격 단계 것으로 나타나지만 아닙니다 하는 작업입니다.  

Security Center 각 우연 하 게 연결 하는 대신 경고 널리 알려진 패턴으로 식별할 수에 각 구독에 보고 되는 공격 시퀀스 분석에 AI 알고리즘을 결합할 수 있습니다 공격 종종 서로 다른 테 넌 트에서 발생 하므로 다른 합니다.

인시던트의 조사 중 분석가 종종 위협 및 완화 하는 방법에 대 한 verdict 연결할 추가 컨텍스트가 필요 합니다. 예를 들어 네트워크가 변칙적 감지 되 면 경우에 네트워크 또는 대상된 리소스와 관련 하 여 발생 하는 다른 상황을 알 수 없는 것 수행한 다음 작업을 이해 하기 어려운 합니다. 를 위해 보안 인시던트는 아티팩트, 관련된 이벤트 및 정보를 포함할 수 있습니다. 보안 인시던트에 대 한 추가 정보가 위협 검색 유형 및 사용자 환경의 구성에 따라 달라 집니다. 

![보안 인시던트 세부 정보](./media/security-center-alerts-cloud-smart/security-incident.png)

보안 인시던트를 더 잘 이해 하려면 참조 [Azure Security Center에서 보안 인시던트를 처리 하는 방법을](https://docs.microsoft.com/azure/security-center/security-center-incident)합니다.

