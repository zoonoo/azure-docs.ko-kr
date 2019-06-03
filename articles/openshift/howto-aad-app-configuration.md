---
title: Azure Red Hat OpenShift에 대 한 azure Active Directory 통합 | Microsoft Docs
description: Microsoft Azure의 Red Hat OpenShift 클러스터에서 앱을 테스트 하는 것에 대 한 Azure AD 보안 그룹 및 사용자를 만드는 방법에 알아봅니다.
author: jimzim
ms.author: jzim
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/13/2019
ms.openlocfilehash: adc5a601a04936a376d7c69b26c2429940ebdf6e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306465"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift에 대 한 azure Active Directory 통합

Azure Active Directory (Azure AD) 테 넌 트에 아직 만들지 않은 경우의 지침을 따르고 [Azure Red Hat OpenShift는 Azure AD 테 넌 트 만들기](howto-create-tenant.md) 이러한 지침을 사용 하 여 계속 하기 전에 합니다.

Microsoft Azure의 Red Hat OpenShift 클러스터를 대신 하 여 작업을 수행할 권한이 필요 합니다. 조직 아직 없으면 Azure AD 사용자, Azure AD 보안 그룹 또는 서비스 주체를 사용 하 여 Azure AD 앱 등록을 만드는 데 이러한 지침을 따릅니다.

## <a name="create-a-new-azure-active-directory-user"></a>새 Azure Active Directory 사용자 만들기

에 [Azure portal](https://portal.azure.com), 테 넌 트 위쪽에서 사용자 이름 아래에 나타나는지 확인 포털의 오른쪽:

![맨 위 오른쪽에 나열 된 테 넌 트를 사용 하 여 포털의 스크린샷](./media/howto-create-tenant/tenant-callout.png) 잘못 된 테 넌 트 표시 되지 않으면 오른쪽 위에서 사용자 이름을 클릭 한 다음 클릭 **디렉터리 전환**를 올바른 테 넌 트를 선택 하 고는 **모든 디렉터리** 목록입니다.

Azure Red Hat OpenShift 클러스터에 로그인 할 새 Azure Active Directory 전역 관리자 사용자를 만듭니다.

1. 로 이동 합니다 [사용자가 모든 사용자](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) 블레이드입니다.
2. 클릭 **+ 새 사용자** 열려는 합니다 **사용자** 창입니다.
3. 입력 한 **이름을** 이 사용자에 대 한 합니다.
4. 만들기는 **사용자 이름** 만든 테 넌 트 이름을 기반으로 사용 하 여 `.onmicrosoft.com` 끝에 추가 됩니다. 예: `yourUserName@yourTenantName.onmicrosoft.com`. 이 사용자 이름을 적어 둡니다. 클러스터에 로그인 되도록 해야 합니다.
5. 클릭 **디렉터리 역할** 디렉터리 역할 창을 열려면 선택 하 **전역 관리자** 클릭 하 고 **확인** 창의 맨 아래에 있습니다.
6. 에 **사용자** 창 클릭 **암호 표시** 하 고 임시 암호를 기록 합니다. 처음으로 로그인 한 후 다시 설정 하려면 묻는 메시지가 나타납니다.
7. 창의 맨 아래에서 클릭 **만들기** 사용자를 만들려고 합니다.

## <a name="create-an-azure-ad-security-group"></a>Azure AD 보안 그룹 만들기

클러스터 관리 액세스에 권한을 부여 하려면 Azure AD 보안 그룹의 멤버 자격 OpenShift 그룹 "osa-고객-관리자"로 동기화 됩니다. 지정 하지 않으면 클러스터 관리자 액세스 권한 없음 부여 됩니다.

1. 엽니다는 [Azure Active Directory 그룹](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) 블레이드입니다.
2. 클릭 **+ 새 그룹**
3. 그룹 이름 및 설명을 제공 합니다.
4. 설정할 **그룹 종류** 하 **보안**합니다.
5. 설정할 **멤버 자격 유형** 하 **할당**합니다.

    이 보안 그룹에 이전 단계에서 만든 Azure AD 사용자를 추가 합니다.

6. 클릭 **멤버** 열려는 합니다 **멤버 선택** 창입니다.
7. 멤버 목록에서 위에서 만든 Azure AD 사용자를 선택 합니다.
8. 포털의 맨 아래에서 클릭 **선택** 차례로 **만들기** 보안 그룹을 만듭니다.

    그룹 ID 값을 기록해 둡니다

9. 그룹이 만들어지면 모든 그룹 목록에 표시 됩니다. 새 그룹을 클릭 합니다.
10. 표시 되는 페이지에서 복사 합니다 **개체 ID**합니다. 이 값으로 이라고 `GROUPID` 에 [Azure Red Hat OpenShift 클러스터를 만드는](tutorial-create-cluster.md) 자습서.

## <a name="create-an-azure-ad-app-registration"></a>Azure AD 앱 등록 만들기

생략 하 여 클러스터 만들기의 일환으로 Azure Active Directory (Azure AD) 응용 프로그램 등록 클라이언트를 자동으로 만들 수는 `--aad-client-app-id` 플래그를 `az openshift create` 명령입니다. 이 자습서에서는 완전성을 위해 Azure AD 앱 등록을 만드는 방법을 보여 줍니다.

조직에 서비스 주체를 사용 하려면 Azure Active Directory (Azure AD) 앱 등록을 아직 만들려면 다음이 지침을 따릅니다.

1. 엽니다는 [앱 등록 블레이드](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) 클릭 **+ 새 등록**합니다.
2. 에 **응용 프로그램을 등록** 창 응용 프로그램 등록에 대 한 이름을 입력 합니다.
3. 아래에서 확인 **지원 되는 계정 유형** 하는 **만이 조직 디렉터리의 계정** 을 선택 합니다. 이것이 가장 안전한 선택 사항입니다.
4. 클러스터의 URI를 알게 되 면 리디렉션 URI를 나중에 추가 합니다. 클릭 합니다 **등록** 를 Azure AD 응용 프로그램 등록을 만드는 단추입니다.
5. 표시 되는 페이지에서 복사 합니다 **응용 프로그램 (클라이언트) ID**합니다. 이 값으로 이라고 `APPID` 에 [Azure Red Hat OpenShift 클러스터를 만드는](tutorial-create-cluster.md) 자습서.

![앱 개체 페이지의 스크린샷](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

Azure Active Directory에 앱을 인증 하는 것에 대 한 클라이언트 비밀을 생성 합니다.

1. 에 **관리** 섹션 앱 등록 페이지에서 클릭 **인증서 및 비밀**합니다.
2. 에 **인증서 및 비밀** 창 클릭 **+ 새 클라이언트 암호**합니다.  합니다 **클라이언트 암호 추가** 창이 나타납니다.
3. 제공 된 **설명을**합니다.
4. 설정할 **Expires** 예를 들어 원하는 기간 **In 2 Years**합니다.
5. 클릭 **추가** 키 값이 표시 됩니다 및 합니다 **클라이언트 비밀** 페이지의 섹션입니다.
6. 키 값을 적어둡니다. 이 값으로 이라고 `SECRET` 에 [Azure Red Hat OpenShift 클러스터를 만드는](tutorial-create-cluster.md) 자습서.
 
![인증서 및 비밀 창의 스크린샷](./media/howto-create-tenant/create-key.png)
 
Azure 응용 프로그램 개체에 대 한 자세한 내용은 참조 하세요. [응용 프로그램 및 Azure Active Directory에서 서비스 주체 개체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)합니다.

새로 만들기에 대 한 Azure AD 응용 프로그램을 참조 하세요 [Azure Active Directory v1.0 끝점을 사용 하 여 앱을 등록](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)합니다.

## <a name="resources"></a>리소스

* [응용 프로그램 및 Azure Active Directory에서 서비스 주체 개체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [빠른 시작: Azure Active Directory v1.0 끝점을 사용 하 여 앱을 등록 합니다.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

## <a name="next-steps"></a>다음 단계

모든 충족 한 경우는 [Azure Red Hat OpenShift 필수 구성 요소](howto-setup-environment.md), 첫 번째 클러스터를 만들 준비가 되었습니다.!

이 자습서를 시도해 보세요.
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md)