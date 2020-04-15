---
title: Azure Red Hat 오픈시프트용 Azure Active Directory 통합
description: Microsoft Azure Red Hat OpenShift 클러스터에서 앱을 테스트하기 위해 Azure AD 보안 그룹 및 사용자를 만드는 방법에 대해 알아봅니다.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: f6c4fb5caf746650f95872d50afe31e5693422be
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382908"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Red Hat 오픈시프트용 Azure Active Directory 통합

아직 Azure Active Directory(Azure AD) 테넌트를 만들지 않은 경우 이러한 지침을 계속진행하기 전에 [Azure Red Hat OpenShift에 대한 Azure AD 테넌트 만들기의](howto-create-tenant.md) 지침을 따릅니다.

Microsoft Azure Red Hat OpenShift는 클러스터를 대신하여 작업을 수행할 수 있는 권한이 필요합니다. 조직에 아직 Azure AD 사용자, Azure AD 보안 그룹 또는 서비스 주체로 사용할 Azure AD 앱 등록이 없는 경우 다음 지침을 따라 만들어 만듭니다.

## <a name="create-a-new-azure-active-directory-user"></a>새 Azure Active Directory 사용자 만들기

Azure [포털에서](https://portal.azure.com)테넌트가 포털 오른쪽 상단에 사용자 이름 아래에 표시되는지 확인합니다.

![오른쪽 상단에 테넌트가 나열된](./media/howto-create-tenant/tenant-callout.png) 테넌트의 스크린샷 잘못된 테넌트가 표시되는 경우 오른쪽 상단에서 사용자 이름을 클릭한 다음 **디렉터리 전환**을 클릭하고 **모든 디렉터리** 목록에서 올바른 테넌트를 선택합니다.

Azure Red Hat OpenShift 클러스터에 로그인할 새 Azure Active Directory '소유자' 사용자를 만듭니다.

1. [사용자-모든 사용자](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) 블레이드로 이동합니다.
2. **새 사용자를 클릭하여** **사용자** 창을 엽니다.
3. 이 사용자의 **이름을** 입력합니다.
4. 만든 테넌트의 이름을 기반으로 **사용자 이름을** 만들고 `.onmicrosoft.com` 끝에 추가합니다. `yourUserName@yourTenantName.onmicrosoft.com`)을 입력합니다. 이 사용자 이름을 적어 둡니다. 클러스터에 로그인해야 합니다.
5. **디렉터리 역할을** 클릭하여 디렉터리 역할 창을 열고 **소유자를** 선택한 다음 창 하단에서 **확인을** 클릭합니다.
6. **사용자** 창에서 암호 **표시를** 클릭하고 임시 암호를 기록합니다. 처음 로그인하면 재설정하라는 메시지가 표시됩니다.
7. 창 아래쪽에서 **만들기를** 클릭하여 사용자를 만듭니다.

## <a name="create-an-azure-ad-security-group"></a>Azure AD 보안 그룹 만들기

클러스터 관리자 액세스 권한을 부여하기 위해 Azure AD 보안 그룹의 구성원은 OpenShift 그룹 "osa-customer-admins"에 동기화됩니다. 지정하지 않으면 클러스터 관리자 액세스가 허용되지 않습니다.

1. Azure [Active 디렉터리 그룹](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) 블레이드를 엽니다.
2. **+새 그룹을 클릭합니다.**
3. 그룹 이름과 설명을 제공합니다.
4. 그룹 유형을 **보안으로** **설정합니다.**
5. 멤버 자격 유형을 **할당된**로 **설정합니다.**

    이전 단계에서 만든 Azure AD 사용자를 이 보안 그룹에 추가합니다.

6. **멤버 선택을** 클릭하여 멤버 선택 창을 **엽니다.**
7. 구성원 목록에서 위에서 만든 Azure AD 사용자를 선택합니다.
8. 포털 하단에서 **선택을** 클릭한 다음 **만들기를** 클릭하여 보안 그룹을 만듭니다.

    그룹 ID 값을 적어 둡니다.

9. 그룹이 만들어지면 모든 그룹 목록에 그룹이 표시됩니다. 새 그룹을 클릭합니다.
10. 표시되는 페이지에서 **개체 ID를**복사합니다. [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md) 자습서에서와 같이 `GROUPID` 이 값을 참조합니다.

> [!IMPORTANT]
> 이 그룹을 osa-customer-admins OpenShift 그룹과 동기화하려면 Azure CLI를 사용하여 클러스터를 만듭니다. Azure 포털에는 현재 이 그룹을 설정할 필드가 없습니다.

## <a name="create-an-azure-ad-app-registration"></a>Azure AD 앱 등록 만들기

`az openshift create` 명령에 `--aad-client-app-id` 플래그를 생략하여 클러스터를 만드는 과정에서 Azure Active Directory(Azure AD) 앱 등록 클라이언트를 자동으로 만들 수 있습니다. 이 자습서에서는 완전성을 위해 Azure AD 앱 등록을 만드는 방법을 보여 주며, 이 자습서에서는 완료를 위해 Azure AD 앱 등록을 만드는 방법을 보여 주며, 이 자습서에서는 이러한 작업을 완료할 수 있습니다.

조직에 서비스 주체로 사용할 Azure Active Directory(Azure AD) 앱 등록이 아직 없는 경우 다음 지침에 따라 앱을 만듭니다.

1. 앱 등록 블레이드를 열고 **+New 등록을** [클릭합니다.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)
2. 응용 **프로그램 등록** 창에 응용 프로그램 등록에 이름을 입력합니다.
3. **지원되는 계정 유형에서** **이 조직 디렉터리내의 계정만** 선택되어 있는지 확인합니다. 이것은 가장 안전한 선택입니다.
4. 클러스터의 URI를 알고 나면 나중에 리디렉션 URI를 추가합니다. **등록** 단추를 클릭하여 Azure AD 응용 프로그램 등록을 만듭니다.
5. 표시되는 페이지에서 **응용 프로그램(클라이언트) ID를**복사합니다. [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md) 자습서에서와 같이 `APPID` 이 값을 참조합니다.

![앱 개체 페이지의 스크린샷](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

Azure Active Directory에 앱을 인증하기 위한 클라이언트 비밀을 생성합니다.

1. 앱 등록 **관리** 페이지에서 인증서 & **비밀을 클릭합니다.**
2. 인증서 **& 비밀** 창에서 **+새 클라이언트 비밀을**클릭합니다.  **클라이언트 비밀 추가 창이** 나타납니다.
3. **설명을**제공합니다.
4. 설정은 예를 들어 **2년**에서 원하는 기간으로 **만료됩니다.**
5. **추가를** 클릭하면 키 값이 페이지의 **클라이언트 비밀** 섹션에 표시됩니다.
6. 키 값을 복사합니다. [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md) 자습서에서와 같이 `SECRET` 이 값을 참조합니다.

![인증서 및 비밀 창의 스크린샷](./media/howto-create-tenant/create-key.png)

Azure 응용 프로그램 개체에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 및 서비스 주체 개체를](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)참조하십시오.

새 Azure AD 응용 프로그램 만들기에 대한 자세한 내용은 [Azure Active Directory v1.0 끝점을 사용 하 여 응용 프로그램 등록을](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)참조 합니다.

## <a name="add-api-permissions"></a>API 사용 권한 추가

[//]: # (마이크로소프트 그래프로 변경하지 마십시오. 그것은 마이크로 소프트 그래프와 함께 작동하지 않습니다.)
1. **관리** 섹션에서 **API 권한을 클릭합니다.**
2. **권한 추가를** 클릭하고 **Azure Active Directory 그래프를** 선택한 다음 **위임된 권한을 선택합니다.**
> [!NOTE]
> "Microsoft 그래프" 타일이 아니라 "Azure 활성 디렉터리 그래프"를 선택했는지 확인합니다.

3. 아래 목록에서 **사용자를** 확장하고 **User.Read** 권한을 사용하도록 설정합니다. **User.Read가** 기본적으로 활성화되어 있는 경우 **Azure Active Directory Graph** 권한 **User.Read**입니다.
4. 위로 스크롤하고 **응용 프로그램 권한을 선택합니다.**
5. 아래 목록에서 **디렉토리를** 확장하고 **Directory.ReadAll을**사용 합니다.
6. 변경 내용을 수락하려면 **권한 추가를 클릭합니다.**
7. API 권한 패널에 *User.Read* 및 *Directory.ReadAll*이 모두 표시됩니다. **관리자 동의의** 경고는 *Directory.ReadAll*옆에 있는 열에 필요합니다.
8. Azure 구독 *관리자인*경우 아래에서 **구독 *이름에* 대한 관리자 동의를** 클릭합니다. *Azure 구독 관리자가*아닌 경우 관리자의 동의를 요청합니다.

![API 권한 패널의 스크린샷입니다. User.Read and Directory.ReadAll 권한 추가, Directory.ReadAll에 필요한 관리자 동의](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> 클러스터 관리자 그룹의 동기화는 동의가 부여된 후에만 작동합니다. *관리자 동의 필수* 열에 확인 표시와 *"구독 이름에*대한 부여" 메시지가 있는 녹색 원이 표시됩니다.

관리자 및 기타 역할 관리에 대한 자세한 내용은 [Azure 구독 관리자 추가 또는 변경](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator)을 참조하십시오.

## <a name="resources"></a>리소스

* [Azure Active 디렉터리에서 응용 프로그램 및 서비스 주체 개체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [빠른 시작: Azure Active Directory v1.0 엔드포인트에 앱 등록](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>다음 단계

모든 [Azure Red Hat OpenShift 필수 구성 조건을](howto-setup-environment.md)충족한 경우 첫 번째 클러스터를 만들 준비가 된 것입니다!

튜토리얼을 보십시오 :
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md)
