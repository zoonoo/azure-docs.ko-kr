<properties
   pageTitle="Azure 권위 있는 ID 관리: MFA를 요구하는 방법"
   description="Azure 권위 있는 ID 관리 확장을 사용하여 권한 있는 ID에 대해 MFA(다단계 인증)를 요구하는 방법을 알아봅니다."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Azure Privileged Identity Management: MFA를 요구하는 방법

모든 관리자에 대해 다단계 인증을 요구하는 것이 좋습니다.

##Azure 권위 있는 ID 관리에서 MFA 요구
PIM 관리자로 로그인하는 경우 권한 있는 계정에 MFA(다단계 인증)가 필요하다는 경고가 표시됩니다. PIM 대시보드에서 보안 경고를 클릭하면 MFA가 필요한 관리자 계정 목록이 포함된 새 블레이드가 열립니다. 여러 역할을 선택하고 **수정** 단추를 클릭하여 MFA를 요구하거나, 개별 역할 옆에 있는 줄임표를 클릭한 다음 **수정** 단추를 클릭할 수 있습니다.

또한 대시보드의 역할 섹션에서 역할을 클릭한 다음 역할 블레이드에서 **설정**을 클릭하고 Multi-Factor Authentication에서 **사용**을 선택하여 해당 역할에 대해 MFA를 사용하도록 설정하면 특정 역할에 대한 MFA 요구 사항을 변경할 수 있습니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0128_2016-->