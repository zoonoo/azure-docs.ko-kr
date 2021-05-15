---
title: 'Azure AD Connect: 설치 중 원본 앵커 문제 해결 | Microsoft Docs'
description: 이 항목에서는 설치 중에 원본 앵커와 관련된 문제를 해결하는 방법에 대한 단계를 제공합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 4e6460015430850c11fbf70a005d7440ce1b92fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91306007"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>설치 중 원본 앵커 문제 해결
이 문서에서는 설치 중에 발생할 수 있는 다양한 원본 앵커 관련 문제를 설명하고 이러한 문제를 해결하는 방법을 제공합니다.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Azure Active Directory의 잘못된 원본 앵커

### <a name="custom-installation"></a>사용자 지정 설치

사용자 지정 설치 중에 Azure AD Connect는 Azure Active Directory에서 원본 앵커 정책을 읽습니다. Azure Active Directory에 정책이 있는 경우 Azure AD Connect는 고객이 재정의하지 않는 한 동일한 정책을 적용합니다. 마법사가 읽은 특성을 알려줍니다. 또한 원본 앵커 정책을 재정의하려고 하면 마법사가 경고합니다.

이 읽기 작업 중에 Azure Active Directory의 원본 앵커 정책이 예기치 않게 발생할 수 있습니다. 이 경우 Azure AD Connect는 사용할 원본 앵커를 알지 못하므로 수동 재정의가 필요합니다.</br>
![원본 앵커를 수동으로 재정의할 위치를 보여 주는 스크린샷.](media/tshoot-connect-source-anchor/source1.png)

이 문제를 해결하기 위해 특정 특성을 선택하여 원본 앵커를 수동으로 재정의할 수 있습니다. 선택할 특성이 확실한 경우에만 이 옵션을 계속 진행합니다. 확실하지 않은 경우 지침은 [Microsoft 지원](https://support.microsoft.com/contactus/)에 문의하세요. 원본 앵커 정책을 변경하면 온-프레미스 사용자와 관련 Azure 리소스 간의 연결이 끊어질 수 있습니다.</br>
![원본 앵커를 재정의하는 지정된 특성을 보여주는 스크린샷.](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>기본 설치
기본 설치 중에 Azure AD Connect는 Azure Active Directory에서 원본 앵커 정책을 읽습니다. 정책이 Azure Active Directory에 있는 경우 Azure AD Connect도 동일한 정책을 적용합니다. 수동 재정의를 수행하는 옵션은 없습니다.

이 읽기 작업 중에 Azure Active Directory의 원본 앵커 정책이 예기치 않게 발생할 수 있습니다. 이 경우 Azure AD Connect는 원본 앵커가 무엇이어야 하는지 알지 못합니다.</br>
![Azure Active Directory의 원본 앵커가 예기치 않은 경우 발생하는 상황을 보여주는 스크린샷.](media/tshoot-connect-source-anchor/source3.png)

이 문제를 해결하려면 사용자 지정 모드를 사용하여 다시 설치하고 특정 특성을 선택하여 원본 앵커를 수동으로 재정의해야 합니다. 선택할 특성이 확실한 경우에만 이 옵션을 계속 진행합니다. 확실하지 않은 경우 지침은 [Microsoft 지원](https://support.microsoft.com/contactus/)에 문의하세요. 원본 앵커 정책을 변경하면 온-프레미스 사용자와 관련 Azure 리소스 간의 연결이 끊어질 수 있습니다.

### <a name="invalid-source-anchor-in-sync-engine"></a>동기화 엔진의 잘못된 원본 앵커
설치하는 동안 Azure AD Connect가 잘못된 원본 앵커를 사용하여 동기화 엔진을 구성하려고 시도할 수 있습니다. 이 작업은 제품 문제일 가능성이 높으며 Azure AD Connect 설치가 실패할 수 있습니다. 이 문제를 경험하는 경우 [Microsoft 지원](https://support.microsoft.com/contactus/)에 문의하세요.</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
