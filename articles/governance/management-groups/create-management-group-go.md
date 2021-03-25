---
title: '빠른 시작: Go를 사용하여 관리 그룹 만들기'
description: 이 빠른 시작에서는 Go를 사용하여 리소스를 리소스 계층 구조로 구성하는 관리 그룹을 만듭니다.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 7b6ca4d10f2a86ecb55fec2afe72b4aabfbb94f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100101670"
---
# <a name="quickstart-create-a-management-group-with-go"></a>빠른 시작: Go를 사용하여 관리 그룹 만들기

관리 그룹은 여러 구독에서 액세스, 정책 및 규정 준수를 관리하는 데 도움이 되는 컨테이너입니다. 이러한 컨테이너를 만들어 [Azure 정책](../policy/overview.md) 및 [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)와 함께 사용할 수 있는 효과적이고 효율적인 계층을 구축합니다. 관리 그룹에 대한 자세한 내용은 [Azure 관리 그룹으로 리소스 구성](overview.md)을 참조하세요.

디렉터리에서 만드는 첫 번째 관리 그룹을 완료하려면 최대 15분이 소요될 수 있습니다. 디렉터리에 대해 Azure 내의 관리 그룹 서비스를 설정하기 위해 처음으로 실행되는 프로세스가 있습니다. 프로세스가 완료되면 알림이 수신됩니다. 자세한 내용은 [관리 그룹의 초기 설정](./overview.md#initial-setup-of-management-groups)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

- _clientId_ 및 _clientSecret_ 을 포함하는 Azure 서비스 주체. Azure Policy에 사용할 서비스 주체가 없거나 새로 만들려면 [.NET 인증을 위한 Azure 관리 라이브러리](/dotnet/azure/sdk/authentication#mgmt-auth)를 참조하세요.
  .NET Core 패키지를 설치하는 단계는 건너뜁니다. 다음 단계에서 수행할 것입니다.

- 테넌트의 모든 Azure AD 사용자는 [계층 구조 보호](./how-to/protect-resource-hierarchy.md#setting---require-authorization)가 활성화되지 않은 경우 해당 사용자에게 할당된 관리 그룹 쓰기 권한 없이 관리 그룹을 만들 수 있습니다. 이 새 관리 그룹은 루트 관리 그룹 또는 [기본 관리 그룹](./how-to/protect-resource-hierarchy.md#setting---default-management-group)의 자식이 되며 작성자에게 "소유자" 역할 할당이 부여됩니다. 관리 그룹 서비스는 이 기능을 허용하므로 루트 수준에서 역할 할당이 필요하지 않습니다. 루트 관리 그룹이 생성될 때 사용자는 액세스할 수 있는 권한이 없습니다. 관리 그룹 사용을 시작하기 위해 Azure AD 전역 관리자를 찾는 장애물을 방지하기 위해 루트 수준에서 초기 관리 그룹을 만들 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-management-group-package"></a>관리 그룹 패키지 추가

Go에서 관리 그룹을 관리할 수 있도록 하려면 패키지를 추가해야 합니다. 이 패키지는 [Windows 10의 bash](/windows/wsl/install-win10) 또는 로컬로 설치된 경우를 포함하여 Go를 사용할 수 있을 때마다 작동합니다.

1. 최신 Go가 설치되었는지 확인합니다(**1.15** 이상). 아직 설치되지 않았으면 [Golang.org](https://golang.org/dl/)에서 다운로드합니다.

1. 최신 Azure CLI가 설치되었는지 확인합니다(**2.5.1** 이상). 아직 설치되지 않았으면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

   > [!NOTE]
   > 다음 예제에서 `auth.NewAuthorizerFromCLI()` 메서드를 사용하도록 Go를 설정하려면 Azure CLI가 필요합니다. 다른 옵션에 대한 자세한 내용은 [Go용 Azure SDK - 추가 인증 정보](https://github.com/Azure/azure-sdk-for-go#more-authentication-details)를 참조하세요.

1. Azure CLI를 통해 인증을 수행합니다.

   ```azurecli
   az login
   ```

1. 선택한 Go 환경에서 관리 그룹에 필요한 패키지를 설치합니다.

   ```bash
   # Add the management group package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="application-setup"></a>애플리케이션 설정

선택한 환경에 Go 패키지를 추가했으므로 이제 관리 그룹을 만들 수 있는 Go 애플리케이션을 설정할 차례입니다.

1. Go 애플리케이션을 만들고 `mgCreate.go`로 다음 원본을 저장합니다.

   ```Go
   package main
   
   import (
    "context"
    "fmt"
    "os"
   
    mg "github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups"
    "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
    // Get variables from command line arguments
    var mgName = os.Args[1]
   
    // Create and authorize a client
    mgClient := mg.NewClient()
    authorizer, err := auth.NewAuthorizerFromCLI()
    if err == nil {
        mgClient.Authorizer = authorizer
    } else {
        fmt.Printf(err.Error())
    }
   
    // Create the request
    Request := mg.CreateManagementGroupRequest{
        Name: &mgName,
    }
   
    // Run the query and get the results
    var results, queryErr = mgClient.CreateOrUpdate(context.Background(), mgName, Request, "no-cache")
    if queryErr == nil {
        fmt.Printf("Results: " + fmt.Sprint(results) + "\n")
    } else {
        fmt.Printf(queryErr.Error())
    }
   }
   ```

1. Go 애플리케이션을 빌드합니다.

   ```bash
   go build mgCreate.go
   ```

1. 컴파일된 Go 애플리케이션을 사용하여 관리 그룹을 만듭니다. `<Name>`을 새 관리 그룹의 이름으로 바꿉니다.

   ```bash
   mgCreate "<Name>"
   ```

결과적으로 루트 관리 그룹에 새 관리 그룹이 생성됩니다.

## <a name="clean-up-resources"></a>리소스 정리

Go 환경에서 설치된 패키지를 제거하려면 다음 명령을 사용할 수 있습니다.

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 리소스 계층 구조를 구성하기 위한 관리 그룹을 만들었습니다. 관리 그룹은 구독 또는 기타 관리 그룹을 포함할 수 있습니다.

관리 그룹 및 리소스 계층 구조를 관리하는 방법에 대해 자세히 알아보려면 다음을 계속 진행하세요.

> [!div class="nextstepaction"]
> 관리 그룹으로 리소스 관리