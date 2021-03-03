---
title: 통신 서비스에서 관리 되는 id 사용
titleSuffix: An Azure Communication Services quickstart
description: 관리 id를 사용 하면 azure Vm, 함수 앱 및 기타 리소스에서 실행 되는 응용 프로그램에서 Azure Communication Services 액세스 권한을 부여할 수 있습니다.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 2/24/2021
ms.author: peiliu
ms.reviewer: mikben
ms.openlocfilehash: 0d25e5dc97c700daf6655ecd270bfda469a9d353
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657623"
---
# <a name="use-managed-identities"></a>관리 ID 사용
관리 id를 사용 하 여 Azure 통신 서비스를 시작 하세요. 통신 서비스 Id 및 SMS 클라이언트 라이브러리는 [azure 리소스에 대 한 관리 id](../../active-directory/managed-identities-azure-resources/overview.md)를 사용 하 여 azure AD (Azure Active Directory) 인증을 지원 합니다.

이 빠른 시작에서는 관리 되는 id를 지 원하는 Azure 환경에서 Id 및 SMS 클라이언트 라이브러리에 대 한 액세스 권한을 부여 하는 방법을 보여 줍니다. 개발 환경에서 코드를 테스트 하는 방법에 대해서도 설명 합니다.

## <a name="prerequisites"></a>필수 구성 요소

 - 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free)
 - 활성 Communication Services 리소스 및 연결 문자열. [Communication Services 리소스를 만듭니다](./create-communication-resource.md?pivots=platform-azp&tabs=windows).

## <a name="setting-up"></a>설치

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>가상 컴퓨터 또는 App service에서 관리 되는 id 사용

권한을 부여 하는 Azure 리소스에서 관리 되는 id를 사용 하도록 설정 해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 클라이언트 라이브러리](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [앱 서비스](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure 역할 할당

1. Azure Portal로 이동합니다.
1. Azure 통신 서비스 리소스로 이동 합니다.
1. Access Control (IAM) 메뉴-> + 추가 > 역할 할당 추가로 이동 합니다.
1. 역할 "참여자" (유일 하 게 지원 되는 역할)를 선택 합니다.
1. "사용자 할당 관리 id" (또는 "시스템 할당 관리 id")를 선택 하 고 원하는 id를 선택 합니다. 선택 항목을 저장 합니다.

![관리 id 역할](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>PowerShell을 사용 하 여 Azure 역할 할당

PowerShell을 사용 하 여 역할 및 사용 권한을 할당 하려면 [Azure PowerShell을 사용 하 여 Azure 역할 할당 추가 또는 제거](../../../articles/role-based-access-control/role-assignments-powershell.md) 를 참조 하세요.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end