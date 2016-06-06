<properties
   pageTitle="보안 검토를 시작하는 방법 | Microsoft Azure"
   description="Azure Privileged Identity Management 응용 프로그램을 사용하여 권한 있는 ID에 대해 보안 검토를 만드는 방법을 알아봅니다."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management에서 보안 검토를 시작하는 방법

사용자가 더 이상 필요 없는 권한 있는 액세스를 가진 경우 "오래된" 역할 할당이 됩니다. 오래된 역할 할당과 관련된 위험을 줄이기 위해 권한 있는 역할 관리자는 사용자에게 부여된 역할을 정기적으로 검토해야 합니다. 이 문서에서 Azure AD PIM(Privileged Identity Management)에서 보안 검토를 시작하는 단계를 설명합니다.

## 보안 검토 시작
> [AZURE.NOTE] Azure 포털의 대시보드에 PIM 응용 프로그램을 추가하지 않았으면 [Azure Privileged Identity Management 시작](active-directory-privileged-identity-management-getting-started.md)의 단계를 참조하세요.

PIM 응용 프로그램 기본 페이지에서 보안 검토를 시작하는 방법은 세 가지가 있습니다.

- **보안 검토** > **검토** > **검토** 단추
- **역할** > **검토** 단추
- 역할 목록에서 검토할 역할 선택 > **검토** 단추

**검토** 단추를 클릭하면 **역할 검토 시작** 및 **검토할 역할 선택** 블레이드가 표시됩니다. **검토할 역할 선택** 항목이 선택됩니다.

### 검토할 역할 선택

1. **Select a role to review(검토할 역할 선택)** 블레이드의 역할 목록에서 역할을 선택합니다. 한번에 하나의 역할만 선택할 수 있습니다. **Select a role to review**(검토할 역할 선택) 블레이드가 **Select reviewers blade**(검토자 블레이드 선택)으로 교체됩니다. 검토자를 선택하는 옵션이 두 가지 있습니다.
  - Me – 다른 관리자의 관여 없이 보안 검토가 작동하는 방식을 미리 보려면 이 옵션을 사용합니다.
  - 역할 멤버에 의한 자체 검토 – 사용자 스스로 자신의 역할 할당을 검토하도록 하려면 이 옵션을 사용합니다.
2. 이들 중 하나를 선택하여 검토 세부 정보 작업을 시작합니다. **기본값 변경** 블레이드가 표시됩니다.

### Me에 의한 검토

검토자로 "Me" 옵션을 선택한 경우 보안 검토를 진행합니다. 검토 완료에 대한 자세한 내용은 [Azure 권한 있는 ID 관리: 보안 검토를 수행하는 방법](active-directory-privileged-identity-management-how-to-perform-security-review.md)을 참조합니다.

### 역할 구성원의 자체 검토

사용자가 자체 역할 할당을 검토하도록 선택한 경우 다음 단계를 수행하여 검토를 설정하고 알림을 보냅니다.

1. **이름** 필드에 검토 이름을 입력하여 검토의 이름을 지정합니다. 상태 추적이 쉽도록 검토를 묘사하는 고유한 이름을 해당 검토에 부여합니다.
2. **시작 날짜** 필드에 검토의 시작 날짜를 입력합니다.
3. **종료 날짜** 필드에 검토의 종류 날짜를 입력합니다. 검토의 종료 날짜를 설정할 때 고려해야 할 사항은 다음과 같습니다.
  - 얼마나 많은 사람들을 검토 중입니까?
  - 사용자가 얼마나 신속하게 Azure 포털에 PIM 응용 프로그램을 추가하고 검토를 완료할 수 있나요?
4. **기본값 변경** 블레이드에서 **확인** 단추를 클릭합니다. 블레이드가 닫힙니다.
5. **Start a review of a role(역할에 대한 검토 시작)** 블레이드에서 **확인** 단추를 클릭합니다. 블레이드가 닫힙니다. Azure 포털 주 메뉴에 알림이 표시됩니다. **새로 고침** 단추를 클릭하여 대시보드를 새로 고치면 **Security reviews(보안 검토)** 섹션에 보안 검토가 표시됩니다.
6. 역할에 포함된 개별 사용자에게 PIM 응용 프로그램을 추가해야 하고 [자신의 관리 액세스를 검토](active-directory-privileged-identity-management-how-to-perform-security-review.md)해야 한다고 알립니다.  

## 보안 검토 관리

보안 검토 섹션에서 검토자가 Azure AD PIM 대시보드에서 검토를 완료하는 진행률을 추적할 수 있습니다. 액세스 권한은 [검토가 완료](active-directory-privileged-identity-management-how-to-complete-review.md)될 때까지 디렉터리에서 변경되지 않습니다.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## PIM 목차
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->