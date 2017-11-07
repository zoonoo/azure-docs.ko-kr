---
title: "사용자 지정 역할 기반 액세스 제어 역할 만들기 및 Azure에서 내부 및 외부 사용자에게 할당 | Microsoft Docs"
description: "내부 및 외부 사용자에게 PowerShell 및 CLI를 사용하여 만든 사용자 지정 RBAC 역할 할당"
services: active-directory
documentationcenter: 
author: andreicradu
manager: catadinu
editor: kgremban
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/10/2017
ms.author: a-crradu
ms.openlocfilehash: bb9b89d087cfb62efe63cf0ff600d7faa58a7b8b
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2017
---
## <a name="intro-on-role-based-access-control"></a>역할 기반 액세스 제어 소개

역할 기반 액세스 제어는 구독의 소유자가 해당 환경에서 특정 리소스 범위를 관리할 수 있는 다른 사용자에게 세분화된 역할을 할당하는 Azure Portal에만 있는 기능입니다.

RBAC를 통해 환경에서 특정 리소스에 액세스해야 하지만 전체 인프라 또는 청구 관련 범위에 액세스하지 않아도 되는 외부 공동 작업자, 공급 업체 또는 프리랜서와 함께 작업하는 대규모 조직 및 SMB의 경우 더 나은 보안 관리가 가능합니다. RBAC를 통해 관리자 계정에서 관리하는 Azure 구독을 소유하는 유연성(구독 수준에서 서비스 관리자 역할)을 제공하고 동일한 구독이지만 관리 권한 없이 작업하도록 여러 사용자를 초대할 수 있습니다. 관리 및 청구 관점에서 RBAC 기능은 다양한 시나리오에서 Azure를 사용하는 시간 및 관리 효율성 옵션으로 증명됩니다.

## <a name="prerequisites"></a>필수 조건
Azure 환경에서 RBAC를 사용하려면 다음 항목이 필요합니다.

* 소유자인 사용자에게 할당된 독립 실행형 Azure 구독(구독 역할)
* Azure 구독의 소유자 역할
* [Azure Portal](https://portal.azure.com)에 대한 액세스 권한
* 사용자 구독에 등록된 다음과 같은 리소스 공급자: **Microsoft.Authorization** 리소스 공급자를 등록하는 방법에 대해 자세히 알아보려면 [Resource Manager 공급자, 지역, API 버전 및 스키마](/azure-resource-manager/resource-manager-supported-services.md)를 참조하세요.
<!---Loc Comment: Link [Resource Manager providers, regions, API versions and schemas] is broken with an error message "404 - Content Not Found---->

> [!NOTE]
> O365 포털에서 프로비전된 Office 365 구독 또는 Azure Active Directory 라이선스(예: Azure Active Directory에 액세스)는 RBAC를 사용할 자격이 없습니다.

## <a name="how-can-rbac-be-used"></a>RBAC를 사용할 수 있는 방법
RBAC는 Azure에서 세 가지 각기 다른 범위에 적용할 수 있습니다. 높은 범위에서 낮은 범위 순으로 다음과 같습니다.

* 구독(높음)
* 리소스 그룹
* 리소스 범위(개별 Azure 리소스에 대상으로 지정된 사용 권한을 제공하는 최저 액세스 수준)

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>구독 범위에서 RBAC 역할 할당
RBAC가 사용되는 일반적인 두 가지 예는 다음과 같습니다(제한되지는 않음).

* 조직의 외부 사용자를 특정 리소스 또는 전체 구독을 관리하도록 초대한 경우(관리 사용자의 Azure Active Directory 테넌트 중 일부가 아님)
* 조직 내에서 사용자와 작업(사용자의 Azure Active Directory 테넌트 중 일부)하지만 환경에서 전체 구독 또는 특정 리소스 그룹이나 리소스 범위에 대한 세분화된 액세스 권한이 필요한 다른 팀 또는 그룹의 일부인 경우

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>구독 수준에서 Azure Active Directory 외부 사용자에 대한 액세스 권한 부여
RBAC 역할은 구독의 **소유자**만이 부여할 수 있으므로 관리 사용자는 이 역할을 미리 할당받거나 Azure 구독을 만든 사용자 이름으로 로그온해야 합니다.

Azure Portal에서 관리자로 로그인한 후에 "구독"을 선택하고 원하는 구독을 선택합니다.
![Azure Portal의 구독 블레이드](./media/role-based-access-control-create-custom-roles-for-internal-external-users/0.png) 기본적으로 관리 사용자가 Azure 구독을 구매한 경우 사용자는 **계정 관리자**로 표시되고 이는 구독 역할입니다. Azure 구독 역할에 대한 자세한 내용은 [구독 또는 서비스를 관리하는 Azure 관리자 역할 추가 또는 변경](/billing/billing-add-change-azure-subscription-administrator.md)을 참조하세요.

이 예제에서 사용자 "alflanigan@outlook.com"은 AAD 테넌트 "기본 테넌트 Azure"에서 "체험 평가판" 구독의 **소유자**입니다. 이 사용자가 초기 Microsoft 계정 "Outlook"을 사용하여 Azure 구독을 만들었으므로(Microsoft 계정 = Outlook, Live 등) 이 테넌트에 추가된 다른 모든 사용자의 기본 도메인 이름은 **"@alflaniganuoutlook.onmicrosoft.com"**입니다. 기본적으로 테넌트를 만든 사용자의 사용자 이름 및 도메인 이름을 결합하고 **".onmicrosoft.com"** 확장을 추가하여 새 도메인의 구문 형식을 지정합니다.
또한 사용자는 새 테넌트에 추가하고 확인한 후에 테넌트에 있는 사용자 지정 도메인 이름을 사용하여 로그인할 수 있습니다. Azure Active Directory 테넌트에서 사용자 지정 도메인 이름을 확인하는 방법에 대한 자세한 내용은 [디렉터리에 사용자 지정 도메인 이름 추가](/active-directory/active-directory-add-domain)를 참조하세요.

이 예제에서 "기본 테넌트 Azure" 디렉터리에는 도메인 이름 "@alflanigan.onmicrosoft.com"을 가진 사용자만이 포함됩니다.

관리 사용자는 구독을 선택한 후에 **액세스 제어(IAM)** 및 **새 역할 추가**를 차례로 클릭해야 합니다.





![Azure Portal의 액세스 제어 IAM 기능](./media/role-based-access-control-create-custom-roles-for-internal-external-users/1.png)





![Azure Portal의 액세스 제어 IAM 기능에서 새 사용자 추가](./media/role-based-access-control-create-custom-roles-for-internal-external-users/2.png)

다음 단계에서는 할당하고 역할 및 RBAC 역할에 할당할 사용자를 선택합니다. **역할** 드롭다운 메뉴에서는 Azure에서 사용할 수 있는 기본 제공 RBAC 역할만을 관리 사용자에게 표시합니다. 각 역할 및 해당 할당 가능한 범위에 대한 자세한 설명은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](/active-directory/role-based-access-built-in-roles.md)을 참조하세요.
<!---Loc Comment: Link [Built-in roles for Azure Role-Based Access Control] is broken with an error message "404 - Content Not Found---->

관리 사용자는 외부 사용자의 이메일 주소를 추가해야 합니다. 예상된 동작을 기존 테넌트에서 외부 사용자에세 표시하지 않습니다. 외부 사용자를 초대한 후에 현재 구독 범위에서 RBAC 역할에 할당한 모든 사용자와 함께 **구독 > 액세스 제어(IAM)**를 표시합니다.





![새 RBAC 역할에 사용 권한 추가](./media/role-based-access-control-create-custom-roles-for-internal-external-users/3.png)





![구독 수준에서 RBAC 역할 목록](./media/role-based-access-control-create-custom-roles-for-internal-external-users/4.png)

사용자 "chessercarlton@gmail.com"은 "체험 평가판" 구독에 대한 **소유자**로 초대되었습니다. 외부 사용자는 초대를 보낸 후에 활성화 링크를 포함한 이메일 확인을 받게 됩니다.
![RBAC 역할에 대한 이메일 초대](./media/role-based-access-control-create-custom-roles-for-internal-external-users/5.png)

조직의 외부에 있는 새 사용자는 "기본 테넌트 Azure" 디렉터리에서 기존 특성을 갖지 않습니다. 해당 특성은 외부 사용자가 역할을 할당한 구독과 연결되어 있는 디렉터리에 기록하는 데 동의한 후에 만들어집니다.





![RBAC 역할에 대한 이메일 초대 메시지](./media/role-based-access-control-create-custom-roles-for-internal-external-users/6.png)

외부 사용자는 지금부터 Azure Active Directory 테넌트에서 외부 사용자로 표시되고 Azure Portal 및 클래식 포털에서 볼 수 있습니다.





![사용자 블레이드 Azure Active Directory Azure Portal](./media/role-based-access-control-create-custom-roles-for-internal-external-users/7.png)





![사용자 블레이드 Azure Active Directory Azure 클래식 포털](./media/role-based-access-control-create-custom-roles-for-internal-external-users/8.png)

두 포털의 **사용자** 보기에서 외부 사용자는 다음으로 인식될 수 있습니다.

* Azure Portal에서 다른 아이콘 형식
* 클래식 포털에서 다른 소싱 지점

그러나 **구독** 범위에서 외부 사용자에 대한 **소유자** 또는 **참가자** 액세스 권한을 부여하면 **전역 관리자**가 허용하지 않는 한 관리 사용자의 디렉터리에 대한 액세스를 허용하지 않습니다. 사용자 속성에서 두 공통 매개 변수가 있는 **사용자 형식**, **멤버** 및 **게스트**를 식별할 수 있습니다. 구성원은 디렉터리에 등록되어 있는 사용자인 반면 게스트는 외부 소스의 디렉터리로 초대된 사용자입니다. 자세한 내용은 [Azure Active Directory 관리자가 B2B 공동 작업 사용자를 추가하는 방법](/active-directory/active-directory-b2b-admin-add-users)을 참조하세요.
<!---Loc Comment: Link [How do Azure Active Directory admins add B2B collaboration users] is broken with an error message "404 - Content Not Found--->

> [!NOTE]
> 포털에서 자격 증명을 입력한 후에 외부 사용자가 올바른 디렉터리에 로그인하는지 확인합니다. 동일한 사용자는 여러 디렉터리에 대한 액세스 권한이 있을 수 있고 Azure Portal의 오른쪽 위에 있는 사용자 이름을 클릭 그 중 하나를 선택한 다음 드롭다운 목록에서 적절한 디렉터리를 선택할 수 있습니다.

디렉터리에 있는 게스트인 경우 외부 사용자는 Azure 구독에 대한 모든 리소스를 관리할 수 있지만 디렉터리에 액세스할 수 없습니다.





![Azure Active Directory Azure Portal에 제한된 액세스](./media/role-based-access-control-create-custom-roles-for-internal-external-users/9.png)

Azure Active Directory와 Azure 구독에는 다른 Azure 리소스와 Azure 구독이 가진 것과 같은 자식-부모 관계가 없습니다(예: 가상 컴퓨터, 가상 네트워크, 웹앱, 저장소 등). 후자가 모두 Azure 구독에서 생성되고 관리되며 요금이 청구되는 반면 Azure 구독은 Azure 디렉터리에 대한 액세스를 관리하는 데 사용됩니다. 자세한 내용은 [Azure 구독과 Azure AD의 연관 관계](/active-directory/active-directory-how-subscriptions-associated-directory)를 참조하세요.

모든 기본 제공 RBAC 역할에서 **소유자** 및 **참가자**는 환경에서 모든 리소스에 대한 완전한 관리 액세스를 제공합니다. 두 역할의 차이는 새 RBAC 역할을 만들고 삭제할 수 없다는 점입니다. **가상 컴퓨터 참여자**와 같은 다른 기본 제공 역할은 생성되는 **리소스 그룹**에 관계없이 이름으로 표시된 리소스에만 완전한 관리 액세스를 제공합니다.

**가상 컴퓨터 참여자**의 기본 제공 RBAC 역할을 구독 수준에서 할당한다는 것은 사용자에게 역할을 할당했다는 의미입니다.

* 해당 배포 날짜 및 일부인 리소스 그룹에 관계 없이 모든 가상 컴퓨터를 볼 수 있습니다.
* 구독에는 가상 컴퓨터에 대한 완전한 관리 액세스 권한이 있습니다.
* 구독에서 다른 리소스 형식을 볼 수 없습니다.
* 요금 청구 관점에서 변경 사항을 수행할 수 없습니다.

> [!NOTE]
> Azure Portal 기능만 있는 RBAC는 클래식 포털에 대한 액세스 권한을 부여하지 않습니다.

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>외부 사용자에게 기본 제공 RBAC 역할 할당
이 테스트의 다른 시나리오에서 외부 사용자 "alflanigan@gmail.com"은 **가상 컴퓨터 참여자**로 추가됩니다.




![가상 컴퓨터 참여자 기본 제공 역할](./media/role-based-access-control-create-custom-roles-for-internal-external-users/11.png)

이 기본 제공 역할을 가진 외부 사용자에 대한 정상 동작은 가상 컴퓨터와 배포 시 필요한 리소스에 인접한 Resource Manager만을 보고 관리하는 것입니다. 기본적으로 이러한 제한된 역할은 Azure Portal에서 만든 해당 리소스에 대해서만 액세스를 제공하지만 일부는 클래식 포털에서도 배포할 수 있습니다(예: 가상 컴퓨터).





![Azure Portal에서 가상 컴퓨터 참가자 역할 개요](./media/role-based-access-control-create-custom-roles-for-internal-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>구독 수준에서 동일한 디렉터리의 사용자에 대한 액세스 권한 부여
RBAC 역할을 부여하는 관리자 관점뿐만 아니라 역할에 대한 액세스 권한이 부여된 사용자 관점에서도 프로세스 흐름은 외부 사용자를 추가하는 것과 동일합니다. 여기서 차이점은 구독 내의 모든 리소스 범위를 로그인한 후에 대시보드에서 사용할 수 있는 경우 초대받은 사용자가 구독 내에서 어떤 이메일 초대도 수신하지 않는다는 것입니다.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>리소스 그룹 범위에서 RBAC 역할 할당
**리소스 그룹** 범위에서 RBAC 역할을 할당하는 작업은 외부 또는 내부라는 두 종류의 사용자(동일한 디렉터리의 일부)에게 구독 수준에서 역할을 할당하는 동일한 프로세스입니다. 환경에서 리소스 그룹을 확인하는 RBAC 역할이 할당되어 있는 사용자는 Azure Portal의 **리소스 그룹** 아이콘에서 액세스 권한이 할당됩니다.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>리소스 범위에서 RBAC 역할 할당
Azure의 리소스 범위에서 RBAC 역할을 할당하는 작업은 구독 수준 또는 리소스 그룹 수준에서 역할을 할당하는 동일한 프로세스이며 두 시나리오에 대해 동일한 워크플로를 따릅니다. 액세스가 할당된 항목만을 볼 수 있는 RBAC 역할이 할당되어 있는 사용자는 **모든 리소스** 탭 또는 대시보드에서 직접 확인할 수 있습니다.

RBAC에서 리소스 그룹 범위 또는 리소스 범위에 대한 중요한 측면은 사용자가 올바른 디렉터리에 로그인하는 것입니다.





![Azure Portal에서 디렉터리 로그인](./media/role-based-access-control-create-custom-roles-for-internal-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Azure Active Directory 그룹에 대한 RBAC 역할 할당
Azure의 세 가지 다른 범위에서 RBAC를 사용하는 모든 시나리오는 개인 구독을 관리할 필요 없이 할당된 사용자로 다양한 리소스를 관리, 배포 및 관리하는 권한을 제공합니다. RBAC 역할이 구독, 리소스 그룹 또는 리소스 범위에 할당되었는지와 관계없이 뒤에 나오는 할당된 사용자가 만든 리소스는 모두 사용자가 액세스할 수 있는 하나의 Azure 구독에서 청구됩니다. 이러한 방식으로 전체 Azure 구독에 대해 청구 관리자 권한을 가진 사용자는 리소스를 관리하는 사용자가 누구인지와 관계없이 소비에 대한 전체 개요를 가집니다.

대규모 조직의 경우 RBAC 역할은 Azure Active Directory 그룹에게 동일한 방식으로 적용되어 관리 사용자가 각 사용자에 각각이 아닌 팀 또는 전체 부서에 대한 세부적인 액세스 권한을 부여하는 관점을 고려할 수 있습니다. 따라서 시간 및 관리 효율성이 뛰어난 옵션으로 생각합니다. 이 예제를 설명하기 위해 **참가자** 역할은 구독 수준에서 테넌트의 그룹 중 하나에 추가되었습니다.





![AAD 그룹에 대한 RBAC 역할 추가](./media/role-based-access-control-create-custom-roles-for-internal-external-users/14.png)

이러한 그룹은 Azure Active Directory 내에서만 프로비전되고 관리되는 보안 그룹입니다.

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-powershell"></a>PowerShell을 사용하여 지원 요청을 여는 사용자 지정 RBAC 역할 만들기
Azure에서 사용할 수 있는 기본 제공 RBAC 역할은 환경에서 사용할 수 있는 리소스에 따라 특정 사용 권한 수준을 보장합니다. 그러나 이러한 역할이 관리 사용자의 요구에 맞지 않으면 사용자 지정 RBAC 역할을 만들어 액세스를 제한하는 옵션이 있습니다.

사용자 지정 RBAC 역할을 만들려면 기본 제공 역할을 사용하고 편집한 다음 환경에서 다시 가져와야 합니다. 역할의 다운로드 및 업로드는 PowerShell 또는 CLI를 사용하여 관리됩니다.

구독 수준에서 세분화된 액세스 권한을 부여하고 초대받은 사용자에게 지원 요청을 여는 유연성을 허용할 수 있는 사용자 지정 역할을 만드는 필수 구성 요소를 이해해야 합니다.

이 예제에서 기본 제공 역할 **판독기**는 사용자에게 모든 리소스 범위를 볼 수 있는 액세스 권한을 허용하지만 편집하거나 새로 만들지 않도록 사용자가 지원 요청을 열 수 있는 옵션을 사용자 지정합니다.

**판독기** 역할을 내보내는 첫 번째 작업은 PowerShell에서 완료해야 하고 관리자로 승격된 권한으로 실행해야 합니다.

```
Login-AzureRMAccount

Get-AzureRMRoleDefinition -Name "Reader"

Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json

```





![판독기 RBAC 역할의 PowerShell 스크린샷](./media/role-based-access-control-create-custom-roles-for-internal-external-users/15.png)

그런 다음 역할의 JSON 템플릿을 추출해야 합니다.





![사용자 지정 판독기 RBAC 역할의 JSON 템플릿](./media/role-based-access-control-create-custom-roles-for-internal-external-users/16.png)

일반적인 RBAC 역할은 **작업**, **NotActions** 및 **AssignableScopes**라는 세 가지 주요 섹션으로 구성됩니다,

**작업** 섹션에서는 이 역할에 대해 허용된 모든 작업을 나열합니다. 리소스 공급자로부터 각 작업이 할당되었음을 이해해야 합니다. 이 경우에 지원 티켓을 만들기 위해 **Microsoft.Support** 리소스 공급자를 나열해야 합니다.

사용 가능하고 구독에 등록된 모든 리소스 공급자를 보기 위해 PowerShell을 사용할 수 있습니다.
```
Get-AzureRMResourceProvider

```
또한 모든 사용할 수 있는 PowerShell cmdlet을 확인하여 리소스 공급자를 관리할 수 있습니다.
    ![리소스 공급자 관리의 PowerShell 스크린샷](./media/role-based-access-control-create-custom-roles-for-internal-external-users/17.png)

특정 RBAC 역할에 대해 모든 작업을 제한하려면 리소스 공급자는 **NotActions** 섹션 아래에 나열됩니다.
마지막으로 RBAC 역할이 포함한 필수 권한은 사용된 명시적 구독 ID입니다. 구독 ID는 **AssignableScopes** 아래에 나열됩니다. 그렇지 않으면 구독에 역할을 가져올 수 없습니다.

RBAC 역할을 만들고 사용자 지정한 후에 환경으로 다시 가져와야 합니다.

```
New-AzureRMRoleDefinition -InputFile "C:\rbacrole2.json"

```

이 예제에서 이 RBAC 역할에 대한 사용자 지정 이름은 "판독기 지원 티켓 액세스 수준"이며 사용자가 구독에서 모든 항목을 확인하고 지원 요청을 열 수 있도록 합니다.

> [!NOTE]
> 지원 요청을 여는 작업을 허용하는 두 개의 기본 제공 RBAC 역할은 **소유자** 및 **참가자**입니다. 지원 요청을 열 수 있는 사용자의 경우 모든 지원 요청이 Azure 구독에 따라 만들어지기 때문에 구독 범위에서만 RBAC 역할을 할당해야 합니다.

이 새 사용자 지정 역할이 디렉터리에서 사용자에게 할당되었습니다.





![Azure Portal에서 가져온 사용자 지정 RBAC 역할의 스크린샷](./media/role-based-access-control-create-custom-roles-for-internal-external-users/18.png)





![동일한 디렉터리에서 사용자에게 가져온 사용자 지정 RBAC 역할 할당의 스크린샷](./media/role-based-access-control-create-custom-roles-for-internal-external-users/19.png)





![가져온 사용자 지정 RBAC 역할에 대한 사용 권한 스크린샷](./media/role-based-access-control-create-custom-roles-for-internal-external-users/20.png)

예제는 다음과 같이 이 사용자 지정 RBAC 역할의 한계를 강조하여 추가적으로 설명합니다.
* 새 지원 요청을 만들 수 있습니다.
* 새 리소스 범위를 만들 수 없습니다(예: 가상 컴퓨터).
* 새 리소스 그룹을 만들 수 없습니다.





![지원 요청을 만드는 사용자 지정 RBAC 역할의 스크린샷](./media/role-based-access-control-create-custom-roles-for-internal-external-users/21.png)





![VM을 만들 수 없다는 사용자 지정 RBAC 역할의 스크린샷](./media/role-based-access-control-create-custom-roles-for-internal-external-users/22.png)





![새 RG를 만들 수 없다는 사용자 지정 RBAC 역할의 스크린샷](./media/role-based-access-control-create-custom-roles-for-internal-external-users/23.png)

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-azure-cli"></a>Azure CLI를 사용하여 지원 요청을 여는 사용자 지정 RBAC 역할 만들기
Mac에서 및 PowerShell에 액세스하지 않고 실행하려면 Azure CLI를 사용합니다.

CLI를 사용하여 JSON 템플릿에서 역할을 다운로드할 수 없지만 CLI에서 볼 수 있다는 예외를 제외하면 사용자 지정 역할을 만드는 단계는 동일합니다.

이 예제에서 **백업 판독기**의 기본 제공 역할을 선택했습니다.

```

azure role show "backup reader" --json

```





![백업 읽기 역할의 CLI 스크린샷 표시](./media/role-based-access-control-create-custom-roles-for-internal-external-users/24.png)

JSON 템플릿의 속성을 복사한 후에 Visual Studio에서 역할을 편집하면 **Microsoft.Support** 리소스 공급자가 **작업** 섹션에 추가되므로 이 사용자는 백업 자격 증명 모음에 대한 판독기를 계속 진행하면서 지원 요청을 열 수 있습니다. 다시 이 역할을 **AssignableScopes** 섹션에서 사용하는 구독 ID를 추가해야 합니다.

```

azure role create --inputfile <path>

```





![사용자 지정 RBAC 역할 가져오기의 CLI 스크린샷](./media/role-based-access-control-create-custom-roles-for-internal-external-users/25.png)

이제 새 역할을 Azure Portal에서 사용할 수 있으며 할당 프로세스는 이전의 예와 동일합니다.





![CLI 1.0을 사용하여 만든 사용자 지정 RBAC 역할의 Azure Portal 스크린샷](./media/role-based-access-control-create-custom-roles-for-internal-external-users/26.png)

최신 빌드 2017인 Azure Cloud Shell을 일반적으로 사용할 수 있습니다. Azure Cloud Shell은 IDE 및 Azure Portal을 보완합니다. 이 서비스에서 Azure 내에서 인증되고 호스트되는 브라우저 기반 셸을 가져오고 컴퓨터에 설치된 CLI 대신 사용할 수 있습니다.





![Azure Cloud Shell](./media/role-based-access-control-create-custom-roles-for-internal-external-users/27.png)
