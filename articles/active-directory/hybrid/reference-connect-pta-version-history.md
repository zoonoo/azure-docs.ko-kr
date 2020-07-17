---
title: 'Azure AD 통과 인증: 버전 릴리스 내역 | Microsoft Docs'
description: 이 문서는 Azure AD 통과 인증 에이전트의 모든 릴리스를 나열 합니다.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e5e6afef70ee3d4f4d84cb6b867a916ae57da5a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85356307"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD 통과 인증 에이전트: 버전 릴리스 기록 
 
통과 인증을 사용 하는 온-프레미스에 설치 된 에이전트는 새로운 기능을 제공 하기 위해 정기적으로 업데이트 됩니다. 이 문서에는 새로운 기능이 도입 될 때 추가 되는 버전 및 기능이 나열 되어 있습니다. 통과 인증 에이전트는 새 버전이 릴리스되면 자동으로 업데이트 됩니다. 

관련 항목은 다음과 같습니다. 

- [Azure AD 통과 인증을 사용 하 여 사용자 로그인](how-to-connect-pta.md) 
- [Azure AD 통과 인증 에이전트 설치](how-to-connect-pta-quick-start.md) 

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>릴리스 상태: 
2020년 4월 9일: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

- 설치 시 클라우드 환경을 대상으로 지정 하기 위한 지원이 추가 되었습니다. 번들은 지정 된 클라우드 환경에 고정 될 수 있습니다.



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>릴리스 상태 
1/22/2019: 다운로드를 위해 릴리스 됨  
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 
- 아웃 바운드 연결에 대 한 연결 복원 력을 다른 계층을 추가 하 Service Bus 신뢰할 수 있는 채널에 대 한 지원 추가 
- 에이전트를 등록 하는 동안 TLS 1.2 적용 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>릴리스 상태 
4/10/2018: 다운로드를 위해 릴리스 됨  
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 
- 웹 소켓 연결 지원 
- TLS 1.2을 에이전트의 기본 프로토콜로 설정 합니다. 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>릴리스 상태 
1/31/2018: 다운로드를 위해 릴리스 됨  
### <a name="fixed-issues"></a>해결된 문제 

- 에이전트에서 약간의 메모리 누수를 일으킨 버그를 수정 했습니다. 
- 커넥터 시간 제한 문제에 대 한 버그 수정을 포함 하는 Azure Service Bus 버전이 업데이트 되었습니다. 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>릴리스 상태 
11/26/2017: 다운로드를 위해 릴리스 됨  
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 
- 연결 복원 력을 개선 하기 위해 에이전트와 Azure AD 서비스 간의 websocket 기반 연결에 대 한 지원이 추가 됨 

## <a name="154020"></a>1.5.402.0이 
### <a name="release-status"></a>릴리스 상태 
11/25/2017: 다운로드를 위해 릴리스 됨  
### <a name="fixed-issues"></a>해결된 문제 
- 기본 프록시 시나리오에 대 한 DNS 캐시와 관련 된 버그 수정 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>릴리스 상태 
10/17/2017: 다운로드를 위해 릴리스 됨  
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 
- DNS 오류에서 복원 력을 추가 하기 위해 아웃 바운드 연결에 대 한 DNS 캐시 기능이 추가 됨 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>릴리스 상태 
08/31/2017: 다운로드를 위해 릴리스 됨  
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 
- Azure AD 통과 인증 에이전트의 GA 버전 

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 통과 인증으로 사용자 로그인](how-to-connect-pta.md)
