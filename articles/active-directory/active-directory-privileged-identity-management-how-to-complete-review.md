<properties
   pageTitle="보안 검토를 완료하는 방법 | Microsoft Azure"
   description="Azure Privileged Identity Management 응용 프로그램을 사용하여 검토를 완료하는 방법을 알아봅니다."
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
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management의 보안 검토를 완료하는 방법


[보안 검토가 시작](active-directory-privileged-identity-management-how-to-start-security-review.md)된 후에 보안 관리자가 권한이 있는 액세스를 검토할 수 있습니다. Azure AD PIM(Privileged Identity Management)이 사용자에게 해당 액세스를 검토하도록 요청하는 메일을 자동으로 전송합니다. 사용자가 메일을 받지 못한 경우 [보안 검토를 수행하는 방법](active-directory-privileged-identity-management-how-to-perform-security-review.md)의 지침을 보낼 수 있습니다.

보안 검토 기간이 끝나거나 모든 사용자가 자체 검토를 완료하면 이 문서의 단계에 따라 검토를 관리하고 결과를 표시합니다.

## 보안 검토 관리

1. [Azure 포털](https://portal.azure.com/)로 이동하고 대시보드에서 **Azure AD Privileged Identity Management** 응용 프로그램을 선택합니다.
2. 대시보드의 **Security reviews(보안 검토)**섹션을 선택합니다. **Security reviews(보안 검토)** 블레이드가 표시됩니다.
3. 관리하려는 보안 검토를 선택합니다. 보안 검토의 세부 정보 블레이드가 표시됩니다.

보안 검토의 세부 정보 블레이드에는 검토를 관리하는 세 가지 옵션이 있습니다. 검토 완료, 세부 정보 내보내기 또는 삭제할 수 있습니다.

### 검토를 완료 또는 중지합니다.

사용자가 액세스를 거부하면 검토를 완료하여 디렉터리에 해당 사용자 역할 할당을 제거할 수 있습니다. **검토 중지** 단추는 검토를 보관합니다.

### 검토 내보내기

보안 검토 결과를 수동으로 적용하려는 경우 검토를 내보낼 수 있습니다. **내보내기** 단추는 CSV 파일 다운로드를 시작합니다. Excel 또는 CSV 파일을 여는 다른 프로그램에서 결과를 관리할 수 있습니다.

### 검토 삭제

> [AZURE.IMPORTANT] 삭제가 일어나기 전에 경고가 표시되지 않으므로 삭제를 원하는 검토인지 확인해야 합니다.

더 이상 검토가 필요 없는 경우 검토를 삭제합니다. **삭제** 단추는 PIM 응용 프로그램에서 검토를 제거합니다.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->