<properties
    pageTitle="Azure AD Connect Health 작업."
    description="이 문서는 Azure AD Connect Health가 배포되면 수행할 수 있는 추가 작업에 대해 설명합니다."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>


# <a name="azure-ad-connect-health-operations"></a>Azure AD Connect Health 작업

다음 항목에서는 Azure AD Connect Health를 사용하여 수행할 수 있는 다양한 작업에 대해 설명합니다.

## <a name="enable-email-notifications"></a>전자 메일 알림 사용
ID 인프라가 정상이 아님을 나타내는 경고가 생성되면 전자 메일 알림을 보내도록 Azure AD Connect Health Service를 구성할 수 있습니다. 알림은 경고가 생성되는 경우 및 해결된 것으로 표시되는 경우에 발생합니다. 전자 메일 알림을 구성하려면 아래 지침을 따르세요.

![Azure AD Connect Health 메일 알림 검색](./media/active-directory-aadconnect-health/email_noti_discover.png)

>[AZURE.NOTE] 전자 메일 알림은 기본적으로 사용하지 않도록 설정되어 있습니다.


### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Azure AD Connect Health 전자 메일 알림을 사용하도록 설정 하려면

1. 전자 메일 알림을 받을 서비스의 경고 블레이드를 엽니다.
2. 작업 모음에서 "알림 설정" 단추를 클릭합니다.
3. 전자 메일 알림 스위치를 켜기로 설정합니다.
4. 모든 전역 관리자가 전자 메일 알림을 받도록 구성하려면 확인란을 선택합니다.
5. 다른 전자 메일 주소로 전자 메일 알림을 받으려는 경우 추가 전자 메일 수신자 상자에 주소를 지정할 수 있습니다. 이 목록에서 전자 메일 주소를 제거하려면 항목을 마우스 오른쪽 단추로 클릭하고 삭제를 선택합니다.
6. 변경을 완료하려면 "저장"을 클릭합니다. 변경 내용은 모두 "저장"을 선택해야 적용됩니다.

## <a name="delete-a-server-or-service-instance"></a>서버 또는 서비스 인스턴스 삭제

### <a name="delete-a-server-from-azure-ad-connect-health-service"></a>Azure AD Connect Health Service에서 서버 삭제
서버를 모니터링 대상에서 삭제하려는 경우가 있습니다. Azure AD Connect Health Service에서 서버를 제거하려면 아래 지침을 따르세요.

서버를 삭제하는 경우 다음 사항에 유의하세요.

- 이 작업은 해당 서버로부터의 모든 추가 데이터 수집을 중지합니다. 모니터링 서비스에서 이 서버가 제거됩니다. 이 작업을 수행한 후에는 이 서버에 대한 새 경고나 모니터링 데이터, 사용 현황 분석 데이터를 볼 수 없습니다.
- 이 작업은 서버에서 Health Agent를 제거하지 않습니다. 이 단계를 수행하기 전에 Health Agent를 제거하지 않은 경우 Health Agent와 관련된 서버에 오류 이벤트가 표시될 수 있습니다.
- 이 작업은 이 서버에서 이미 수집된 데이터를 삭제하지 않습니다. 해당 데이터는 Microsoft Azure 데이터 보존 정책에 따라 삭제됩니다.
- 이 작업을 수행한 후 동일한 서버 모니터링을 다시 시작하려면 이 서버에서 Health Agent를 제거한 후 다시 설치해야 합니다.


#### <a name="to-delete-a-server-from-azure-ad-connect-health-service"></a>Azure AD Connect Health Service에서 서버를 삭제하려면

AD FS에 대한 Azure AD Connect Health 및 Azure AD Connect(Sync):

1. 서버 목록 블레이드에서 제거할 서버 이름을 선택하여 서버 블레이드를 엽니다.
2. 서버 블레이드의 작업 모음에서 "삭제" 단추를 클릭합니다.
3. 확인 상자에 서버 이름을 입력하여 서버 삭제 작업을 확인합니다.
4. "삭제" 단추를 클릭합니다.

AD DS용 Azure AD Connect Health:

1. 도메인 컨트롤러 대시보드를 엽니다.
2. 제거할 도메인 컨트롤러를 선택합니다.
3. 작업 모음에서 "선택한 항목 삭제" 단추를 클릭합니다.
4. 서버 삭제 작업을 확인합니다.
5. "삭제" 단추를 클릭합니다.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Azure AD Connect Health Service에서 서비스 인스턴스 삭제

서비스 인스턴스를 제거하려는 경우가 있습니다. Azure AD Connect Health Service에서 서비스 인스턴스를 제거하려면 아래 지침을 따르세요.

서비스 인스턴스를 삭제하는 경우 다음 사항에 유의하세요.

- 이 작업은 현재 서비스 인스턴스를 모니터링 서비스에서 제거합니다.
- 이 작업은 이 서비스 인스턴스의 일부로 모니터링된 서버에서 Health Agent를 제거하지 않습니다. 이 단계를 수행하기 전에 Health Agent를 제거하지 않은 경우 Health Agent와 관련된 서버에 오류 이벤트가 표시될 수 있습니다.
- 이 서비스 인스턴스의 모든 데이터는 Microsoft Azure 데이터 보존 정책에 따라 삭제됩니다.
- 이 작업을 수행한 후 서비스 모니터링을 시작하려면 모니터링될 모든 서버에서 Health Agent를 제거한 후 다시 설치하세요. 이 작업을 수행한 후 동일한 서버 모니터링을 다시 시작하려면 이 서버에서 Health Agent를 제거한 후 다시 설치해야 합니다.


#### <a name="to-delete-a-service-instance-from-azure-ad-connect-health-service"></a>Azure AD Connect Health Service에서 서비스 인스턴스를 삭제하려면

1. 서비스 목록 블레이드에서 제거하려는 서비스 식별자(팜 이름)를 선택하여 서비스 블레이드를 엽니다.
2. 서버 블레이드의 작업 모음에서 "삭제" 단추를 클릭합니다.
3. 확인 상자에 서비스 이름을 입력하여 작업을 확인합니다. (예: sts.contoso.com)
4. "삭제" 단추를 클릭합니다.
<br><br>


[//]: # (Start of RBAC section)
## <a name="manage-access-with-role-based-access-control"></a>역할 기반 액세스 제어를 사용하여 액세스 관리
### <a name="overview"></a>개요
[역할 기반 액세스 제어](role-based-access-control-configure.md) 는 전역 관리자 외부에서 사용자 및/또는 그룹을 Azure AD Connect Health 서비스에 액세스를 제공합니다. 사용자 및/또는 그룹에 역할을 할당하여 이루어지고 디렉터리 내에서 전역 관리자를 제한하는 메커니즘을 제공합니다.

#### <a name="roles"></a>역할
Azure AD Connect Health는 다음과 같은 기본 제공 역할을 지원합니다.

| 역할 | 권한 |
| ----------- | ---------- |
| 소유자 | 소유자는 ***액세스를 관리***(예: 사용자/그룹에 역할 할당)하고 포털에서 ***모든 정보를 확인***(예: 경고 보기)하며 Azure AD Connect Health 내에서 ***설정을 변경***(예: 전자 메일 알림)할 수 있습니다. <br>기본적으로 Azure AD 전역 관리자는 해당 역할에 할당되며 이는 변경될 수 없습니다.  |
|참여자|  참여자는 포털에서 ***모든 정보를 확인***(예: 경고 보기)하며 Azure AD Connect Health 내에서 ***설정을 변경***(예: 전자 메일 알림)할 수 있습니다.|
|리더| 판독기는 Azure AD Connect Health 내의 포털에서 ***모든 정보를 볼***(예: 경고 보기) 수 있습니다.|

다른 모든 역할(예: 사용자 액세스 관리자' 또는 'DevTest 랩 사용자')은 포털 환경에서 사용할 수 있는 경우에도 Azure AD Connect Health 내에 액세스하는 데 아무런 영향을 주지 않습니다.

#### <a name="access-scope"></a>액세스 범위

Azure AD Connect는 두 수준에서 액세스 관리를 지원합니다.

- ***모든 서비스 인스턴스***: 대부분의 고객에 대 한 권장되는 경로이며 Azure AD Connect Health에서 모니터링되는 모든 역할 형식에서 모든 서비스 인스턴스(예: ADFS 팜)에 대한 액세스를 제어합니다.

- ***서비스 인스턴스***: 역할 유형에 기반하거나 서비스 인스턴스에 의해 액세스를 분리해야 하는 경우도 있습니다. 이 경우 서비스 인스턴스 수준에서 액세스를 관리할 수 있습니다.  

권한은 최종 사용자가 디렉터리 또는 서비스 인스턴스 수준에 엑세스를 갖는 경우 부여됩니다.


### <a name="how-to-allow-users-or-groups-access-to-azure-ad-connect-health"></a>사용자 또는 그룹에 Azure AD Connect Health에 대한 액세스를 허용하는 방법
#### <a name="steps-1:-select-the-appropriate-access-scope"></a>1단계: 적절한 액세스 범위 선택
Azure AD Connect Health 내에서 *모든 서비스 인스턴스* 수준으로 사용자에게 액세스를 허용하려면 Azure AD Connect Health에서 주 블레이드를 엽니다.<br>
#### <a name="step-2:-add-users,-groups-and-assign-roles"></a>2단계: 사용자, 그룹 추가 및 역할 할당
1. 구성 섹션에서 "사용자" 부분을 클릭합니다.<br>
![Azure AD Connect Health RBAC 기본 블레이드](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. "추가" 선택
3. "소유자"와 같은 "역할"을 선택합니다.<br>
![Azure AD Connect Health RBAC 사용자 추가](./media/active-directory-aadconnect-health/RBAC_add.png)
4. 사용자 또는 그룹을 대상으로 한 이름 또는 식별자를 입력합니다. 동시에 하나 이상의 사용자 또는 그룹을 선택할 수 있습니다. "선택"을 클릭합니다.
![Azure AD Connect Health RBAC 사용자 선택](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. "확인"을 선택합니다.<br>

6. 역할 할당이 완료되면 목록에서 사용자 및/또는 그룹이 표시됩니다.<br>
![Azure AD Connect Health RBAC 사용자 목록](./media/active-directory-aadconnect-health/RBAC_user_list.png)

이러한 단계는 할당된 역할에 맞춰 나열된 사용자 및 그룹 액세스에 따라 허용합니다.
>[AZURE.NOTE]
- 전역 관리자는 항상 모든 작업에 모든 권한을 갖지만 전역 권리자 계정은 위의 목록에 표시되지 않습니다.
- "사용자 초대" 기능은 Azure AD Connect Health 내에서 지원되지 않습니다.

#### <a name="step-3:-share-the-blade-location-with-users-or-groups"></a>3단계: 사용자 또는 그룹을 사용하여 블레이드 위치 공유
1. 사용 권한을 할당한 후에 사용자는 [http://aka.ms/aadconnecthealth](http://aka.ms/aadconnecthealth)에 이동하여 Azure AD Connect Health를 액세스할 수 있습니다.
2. 블레이드에서 사용자는 "대시보드에 고정"를 클릭하여 대시보드에 블레이드 또는 다른 부분을 고정할 수 있습니다.<br>
![Azure AD Connect Health RBAC 블레이드 고정](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)


>[AZURE.NOTE] "읽기" 역할이 할당된 사용자는 "만들기" 작업을 수행하여 Azure 마켓플레이스에서 Azure AD Connect Health 확장을 가져올 수 없습니다. 이 사용자는 위의 링크로 이동하여 블레이드를 가져올 수 있습니다. 이후 사용의 경우 사용자는 대시보드에 블레이드를 고정할 수 있습니다.

### <a name="remove-users-and/or-groups"></a>사용자 및/또는 그룹 제거
마우스 오른쪽 단추로 클릭하고 제거를 선택하여 Azure AD Connect Health 역할 기반 액세스 제어 부분에 추가된 그룹 또는 사용자를 제거할 수 있습니다.<br>
![Azure AD Connect Health RBAC 사용자 제거](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (End of RBAC section)

## <a name="related-links"></a>관련 링크

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health Agent 설치](active-directory-aadconnect-health-agent-install.md)
* [AD FS와 함께 Azure AD Connect Health 사용](active-directory-aadconnect-health-adfs.md)
* [동기화에 대한 Azure AD Connect Health 사용](active-directory-aadconnect-health-sync.md)
* [AD DS와 함께 Azure AD Connect Health 사용](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health FAQ](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health 버전 내역](active-directory-aadconnect-health-version-history.md)



<!--HONumber=Oct16_HO2-->


