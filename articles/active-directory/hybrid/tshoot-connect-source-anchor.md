---
title: 'Azure AD Connect: 설치 하는 동안 원본 앵커 문제 해결 | Microsoft Docs'
description: 이 항목에서는 설치 중에 원본 앵커와 관련 된 문제를 해결 하는 방법에 대 한 단계를 제공 합니다.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306007"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>설치 하는 동안 원본 앵커 문제 해결
이 문서에서는 설치 중 발생할 수 있는 다양 한 원본 앵커 관련 문제를 설명 하 고 이러한 문제를 해결 하는 방법을 제공 합니다.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Azure Active Directory의 소스 앵커가 잘못 되었습니다.

### <a name="custom-installation"></a>사용자 지정 설치

사용자 지정 설치 중에 Azure AD Connect는 Azure Active Directory에서 원본 앵커 정책을 읽습니다. 정책이 Azure Active Directory에 있는 경우 고객에 의해 재정의 되지 않는 한, Azure AD Connect는 동일한 정책을 적용 합니다. 마법사에서 읽은 특성을 알려 줍니다. 또한 원본 앵커 정책을 재정의 하려고 하면 마법사에서 경고를 표시 합니다.

이 읽기 작업을 수행 하는 동안 Azure Active Directory의 소스 앵커 정책이 예기치 않게 될 수 있습니다. 이 경우 Azure AD Connect은 사용할 소스 앵커를 알지 않으며 수동으로 재정의 해야 합니다.</br>
![원본 앵커를 수동으로 재정의할 위치를 보여 주는 스크린샷](media/tshoot-connect-source-anchor/source1.png)

이 문제를 해결 하려면 특정 특성을 선택 하 여 원본 앵커를 수동으로 재정의할 수 있습니다. 선택할 특성을 확실히 알고 있는 경우에만이 옵션을 진행 합니다. 확실 하지 않은 경우 지침은 [Microsoft 지원](https://support.microsoft.com/contactus/) 에 문의 하세요. 원본 앵커 정책을 변경 하면 온-프레미스 사용자와 연결 된 Azure 리소스 간의 연결이 끊어질 수 있습니다.</br>
![원본 앵커를 재정의 하는 지정 된 특성을 보여 주는 스크린샷](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>기본 설치
Express를 설치 하는 동안 Azure AD Connect Azure Active Directory에서 원본 앵커 정책을 읽습니다. 정책이 Azure Active Directory에 있는 경우 Azure AD Connect는 동일한 정책을 적용 합니다. 수동 재정의를 수행할 수 있는 옵션은 없습니다.

이 읽기 작업을 수행 하는 동안 Azure Active Directory의 소스 앵커 정책이 예기치 않게 될 수 있습니다. 이 경우 Azure AD Connect는 소스 앵커가 무엇 인지 알 수 없습니다.</br>
![Azure Active Directory의 소스 앵커가 예기치 않은 경우 발생 하는 상황을 보여 주는 스크린샷](media/tshoot-connect-source-anchor/source3.png)

이 문제를 해결 하려면 사용자 지정 모드를 사용 하 여 다시 설치 하 고 특정 특성을 선택 하 여 원본 앵커를 수동으로 재정의 해야 합니다. 선택할 특성을 확실히 알고 있는 경우에만이 옵션을 진행 합니다. 확실 하지 않은 경우 지침은 [Microsoft 지원](https://support.microsoft.com/contactus/) 에 문의 하세요. 원본 앵커 정책을 변경 하면 온-프레미스 사용자와 연결 된 Azure 리소스 간의 연결이 끊어질 수 있습니다.

### <a name="invalid-source-anchor-in-sync-engine"></a>동기화 엔진의 원본 앵커가 잘못 되었습니다.
설치 하는 동안 잘못 된 원본 앵커를 사용 하 여 동기화 엔진을 구성 하려고 하면 Azure AD Connect 될 수 있습니다. 이 작업을 수행 하면 제품 문제가 발생 하 고 Azure AD Connect 설치가 실패 합니다. 이 문제에 대해를 실행 하는 경우 [Microsoft 지원](https://support.microsoft.com/contactus/) 에 문의 하세요.</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
