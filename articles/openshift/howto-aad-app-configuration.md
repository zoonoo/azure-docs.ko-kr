---
title: Azure Red Hat OpenShift에 대 한 Azure AD 앱 등록 및 사용자 만들기 | Microsoft Docs
description: 서비스 주체 만들기, 클라이언트 암호 및 인증 콜백 URL을 생성 및 Microsoft Azure의 Red Hat OpenShift 클러스터에서 앱을 테스트 하는 것에 대 한 새 Active Directory 사용자를 만드는 방법에 알아봅니다.
author: tylermsft
ms.author: twhitney
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/06/2019
ms.openlocfilehash: de3f3c30848d26ea399bcccc29a6149a149f6a55
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078522"
---
# <a name="create-an-azure-ad-app-registration-and-user-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift에 대 한 Azure AD 앱 등록 및 사용자 만들기

Microsoft Azure의 Red Hat OpenShift 클러스터를 대신 하 여 작업을 수행할 권한이 필요 합니다. 조직에 서비스 주체를 사용 하려면 Azure Active Directory (Azure AD) 앱 등록을 아직 만들려면 다음이 지침을 따릅니다.

이 항목에서는 새 만들기에 대 한 지침을 사용 하 여 마지막으로 Azure의 Red Hat OpenShift 클러스터에서 실행 되는 앱에 액세스 해야 하는 Azure AD 사용자입니다.

이미 Azure AD 테 넌 트를 만든 하지 않은 경우의 지침을 따르고 [Azure Red Hat OpenShift는 Azure AD 테 넌 트 만들기](howto-create-tenant.md) 이러한 지침을 사용 하 여 계속 하기 전에 합니다.

## <a name="create-a-new-app-registration"></a>새 앱 등록 만들기

Azure AD의 기능을 사용 하려는 응용 프로그램은 Azure AD 테 넌 트에 먼저 등록 되어야 합니다. 이 등록 프로세스에서는 응용 프로그램이 위치한 URL, 사용자가 인증 후 회신을 보낼 URL, 앱 및 등을 식별 하는 URI와 같은 응용 프로그램에 대 한 Azure AD 세부 정보를 제공 합니다.

1. 에 [Azure portal](https://portal.azure.com), 테 넌 트 위쪽에서 사용자 이름 아래에 나타나는지 확인 포털의 오른쪽:

    ![맨 위 오른쪽에 나열 된 테 넌 트를 사용 하 여 포털의 스크린샷][tenantcallout] 잘못 된 테 넌 트 표시 되지 않으면 오른쪽 위에서 사용자 이름을 클릭 한 다음 클릭 **디렉터리 전환**를 올바른 테 넌 트를 선택 하 고는 **모든 디렉터리** 목록입니다.

2. 엽니다는 [앱 등록 블레이드](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) 클릭 **새 응용 프로그램 등록**합니다.
3. 에 **만들기** 창 응용 프로그램 개체에 대 한 이름을 입력 합니다.
4. 했는지 **응용 프로그램 종류** 로 설정 된 *웹 앱/API*합니다.
5. 만들기는 **로그온 URL** 패턴을 사용 하 여:

    ```
    https://<cluster-name>.<azure-region>.cloudapp.azure.com
    ```

    . . . 여기서 `<cluster-name>` Azure Red Hat OpenShift 클러스터의 의도 한 이름 및 `<azure-region>` 는 [Azure Red Hat OpenShift 클러스터를 호스트 하는 Azure 지역과](supported-resources.md#azure-regions)합니다. 예를 들어 클러스터 이름이 되도록 `contoso`, 만들어야에서 및는 `eastus` 지역에 입력할 수 있는 정규화 된 도메인 이름 (FQDN)를 **로그온 URL** 것 `https://contoso.eastus.cloudapp.azure.com`

    > [!IMPORTANT]
    > 클러스터 이름은 모두 소문자 여야 합니다 및 FQDN URL은 고유 해야 합니다.
    > 클러스터에 대해 충분히 고유 이름을 선택 하면 확인 합니다.

    클러스터 이름을 기록해 및 로그온 URL-해야 해당 나중에 클러스터에서 실행 되는 앱 액세스 합니다. 클러스터 이름으로 참조 `CLUSTER_NAME`, 및이 로그온 URL로 `FQDN` 에 [Azure Red Hat OpenShift 클러스터를 만드는](tutorial-create-cluster.md) 자습서입니다.

6. 확인 프로그램 **로그온 URL** 녹색 확인 표시를 사용 하 여 값의 유효성을 검사 합니다. (의 초점을 맞춰 Tab 키를 *로그온 URL* 필드 및 유효성 검사를 실행 합니다.)
7. 클릭 합니다 **만들기** 단추를 Azure AD 응용 프로그램 개체를 만듭니다.
8. 에 **등록 된 앱** 적어둡니다 나타나는 페이지의 **응용 프로그램 ID**합니다. 이 값으로 이라고 `APPID` 에 [Azure Red Hat OpenShift 클러스터를 만드는](tutorial-create-cluster.md) 자습서.

    ![응용 프로그램 ID 텍스트 상자 스크린샷][appidimage]
    
Azure 응용 프로그램 개체에 대 한 자세한 내용은 참조 하세요. [응용 프로그램 및 Azure Active Directory에서 서비스 주체 개체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)합니다.

새로 만들기에 대 한 Azure AD 응용 프로그램을 참조 하세요 [Azure Active Directory v1.0 끝점을 사용 하 여 앱을 등록](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)합니다.

### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

이제 Azure Active Directory에 앱을 인증 하는 것에 대 한 클라이언트 암호를 생성할 준비가 되었습니다.

1. **앱 등록** 페이지에서 클릭 **설정** 하 여 등록 된 앱에 대 한 설정을 엽니다.
2. 에 **설정을** 나타나는 창 클릭 **키**합니다.  합니다 **키** 창이 나타납니다.
![포털에서 키 만들기 페이지의 스크린샷][createkeyimage]
3. 키를 제공 **설명을**합니다.
4. 값을 설정 **Expires**예를 들어 *In 2 Years*합니다.
5. 클릭 **저장할** 및 키 값이 표시 됩니다.
6. 키 값을 적어둡니다. 이 값으로 이라고 `SECRET` 에 [Azure Red Hat OpenShift 클러스터를 만드는](tutorial-create-cluster.md) 자습서.

### <a name="create-a-reply-url"></a>회신 URL 만들기

앱 개체를 사용 하는 azure AD *회신 URL* 앱에 권한을 부여할 때 사용 하 여 콜백을 지정 합니다. Web API 또는 웹 응용 프로그램의 경우 회신 URL에 있는 Azure AD가 인증에 성공 하는 경우 토큰을 포함 하는 인증 응답을 보내는 위치입니다.

최소 일치 하지 않는 (후행 슬래시 누락 된 경우 다른 대/소문자 구분) 토큰 발급 작업이 실패 하 고 로그인 할 수 없습니다.

1. 돌아가서 합니다 **설정** 창 (클릭할 수 **설정** 포털의 맨 위에 있는 이동 경로 탐색), 클릭 **회신 Url** 오른쪽.  합니다 **회신 Url** 창이 나타납니다.
2. 목록의 첫 번째 회신 url을 `FQDN` 의 6 단계에서 값 [새 앱 등록 만들기](#create-a-new-app-registration)합니다. 편집 하 고 추가 `/oauth2callback/Azure%20AD`합니다.  예를 들어, 회신 URL 이제 비슷합니다. `https://mycluster.eastus.cloudapp.azure.com/oauth2callback/Azure%20AD`
3. 클릭 **저장할** 회신 URL을 저장 합니다.

## <a name="create-a-new-active-directory-user"></a>새 Active Directory 사용자 만들기

Azure Red Hat OpenShift 클러스터에서 실행 되는 앱에 로그인 하는 데 Active Directory에서 새 사용자를 만듭니다.

1. 로 이동 합니다 [사용자-모든 사용자에 게](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) 블레이드입니다.
2. **+ 새 사용자**를 클릭합니다. 합니다 **사용자** 창이 나타납니다.
3. 입력을 **이름** 이 사용자에 대 한 선택입니다.
4. 만들기는 **사용자 이름** 사용 하 여 만든 테 넌 트 이름을 기반으로 `.onmicrosoft.com` 끝에 추가 됩니다. 예: `yourUserName@yourTenantName.onmicrosoft.com`. 이 사용자 이름을 적어 둡니다. 하 여 클러스터에서 앱을 사용 하려면 로그인 해야 합니다.
5. 클릭 **디렉터리 역할** 선택한 **전역 관리자** 클릭 하 고 **확인** 창의 맨 아래에 있습니다.
6. 가운데에 **사용자** 창 클릭 **암호 표시** 하 고 임시 암호를 기록 합니다. 처음으로 로그인 한 후 다시 설정 하려면 묻는 메시지가 나타납니다.
7. 창의 맨 아래에서 클릭 **만들기** 사용자를 만들려고 합니다.

## <a name="resources"></a>리소스

* [응용 프로그램 및 Azure Active Directory에서 서비스 주체 개체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [빠른 시작: Azure Active Directory v1.0 끝점을 사용 하 여 앱을 등록 합니다.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

[appidimage]: ./media/howto-create-tenant/get-app-id.png
[createkeyimage]: ./media/howto-create-tenant/create-key.png
[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="next-steps"></a>다음 단계

모든 충족 한 경우는 [Azure Red Hat OpenShift 필수 구성 요소](howto-setup-environment.md), 첫 번째 클러스터를 만들 준비가 되었습니다.!

이 자습서를 시도해 보세요.
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md)
