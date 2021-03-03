---
title: Azure Active Directory를 사용 하 여 관리 id 인증
description: 이 문서에서는 Azure SignalR Service에 액세스 하기 위해 Azure Active Directory를 사용 하 여 관리 id를 인증 하는 방법을 설명 합니다.
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: c561bb507a5178f4a838b370a3da8af9447829f4
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092553"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>Azure SignalR 리소스에 액세스 하기 위해 Azure Active Directory를 사용 하 여 관리 id 인증
Azure SignalR Service [는 azure 리소스에 대 한 관리 id](../active-directory/managed-identities-azure-resources/overview.md)를 사용 하 여 azure AD (Azure Active Directory) 인증을 지원 합니다. Azure 리소스에 대 한 관리 id는 azure Virtual Machines (Vm), 함수 앱, Virtual Machine Scale Sets 및 기타 서비스에서 실행 되는 응용 프로그램의 Azure AD 자격 증명을 사용 하 여 azure SignalR Service 리소스에 대 한 액세스 권한을 부여할 수 있습니다 Azure 리소스에 대 한 관리 되는 id를 Azure AD 인증과 함께 사용 하 여 클라우드에서 실행 되는 응용 프로그램에 자격 증명을 저장 하지 않을 수 있습니다.

이 문서에서는 Azure VM에서 관리 되는 id를 사용 하 여 Azure SignalR 서비스에 대 한 액세스 권한을 부여 하는 방법을 보여 줍니다.

## <a name="enable-managed-identities-on-a-vm"></a>VM에서 관리 ID 사용
Azure 리소스에 관리 되는 id를 사용 하 여 VM에서 Azure SIgnalR Service 리소스에 권한을 부여 하려면 먼저 VM에서 Azure 리소스에 대 한 관리 되는 id를 사용 하도록 설정 해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 클라이언트 라이브러리](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Azure AD에서 관리 되는 id에 대 한 사용 권한 부여
응용 프로그램의 관리 되는 id에서 Azure SignalR Service에 대 한 요청에 권한을 부여 하려면 먼저 관리 되는 id에 대 한 RBAC (역할 기반 액세스 제어) 설정을 구성 합니다. Azure SignalR 서비스는 또는 획득에 대 한 권한을 포함 하는 RBAC 역할을 정의 `AccessKey` `ClientToken` 합니다. RBAC 역할이 관리 id에 할당 되 면 관리 되는 id에 적절 한 범위에서 Azure SignalR Service에 대 한 액세스 권한이 부여 됩니다.

RBAC 역할을 할당 하는 방법에 대 한 자세한 내용은 [Azure SignalR Service 리소스에 대 한 액세스를 위해 Azure Active Directory 인증](authorize-access-azure-active-directory.md)을 참조 하세요.

## <a name="connect-to-azure-signalr-service-with-managed-identities"></a>관리 id를 사용 하 여 Azure SignalR Service에 연결
관리 id를 사용 하 여 Azure SignalR Service에 연결 하려면 역할 및 적절 한 범위에 id를 할당 해야 합니다. 이 섹션의 절차에서는 관리 되는 id로 실행 되 고 Azure SignalR Service 리소스에 액세스 하는 간단한 응용 프로그램을 사용 합니다.

여기서는 샘플 Azure virtual machine 리소스를 사용 하 고 있습니다.

1. **설정** 으로 이동 하 여 **id** 를 선택 합니다. 
1. **상태** **를 선택 합니다.** 
1. **저장** 을 선택하여 설정을 저장합니다. 

    ![가상 컴퓨터에 대 한 관리 id](./media/authenticate/identity-virtual-machine.png)

이 설정을 사용 하도록 설정 하면 Azure Active Directory (Azure AD)에서 새 서비스 id가 만들어지고 App Service 호스트로 구성 됩니다.

이제 Azure SignalR Service 리소스에서 필요한 범위의 역할에이 서비스 id를 할당 합니다.

## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure 역할 할당  
Azure RBAC 및 Azure Portal를 사용 하 여 Azure 리소스에 대 한 액세스를 관리 하는 방법에 대 한 자세한 내용은 [이 문서](..//role-based-access-control/role-assignments-portal.md)를 참조 하세요. 

역할 할당에 적절 한 범위를 결정 한 후 Azure Portal에서 해당 리소스로 이동 합니다. 리소스에 대 한 액세스 제어 (IAM) 설정을 표시 하 고 다음 지침에 따라 역할 할당을 관리 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 SignalR 리소스로 이동 합니다.
1. **Access Control (IAM)** 을 선택 하 여 Azure SignalR에 대 한 액세스 제어 설정을 표시 합니다. 
1. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다. 도구 모음에서 **추가** 단추를 선택한 다음 **역할 할당 추가** 를 선택 합니다. 

    ![도구 모음에 단추 추가](./media/authenticate/role-assignments-add-button.png)

1. **역할 할당 추가** 페이지에서 다음 단계를 수행 합니다.
    1. 할당 하려는 **Azure SignalR 역할** 을 선택 합니다. 
    1. 역할을 할당 하려는 **보안 주체** (사용자, 그룹, 서비스 사용자)를 검색 합니다.
    1. **저장** 을 선택 하 여 역할 할당을 저장 합니다. 

        ![응용 프로그램에 역할 할당](./media/authenticate/assign-role-to-application.png)

    1. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지는 해당 응용 프로그램을 보여 주며 `signalr-dev` `signalr-service` SignalR App 서버 역할에 있습니다. 
        
        ![역할 할당 목록](./media/authenticate/role-assignment-list.png)

비슷한 단계에 따라 리소스 그룹 또는 구독에 범위가 지정 된 역할을 할당할 수 있습니다. 역할 및 해당 범위를 정의한 후 [에는이 GitHub 위치에서](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)샘플을 사용 하 여이 동작을 테스트할 수 있습니다.

## <a name="samples-code-while-configuring-your-app-server"></a>앱 서버를 구성 하는 동안 샘플 코드

다음 옵션을 추가 합니다 `AddAzureSignalR` .

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;";
});
```

## <a name="next-steps"></a>다음 단계
- RBAC에 대해 자세히 알아보려면 [azure 역할 기반 액세스 제어 란? (AZURE RBAC)](../role-based-access-control/overview.md)을 참조 하세요.
- Azure PowerShell, Azure CLI 또는 REST API를 사용 하 여 Azure 역할 할당을 할당 하 고 관리 하는 방법을 알아보려면 다음 문서를 참조 하세요.
    - [Azure PowerShell을 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-cli.md)
    - [REST API를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-rest.md)
    - [Azure Resource Manager 템플릿으로 RBAC (역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-template.md)

다음 관련 문서를 참조 하세요.
- [Azure Active Directory를 사용 하 여 Azure SignalR Service에 액세스 하는 응용 프로그램 인증](authenticate-application.md)
- [Azure Active Directory를 사용 하 여 Azure SignalR Service에 대 한 액세스 권한 부여](authorize-access-azure-active-directory.md)