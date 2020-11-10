---
title: 호스트 된 테스트 드라이브에 대 한 Azure Marketplace 구독 설정
description: Dynamics 365 for Customer Engagement 및 운영 시험 드라이브용 Dynamics 365에 대 한 Azure Marketplace 구독을 설정 하는 방법을 설명 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 11/09/2020
ms.openlocfilehash: 8e77c21c6d776aef20fa3ca71ad12fdb424b07ab
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94415020"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>호스트 된 테스트 드라이브에 대 한 Azure Marketplace 구독 설정

이 문서에서는 테스트 드라이브에 대해 고객 Engagement 또는 **dynamics 365 For Operations** 환경용 Azure Marketplace 구독 및 **dynamics 365** 을 설정 하는 방법을 설명 합니다.

## <a name="set-up-for-dynamics-365-for-customer-engagement"></a>Dynamics 365 for Customer Engagement 설정

1. 관리자 계정으로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.
2. 오른쪽 위 모서리의 계정 아이콘을 가리켜 Dynamics 365 테스트 드라이브 인스턴스와 연결 된 테 넌 트에 있는지 확인 합니다. 올바른 테 넌 트에 없는 경우 계정 아이콘을 선택 하 여 올바른 테 넌 트로 전환 합니다.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="올바른 테 넌 트를 선택 합니다.":::

3. **Dynamics 365 Customer Engagement 계획** 라이선스를 사용할 수 있는지 확인 합니다.

    [![계획 라이선스를 확인 하는 중입니다.](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. Azure에서 AD (Azure Active Directory) 앱을 만듭니다. AppSource는이 앱을 사용 하 여 테 넌 트에서 테스트 드라이브 사용자를 프로 비전 하 고 프로 비전 해제 합니다.
    1. 필터 창에서 **Azure Active Directory** 을 선택 합니다.
    2. **앱 등록** 을 선택합니다.

        [![앱 등록을 선택 합니다.](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. **새 등록** 을 선택합니다.
    4. 적절 한 응용 프로그램 이름을 제공 합니다.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="응용 프로그램을 등록 합니다.":::

    5. 지원 되는 계정 유형에서 **조직 디렉터리와 개인 Microsoft 계정의 계정** 을 선택 합니다.
    6. **만들기** 를 선택 하 고 앱이 생성 될 때까지 기다립니다.
    7. 앱이 만들어지면 개요 화면에 표시 된 **응용 프로그램 ID** 를 확인 합니다. 나중에 테스트 드라이브를 구성할 때이 값이 필요 합니다.
    8. Nativeclient 리디렉션 URI를 추가 하려면 **인증** 블레이드를 선택 합니다. **플랫폼 구성** 에서 **플랫폼**  >  **모바일**  >  **데스크톱** 응용 프로그램 추가 타일을 선택 합니다. **Nativeclient** 리디렉션 URI를 선택 하 고 **구성** 을 선택 합니다.

        :::image type="content" source="./media/test-drive/configure-desktop-devices.png" alt-text="Nativeclient 리디렉션 URI를 추가 합니다.":::

    9. **응용 프로그램 관리** 에서 **API 권한** 을 선택 합니다.
    10. **권한 추가** 를 선택 하 고 **API를 Microsoft Graph** 합니다.
    11. **응용 프로그램** 권한 범주를 선택 하 고 **디렉터리. all** 및 **디렉터리. ReadWrite** 를 선택 합니다.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="응용 프로그램 사용 권한 설정":::

    12. 허용 목록 Azure AD 앱에 대 한 **DYNAMICS CRM-사용자 가장** 액세스를 추가 하려면 **권한 추가** 를 다시 선택 합니다.

        :::image type="content" source="./media/test-drive/request-api-permissions.png" alt-text="응용 프로그램 사용 권한 요청":::

    13. 권한이 추가 되 면 **Microsoft에 관리자 동의 부여** 를 선택 합니다.
    14. 메시지 경고에서 **예** 를 선택 합니다.

        [![응용 프로그램 권한을 성공적으로 부여 하는 방법을 보여 줍니다.](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    15. Azure AD 앱에 대 한 암호를 생성 하려면:
        1. **응용 프로그램 관리** 에서 **인증서 및 암호** 를 선택 합니다.
        2. 클라이언트 암호에서 **새 클라이언트 암호** 를 선택 합니다.
        3. *시험 드라이브* 와 같은 설명을 입력 하 고 적절 한 기간을 선택 합니다. 이 키가 만료 되 면 테스트 드라이브가 중단 됩니다 .이 시점에서 AppSource에 새 키를 생성 하 고 제공 해야 합니다.
        4. **추가** 를 선택 하 여 Azure 앱 암호를 생성 합니다. 이 블레이드를 최대한 빨리 숨길 수 있으므로이 값을 복사 합니다. 나중에 테스트 드라이브를 구성할 때이 값이 필요 합니다.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="클라이언트 암호를 추가 합니다.":::

5. 경우에 따라 사용자를 Azure AD에서 CRM 인스턴스로 동기화 하는 데 예상 보다 오래 걸립니다. 이를 지원 하기 위해 사용자를 강제로 동기화 하는 프로세스를 추가 했지만 파트너 센터에서 Azure AD 응용 프로그램을 허용 목록 해야 합니다. 이렇게 하려면 [고객 참여 인스턴스로 사용자 동기화](https://github.com/microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/CDS_Utility_to_ForceUserSync_in_CRM_Instance.md)를 참조 하세요.
6. 응용 프로그램에 서비스 주체 역할을 추가 하 여 Azure AD 앱이 Azure 테 넌 트에서 사용자를 제거할 수 있도록 합니다.
    1. 관리 수준 PowerShell 명령 프롬프트를 엽니다.
    2. MSOnline을 Install-Module (MSOnline이 설치 되어 있지 않은 경우이 명령을 실행).
    3. Connect-MsolService (팝업 창이 표시 됩니다. 새로 만든 조직 테 넌 트를 사용 하 여 로그인).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = Get-MsolServicePrincipal-AppPrincipalId $applicationId.
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1-RoleMemberObjectId $sp. ObjectId-RoleMemberType servicePrincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="계정에 로그인 합니다.":::

7. 위에서 만든 Azure 앱을 테스트 드라이브 CRM 인스턴스에 응용 프로그램 사용자로 추가 합니다.
    1. **Azure Active Directory** 에 새 사용자를 추가 합니다. 동일한 테 넌 트에 속하는 **이름** 및 **사용자** 이름 값만이 사용자를 만드는 데 필요 하며 다른 필드는 기본값으로 둡니다. 사용자 이름 값을 복사 합니다.
    2. **CRM 인스턴스에** 로그인 하 고 보안 **사용자 설정** 을 선택  >  **Security**  >  **Users** 합니다.
    3. **응용 프로그램 사용자** 로 보기를 변경 합니다.

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="사용자에 대 한 계정 정보를 설정 합니다.":::

    4. 새 사용자를 추가 합니다 (응용 프로그램 사용자에 대 한 양식 인지 확인).
    5. **기본 전자 메일** 및 **사용자 이름** 필드에 같은 사용자 이름을 입력 합니다. **응용 프로그램 ID** 에 **Azure ApplicationId** 를 추가 합니다.
    6. **전체 이름을** 지정 합니다.
    7. **저장** 을 선택합니다.
    8. **역할 관리** 를 선택 합니다.
    9. *시스템 관리자* 와 같은 읽기, 쓰기 및 할당 역할 권한을 포함 하는 사용자 지정 또는 OOB 보안 역할을 할당 합니다.

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="역할 권한 선택":::

    10. 테스트 드라이브에 대해 만든 사용자 지정 보안 역할을 응용 프로그램 사용자에 게 할당 합니다.

## <a name="set-up-for-dynamics-365-for-operations"></a>운영에 대 한 Dynamics 365 설정

1. 관리자 계정으로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.
2. 오른쪽 위 모서리의 계정 아이콘을 가리켜 Dynamics 365 테스트 드라이브 인스턴스와 연결 된 테 넌 트에 있는지 확인 합니다. 올바른 테 넌 트에 없는 경우 계정 아이콘을 선택 하 여 올바른 테 넌 트로 전환 합니다.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="올바른 테 넌 트를 선택 합니다.":::

3. Azure에서 Azure AD 앱를 만듭니다. AppSource는이 앱을 사용 하 여 테 넌 트에서 테스트 드라이브 사용자를 프로 비전 하 고 프로 비전 해제 합니다.
    1. 필터 창에서 **Azure Active Directory** 을 선택 합니다.
    2. **앱 등록** 을 선택합니다.

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="앱 등록을 선택 합니다.":::

    3. **새 등록** 을 선택합니다.
    4. 적절 한 응용 프로그램 이름을 제공 합니다.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="응용 프로그램을 등록 합니다.":::

    5. 지원 되는 계정 유형에서 **조직 디렉터리와 개인 Microsoft 계정의 계정** 을 선택 합니다.
    6. **만들기** 를 선택 하 고 앱이 생성 될 때까지 기다립니다.
    7. 앱이 만들어지면 개요 화면에 표시 된 **응용 프로그램 ID** 를 확인 합니다. 나중에 테스트 드라이브를 구성할 때이 값이 필요 합니다.
    8. **응용 프로그램 관리** 에서 **API 권한** 을 선택 합니다.
    9. **권한 추가** 를 선택 하 고 **API를 Microsoft Graph** 합니다.
    10. **응용 프로그램** 권한 범주를 선택 하 고 **디렉터리. all** 및 **디렉터리. ReadWrite** 를 선택 합니다.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="응용 프로그램 사용 권한 설정":::

    11. **권한 추가** 를 선택 합니다.
    12. 권한이 추가 되 면 **Microsoft에 관리자 동의 부여** 를 선택 합니다.
    13. 메시지 경고에서 **예** 를 선택 합니다.

        [![응용 프로그램 권한을 성공적으로 부여 하는 방법을 보여 줍니다.](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. Azure AD 앱에 대 한 암호를 생성 하려면:
        1. **응용 프로그램 관리** 에서 **인증서 및 암호** 를 선택 합니다.
        2. 클라이언트 암호에서 **새 클라이언트 암호** 를 선택 합니다.
        3. *시험 드라이브* 와 같은 설명을 입력 하 고 적절 한 기간을 선택 합니다. 이 키가 만료 되 면 테스트 드라이브가 중단 됩니다 .이 시점에서 AppSource에 새 키를 생성 하 고 제공 해야 합니다.
        4. **추가** 를 선택 하 여 Azure 앱 암호를 생성 합니다. 이 블레이드를 최대한 빨리 숨길 수 있으므로이 값을 복사 합니다. 나중에 테스트 드라이브를 구성할 때이 값이 필요 합니다.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="클라이언트 암호를 추가 합니다.":::

4. 응용 프로그램에 서비스 주체 역할을 추가 하 여 Azure AD 앱이 Azure 테 넌 트에서 사용자를 제거할 수 있도록 합니다.
    1. 관리 수준 PowerShell 명령 프롬프트를 엽니다.
    2. MSOnline을 Install-Module (MSOnline이 설치 되어 있지 않은 경우이 명령을 실행).
    3. Connect-MsolService (팝업 창이 표시 됩니다. 새로 만든 조직 테 넌 트를 사용 하 여 로그인).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = Get-MsolServicePrincipal-AppPrincipalId $applicationId.
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1-RoleMemberObjectId $sp. ObjectId-RoleMemberType servicePrincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="계정에 로그인 합니다.":::

5. 이제 앱이 사용자를 관리할 수 있도록 하는 **작업을 위해 Dynamics 365** 에 위의 앱을 추가 합니다.
    1. 운영 인스턴스의 **Dynamics 365** 을 찾습니다.
    2. 왼쪽 위 모서리에서 3 선 메뉴 (햄버거)를 클릭 합니다.
    3. **시스템 관리** 를 선택 합니다.
    4. **Azure Active Directory 응용 프로그램** 을 선택 합니다.
    5. **+새로 만들기** 를 선택합니다.
    6. 작업을 대신 하 여 수행할 **AZURE AD 앱의 클라이언트 ID** 를 입력 합니다.

    > [!NOTE]
    > 작업을 수행 하는 사용자 ID입니다. 일반적으로 인스턴스의 시스템 관리자 이거나 다른 사용자를 추가할 수 있는 권한이 있는 사용자입니다.

    [![작업을 수행 하는 사용자 ID입니다. 일반적으로 인스턴스의 시스템 관리자 이거나 다른 사용자를 추가할 수 있는 권한이 있는 사용자입니다.](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->
