---
title: 포털에서 서비스 주체를 & Azure AD 앱 만들기
titleSuffix: Microsoft identity platform
description: Azure 리소스 관리자에서 역할 기반 액세스 제어를 통해 리소스에 대한 액세스를 관리하기 위해 서비스 주체에 대한 액세스를 관리하기 위해 새 Azure Active Directory 앱 & 만듭니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: c5f65adfe401f2f6e99234d08b8e8dabeff7d5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264116"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기

이 문서에서는 역할 기반 액세스 제어에 사용할 수 있는 새 Azure Active Directory(Azure AD) 응용 프로그램 및 서비스 주체를 만드는 방법을 보여 주며 있습니다. 리소스에 액세스하거나 리소스를 수정해야 하는 코드가 있는 경우 앱의 ID를 만들 수 있습니다. 이 ID를 서비스 주체라고 합니다. 서비스 주체에 필수 권한을 할당할 수 있습니다. 이 문서에서는 포털을 사용하여 서비스 주체를 만드는 방법을 보여 줍니다. 여기서는 애플리케이션을 하나의 조직 내에서만 실행하게 되는 단일 테넌트 애플리케이션을 중점적으로 다룹니다. 일반적으로 단일 조직 내에서 실행되는 LOB(기간 업무) 애플리케이션에 대해 단일 테넌트 애플리케이션을 사용하게 됩니다.

> [!IMPORTANT]
> 서비스 주체를 만드는 대신 애플리케이션 ID에 Azure 리소스에 대한 관리 ID를 사용하는 것이 좋습니다. 코드가 관리되는 ID를 지원하는 서비스에서 실행되고 Azure AD 인증을 지원하는 리소스에 액세스하는 경우 관리되는 ID가 더 나은 옵션입니다. Azure 리소스에 대한 관리 ID 및 이것이 지원되는 서비스를 알아보려면 [Azure 리소스에 대한 관리 ID란?](../managed-identities-azure-resources/overview.md)을 참조하세요.

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory 애플리케이션 만들기

이제 ID를 만들어 보겠습니다. 문제가 발생하면 [필요한 권한](#required-permissions)을 확인하여 계정이 ID를 만들 수 있는지 확인합니다.

1. [Azure Portal](https://portal.azure.com)을 통해 Azure 계정에 로그인합니다.
1. **Azure 활성 디렉터리**를 선택합니다.
1. **앱 등록을 선택합니다.**
1. **새 등록**을 선택합니다.
1. 응용 프로그램의 이름을 지정합니다. 응용 프로그램을 사용할 수 있는 사람을 결정하는 지원되는 계정 유형을 선택합니다. **URI 리디렉션에서**만들 응용 프로그램 유형에 대한 **웹을** 선택합니다. 액세스 토큰이 전송되는 URI를 입력합니다. [네이티브 애플리케이션](../manage-apps/application-proxy-configure-native-client-application.md)의 자격 증명을 만들 수 없습니다. 자동화된 애플리케이션에 해당 형식을 사용할 수 없습니다. 값을 설정한 후 **레지스터를**선택합니다.

   ![응용 프로그램의 이름 입력](./media/howto-create-service-principal-portal/create-app.png)

Azure AD 애플리케이션 및 서비스 주체를 만들었습니다.

## <a name="assign-a-role-to-the-application"></a>응용 프로그램에 역할 할당

구독의 리소스에 액세스하려면 응용 프로그램에 역할을 할당해야 합니다. 애플리케이션에 적합한 권한을 제공하는 역할을 결정합니다. 사용 가능한 역할에 대해 알아보려면 [RBAC: 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

구독, 리소스 그룹 또는 리소스 수준에서 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어 애플리케이션에 리소스 그룹에 대한 읽기 권한자 역할을 추가하면 애플리케이션이 리소스 그룹과 그 안에 포함된 모든 리소스를 읽을 수 있습니다.

1. Azure 포털에서 응용 프로그램을 할당할 범위 수준을 선택합니다. 예를 들어 구독 범위에서 역할을 할당하거나 구독을 검색 및 **선택하거나** **홈** 페이지에서 **구독을 선택합니다.**

   ![예를 들어 구독 범위에서 역할을 할당합니다.](./media/howto-create-service-principal-portal/select-subscription.png)

1. 애플리케이션을 할당할 특정 구독을 선택합니다.

   ![할당을 위한 구독 선택](./media/howto-create-service-principal-portal/select-one-subscription.png)

   찾고 있는 구독이 표시되지 않으면 **전역 구독 필터**를 선택합니다. 포털에 대해 원하는 구독이 선택되었는지 확인합니다.

1. **액세스 제어(IAM)를**선택합니다.
1. **역할 할당 추가**를 선택합니다.
1. 애플리케이션에 할당할 역할을 선택합니다. 예를 들어 응용 프로그램이 **재부팅,** 인스턴스 **시작** 및 **중지와** 같은 작업을 실행할 수 있도록 하려면 **기여자** 역할을 선택합니다.  사용 가능한 [역할에](../../role-based-access-control/built-in-roles.md) 대해 자세히 알아보기 기본적으로 Azure AD 응용 프로그램은 사용 가능한 옵션에 표시되지 않습니다. 애플리케이션을 찾으려면 이름을 검색하여 선택합니다.

   ![응용 프로그램에 할당할 역할을 선택합니다.](./media/howto-create-service-principal-portal/select-role.png)

1. **저장**을 선택하여 역할 할당을 완료합니다. 해당 범위에 대한 역할이 있는 사용자 목록에 응용 프로그램이 표시됩니다.

서비스 주체가 설정되었습니다. 서비스 주체 사용을 시작하여 스크립트나 앱을 실행할 수 있습니다. 다음 섹션에서는 프로그래밍 방식으로 로그인할 때 필요한 값을 가져오는 방법을 보여 줍니다.

## <a name="get-values-for-signing-in"></a>로그인을 위한 값 가져오기

프로그래밍 방식으로 로그인하는 경우 인증 요청과 함께 테넌트 ID를 전달해야 합니다. 또한 애플리케이션 ID 및 인증 키가 필요합니다. 이러한 값을 가져오려면 다음 단계를 사용합니다.

1. **Azure 활성 디렉터리**를 선택합니다.
1. Azure AD의 **앱 등록**에서 애플리케이션을 선택합니다.
1. 디렉터리(테넌트) ID를 복사하여 응용 프로그램 코드에 저장합니다.

    ![디렉터리(테넌트 ID)를 복사하여 앱 코드에 저장](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. **애플리케이션 ID**를 복사하고 애플리케이션 코드에 저장합니다.

   ![애플리케이션(클라이언트) ID 복사](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>인증서 및 비밀
Daemon 응용 프로그램은 두 가지 형태의 자격 증명을 사용하여 Azure AD( 인증서 및 응용 프로그램 암호)를 사용하여 인증할 수 있습니다.  인증서를 사용하는 것이 좋지만 새 응용 프로그램 검사를 만들 수도 있습니다.

### <a name="upload-a-certificate"></a>인증서 업로드

기존 인증서가 있는 경우 사용할 수 있습니다.  선택적으로 *테스트 목적으로만*자체 서명된 인증서를 만들 수 있습니다. PowerShell을 열고 다음 매개 변수를 사용하여 [새 자체 서명 인증서를](/powershell/module/pkiclient/new-selfsignedcertificate) 실행하여 컴퓨터의 사용자 인증서 저장소에 자체 서명된 인증서를 만듭니다. 

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Windows 제어판에서 액세스할 수 있는 [사용자 인증서](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) MMC 스냅인 관리를 사용하여 이 인증서를 파일로 내보냅니다.

1. **시작** 메뉴에서 **실행을** 선택한 다음 **certmgr.msc를**입력합니다.

   현재 사용자의 인증서 관리자 도구가 나타납니다.

1. 인증서 를 보려면 인증서 - 왼쪽 창의 **현재 사용자는** **개인** 디렉토리를 확장합니다.
1. 만든 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업 >내보내기를**선택합니다.
1. 인증서 내보내기 마법사를 따릅니다.  개인 키를 내보내고 인증서 파일에 대한 암호를 지정하고 파일로 내보냅니다.

인증서를 업로드하려면 다음 을 수행하십시오.

1. **Azure 활성 디렉터리**를 선택합니다.
1. Azure AD의 **앱 등록**에서 애플리케이션을 선택합니다.
1. **인증서 및 비밀**을 선택합니다.
1. **인증서 업로드를** 선택하고 인증서(기존 인증서 또는 내보낸 자체 서명된 인증서)를 선택합니다.

    ![인증서 업로드를 선택하고 추가할 인증서를 선택합니다.](./media/howto-create-service-principal-portal/upload-cert.png)

1. **추가**를 선택합니다.

응용 프로그램 등록 포털에서 응용 프로그램에 인증서를 등록한 후 클라이언트 응용 프로그램 코드가 인증서를 사용하도록 설정해야 합니다.

### <a name="create-a-new-application-secret"></a>새 응용 프로그램 비밀 만들기

인증서를 사용하지 않도록 선택하면 새 응용 프로그램 검사를 만들 수 있습니다.

1. **인증서 및 비밀**을 선택합니다.
1. **클라이언트 비밀 -> 새 클라이언트 비밀**을 차례로 선택합니다.
1. 비밀에 대한 설명과 기간을 제공합니다. 완료되면 **추가**를 선택합니다.

   클라이언트 비밀이 저장되면 클라이언트 비밀의 값이 표시됩니다. 나중에 키를 검색할 수 없으므로 이 값을 복사합니다. 응용 프로그램으로 로그인할 응용 프로그램 ID와 함께 키 값을 제공 합니다. 애플리케이션에서 검색할 수 있는 위치에 키 값을 저장합니다.

   ![나중에 이 키를 검색할 수 없으므로 비밀 값 복사](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>리소스에 대한 액세스 정책 구성
응용 프로그램에서 액세스해야 하는 리소스에 대한 추가 권한을 구성해야 할 수 있습니다. 예를 들어 키, 비밀 또는 인증서에 대한 응용 프로그램 액세스를 제공하려면 [키 자격 증명 모음의 액세스 정책을 업데이트해야](/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) 합니다.  

1. Azure [포털에서](https://portal.azure.com)키 자격 증명 모음으로 이동하여 **액세스 정책을**선택합니다.  
1. **액세스 정책 추가를**선택한 다음 응용 프로그램에 부여할 키, 비밀 및 인증서 권한을 선택합니다.  이전에 만든 서비스 주체를 선택합니다.
1. **추가를** 선택하여 액세스 정책을 추가한 다음 **저장하여** 변경 내용을 커밋합니다.
    ![액세스 정책 추가](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="required-permissions"></a>필요한 사용 권한

Azure AD 테넌트에 응용 프로그램을 등록하고 응용 프로그램에 Azure 구독의 역할을 할당할 수 있는 충분한 권한이 있어야 합니다.

### <a name="check-azure-ad-permissions"></a>Azure AD 권한 확인

1. **Azure 활성 디렉터리**를 선택합니다.
1. 역할을 기록해 둡니다. **사용자** 역할이 있는 경우 비관리자가 애플리케이션을 등록할 수 있는지 확인해야 합니다.

   ![자신의 역할을 찾습니다. 사용자인 경우 관리자가 아닌 사용자가 앱을 등록할 수 있도록 합니다.](./media/howto-create-service-principal-portal/view-user-info.png)

1. 왼쪽 창에서 사용자 **설정을**선택합니다.
1. **앱 등록** 설정을 확인합니다. 이 값은 관리자만 설정할 수 있습니다. **예**로 설정하면 Azure AD 테넌트의 모든 사용자가 앱을 등록할 수 있습니다.

앱 등록 설정이 **아니요**로 설정되어 있으면 관리자 역할이 있는 사용자만 이러한 유형의 애플리케이션을 등록할 수 있습니다. Azure AD에서 사용 가능한 관리자 역할 및 각 역할에 제공되는 특정 권한에 대한 자세한 내용은 [사용 가능한 역할](../users-groups-roles/directory-assign-admin-roles.md#available-roles) 및 [역할 권한](../users-groups-roles/directory-assign-admin-roles.md#role-permissions)을 참조하세요. 계정에 사용자 역할이 할당되었지만 앱 등록 설정이 관리자 사용자로 제한되는 경우 관리자에게 앱 등록의 모든 측면을 만들고 관리할 수 있는 관리자 역할 중 하나를 할당하거나 사용자가 등록할 수 있도록 하도록 요청합니다. 애플 리 케이 션.

### <a name="check-azure-subscription-permissions"></a>Azure 구독 권한 확인

Azure 구독에서 AD 앱에 역할을 할당할 `Microsoft.Authorization/*/Write` 수 있는 액세스 권한이 있어야 합니다. 이 작업에 대한 권한은 [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할 또는 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) 역할을 통해 부여됩니다. 계정에 **기여자** 역할이 할당된 경우 적절한 권한이 없습니다. 서비스 주체역할을 할당하려고 할 때 오류가 발생합니다.

Azure 구독 권한을 확인하려면

1. **구독을**검색하고 선택하거나 **홈** 페이지에서 **구독을 선택합니다.**

   ![검색](./media/howto-create-service-principal-portal/select-subscription.png)

1. 서비스 주체를 만들 구독을 선택합니다.

   ![할당을 위한 구독 선택](./media/howto-create-service-principal-portal/select-one-subscription.png)

   찾고 있는 구독이 표시되지 않으면 **전역 구독 필터**를 선택합니다. 포털에 대해 원하는 구독이 선택되었는지 확인합니다.

1. **내 사용 권한**을 선택합니다. 그런 다음, **이 구독에 대한 전체 액세스 세부 정보를 보려면 여기를 클릭합니다.** 를 선택합니다.

   ![에서 서비스 주체를 만들 구독을 선택합니다.](./media/howto-create-service-principal-portal/view-details.png)

1. 역할 **할당에서** **보기를** 선택하여 할당된 역할을 보고 AD 앱에 역할을 할당할 수 있는 적절한 권한이 있는지 확인합니다. 이러한 권한이 없으면 구독 관리자에게 사용자 액세스 관리자 역할에 사용자를 추가할 것을 요청합니다. 다음 이미지에서 사용자에게 는 적절한 권한이 있음을 의미하는 소유자 역할이 할당됩니다.

   ![이 예제에서는 사용자에게 소유자 역할이 할당되어 있습니다.](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>다음 단계

* 보안 정책 지정에 대해 자세히 알아보려면 [Azure 역할 기반 Access Control](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.  
* 권한이 부여되거나 사용자에 대해 거부될 수 있는 작업 목록은 [Azure Resource Manager 리소스 공급자 작업](../../role-based-access-control/resource-provider-operations.md)을 참조하세요.
