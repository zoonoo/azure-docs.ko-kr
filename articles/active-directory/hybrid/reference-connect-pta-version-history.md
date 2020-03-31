---
title: 'Azure AD 통과 인증: 버전 릴리스 기록 | 마이크로 소프트 문서'
description: 이 문서에서는 Azure AD 통과 인증 에이전트의 모든 릴리스를 나열합니다.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 11/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c5d0efe8e662544dc69356c6b17dd7eca6f3a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786455"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD 통과 인증 에이전트: 버전 릴리스 기록 
 
통과 인증을 사용하도록 설정하는 온-프레미스에 설치된 에이전트는 새로운 기능을 제공하기 위해 정기적으로 업데이트됩니다. 이 문서에서는 새 기능이 도입될 때 추가되는 버전과 기능을 나열합니다. 통과 인증 에이전트는 새 버전이 릴리스되면 자동으로 업데이트됩니다. 

관련 항목은 다음과 같습니다. 

- [Azure AD 통과 인증을 사용 하 인 하는 사용자 로그인](how-to-connect-pta.md) 
- [Azure AD 통과 인증 에이전트 설치](how-to-connect-pta-quick-start.md) 



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>릴리스 상태 
2019년 1월 22일: 다운로드용으로 출시됨  
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 
- 서비스 버스 신뢰할 수 있는 채널에 대한 지원이 추가되어 아웃바운드 연결에 대한 또 다른 연결 복원력 계층을 추가했습니다. 
- 에이전트 등록 중 TLS 1.2 적용 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>릴리스 상태 
2018년 4월 10일: 다운로드용으로 출시됨  
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 
- 웹 소켓 연결 지원 
- 에이전트의 기본 프로토콜로 TLS 1.2 설정 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>릴리스 상태 
2018년 1월 31일: 다운로드용으로 출시됨  
### <a name="fixed-issues"></a>해결된 문제 

- 에이전트에서 메모리 누수의 원인이 되는 버그를 수정했습니다. 
- 커넥터 시간 시간 지정 문제에 대한 버그 수정이 포함된 Azure Service Bus 버전을 업데이트했습니다. 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>릴리스 상태 
2017년 11월 26일: 다운로드용으로 출시됨  
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 
- 에이전트와 Azure AD 서비스 간의 웹 소켓 기반 연결에 대한 지원이 추가되어 연결 복원력을 향상시켰습니다. 

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>릴리스 상태 
2017년 11월 25일: 다운로드용으로 출시됨  
### <a name="fixed-issues"></a>해결된 문제 
- 기본 프록시 시나리오에 대 한 DNS 캐시와 관련 된 버그를 수정 했습니다. 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>릴리스 상태 
2017년 10월 17일: 다운로드용으로 출시됨  
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 
- DNS 오류에서 복원력을 추가 하기 위해 아웃바운드 연결에 대 한 DNS 캐시 기능을 추가 했습니다. 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>릴리스 상태 
2017/08/31: 다운로드용으로 출시됨  
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 
- AZURE AD 통과 인증 에이전트의 GA 버전 

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 통과 인증으로 사용자 로그인](how-to-connect-pta.md)