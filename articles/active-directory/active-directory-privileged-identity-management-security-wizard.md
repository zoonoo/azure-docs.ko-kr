<properties
   pageTitle="Azure 권한 있는 ID 관리 보안 마법사"
   description="Azure Privileged Identity Management 확장을 처음 실행하면 보안 마법사가 표시됩니다. 이 문서는 마법사를 사용하는 단계를 설명합니다."
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

# Azure Privileged Identity Management 보안 마법사

Azure Privileged Identity Management(PIM)를 처음 실행하면 마법사가 표시됩니다. 마법사는 권한 있는 ID에 대한 보안 위험을 이해하고 위험을 줄이도록 권한 있는 ID 관리를 사용하는 방법을 이해하도록 도와줍니다.

세 가지 검토할 섹션은 **YOUR ADMINS MIGHT PUT YOU AT RISK, MANAGE YOUR ADMINS’ ATTACK SURFACE**(관리자로 인해 위험에 처할 수 있음, 관리자의 공격 표면 관리) 및 **DEFINE TEMPORARY ADMIN SETTINGS**(임시 관리자 설정 정의)입니다. 각 섹션에는 수행할 작업에 대한 설명 및 개념에 대한 개요가 제공됩니다.

우선 모든 전역 관리자는 영구적입니다. **YOUR ADMINS MIGHT PUT YOU AT RISK**(관리자로 인해 위험에 처할 수 있음)을 클릭하면 전역 관리자 역할 목록과 현재 보유하고 있는 개수가 표시됩니다.

**MANAGE YOUR ADMINS’ ATTACK SURFACE**(관리자의 공격 표면 관리)를 클릭하면 관리자를 임시로 변경하거나 영구적으로 유지하거나 역할에서 완전히 삭제할 기회가 제공됩니다.

**DEFINE TEMPORARY ADMINS SETTINGS**(임시 관리자 설정 정의)는 Multi-Factor Authentication을 요청하고 알림을 활성화하며 임시 관리자가 권한을 소유하는 기간을 정할 수 있도록 합니다.

## 전역 관리자 역할을 임시 또는 영구적으로 변경

전역 관리자의 시간 창을 변경하는 옵션이 세 가지 있습니다.

1.  모든 전역 관리자를 임시로 만들려면 **Make all temporary**(모두 임시로 설정) 단추를 클릭합니다.

2.  모든 전역 관리자를 영구적으로 만들려면 **Make all permanent**(모두 영구적으로 설정) 단추를 클릭합니다.

3.  각각의 전역 관리자에 대해 **Keep Perm**(영구적으로 유지), **Make Temp**(임시로 설정), 또는 **Remove from Role**(역할에서 제거)을 선택합니다.

## 전역 관리자 역할에 대한 활성화 기간을 변경합니다.

전역 관리자 역할에 대한 활성화 기간을 설정하는 방법에는 두 가지가 있습니다.

1.  **activation period**(활성화 기간) 슬라이더를 왼쪽 또는 오른쪽으로 이동하여 활성화 기간을 증가 또는 감소시킵니다. 활성화 기간은 최대 72시간 까지 가능합니다.

2.  **hours**(시간) 필드에 시간을 입력합니다.

## 알림 사용

일부 관리자는 역할이 활성화되면 메일을 수신할 수 있습니다. **사용** 단추를 클릭하여 알림을 사용합니다. 나중에 이 기능을 해제할 수도 있습니다.

## Multi-Factor Authentication 요구

관리자가 자신의 계정에 로그인하고 역할에 대한 확장을 요청하기 위해 MFA를 사용하도록 요구하려면 **사용** 단추를 클릭하여 MFA를 사용합니다. 나중에 이 기능을 해제할 수도 있습니다.

<!--For more information about MFA and PIM, click here. PLACEHOLDER: NEED LINK TO MFA DOC.-->

이러한 설정이 적용될 역할을 선택합니다. **확인**을 클릭합니다.

> [AZURE.WARNING] 현재는 두 개 이상의 보안 관리자가 있어야 합니다. 영구적으로 설정되지 않은 보안 관리자가 하나만 있고 MFA를 설정하지 않은 경우 사용자는 역할 할당이 만료된 후 PIM을 전혀 관리할 수 없게 됩니다.

작업이 완료되었으면 **확인** 단추를 클릭합니다.

항목을 변경한 후에는 마법사가 표시되지 않습니다. 하지만 **Manage identities**(ID 관리) 아래의 **마법사** 단추를 클릭하면 마법사에 다시 액세스할 수 있습니다.

## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0128_2016-->