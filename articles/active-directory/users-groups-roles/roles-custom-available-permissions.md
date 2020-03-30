---
title: 사용 가능한 사용자 지정 관리자 역할 권한 - Azure AD | 마이크로 소프트 문서
description: ID 관리를 위임하기 위한 사용자 지정 관리자 역할 권한입니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6156857202c1cca94df6d70ec2059daf55178f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025158"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Azure Active Directory의 응용 프로그램 등록 하위 유형 및 사용 권한

이 문서에는 Azure Active Directory(Azure AD)에서 사용자 지정 역할 정의에 대한 현재 사용 가능한 앱 등록 권한이 포함되어 있습니다.

## <a name="permissions-for-managing-single-directory-applications"></a>단일 디렉터리 응용 프로그램 관리에 대한 권한

사용자 지정 역할에 대한 사용 권한을 선택할 때 단일 디렉터리 응용 프로그램만 관리할 수 있는 액세스 권한을 부여할 수 있습니다. 단일 디렉터리 응용 프로그램은 응용 프로그램이 등록된 Azure AD 조직의 사용자만 사용할 수 있습니다. 단일 디렉터리 응용 프로그램은 **지원되는 계정 유형이** "이 조직 디렉터리계정만"으로 설정된 것으로 정의됩니다. 그래프 API에서 단일 디렉터리 응용 프로그램에는 signInAudience 속성이 "AzureADMyOrg"로 설정되어 있습니다.

단일 디렉터리 응용 프로그램만 관리할 수 있는 액세스 권한을 부여하려면 하위 형식 **응용 프로그램.myOrganization.** 예를 들어 microsoft.directory/application.myOrganization/기본/업데이트.

일반적인 용어 하위 유형, 사용 권한 및 속성 집합의 의미에 대한 설명은 [사용자 지정 역할 개요를](roles-custom-overview.md) 참조하세요. 다음 정보는 신청 등록에 따라 다릅니다.

### <a name="create-and-delete"></a>만들기 및 삭제

응용 프로그램 등록을 만들 수 있는 기능을 부여하는 데 사용할 수 있는 두 가지 권한이 있으며 각 권한은 서로 다른 동작으로 처리됩니다.

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>마이크로 소프트 디렉토리 / 응용 프로그램 / createA소유자

이 권한을 할당하면 생성자가 생성된 앱 등록의 첫 번째 소유자로 추가되고 생성된 앱 등록은 생성자의 생성된 개체 할당량 250개에 계산됩니다.

#### <a name="microsoftdirectoryapplicationscreate"></a>마이크로 소프트 디렉토리 / 응용 프로그램 / 만들기

이 권한을 할당하면 생성자가 생성된 앱 등록의 첫 번째 소유자로 추가되지 않으며 생성된 앱 등록은 생성자의 생성된 개체 할당량 250개에 포함되지 않습니다. 디렉터리 수준 할당량에 도달할 때까지 할당인이 앱 등록을 만드는 데 방해가 되는 것은 없으므로 이 권한을 신중하게 사용하십시오. 두 사용 권한이 모두 할당된 경우 이 사용 권한이 우선합니다.

두 사용 권한이 모두 할당된 경우 /create 권한이 우선합니다. /createAOwner 권한은 작성자를 첫 번째 소유자로 자동으로 추가하지 않지만 그래프 API 또는 PowerShell cmdlet을 사용할 때 앱 등록을 만드는 동안 소유자를 지정할 수 있습니다.

권한을 만들면 새 **등록** 명령에 대한 액세스 권한을 부여합니다.

[이러한 권한은 새 등록 포털 명령에 대한 액세스 권한을 부여합니다.](./media/roles-create-custom/new-custom-role.png)

앱 등록을 삭제하는 기능을 부여하는 데 사용할 수 있는 두 가지 권한이 있습니다.

#### <a name="microsoftdirectoryapplicationsdelete"></a>마이크로 소프트 디렉토리 / 응용 프로그램 / 삭제

하위 유형에 관계없이 앱 등록을 삭제할 수 있는 기능이 부여됩니다. 즉, 단일 테넌트 및 다중 테넌트 응용 프로그램입니다.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>마이크로 소프트 디렉토리 / 응용 프로그램.my조직 / 삭제

조직 또는 단일 테넌트 응용 프로그램(myOrganization 하위 유형)의 계정으로만 액세스할 수 있는 앱 등록으로 제한되는 앱 등록을 삭제할 수 있는 기능을 부여합니다.

![이러한 권한은 앱 등록 삭제 명령에 대한 액세스 권한을 부여합니다.](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> create 사용 권한이 포함된 역할을 할당할 때 는 디렉터리 범위에서 역할 할당을 수행해야 합니다. 리소스 범위에 할당된 권한 만들기는 앱 등록을 만드는 기능을 부여하지 않습니다.

### <a name="read"></a>읽기

조직의 모든 구성원 사용자는 기본적으로 앱 등록 정보를 읽을 수 있습니다. 그러나 게스트 사용자와 응용 프로그램 서비스 주체는 할 수 없습니다. 게스트 사용자 또는 응용 프로그램에 역할을 할당하려는 경우 적절한 읽기 권한을 포함해야 합니다.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>마이크로 소프트 디렉토리 / 응용 프로그램 / allProperties / 읽기

자격 증명과 같은 상황에서는 읽을 수 없는 속성 외부에서 단일 테넌트 및 다중 테넌트 응용 프로그램의 모든 속성을 읽을 수 있는 기능입니다.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>마이크로 소프트 디렉토리 / 응용 프로그램.my조직 / 모든속성 / 읽기

microsoft.directory/응용 프로그램/allProperties/읽기와 동일한 권한을 부여하지만 단일 테넌트 응용 프로그램에만 부여합니다.

#### <a name="microsoftdirectoryapplicationsownersread"></a>마이크로 소프트 디렉토리 / 응용 프로그램 / 소유자 / 읽기

단일 테넌트 및 다중 테넌트 응용 프로그램에서 소유자 속성을 읽을 수 있는 기능을 부여합니다. 응용 프로그램 등록 소유자 페이지의 모든 필드에 대한 액세스 권한을 부여합니다.

![이 권한은 앱 등록 소유자 페이지에 대한 액세스 권한을 부여합니다.](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>마이크로 소프트 디렉토리 / 응용 프로그램 / 표준 / 읽기

표준 응용 프로그램 등록 속성을 읽을 수 있는 권한을 부여합니다. 여기에는 응용 프로그램 등록 페이지 전체의 속성이 포함됩니다.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>마이크로 소프트 디렉토리 / 응용 프로그램.my조직 / 표준 / 읽기

microsoft.directory/응용 프로그램/표준/읽기와 동일한 권한을 부여하지만 단일 테넌트 응용 프로그램에만 대해 권한을 부여합니다.

### <a name="update"></a>업데이트

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>마이크로 소프트 디렉토리 / 응용 프로그램 / allProperties / 업데이트

단일 디렉터리 및 다중 디렉터리 응용 프로그램에서 모든 속성을 업데이트할 수 있습니다.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>마이크로 소프트 디렉토리 / 응용 프로그램.my조직 / 모든속성 / 업데이트

microsoft.directory/응용 프로그램/allProperties/업데이트와 동일한 권한을 부여하지만 단일 테넌트 응용 프로그램에만 부여합니다.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/응용 프로그램/대상/업데이트

단일 디렉터리 및 다중 디렉터리 응용 프로그램에서 지원되는 계정 유형(signInAudience) 속성을 업데이트할 수 있습니다.

![이 권한은 인증 페이지에서 앱 등록 지원 계정 유형 속성에 대한 액세스 권한을 부여합니다.](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.directory/application.myOrganization/Audience/update

microsoft.directory/응용 프로그램/Audience/update와 동일한 권한을 부여하지만 단일 테넌트 응용 프로그램에만 부여합니다.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/응용 프로그램/인증/업데이트

단일 테넌트 및 다중 테넌트 응용 프로그램에서 회신 URL, 로그아웃 URL, 암시적 흐름 및 게시자 도메인 속성을 업데이트하는 기능입니다. 지원되는 계정 유형을 제외한 응용 프로그램 등록 인증 페이지의 모든 필드에 대한 액세스 권한을 부여합니다.

![앱 등록 인증에 대한 액세스 권한을 부여하지만 지원되지 않는 계정 유형](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/application.myOrganization/인증/업데이트

microsoft.directory/응용 프로그램/인증/업데이트와 동일한 권한을 부여하지만 단일 테넌트 응용 프로그램에만 적용됩니다.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>마이크로 소프트 디렉토리 / 응용 프로그램 / 기본 / 업데이트

단일 테넌트 및 다중 테넌트 응용 프로그램에서 이름, 로고, 홈페이지 URL, 서비스 URL 및 개인 정보 취급 방침 URL 속성을 업데이트할 수 있습니다. 응용 프로그램 등록 브랜딩 페이지의 모든 필드에 대한 액세스 권한을 부여합니다.

![이 권한은 앱 등록 브랜딩 페이지에 대한 액세스 권한을 부여합니다.](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>마이크로 소프트 디렉토리 / 응용 프로그램.my조직 / 기본 / 업데이트

microsoft.directory/응용 프로그램/기본/업데이트와 동일한 권한을 부여하지만 단일 테넌트 응용 프로그램에만 부여합니다.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/응용 프로그램/자격 증명/업데이트

단일 테넌트 및 다중 테넌트 응용 프로그램에서 인증서 및 클라이언트 암호 속성을 업데이트할 수 있습니다. 응용 프로그램 등록 인증서 & 비밀 페이지의 모든 필드에 대한 액세스 권한을 부여합니다.

![이 권한은 앱 등록 인증서 & 비밀 페이지에 대한 액세스 권한을 부여합니다.](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.directory/application.myOrganization/자격 증명/업데이트

microsoft.directory/응용 프로그램/자격 증명/업데이트와 동일한 권한을 부여하지만 단일 디렉터리 응용 프로그램에만 부여합니다.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/응용 프로그램/소유자/업데이트

단일 테넌트 및 다중 테넌트에서 소유자 속성을 업데이트할 수 있는 기능입니다. 응용 프로그램 등록 소유자 페이지의 모든 필드에 대한 액세스 권한을 부여합니다.

![이 권한은 앱 등록 소유자 페이지에 대한 액세스 권한을 부여합니다.](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.directory/application.myOrganization/소유자/업데이트

microsoft.directory/응용 프로그램/소유자/업데이트와 동일한 권한을 부여하지만 단일 테넌트 응용 프로그램에만 부여합니다.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>마이크로 소프트 디렉토리 / 응용 프로그램 / 권한 / 업데이트

위임된 권한, 응용 프로그램 권한, 권한 있는 클라이언트 응용 프로그램, 필수 사용 권한을 업데이트하고 단일 테넌트 및 다중 테넌트 응용 프로그램에 대한 동의 속성을 부여할 수 있습니다. 동의를 수행할 수 있는 권한을 부여하지 않습니다. 응용 프로그램 등록 API 사용 권한의 모든 필드에 대한 액세스 권한을 부여하고 API 페이지를 노출합니다.

![이 권한은 앱 등록 API 권한 페이지에 대한 액세스 권한을 부여합니다.](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![이 권한은 앱 등록에 대한 액세스 권한을 부여합니다API 페이지 노출](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/application.myOrganization/권한/업데이트

microsoft.directory/응용 프로그램/사용 권한/업데이트와 동일한 권한을 부여하지만 단일 테넌트 응용 프로그램에만 부여합니다.

## <a name="required-license-plan"></a>필수 라이센스 플랜

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>다음 단계

- [Azure 포털, Azure AD PowerShell 및 그래프 API를](roles-create-custom.md) 사용하여 사용자 지정 역할 만들기
- [사용자 지정 역할에 대한 할당 보기](roles-view-assignments.md)
