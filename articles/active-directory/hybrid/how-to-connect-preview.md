---
title: 'Azure AD Connect: 미리 보기의 기능 | Microsoft Docs'
description: 이 항목에서는 Azure AD Connect에서 미리 보기 상태인 기능을 더 자세하게 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b543cf4655d0b961a144e9180385a532ae4216d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89657106"
---
# <a name="more-details-about-features-in-preview"></a>미리 보기 기능에 대한 자세한 내용
이 항목에서는 현재 미리 보기 상태인 기능을 사용하는 방법에 대해 설명합니다.

## <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>Azure AD Connect 동기화 V2 엔드포인트 API(공개 미리 보기) 

Azure AD Connect에 대한 새 엔드포인트(API)를 배포하여 Azure Active Directory에 대한 동기화 서비스 작업의 성능을 향상시켰습니다. 새 V2 엔드포인트를 활용하면 Azure AD로 내보내기 및 가져오기에 대한 성능이 크게 향상됩니다. 이 새 엔드포인트는 최대 250k의 멤버가 있는 그룹 동기화도 지원합니다. 또한이 끝점을 사용 하면 그룹 쓰기 저장을 사용 하도록 설정 된 경우 최대 구성원 한도를 포함 하지 않는 통합 그룹 Microsoft 365 온-프레미스 Active Directory에 다시 쓸 수 있습니다. 자세한 내용은 [Azure AD Connect 동기화 V2 엔드포인트 API(공개 미리 보기)](how-to-connect-sync-endpoint-api-v2.md)를 참조하세요.

## <a name="user-writeback"></a>사용자 쓰기 저장
> [!IMPORTANT]
> 사용자 쓰기 저장 미리 보기 기능은 Azure AD Connect 2015년 8월 업데이트에서 제거되었습니다. 이 기능을 사용하도록 설정한 경우 사용하지 않도록 설정해야 합니다.
>
>

## <a name="next-steps"></a>다음 단계
[Azure AD Connect의 사용자 지정 설치](how-to-connect-install-custom.md)를 계속 진행합니다.

[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
