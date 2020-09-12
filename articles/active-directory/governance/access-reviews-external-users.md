---
title: Azure AD Identity Governance를 사용 하 여 더 이상 리소스 액세스가 없는 외부 사용자를 검토 하 고 제거 합니다.
description: 액세스 검토를 사용 하 여 파트너 조직의 구성원 으로부터 제거 권한 확장
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/06/2020
ms.author: barclayn
ms.openlocfilehash: 64ff2a2a7ad6f07aac959422eadec7f24b210d88
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505837"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>Azure AD (Azure Active Directory) Id 거 버 넌 스를 사용 하 여 더 이상 리소스 액세스가 없는 외부 사용자를 검토 하 고 제거 합니다.

이 문서에서는 외부 id를 확인 하 고 선택 하 여 더 이상 필요 하지 않은 경우 Azure AD에서 제거 하 고 제거할 수 있도록 하는 기능 및 방법을 설명 합니다. 클라우드를 사용 하면 내부 또는 외부 사용자와 쉽게 공동 작업할 수 있습니다. Office 365을 적용 하면 사용자가 데이터, 문서 또는 디지털 작업 영역 (예: 팀)에서 함께 작업할 때 외부 id (게스트 포함)의 확산을 확인 하기 시작 합니다. 조직은 공동 작업을 가능 하 게 하 고 보안 및 거 버 넌 스 요구 사항을 충족 해야 합니다. 이러한 노력의 일환으로는 외부 사용자를 평가 하 고, 테 넌 트에 공동 작업을 위해 초대 하 고, 파트너 조직에서 시작 하 고, 더 이상 필요 하지 않은 경우 Azure AD에서 제거 하는 외부 사용자를 평가 하 고 정리 해야 합니다.

>[!NOTE]
>Azure AD 액세스 검토를 사용하는 데 유효한 Azure AD Premium P2, Enterprise Mobility + Security E5 유료 또는 평가판 라이선스가 필요합니다. 자세한 내용은 [Azure Active Directory 버전](../fundamentals/active-directory-whatis.md)을 참조하세요.

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>테 넌 트의 외부 조직에서 사용자를 검토 하는 이유는 무엇 인가요?

대부분의 조직에서 최종 사용자는 공동 작업을 위해 비즈니스 파트너 및 공급 업체를 초대 하는 프로세스를 시작 합니다. 리소스 소유자와 최종 사용자에 게 외부 사용자를 정기적으로 평가 하 고 증명할 수 있는 방법을 제공 하기 위해 공동 작업을 수행 해야 합니다. 새 공동 작업 파트너를 등록 하는 과정은 종종 계획 되 고 고려 되지만 많은 공동 작업에서 종료 날짜를 사용 하지 않는 것은 아니지만 사용자가 더 이상 액세스할 필요가 없는 경우에는 항상 명확 하지 않습니다. 또한 id 수명 주기 관리를 통해 기업은 Azure AD 정리를 유지 하 고 더 이상 조직의 리소스에 액세스할 필요가 없는 사용자를 제거 합니다. 디렉터리의 파트너와 공급 업체에 대 한 관련 id 참조만 유지 하면 직원의 위험을 줄이고 실수로 제거 해야 하는 외부 사용자에 대 한 액세스를 선택 하 고 부여할 수 있습니다. 이 문서에서는 권장 되는 사전 제안에서 사후 대응 및 정리 작업에 이르기까지 외부 id를 관리 하는 몇 가지 옵션을 안내 합니다.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>자격 관리를 사용 하 여 액세스 권한 부여 및 취소

권한 관리 기능을 사용 하면 리소스에 액세스할 수 있는 [외부 id의 자동화 된 수명 주기](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) 를 사용할 수 있습니다. 자격 관리를 통해 액세스를 관리 하 고 액세스 패키지를 통해 리소스를 게시 하는 프로세스 및 프로시저를 설정 하면 리소스에 대 한 외부 사용자 액세스를 추적 하는 것은 문제를 해결 하는 데 그다지 복잡 하지 않습니다. Azure AD에서 [자격 관리 액세스 패키지](entitlement-management-overview.md) 를 통해 액세스를 관리 하는 경우 조직에서 파트너 조직의 사용자 뿐만 아니라 사용자에 대 한 액세스를 중앙에서 정의 하 고 관리할 수 있습니다. 권한 관리는 액세스 패키지의 승인 및 할당을 사용 하 여 외부 사용자가 요청 하 고 액세스 권한을 할당 받은 위치를 추적 합니다. 외부 사용자가 모든 할당을 잃어 버릴 때 권한 부여 관리에서는 테 넌 트에서 자동으로 이러한 외부 사용자를 제거할 수 있습니다. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>자격 관리를 통해 초대 되지 않은 게스트 찾기

직원에 게 외부 사용자와 공동 작업을 할 수 있는 권한이 있는 경우 조직 외부 사용자에 게 원하는 수의 사용자를 초대할 수 있습니다. 외부 파트너를 회사에 맞춘 동적 그룹으로 그룹화 하 고 이러한 그룹을 검토 하는 것이 불가능 하거나, 검토할 개별 회사가 너무 많거나 조직의 소유자 또는 스폰서가 없을 수 있습니다. Microsoft에서는 테 넌 트의 외부 id 사용을 분석 하는 데 도움이 되는 샘플 PowerShell 스크립트를 제공 합니다. 스크립트는 외부 id를 열거 하 고 분류 합니다. 스크립트는 더 이상 필요 하지 않을 수 있는 외부 id를 식별 하 고 정리 하는 데 도움이 될 수 있습니다. 스크립트의 출력의 일부로 스크립트 샘플에서는 식별 된 그룹 없는 외부 파트너를 포함 하는 보안 그룹의 자동화 된 생성을 지원 합니다. 추가 분석을 위해 Azure AD 액세스 검토를 사용할 수 있습니다.
이 스크립트는 [GitHub](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)에서 사용할 수 있습니다. 스크립트 실행이 완료 되 면 다음을 수행 하는 외부 id를 설명 하는 HTML 출력 파일을 생성 합니다.

- 더 이상 테 넌 트에 그룹 멤버 자격이 없습니다.
- 테 넌 트에서 권한 있는 역할에 대 한 할당을 가집니다.
- 테 넌 트에서 응용 프로그램에 대 한 할당을 가집니다.

출력에는 이러한 각 외부 id에 대 한 개별 도메인도 포함 됩니다. 

>[!NOTE]
>위에서 참조 된 스크립트는 Azure AD에서 그룹 멤버 자격, 역할 할당 및 응용 프로그램 할당을 확인 하는 샘플 스크립트입니다. 외부 사용자가 Azure AD 외부 외부에서 받은 응용 프로그램에는 SharePoint (직접 멤버 자격 할당) 또는 Azure RBAC 또는 Azure DevOps와 같은 다른 할당이 있을 수 있습니다.

## <a name="review-resources-used-by-external-identities"></a>외부 id에 사용 되는 리소스 검토

아직 권한 관리를 통해 관리 되지 않는 팀 또는 다른 응용 프로그램과 같은 리소스를 사용 하는 외부 id가 있는 경우 이러한 리소스에 대 한 액세스를 정기적으로 검토 하는 것이 좋습니다. Azure AD [액세스 검토](create-access-review.md) 는 리소스 소유자, 외부 id 자체 또는 신뢰할 수 있는 다른 위임 된 사용자가 필요한 경우에도 액세스를 계속 하 여 액세스할 수 있도록 하 여 외부 id의 액세스를 검토할 수 있는 기능을 제공 합니다. 액세스 검토는 리소스를 대상으로 하며 리소스 또는 게스트 사용자만 액세스할 수 있는 모든 사용자로 범위가 지정 된 검토 작업을 만듭니다. 그러면 검토자가 검토 해야 하는 사용자의 결과 목록이 표시 됩니다. 조직의 직원과 외부 id만 포함 하 여 모든 사용자가 검토 해야 합니다.

![그룹을 사용 하 여 액세스 검토](media/access-reviews-external-users/group-members.png)

리소스 소유자 중심의 검토 문화권을 설정 하면 외부 id에 대 한 액세스를 제어할 수 있습니다. 리소스 소유자는 자신이 소유 하 고 있는 정보의 액세스, 가용성 및 보안에 대 한 책임을 지 며, 대부분의 경우 리소스에 대 한 액세스에 대 한 결정을 내릴 수 있는 가장 좋은 대상이 며, 중앙 IT 또는 여러 외부를 관리 하는 스폰서 보다 액세스 하는 사용자에 게 더 가깝습니다.

## <a name="create-access-reviews-for-external-identities"></a>외부 id에 대 한 액세스 검토 만들기

더 이상 사용자의 조직에서 더 이상 사용할 수 없는 테 넌 트의 모든 리소스에 대 한 액세스 권한이 없는 사용자를 제거할 수 있습니다. 이러한 외부 id를 차단 및 삭제 하기 전에 이러한 외부 사용자에 게 연락 하 여 프로젝트를 간과 하거나 필요한 액세스 권한을 간과 하지 않았는지 확인 해야 할 수 있습니다. 찾은 멤버로 모든 외부 id를 포함 하는 그룹을 만들 때 테 넌 트의 리소스에 대 한 액세스 권한이 없는 경우, 액세스 검토를 사용 하 여 모든 외부 사용자가 계속 해 서 액세스할 수 있는지 여부를 확인 하거나 나중에 계속 액세스 해야 합니다. 검토의 일환으로, 액세스 검토의 작성자에 게는 **승인에** 대 한 구성 검토 기능을 사용 하 여 외부 사용자가 계속 액세스를 위한 근거를 제공 하도록 요구할 수 있습니다 .이를 통해 테 넌 트에서 계속 액세스 해야 하는 위치와 방법을 알아볼 수 있습니다. 또한 **검토자 전자 메일에 대 한 추가 콘텐츠** 설정 기능을 사용 하도록 설정 하 여 사용자가 응답 하지 않는 경우 액세스 권한을 상실 하 고 여전히 액세스 해야 하는 경우에는 근거를 요구 합니다. 계속 해 서 액세스 검토에서 외부 id를 **사용 하지 않도록 설정 하 고 삭제** 하는 경우 다음 섹션에 설명 된 대로 비활성화 및 삭제 옵션을 사용할 수 있습니다.

![검토 범위를 게스트 사용자로만 제한](media/access-reviews-external-users/guest-users-only.png)

검토가 완료 되 면 **결과** 페이지에 모든 외부 id에서 제공 된 응답의 개요가 표시 됩니다. 자동으로 결과를 적용 하 고 액세스 검토를 사용 하지 않도록 설정 하 고 삭제 하도록 선택할 수 있습니다. 또는 제공 된 응답을 확인 하 고 사용자의 액세스 권한을 제거할지 아니면 추가 작업을 수행할지 결정 하 고 결정 하기 전에 추가 정보를 얻을 수 있습니다. 아직 검토 하지 않은 리소스에 대 한 액세스 권한이 있는 사용자도 있으면 검색의 일부로 검토를 사용 하 여 다음 검토 및 증명 주기를 보강 합니다.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews-preview"></a>Azure AD 액세스 검토 (미리 보기)를 사용 하 여 외부 id 비활성화 및 삭제

그룹 또는 응용 프로그램과 같은 리소스에서 원치 않는 외부 id를 제거 하는 옵션 외에도, Azure AD 액세스 검토는 외부 id가 테 넌 트에 로그인 하는 것을 차단 하 고 30 일 후에 테 넌 트에서 외부 id를 삭제할 수 있습니다.

![완료 설정 시](media/access-reviews-external-users/upon-completion-settings.png)

새 액세스 검토를 만들 때 "완료 후 설정" 섹션에서 **거부 된 사용자에 게 적용** 되는 작업의 경우 **사용자가 30 일간 로그인 하지 못하도록 차단을 정의한 다음 테 넌 트에서 사용자를 제거할**수 있습니다.
현재 미리 보기로 제공 되는이 설정을 사용 하면 Azure AD 테 넌 트에서 외부 id를 식별, 차단 및 삭제할 수 있습니다. 검토자의 지속적인 액세스를 검토 하 고 거부 한 외부 id는 리소스 액세스 또는 그룹 멤버 자격에 관계 없이 차단 및 삭제 됩니다. 이 설정은 외부 사용자가 검토 한 후에 더 이상 리소스 액세스를 전달 하지 않으며 테 넌 트에서 안전 하 게 제거할 수 있는지 확인 한 후 또는 해당 사용자의 액세스에 관계 없이 제거 되었는지 확인 하는 마지막 단계로 사용 되는 것이 가장 좋습니다. "사용 안 함 및 삭제" 기능은 외부 사용자를 먼저 차단 하 여 테 넌 트에 로그인 하 고 리소스에 액세스 하는 기능을 제공 합니다. 이 단계에서는 리소스 액세스가 취소 되지 않으며, 외부 사용자를 다시 인스턴스화하 데 하는 경우에는 해당 로그온 기능을 다시 구성할 수 있습니다. 추가 작업을 수행 하지 않으면 30 일 후 차단 된 외부 id가 디렉터리에서 삭제 되 고 해당 액세스 뿐만 아니라 계정이 제거 됩니다.

## <a name="next-steps"></a>다음 단계

- [액세스 검토 - Graph API](/graph/api/resources/accessreviews-root?view=graph-rest-beta)
- [권한 관리 - Graph API](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)