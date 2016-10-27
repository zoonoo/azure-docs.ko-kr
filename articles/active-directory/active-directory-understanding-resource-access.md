<properties
    pageTitle="Azure의 리소스 액세스 이해 | Microsoft Azure" 
    description="이 항목에서는 구독 관리자를 사용하여 전체 Azure 포털에서 리소스 액세스를 제어하는 방법에 대한 개념을 설명합니다."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>



# <a name="understanding-resource-access-in-azure"></a>Azure의 리소스 액세스 이해


> [AZURE.NOTE] 이 항목에서는 구독 관리자를 사용하여 전체 Azure 포털에서 리소스 액세스를 제어하는 방법에 대한 개념을 설명합니다. 또는 Azure 미리 보기 포털이 [역할 기반 액세스 제어](role-based-access-control-configure.md) 을 제공하여 Azure 리소스를 보다 정확하게 관리할 수 있습니다.

2013년 10월에 Azure 클래식 포털 및 서비스 관리 API는 Azure 리소스에 대한 액세스를 관리하기 위한 사용자 환경을 개선하기 위한 토대를 마련하기 위해 Azure Active Directory와 통합되었습니다. Azure Active Directory는 이미 사용자 관리, 온-프레미스 디렉터리 동기화, 다단계 인증 및 응용 프로그램 액세스 제어와 같은 훌륭한 기능을 제공합니다. 물론 이러한 기능들로 Azure 리소스를 전반적으로 관리할 수 있습니다.

Azure의 액세스 제어는 결제 관점에서 시작합니다. [Azure 계정 센터](https://account.windowsazure.com/subscriptions)를 방문하여 액세스하는 Azure 계정의 소유자는 계정 관리자(AA)입니다. 구독은 결제를 위한 컨테이너이지만 보안 경계로서의 역할도 합니다. 각 구독에는 [Azure 클래식 포털](https://manage.windowsazure.com/)을 사용하여 해당 구독에서 Azure 리소스를 추가, 제거 및 수정할 수 있는 서비스 관리자(SA)가 있습니다. 새 구독의 기본 SA는 AA이지만 AA가 Azure 계정 센터에서 SA를 변경할 수 있습니다.

<br><br>![Azure 계정][1]

또한 구독은 디렉터리와 연결되어 있습니다. 디렉터리는 사용자 집합을 정의합니다. 사용자 집합은 디렉터리를 만든 회사 또는 학교 사용자가 될 수 있습니다. 또는 외부 사용자(즉, Microsoft 계정)이 될 수 있습니다. 구독은 서비스 관리자(SA) 또는 CA(공동 관리자)로 할당된 해당 디렉터리 사용자의 하위 집합에서 액세스할 수 있습니다. 유일한 예외는 레거시의 이유로 Microsoft 계정(이전의 Windows Live ID)이 디렉터리에 없는 SA 또는 CA로 할당될 수 있다는 점입니다.

<br><br>![Azure의 액세스 제어][2]


Azure 클래식 포털 내의 기능은 Microsoft 계정을 통해 로그인하는 SA를 사용하여 구독이 연결되어 있는 디렉터리를 변경합니다. 이 때, **설정**의 **구독** 페이지에서 **디렉터리 편집** 명령을 사용합니다. 이 작업은 해당 구독의 액세스 제어에 영향을 줍니다.



> [AZURE.NOTE] 회사 또는 학교 계정은 소속된 디렉터리에만 로그인할 수 있기 때문에 Azure 클래식 포털의 **디렉터리 편집** 명령은 회사 또는 학교 계정을 사용하여 로그인하는 사용자에게 사용할 수 없습니다.

<br><br>![간단한 사용자 로그인 흐름][3]

단순한 경우 조직(예: Contoso)은  요금 청구를 적용하고 동일한 집합의 구독에 대해 액세스 제어합니다. 즉, 디렉터리는 단일 Azure 계정에 의해 소유된 구독에 연결됩니다. Azure 클래식 포털에 로그인하면 사용자는 리소스의 두 가지 컬렉션(이전 그림에서 주황색으로 표시)을 볼 수 있습니다.


- 사용자 계정이 있는 디렉터리 (원본 또는 외래 사용자로 추가). 로그인에 사용되는 디렉터리는 이 계산과 관련이 없으므로 디렉터리는 로그인 위치에 관계없이 항상 표시됩니다.

- (SA 또는 CA인 경우) 사용자가 액세스 할 수 있는 로그인 AND로 사용되는 디렉터리와 연결된 구독의 일부인 리소스.


<br><br>![여러 구독 및 디렉터리가 있는 사용자][4]


여러 디렉터리에 구독이 있는 사용자는 구독 필터를 사용하여 Azure 클래식 포털의 현재 컨텍스트를 전환할 수 있습니다. 내부적으로 이렇게 하면 다른 디렉터리에 별도로 로그인하게 되지만, single sign-on (SSO)을 사용하여 원활하게 수행할 수 있습니다.

구독 간에 리소스를 이동하는 등의 작업은 구독의 단일 디렉터리 보기의 결과로 더 어려울 수 있습니다. 리소스 전송을 수행하려면 **설정**에서 구독 페이지의 **디렉터리 편집** 명령을 처음 사용하여 동일한 디렉터리에 구독을 연결해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure 구독에 대한 관리자를 변경하는 방법에 대해 자세히 알아보려면 [Azure 관리자 역할을 추가 또는 변경하는 방법](../billing-add-change-azure-subscription-administrator.md)

- Azure 구독에 Azure Active Directory가 연결되는 방법에 대한 자세한 내용은 [Azure 구독을 Azure Active Directory에 연결하는 방법](active-directory-how-subscriptions-associated-directory.md)

- Azure AD에서 역할을 할당하는 방법에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](active-directory-assign-admin-roles.md)



<!--Image references-->
[1]: ./media/active-directory-understanding-resource-access/IC707931.png
[2]: ./media/active-directory-understanding-resource-access/IC707932.png
[3]: ./media/active-directory-understanding-resource-access/IC707933.png
[4]: ./media/active-directory-understanding-resource-access/IC707934.png



<!--HONumber=Oct16_HO2-->


