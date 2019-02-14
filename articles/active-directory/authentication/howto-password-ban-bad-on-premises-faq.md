---
title: 온-프레미스 Azure AD 암호 보호 FAQ
description: 온-프레미스 Azure AD 암호 보호 FAQ
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a4528cde92c5f738fa3fa7f4a649d84b1e79431
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175951"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>미리 보기: Azure AD 암호 보호 온-프레미스 - 질문과 대답

|     |
| --- |
| Azure AD 암호 보호는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

## <a name="general-questions"></a>일반적인 질문

**Q: Azure AD 암호 보호는 언제 GA(일반 공급)되나요?**

GA는 2019년 1분기(2019년 3월 말 이전)로 예정되어 있습니다. 지금까지 이 기능에 대한 피드백을 제공해 주신 모든 분들께 감사드립니다. 감사합니다!

**Q: 온-프레미스 Azure AD 암호 보호는 비공용 클라우드에서 지원되나요?**

아니요 - 온-프레미스 Azure AD 암호 보호는 공용 클라우드에서만 지원됩니다.

**Q: Azure AD 암호 보호 혜택을 내 온-프레미스 사용자의 하위 집합에 적용하려면 어떻게 해야 할까요?**

지원되지 않습니다. Azure AD 암호 보호가 배포되고 사용하도록 설정되면 모든 사용자가 차별 없이 동등한 보안 혜택을 받습니다.

**Q: Azure AD 암호 보호는 다른 암호 필터 기반 제품과 함께 설치할 수 있나요?**

예. 등록된 여러 암호 필터 DLL에 대한 지원은 Windows의 핵심 기능이며 Azure AD 암호 보호와 관련이 없습니다. 암호가 수락되기 전에 모든 등록된 암호 필터 dll에 동의해야 합니다.

**Q: sysvol 복제에 DFSR이 필요한가요?**

FRS(DFSR에 대한 선행 기술)는 대부분의 알려진 문제를 포함하며, 최신 버전의 Windows Server Active Directory에서 완전히 지원되지 않습니다. Azure AD 암호 보호의 제로 테스트는 FRS 구성 도메인에서 수행됩니다.

자세한 내용은 다음 문서를 참조하세요.

[DFSR로 sysvol 복제를 마이그레이션하는 사례](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[FRS의 경우 종료가 가깝습니다.](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**Q: DC 에이전트 소프트웨어를 설치하거나 업그레이드하는 데 다시 부팅이 필요한 이유는 무엇인가요?**

이 요구 사항은 핵심 Windows 동작으로 인해 발생할 수 있습니다.

**Q: 특정 프록시 서버를 사용하도록 DC 에이전트를 구성하는 방법이 있나요?**

 아니요.

## <a name="next-steps"></a>다음 단계

여기서 답변되지 않은 온-프레미스 Azure AD 암호 보호 질문이 있으면 아래의 피드백 항목을 제출해 주세요. 감사합니다!

[Azure AD 암호 보호 배포](howto-password-ban-bad-on-premises-deploy.md)
