---
title: '하이브리드 ID: 디렉터리 통합 도구 비교 | Microsoft Docs'
description: 이 페이지는 디렉터리 통합을 위해 사용할 수 있는 다양한 디렉터리 통합 도구를 비교하는 포괄적인 테이블을 제공합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70e5c7e5fa428d5a71ca1a7468bbaab2fc94078e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643844"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>하이브리드 ID 디렉터리 통합 도구 비교
디렉터리 통합 도구는 수년에 걸쳐 성장하고 발전했습니다.  


- [FIM](/previous-versions/windows/desktop/forefront-2010/ff182370(v=vs.100)) 과 [MIM](/microsoft-identity-manager/microsoft-identity-manager-2016)은 계속 지원되며 주로 온-프레미스 시스템 간의 동기화를 사용하도록 설정합니다.   [FIM Microsoft Azure AD Connector](/previous-versions/mim/dn511001(v=ws.10))는 FIM과 MIM 모두에서 지원되지만 새 배포에는 권장되지 않습니다. Notes 또는 SAP HCM과 같은 온-프레미스 고객은 MIM을 사용하여 Active Directory Domain Services(AD DS)를 채운 다음 Azure AD Connect sync 또는 Azure AD Connect 클라우드 프로비저닝을 사용하여 AD DS에서 Azure AD로 동기화해야 합니다.
- [Azure AD Connect sync](how-to-connect-sync-whatis.md)는 AD DS 포리스트와 Azure AD 간의 동기화를 위해 DirSync 및 Azure AD Sync에서 이전에 릴리스된 구성 요소 및 기능을 통합합니다.  
- [Azure AD Connect 클라우드 프로비저닝](../cloud-sync/what-is-cloud-sync.md)은 AD DS에서 Azure AD로 동기화하는 데 사용할 수 있는 새로운 Microsoft 에이전트로, 인수된 회사의 AD 포리스트가 모회사의 AD 포리스트에서 분리되는 인수 합병과 같은 시나리오에서 유용합니다.

Azure AD Connect 동기화와 Azure AD Connect 클라우드 프로비저닝의 차이점에 대한 자세한 정보는 [Azure AD Connect 클라우드 프로비저닝이란?](../cloud-sync/what-is-cloud-sync.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.