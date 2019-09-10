---
title: '프로 비전 에이전트 Azure AD Connect: 버전 릴리스 내역 | Microsoft Docs'
description: 이 문서에서는 Azure AD Connect 프로 비전 에이전트의 모든 릴리스를 나열 하 고 새로운 기능 및 해결 된 문제에 대해 설명 합니다.
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: de6ef5dedb0779536cfbc1e6a3440e748ff78524
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862110"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>프로 비전 에이전트 Azure AD Connect: 버전 릴리스 내역
이 문서에서는 릴리스된 Azure AD Connect 프로 비전 에이전트의 버전 및 기능을 나열 합니다. Azure AD 팀은 새로운 기능과 기능으로 프로 비전 에이전트를 정기적으로 업데이트 합니다. 프로 비전 에이전트는 새 버전이 릴리스되면 자동으로 업데이트 됩니다. 

최신 기능과 버그 수정이 있는지 확인 하기 위해 에이전트에 대해 자동 업데이트를 사용 하도록 설정 하는 것이 좋습니다. Microsoft는 최신 에이전트 버전 및 이전 버전을 직접 지원 합니다.

## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>릴리스 상태

2019 년 9 월 9 일: 자동 업데이트를 위해 릴리스 됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* 프로 비전 에이전트 문제를 디버깅 하기 위해 추가 추적 및 로깅을 구성 하는 기능
* 동기화 성능을 향상 시키기 위해 매핑에 구성 된 AD 특성만 페치할 수 있습니다.

### <a name="fixed-issues"></a>해결된 문제

* AD 연결 실패와 관련 된 문제가 있는 경우 에이전트가 응답 하지 않는 상태로 전환 된 버그를 수정 했습니다.
* Active Directory에서 이진 데이터를 읽을 때 문제를 일으킨 버그를 수정 했습니다.
* 에이전트가 클라우드 하이브리드 Id 서비스와의 트러스트를 갱신 하지 못한 버그를 수정 했습니다.

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>릴리스 상태

1 월 23 일: 2019: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* 개선 된 프로 비전 에이전트 & 커넥터 아키텍처를 통해 더 나은 성능, 안정성 및 안정성을 제공 합니다. 
* UI 기반 설치 마법사를 사용 하 여 간소화 된 프로 비전 에이전트 구성 
* 자동 에이전트 업데이트에 대 한 지원 추가

