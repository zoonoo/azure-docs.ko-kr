<properties
  pageTitle="Azure IoT Suite 및 Azure Active Directory | Microsoft Azure"
  description="Azure IoT Suite에서 Azure Active Directory를 사용하여 사용 권한을 관리하는 방법을 설명합니다."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="hero-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="11/17/2015"
  ms.author="araguila"/>
  
# azureiotsuite.com 사이트에 대한 사용 권한

## 로그인 후 진행되는 작업

[azureiotsuite.com][lnk-azureiotsuite]에 처음 로그인하면, 사이트에서는 현재 선택된 Azure Active Directory(AAD) 테넌트 및 Azure 구독을 기반으로 사용자가 소유하는 권한 수준을 판단합니다.

1.  우선, 로그인 사용자 이름 옆에 표시되는 테넌트 목록을 채우기 위해서 Azure로부터 사용자가 속하는 AAD 테넌트를 알아냅니다. 이 때 사이트는 테넌트에 대한 사용자 토큰을 한 번에 하나만 가져올 수 있습니다. 결과적으로 오른쪽 상단 모서리의 드롭다운을 사용하여 다른 테넌트로 전환하면, 사이트는 이 테넌트에 대한 토큰을 가져오기 위해서 해당 테넌트로 사용자를 다시 로그인합니다.

2.  다음으로, 사이트는 사용자가 선택한 테넌트와 연결된 구독을 Azure로부터 알아냅니다. 미리 구성된 솔루션을 새로 만들 때 사용할 수 있는 구독이 표시됩니다.

3.  마지막으로 사이트는 미리 구성된 솔루션으로 태그가 지정된 구독 및 리소스 그룹에서 모든 리소스를 가져와서 홈 페이지에 타일을 채웁니다.

다음 섹션은 미리 구성된 솔루션에 대한 액세스를 제어하는 역할을 설명합니다.

## AAD 역할

AAD 역할은 권한 프로비전 미리 구성된 솔루션을 제어하고 미리 구성된 솔루션의 사용자를 관리합니다.

AAD의 관리자 역할에 대한 자세한 내용은 [Azure AD에서 관리자 역할 할당][lnk-aad-admin]에서 찾아볼 수 있지만, 이 문서는 미리 구성된 솔루션에 사용되는 **전역 관리자** 및 **도메인 사용자/회원**에 주로 중점을 둔 문서입니다.

**전역 관리자:** AAD 테넌트에는 여러 명의 전역 관리자가 있을 수 있습니다. AAD 테넌트를 만들 때, 만드는 사람은 기본적으로 해당 테넌트의 전역 관리자입니다. 전역 관리자는 미리 구성된 솔루션을 프로비전할 수 있으며 자신의 AAD 테넌트 내부 응용 프로그램에 대해 **관리자** 역할이 할당됩니다. 하지만, 동일한 AAD 테넌트의 다른 사용자가 응용 프로그램을 만들 경우, 전역 사용자에게 부여되는 기본 역할은 **IMPLICIT READ ONLY**(암시적 읽기 전용)입니다. 전역 관리자는 [Azure 클래식 포털][lnk-classic-portal]을 사용하여 응용 프로그램에 대한 역할을 할당할 수 있습니다.

**도메인 사용자/회원:** AAD 테넌트에는 여러 명의 도메인 사용자/회원이 있을 수 있습니다. 도메인 사용자는 [azureiotsuite.com][lnk-azureiotsuite] 사이트를 통해 미리 구성된 솔루션을 프로비전할 수 있습니다. 자신이 프로비전하는 응용 프로그램에 대해 부여되는 기본 권한은 **관리자**입니다. [azure-iot-solution][lnk-github-repo] 리포지토리의 build.cmd 스크립트를 사용하여 응용 프로그램을 만들 수 있지만, 역할을 할당할 권한이 없기 때문에 이들에게 부여되는 기본 권한은 **IMPLICIT READONLY**(암시적 읽기 전용)입니다. 동일한 AAD 테넌트의 다른 사용자가 응용 프로그램을 만들 경우, 이들에게는 해당 응용 프로그램에 대해 기본적으로 **IMPLICIT READONLY**(암시적 읽기 전용) 역할이 할당됩니다. 이들에게는 응용 프로그램에 대해 역할을 할당할 권한이 없기 때문에, 응용 프로그램을 프로비전했더라도 응용 프로그램에 대해 사용자를 추가하거나 사용자에 대한 역할을 추가할 수 없습니다.

**게스트 사용자/게스트:** AAD 테넌트에는 여러 명의 게스트 사용자/게스트가 있을 수 있습니다. 게스트 사용자는 AAD 테넌트 내에서 제한된 권한 집합을 갖습니다. 결과적으로 게스트 사용자는 AAD 테넌트 내에서 미리 구성된 솔루션을 프로비전할 수 없습니다.

자세한 내용은 다음 리소스를 참조하세요.

- [Azure AD에서 사용자 만들기 또는 편집][lnk-create-edit-users]
- [AAD에서 앱 역할 할당][lnk-assign-app-roles]

## Azure 구독 관리자 역할

Azure 관리자 역할은 Azure 구독을 AD 텐넌트에 매핑할 수 있는 권한을 제어합니다.

Azure 공동 관리자, 서비스 관리자, 계정 관리자 역할에 대한 자세한 내용은 [Azure 공동 관리자, 서비스 관리자 및 계정 관리자를 추가 또는 변경하는 방법][lnk-admin-roles] 문서에서 찾아볼 수 있습니다.

## 응용 프로그램 역할

응용 프로그램 역할은 미리 구성된 솔루션의 장치에 대한 액세스를 제어합니다.

응용 프로그램에는 미리 구성된 솔루션을 프로비전할 때 생성되는 정의된 역할 2개와 암시적 역할 1개가 있습니다.

-   **관리자:** 장치를 추가, 관리, 제거할 수 있는 모든 권한을 갖습니다.

-   **읽기 전용:** 장치를 볼 수 있는 권한을 갖습니다.

-   **IMPLICIT READ ONLY(암시적 읽기 전용):** 읽기 전용과 같지만 AAD 테넌트의 모든 사용자에게 부여됩니다. 이 작업은 편의를 위해 배포 중에 수행됩니다. 이 역할은 [RolePermissions.cs][lnk-resource-cs] 원본 파일을 수정하여 제거할 수 있습니다.

### 응용 프로그램 역할 변경

사용자에 대한 역할을 변경하려면 AAD 전역 관리자여야 합니다.

1. [Azure 클래식 포털][lnk-classic-portal]로 이동합니다.

2. **Active Directory**를 선택합니다.

3. AAD 테넌트의 이름을 클릭합니다.

4. **응용 프로그램**을 클릭합니다.

5. 목록에 응용 프로그램이 표시되지 않으면 **표시** 드롭다운을 **회사가 보유한 응용 프로그램**으로 전환하고 확인 표시를 클릭합니다.

6. 미리 구성된 솔루션 이름과 일치하는 응용 프로그램의 이름을 클릭합니다.

7. **사용자**를 클릭합니다.

8. 역할을 변경할 사용자를 선택합니다.

9. 할당 단추와 할당할 역할을 클릭하고 확인 표시를 클릭합니다.

## FAQ

### 서비스 관리자가 구독과 특정 AAD 테넌트 간의 디렉터리 매핑을 변경하고자 합니다. 이 작업을 어떻게 수행하나요?

1. [Azure 클래식 포털][lnk-classic-portal]로 이동하여 왼쪽에 있는 서비스 목록에서 **설정**을 클릭합니다.

2. 디렉터리 매핑을 변경할 구독을 선택합니다.

3. **디렉터리 편집**을 클릭합니다.

4. 드롭다운에서 사용할 **디렉터리**를 선택합니다. 앞으로 화살표를 클릭합니다.

5. 디렉터리 매핑과 영향을 받는 공동 관리자를 확인합니다. 다른 디렉터리에서 이동하는 경우에는 원래 디렉터리의 공동 관리자가 모두 제거됩니다.

### AAD 테넌트의 도메인 사용자/회원이 미리 구성된 솔루션을 만들었습니다. 응용 프로그램에 대한 역할을 어떻게 할당하나요?

자신에게 역할을 할당하는 권한을 얻으려면 전역 관리자가 AAD 테넌트에서 글로벌 관리자로 할당해 줄 것을 요청하거나 전역 관리자에게 역할 할당을 요청합니다. 미리 구성된 솔루션이 배포된 AAD 테넌트를 변경하려면 다음 질문을 참조하세요.

### 원격 모니터링 미리 구성된 솔루션 및 응용 프로그램이 할당되어 있는 AAD 테넌트를 어떻게 변경하나요?

<https://github.com/Azure/azure-iot-remote-monitoring>에서 클라우드 배포를 실행하고 새로 생성된 AAD 테넌트와 다시 배포할 수 있습니다. 새로운 AAD 테넌트를 만들 때 기본적으로 전역 관리자가 되기 때문에, 사용자를 추가하고 이 사용자에 대해 역할을 추가하는 권한을 갖게 됩니다.

1. [Azure 관리 포털][lnk-classic-portal]에서 AAD 디렉터리를 새로 만듭니다.

2. <https://github.com/Azure/azure-iot-remote-monitoring>로 이동합니다. 클라우드 배포에 대한 자세한 내용은 [클라우드 배포][lnk-wiki-clouddeployment]를 참조하세요.

3. `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}`(예: `build.cmd cloud debug myRMSolution`)를 실행합니다.

4. 프롬프트가 표시되면, **tenantid**를 새로 만든 테넌트가(이전 테넌트 대신) 되도록 설정합니다.


### 조직 계정으로 로그인 할 때 서비스 관리자 또는 공동 관리자를 변경하려고 합니다.

지원 문서 [조직 계정으로 로그인하였을 때 서비스 관리자 및 공동 관리자 변경][lnk-service-admins]을 참조하세요.

### 왜 이 오류가 표시되나요? "Your account does not have the proper permissions to create a solution.(사용자의 계정에 솔루션을 생성하기에 적합한 권한이 없습니다.) Please check with your account administrator or try with a different account.(계정 관리자에게 문의하거나 다른 계정으로 시도하세요.)"

아래 다이어그램을 살펴보세요.

![][img-flowchart]

**Azure 구독이 있는데 왜 이런 오류가 표시되나요?** *Azure 구독은 미리 구성된 솔루션을 만드는 데 필요합니다. 몇 분 안에 무료 평가판 계정을 만들 수 있습니다.*

Azure 구독이 있는 것이 확실하다면, 구독에 대한 테넌트 매핑의 유효성을 검사하고 드롭다운에 올바른 테넌트가 선택되어 있는지 확인하세요. 원하는 테넌트가 맞는다는 것을 확인했으면, 위의 다이어그램에 따라서 구독과 AAD 테넌트 매핑의 유효성을 검사합니다.

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-github-repo]: https://github.com/Azure/azure-iot-solution
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://github.com/Azure/azure-iot-remote-monitoring/wiki/Manually-setting-up-roles-and-assigning-permissions-in-Azure-Active-Directory-(AAD)#assigning-users-to-the-roles
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-wiki-clouddeployment]: https://github.com/Azure/azure-iot-remote-monitoring/wiki/Cloud-deployment

<!---HONumber=AcomDC_0128_2016-->