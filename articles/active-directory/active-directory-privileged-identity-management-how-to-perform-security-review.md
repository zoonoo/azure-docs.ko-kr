<properties
   pageTitle="Azure 권한 있는 ID 관리: 보안 검토를 수행하는 방법"
   description="Azure 권한 있는 ID 관리 확장을 사용하여 권한 있는 ID에 역할을 추가하는 방법을 알아봅니다."
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="inhenk"/>

# Azure 권한 있는 ID 관리: 보안 검토를 수행하는 방법

## 보안 검토 수행
[보안 검토가 시작](active-directory-privileged-identity-management-how-to-start-security-review.md)된 후에는 권한 있는 액세스를 검토하기가 매우 쉽습니다.

## 검토자용: 액세스 승인 또는 거부

### Me 검토
1. PIM 주 메뉴에서 **Review administrative access**(관리 액세스 검토)를 클릭합니다. 보안 리뷰 목록이 표시됩니다.
2. 목록에서 액세스를 변경할 **사용자**를 선택합니다. 참고: 액세스가 실제로 변경됩니다. 이 프로세스는 역할에 대한 액세스를 변경하는 담당자를 위한 검사 목록만 생성합니다. 최소 한 명의 사용자를 선택하면 **Approve access**(액세스 승인) 및 **Deny access**(액세스 거부) 단추를 사용할 수 있습니다.
3. 선택한 사용자에 대해 **Approve access**(액세스 승인) 또는 **Deny access**(액세스 거부)를 클릭합니다. Azure 포털 주 메뉴에 알림이 표시되고 검토 목록은 사라집니다. **Review Azure AD roles**(Azure AD 역할 검토)를 닫습니다.

### 자체 검토
1. 자신의 액세스를 검토해야 한다는 전자 메일을 사용자가 수신합니다. 메일에는 Azure 포털에 로그인하기 위한 링크가 포함됩니다.
2. 포털에서 사용자는 **Approve access**(액세스 승인) 또는 **Deny access**(액세스 거부) 단추를 클릭하여 자신의 액세스를 승인 또는 거부할 수 있습니다. 목록에서 사용자의 이름이 사라집니다.

## 검토 관리자용: 보안 검토 관리

## 검토 완료 또는 중지
1. PIM 대시보드로 다시 돌아갑니다.
2. **Security reviews**(보안 검토) 목록에서 완료하려는 보안 검토를 클릭합니다. 보안 검토의 세부 정보 블레이드가 표시됩니다.
3. 검토를 완료하거나 중지하려면 **Stop review**(검토 중지)를 클릭합니다. 이렇게 하면 검토가 보관되고 블레이드가 사라집니다.

## 검토 내보내기
Excel 또는 CSV 파일을 사용할 수 있는 다른 프로그램에서 사용하도록 검토를 내보낼 수 있습니다.

1. PIM 대시보드로 다시 돌아갑니다.
2. 대시보드의 **Security reviews**(보안 검토) 섹션을 클릭합니다. **Security reviews**(보안 검토) 블레이드가 표시됩니다.
3. 내보낼 보안 검토를 클릭합니다. 보안 검토의 세부 정보 블레이드가 표시됩니다.
4. **내보내기** 단추를 클릭합니다. CSV 파일 다운로드가 시작됩니다.

## 검토 삭제

> AZURE 경고 [삭제가 일어나기 전에 경고가 표시되지 않으므로 실제로 삭제를 *원하는* 검토인지 판단에 주의해야 합니다.]

1. PIM 대시보드로 다시 돌아갑니다.
2. 대시보드의 **Security reviews**(보안 검토) 섹션을 클릭합니다. **Security reviews**(보안 검토) 블레이드가 표시됩니다.
3. 삭제할 보안 검토를 클릭합니다. 보안 검토의 세부 정보 블레이드가 표시됩니다.
4. **삭제** 단추를 클릭합니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0107_2016-->