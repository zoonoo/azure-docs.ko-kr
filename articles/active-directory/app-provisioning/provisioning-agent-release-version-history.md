---
title: 'Azure AD 연결 프로비전 에이전트: 버전 릴리스 기록 | 마이크로 소프트 문서'
description: 이 문서에서는 Azure AD Connect 프로비전 에이전트의 모든 릴리스를 나열하고 새로운 기능 및 수정된 문제에 대해 설명합니다.
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
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 559bca4f5020cebe06be7f24f7af5ec2e94bec0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183247"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD 연결 프로비전 에이전트: 버전 릴리스 기록
이 문서에서는 릴리스된 Azure Active Directory Connect 프로비전 에이전트의 버전 및 기능을 나열합니다. Azure AD 팀은 프로비저닝 에이전트를 정기적으로 새로운 기능과 기능을 업데이트합니다. 새 버전이 릴리스되면 프로비저닝 에이전트가 자동으로 업데이트됩니다. 

Microsoft는 이전에 최신 에이전트 버전과 한 버전에 대한 직접 지원을 제공합니다.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>릴리스 상태

2019년 12월 4일: 다운로드용으로 출시됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* 온-프레미스 활성 디렉터리에서 Azure AD로 사용자, 연락처 및 그룹 데이터를 동기화하는 [Azure AD Connect 클라우드 프로비저닝에](../cloud-provisioning/what-is-cloud-provisioning.md) 대한 지원이 포함되어 있습니다.


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>릴리스 상태

2019년 9월 9일: 자동 업데이트를 위해 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* 프로비저닝 에이전트 문제를 디버깅하기 위한 추가 추적 및 로깅을 구성할 수 있는 기능
* 동기화 성능을 향상시키기 위해 매핑에 구성된 Azure AD 특성만 가져올 수 있는 기능

### <a name="fixed-issues"></a>해결된 문제

* Azure AD 연결 실패에 문제가 있는 경우 에이전트가 응답하지 않는 상태로 전환된 버그를 수정했습니다.
* Azure Active Directory에서 이진 데이터를 읽을 때 문제를 일으키는 문제를 수정했습니다.
* 에이전트가 클라우드 하이브리드 ID 서비스와 신뢰를 갱신하지 못하던 버그를 수정했습니다.

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>릴리스 상태

2019년 1월 23일: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* 성능, 안정성 및 안정성을 높이기 위해 프로비저닝 에이전트 및 커넥터 아키텍처를 개선했습니다. 
* UI 기반 설치 마법사를 사용하여 프로비저닝 에이전트 구성 간소화 
* 자동 에이전트 업데이트에 대한 추가 지원

