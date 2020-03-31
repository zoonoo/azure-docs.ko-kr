---
title: 'Azure AD 연결: 설치 중 소스 앵커 문제 해결 | 마이크로 소프트 문서'
description: 이 항목에서는 설치 하는 동안 소스 앵커문제를 해결 하는 방법에 대 한 단계를 제공 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fac33a01afc2efc1ab06c4783c11f7a089bb6208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "62114158"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>설치 중 소스 앵커 문제 해결
이 문서에서는 설치 중에 발생할 수 있는 다양한 소스 앵커 관련 문제에 대해 설명하고 이러한 문제를 해결할 수 있는 방법을 제공합니다.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Azure 활성 디렉터리에서 잘못된 원본 앵커

### <a name="custom-installation"></a>사용자 지정 설치

사용자 지정 설치 하는 동안 Azure AD Connect Azure Active Directory에서 원본 앵커 정책을 읽습니다. Azure Active Directory에 정책이 있는 경우 Azure AD Connect는 고객이 재정의하지 않는 한 동일한 정책을 적용합니다. 마법사는 읽은 특성을 알려줍니다. 또한 마법사는 소스 앵커 정책을 재정의하려고 하면 경고합니다.

이 읽기 작업 중에 Azure Active Directory의 원본 앵커 정책이 예기치 않은 일 수 있습니다. 이 경우 Azure AD Connect는 소스 앵커가 무엇을 사용할지 알지 못하며 수동 재정의가 필요합니다.</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

이 문제를 해결하려면 특정 특성을 선택하여 소스 앵커를 수동으로 재정의할 수 있습니다. 선택할 특성이 확실한 경우에만 이 옵션을 계속 진행합니다. 확실하지 않은 경우 [Microsoft 지원팀에](https://support.microsoft.com/contactus/) 문의하여 지침을 확인하십시오. 원본 앵커 정책을 변경하면 온-프레미스 사용자와 관련 Azure 리소스 간의 연결이 끊어질 수 있습니다.</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>기본 설치
빠른 설치 중에 Azure AD Connect는 Azure Active Directory에서 원본 앵커 정책을 읽습니다. Azure Active Directory에 정책이 있는 경우 Azure AD Connect는 동일한 정책을 적용합니다. 수동 재정의를 수행할 수 있는 옵션은 없습니다.

이 읽기 작업 중에 Azure Active Directory의 원본 앵커 정책이 예기치 않은 일 수 있습니다. 이 경우 Azure AD Connect는 원본 앵커가 무엇인지 알지 못합니다.</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

이 문제를 해결하려면 사용자 지정 모드를 사용하여 다시 설치하고 특정 특성을 선택하여 소스 앵커를 수동으로 재정의해야 합니다. 선택할 특성이 확실한 경우에만 이 옵션을 계속 진행합니다. 확실하지 않은 경우 [Microsoft 지원팀에](https://support.microsoft.com/contactus/) 문의하여 지침을 확인하십시오. 원본 앵커 정책을 변경하면 온-프레미스 사용자와 관련 Azure 리소스 간의 연결이 끊어질 수 있습니다.

### <a name="invalid-source-anchor-in-sync-engine"></a>동기화 엔진에서 잘못된 소스 앵커
설치하는 동안 Azure AD Connect가 잘못된 소스 앵커를 사용하여 동기화 엔진을 구성하려고 시도할 수 있습니다. 이 작업은 제품 문제일 가능성이 높으며 Azure AD Connect 설치가 실패할 수 있습니다. 이 문제를 실행하는 경우 [Microsoft 지원에](https://support.microsoft.com/contactus/) 문의하십시오.</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.