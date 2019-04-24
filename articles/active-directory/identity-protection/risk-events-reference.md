---
title: Azure Active Directory ID Protection 위험 이벤트 참조 | Microsoft Docs
description: Azure Active Directory ID Protection 위험 이벤트 참조를 소개합니다.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d009022d4ec03990d3ed0321ebcb13c72c674ed8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60294211"
---
# <a name="azure-active-directory-identity-protection-risk-events-reference"></a>Azure Active Directory ID Protection 위험 이벤트 참조

대부분의 보안 침해는 공격자가 사용자의 ID를 도용하여 환경에 대한 액세스 권한을 얻을 때 발생합니다. 손상된 ID를 검색하는 것은 쉬운 작업이 아닙니다. Azure Active Directory는 적응 기계 학습 알고리즘 및 추론을 사용하여 사용자의 계정과 관련된 의심스러운 작업을 검색합니다. 검색된 각 의심스러운 동작은 위험 이벤트라는 레코드에 저장됩니다.


## <a name="anonymous-ip-address"></a>익명 IP 주소

**검색 유형:** 실시간  
**이전 이름:** 익명 IP 주소에서 로그인


이 위험 이벤트 유형은 Tor 브라우저, 익명성 도구 VPN 등의 익명 IP 주소에서 수행하는 로그인을 나타냅니다.
악의적일 수 있는 의도로 IP 주소, 위치, 디바이스 등의 로그인 원격 분석 정보를 숨기려는 사용자가 대개 이러한 IP 주소를 사용합니다.


## <a name="atypical-travel"></a>비정상적 이동

**검색 유형:** 오프라인  
**이전 이름:** 비정상적 위치 간 이동 불가능


이 위험 이벤트 유형은 지역적으로 떨어진 위치에서 시작한 두 번의 로그인을 식별합니다. 과거 동작을 고려하면 이 위치 중 하나 이상이 사용자에 대해 불규칙적입니다. 다른 요인 중에서 이 Machine Learning 알고리즘은 두 번의 로그인 간 시간과 사용자가 첫 번째 위치에서 두 번째 위치로 이동하는 데 걸리는 시간을 고려하여 서로 다른 사용자가 동일한 자격 증명을 사용하고 있음을 나타냅니다.

이 알고리즘은 조직의 다른 사용자가 정기적으로 사용하는 VPN 및 위치와 같은 불가능한 이동 조건에 영향을 주는 확실한 "가양성"을 무시합니다. 시스템은 초기 학습 기간(14일 또는 로그인 10회 중 먼저 도래하는 날짜) 동안 새 사용자의 로그인 동작을 학습합니다.


## <a name="leaked-credentials"></a>유출된 자격 증명

**검색 유형:** 오프라인  
**이전 이름:** 자격 증명이 유출된 사용자


이 위험 이벤트 유형은 사용자의 유효한 자격 증명이 유출되었음을 나타냅니다.
사이버 범죄자가 합법적인 사용자의 유효한 암호를 손상시키는 경우 범죄자는 종종 이러한 자격 증명을 공유합니다. 보통 이러한 유출은 불법 웹 사이트나 붙여넣기 사이트에 자격 증명을 공개적으로 게시하거나, 암시장에서 자격 증명을 거래 또는 판매하는 방식으로 이루어집니다. Microsoft 유출된 자격 증명 서비스는 공개 및 Dark 웹 사이트를 모니터링하고 다음 대상과 협력하여 사용자 이름/암호 쌍을 획득합니다.

- 연구원

- 사법 기관

- Microsoft 보안 팀

- 신뢰할 수 있는 기타 소스

서비스는 불법 웹 사이트, 붙여넣기 사이트 또는 위에서 설명한 출처에서 사용자 자격 증명을 확보하면 Azure AD 사용자의 유효한 현재 자격 증명과 비교 대조하여 일치하는 항목을 찾습니다.


## <a name="malware-linked-ip-address"></a>맬웨어 연결 IP 주소

**검색 유형:** 오프라인  
**이전 이름:** 감염된 디바이스에서의 로그인


이 위험 이벤트 유형은 봇 서버와 실제로 통신하는 것으로 확인된 맬웨어에 감염된 IP 주소에서 수행하는 로그인을 나타냅니다. 봇 서버가 활성 상태인 동안 해당 서버와 연결되어 있던 IP 주소와 사용자 디바이스의 IP 주소 상관 관계를 설정하는 방식으로 이러한 로그인을 확인합니다.


## <a name="unfamiliar-sign-in-properties"></a>익숙하지 않은 로그인 속성

**검색 유형:** 실시간  
**이전 이름:** 일반적이지 않은 위치에서의 로그인

이 위험 이벤트 유형은 이전 로그인 기록을 고려 (IP, 위도 / 경도 및 ASN) anomalous sign ins를 찾도록 합니다. 시스템은 사용자가 사용한 이전 위치에 대한 정보를 저장하고 이러한 "익숙한" 위치를 고려합니다. 로그인이 익숙한 위치 목록에 없는 위치에서 발생하는 경우 위험 이벤트가 트리거됩니다. 새로 만든된 사용자 됩니다 "학습 모드" 기간에 대 한 알 수 없는 로그인 속성에서 위험 이벤트 해제 됩니다 있지만 당사 알고리즘, 사용자의 동작에 알아봅니다. 학습 모드 기간 동적 및 종속 사용자의 로그인 패턴에 대 한 충분 한 정보를 수집 하는 알고리즘에 얼마나 많은 시간이 걸리는 합니다. 최소 기간은 5 일입니다. 사용자 모드 긴 비활성 기간 후 학습으로 돌아와서 수 있습니다. 또한 시스템은 익숙한 디바이스 및 익숙한 위치에 지리적으로 가까운 위치에서 시도한 로그인을 무시합니다. 

기본 인증이나 레거시 프로토콜의 경우에도 이 검색이 실행됩니다. 이러한 프로토콜에는 클라이언트 ID와 같은 최신 속성이 없으므로 가양성을 줄일 수 있는 원격 분석 정보가 제한적으로만 제공됩니다. 따라서 고객은 최신 인증 방식으로 이전하는 것이 좋습니다.

