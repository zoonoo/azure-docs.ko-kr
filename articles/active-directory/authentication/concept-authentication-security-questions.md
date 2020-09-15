---
title: 보안 질문 인증 방법-Azure Active Directory
description: Azure Active Directory에서 보안 질문을 사용 하 여 로그인 이벤트를 개선 하 고 보안을 유지 하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bf90909cdd3bd3013dc43d50a9589945f5215c7
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532734"
---
# <a name="authentication-methods-in-azure-active-directory---security-questions"></a>Azure Active Directory의 인증 방법-보안 질문

로그인 이벤트 중에는 본인 확인 질문을 인증 방법으로 사용하지 않습니다. 대신 SSPR(셀프 서비스 암호 재설정) 프로세스 중에는 본인 확인 질문을 사용하여 누구인지 확인할 수 있습니다. 관리자 계정은 SSPR에서 본인 확인 질문을 확인 방법으로 사용할 수 없습니다.

사용자가 SSPR에 등록하면 사용할 인증 방법을 선택하라는 메시지가 표시됩니다. 사용자가 본인 확인 질문을 사용하도록 선택하는 경우 메시지로 표시되는 일련의 질문에서 선택한 다음, 자신의 답변을 제공합니다.

![본인 확인 질문에 대한 인증 방법 및 옵션을 보여 주는 Azure Portal의 스크린샷](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> 보안 질문은 디렉터리에서 사용자 개체에 대해 비공개적으로 안전하게 저장되며 등록하는 동안 사용자만이 답변할 수 있습니다. 관리자는 사용자의 질문 또는 대답을 읽거나 수정할 수 없습니다.

어떤 사람들이 다른 사용자의 질문에 대한 답을 알고 있을 수도 있으므로 보안 질문은 다른 방법보다 덜 안전할 수 있습니다. SSPR에 본인 확인 질문을 사용하는 경우 다른 방법과 함께 사용하는 것이 좋습니다. 전화 또는 등록된 디바이스가 없는 경우에만 SSPR 프로세스 중에 Microsoft Authenticator 앱 또는 전화 인증을 사용하여 ID를 확인하고 사용자에게 본인 확인 질문을 선택하라는 메시지가 표시될 수 있습니다.

## <a name="predefined-questions"></a>미리 정의된 질문

다음의 미리 정의된 본인 확인 질문은 SSPR에서 확인 방법으로 사용할 수 있습니다. 이러한 모든 보안 질문은 사용자의 브라우저 로캘을 기반으로 하는 Microsoft 365 언어의 전체 집합으로 변환 되 고 지역화 됩니다.

* 배우자/파트너를 처음 만난 도시는 어디인가요?
* 부모님이 처음 만난 도시는 어디인가요?
* 가장 가까운 형제 자매가 사는 도시는 어디인가요?
* 아버지가 출생하신 도시는 어디인가요?
* 첫 직장이 있는 도시는 어디인가요?
* 어머니가 출생하신 도시는 어디인가요?
* 2000년에 새해를 맞은 도시는 어디인가요?
* 고등학교에서 가장 좋아했던 선생님의 성은 무엇인가요?
* 지원했지만 다니지 않은 대학의 이름은 무엇인가요?
* 첫 번째 결혼 피로연을 열었던 장소의 이름은 무엇인가요?
* 아버지의 중간 이름은 무엇인가요?
* 가장 좋아하는 음식은 무엇인가요?
* 외할머니의 성함은 무엇인가요?
* 어머니의 중간 이름은 무엇인가요?
* 가장 손위인 형제 자매의 생년월일은 언제인가요? (예: 1985년 11월)
* 가장 손위인 형제 자매의 중간 이름은 무엇인가요?
* 친할아버지의 성함은 무엇인가요?
* 가장 손아래인 형제 자매의 중간 이름은 무엇인가요?
* 6학년 때 다닌 학교는 어디인가요?
* 어린 시절 베스트 프렌드의 성함은 무엇인가요?
* 첫 번째 배우자의 성함은 무엇인가요?
* 가장 좋아하는 초등학교 선생님의 성은 무엇인가요?
* 첫 번째 자동차 또는 오토바이의 제조업체와 모델은 무엇인가요?
* 처음 다닌 학교의 이름은 무엇인가요?
* 태어난 병원의 이름은 무엇인가요?
* 어린 시절 첫 번째 집의 거리 이름은 무엇인가요?
* 어린 시절 영웅의 이름은 무엇인가요?
* 좋아하는 봉제 인형의 이름은 무엇인가요?
* 첫 번째 애완동물의 이름은 무엇인가요?
* 어린 시절 별명은 무엇인가요?
* 고등학교 때 가장 좋아한 스포츠는 무엇인가요?
* 첫 번째 직업은 무엇인가요?
* 어린 시절 전화 번호의 마지막 4자리는 무엇인가요?
* 어린 시절 자라서 되고 싶었던 것은 무엇인가요?
* 지금까지 만난 가장 유명한 사람은 누구인가요?

## <a name="custom-security-questions"></a>사용자 지정 보안 질문

유연성을 강화하기 위해 고유한 사용자 지정 본인 확인 질문을 정의할 수 있습니다. 사용자 지정 보안 질문은 최대 200자입니다.

사용자 지정 본인 확인 질문은 기본 본인 확인 질문의 경우와 같이 자동으로 지역화되지 않습니다. 모든 사용자 지정 질문은 사용자의 브라우저 로캘이 다른 경우에도 관리 사용자 인터페이스에 입력한 언어와 동일한 언어로 표시됩니다. 지역화된 질문이 필요한 경우 미리 정의된 질문을 사용해야 합니다.

## <a name="security-question-requirements"></a>보안 질문 요구 사항

기본 및 사용자 지정 본인 확인 질문에는 다음 요구 사항 및 제한 사항이 적용됩니다.

* 최소 응답 문자 제한은 3자입니다.
* 최대 응답 문자 제한은 40자입니다.
* 사용자는 동일한 질문에 대해 한 번만 응답할 수 있습니다.
* 사용자는 둘 이상의 질문에 대해 동일한 응답을 제공할 수 없습니다.
* 유니코드 문자를 포함한 모든 문자 집합을 사용하여 질문과 응답을 정의할 수 있습니다.
* 정의된 질문 수는 등록에 필요한 질문 수보다 크거나 같아야 합니다.

## <a name="next-steps"></a>다음 단계

시작 하려면 [SSPR (셀프 서비스 암호 재설정)에 대 한 자습서][tutorial-sspr]를 참조 하세요.

SSPR 개념에 대한 자세한 내용은 [Azure AD 셀프 서비스 암호 재설정 작동 방법][concept-sspr]을 참조하세요.

[Microsoft Graph REST API 베타](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)를 사용 하 여 인증 방법 구성에 대해 자세히 알아보세요.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[concept-sspr]: concept-sspr-howitworks.md
