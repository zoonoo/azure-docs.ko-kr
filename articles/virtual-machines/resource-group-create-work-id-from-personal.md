<properties
   pageTitle="AAD에서 회사 또는 학교 ID 만들기 | Microsoft Azure"
   description="Azure Active Directory에 리소스 관리자 및 클래식 배포 모델에서 사용할 회사 또는 학교 ID를 만드는 방법을 알아봅니다."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure"
   ms.date="09/01/2015"
   ms.author="rasquill"/>

# Azure Active Directory에서 회사 또는 학교 ID 만들기

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

개인 Azure 계정을 생성하거나 개인 MSDN 구독이 있고 MSDN Azure 크레딧을 활용하기 위해 Azure 계정을 만든 경우는 *Microsoft 계정* ID를 사용하여 만든 것입니다. Azure의 많은 훌륭한 기능(예: [리소스 그룹 템플릿](../resource-group-overview.md))을 사용하기 위해서는 회사 또는 학교 계정(Azure Active Directory에서 관리하는 ID)이 필요합니다.


다행히 개인 Azure 계정이 제공하는 최고의 이점 중 하나는, 계정을 필요로 하는 Azure 기능과 함께 사용할 수 있는 새로운 회사 또는 학교 계정을 만드는 데 사용할 수 있는 기본 Azure Active Directory 도메인으로 제공된다는 점입니다.

> [AZURE.NOTE]관리자가 사용자 이름과 암호를 제공한 경우, 회사 또는 학교 ID(*조직 ID*라고도 부름)가 이미 있을 가능성이 큽니다. 이런 경우, 계정이 필요한 Azure 리소스에 Azure 계정을 사용하여 즉시 액세스를 시작할 수 있습니다. 해당 리소스를 사용할 수 없는 경우는 이 문서의 도움말을 다시 참조해야 할 수 있습니다. 자세한 내용은 [로그인에 사용할 수 있는 계정](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SignInAccounts) 및 [Azure 구독과 Azure AD의 연관 관계](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SubRelationToDir)를 참조하세요.

절차는 간단합니다. Azure 포털에서 로그인한 ID를 찾은 후 기본 Azure Active Directory 도메인을 검색하고 Azure 공동 관리자로 새 사용자를 추가합니다.

## Azure 포털에서 기본 디렉터리 찾기

먼저 개인 Microsoft 계정 ID를 사용하여 [Azure 포털](https://manage.windowsazure.com)에 로그인합니다. 로그인하고 나서 왼쪽의 파란색 패널을 아래로 스크롤하여 **ACTIVE DIRECTORY**를 클릭합니다.

![Azure Active Directory](./media/resource-group-create-work-id-from-personal/azureactivedirectorywidget.png)

Azure에서 사용자 ID에 대한 정보를 검색하여 시작하겠습니다. 주 창에 다음과 같이 기본 디렉터리 하나가 있다는 메시지가 표시됩니다.

![](./media/resource-group-create-work-id-from-personal/defaultaadlisting.png)

정보를 좀더 찾아보겠습니다. 기본 디렉터리 행을 클릭하여 기본 디렉터리 속성을 표시합니다.

![](./media/resource-group-create-work-id-from-personal/defaultdirectorypage.png)

기본 도메인 이름을 보려면 **도메인**을 클릭합니다.

![](./media/resource-group-create-work-id-from-personal/domainclicktoseeyourdefaultdomain.png)

여기서, Azure 계정을 이미 만든 경우 Azure Active Directory가 onmicrosoft.com의 하위 도메인으로 사용되는 개인 ID의 해시 값(텍스트 문자열에서 생성된 숫자)이 도메인을 만들었음을 알 수 있습니다. 이 도메인이 지금 새 사용자를 추가할 도메인입니다.

## 기본 도메인에서 새 사용자 만들기

**사용자**를 클릭하여 단일 개인 계정을 찾습니다. **원본 위치** 열에 **Microsoft 계정**으로 표시되어야 합니다. 기본 .onmicrosoft.com Azure Active Directory 도메인에 사용자를 만들려고 합니다.

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryuserslisting.png)

다음 몇 단계에서는 [이러한 지침](https://technet.microsoft.com/library/hh967632.aspx#BKMK_1)을 따르지만 특정 예제를 사용합니다.

페이지 맨 아래에서 **+사용자 추가**를 클릭합니다. 표시되는 페이지에서 새 사용자 이름을 입력하고 **사용자 유형**을 **조직 내 새 사용자**로 지정합니다. 이 예제에서 새 사용자 이름은 `ahmet`입니다. 이전에서 검색한 기본 도메인을 ahmet의 전자 메일 주소에 대한 도메인으로 선택합니다. 끝나면 다음 화살표를 클릭합니다.

![](./media/resource-group-create-work-id-from-personal/addingauserwithdirectorydropdown.png)

Ahmet에 대한 세부 정보를 추가하되 적절한 **역할** 값을 선택해야 합니다. 작업을 제대로 수행하려면 **전역 관리자**를 사용하는 것이 간편하지만 권한이 더 적은 역할을 사용할 수 있는 경우는 그 역할을 사용하는 것이 좋습니다. 이 예제에서는**사용자** 역할을 사용합니다. (이러한 역할에 대한 자세한 내용은 [여기](https://msdn.microsoft.com/library/azure/dn468213.aspx#BKMK_1)를 참조하세요.) 작업에서 각 로그에 대해 Multi-Factor Authentication을 사용하려는 경우가 아니면 Multi-Factor Authentication을 사용하지 않도록 합니다. 끝나면 다음 화살표를 클릭합니다.

![](./media/resource-group-create-work-id-from-personal/userprofileuseradmin.png)

**만들기** 단추를 클릭하여 Ahmet에 대한 임시 암호를 생성하고 표시합니다.

![](./media/resource-group-create-work-id-from-personal/gettemporarypasswordforuser.png)

사용자 이름 메일 주소를 복사하거나 **암호를 메일로 보내기**를 사용합니다. 로그온하기 위해 이 정보가 곧 필요합니다.

![](./media/resource-group-create-work-id-from-personal/receivedtemporarypassworddialog.png)

이제 Azure Active Directory에서 소싱된 새로운 사용자인 **개발자 Ahmet**가 표시됩니다. Azure Active Directory를 사용하여 새로운 회사 또는 학교 ID를 만들었습니다. 그러나 이 ID에는 아직 Azure 리소스를 사용할 수 있는 권한이 없습니다.

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryusersaftercreate.png)

**암호를 메일로 보내기**를 사용하는 경우 다음과 같은 종류의 메일이 전송됩니다.

![](./media/resource-group-create-work-id-from-personal/emailreceivedfromnewusercreation.png)

## 구독에 대한 Azure 공동 관리자 권한 추가

이제 새 사용자가 관리 포털에 로그인할 수 있도록 새 사용자를 구독의 공동 관리자로 추가해야 합니다. 이 작업을 수행하려면 왼쪽 패널에서 **설정**을 클릭합니다.

![](./media/resource-group-create-work-id-from-personal/thesettingswidget.png)

기본 설정 영역에서 상단의 **관리자**를 클릭하며, 여기에는 개인 Microsoft 계정 ID만 표시되어야 합니다. 페이지의 아래쪽에서 **+추가**를 클릭하여 공동 관리자를 지정합니다. 여기에 기본 도메인을 포함하여, 이미 만들어둔 새 사용자의 전자 메일 주소를 입력합니다. 다음 스크린샷에 표시 된 것과 같이 기본 디렉터리에 대한 사용자 옆에 녹색 확인 표시가 나타납니다. 이 사용자를 관리자로 지정하려는 모든 구독을 선택해야 합니다.

![](./media/resource-group-create-work-id-from-personal/addingnewuserascoadmin.png)

작업이 완료되면 새로운 공동 관리자 ID를 포함하여 두 명의 사용자가 표시됩니다. 포털에서 로그아웃합니다.

![](./media/resource-group-create-work-id-from-personal/newuseraddedascoadministrator.png)

## 로그인 및 새 사용자의 암호 변경

만든 새 사용자로 로그인합니다.

![](./media/resource-group-create-work-id-from-personal/signinginwithnewuser.png)

즉시 새 암호를 만들도록 요청하는 메시지가 표시됩니다.

![](./media/resource-group-create-work-id-from-personal/mustupdateyourpassword.png)

다음과 같이 성공에 대한 보상이 주어집니다.

![](./media/resource-group-create-work-id-from-personal/successtourdialog.png)


## 다음 단계

이제 새 Azure Active Directory ID를 통해 [Azure 리소스 그룹 템플릿](xplat-cli-azure-resource-manager.md)을 사용할 수 있습니다.

     azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@aztrainpassxxxxxoutlook.onmicrosoft.com
    Password: *********
    /info:    Added subscription Azure Pass
    info:    Setting subscription Azure Pass as default
    +
    info:    login command OK
    ralph@local:~$ azure config mode arm
    info:    New mode is arm
    ralph@local:~$ azure group list
    info:    Executing command group list
    + Listing resource groups
    info:    No matched resource groups were found
    info:    group list command OK
    ralph@local:~$ azure group create newgroup westus
    info:    Executing command group create
    + Getting resource group newgroup
    + Creating resource group newgroup
    info:    Created resource group newgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/newgroup
    data:    Name:                newgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK

<!---HONumber=Oct15_HO4-->