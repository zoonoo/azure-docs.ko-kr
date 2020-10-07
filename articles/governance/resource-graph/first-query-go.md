---
title: '빠른 시작: 첫 번째 Go 쿼리'
description: 이 빠른 시작에서는 단계에 따라 Go용 Resource Graph 패키지를 사용하도록 설정하고 첫 번째 쿼리를 실행합니다.
ms.date: 06/16/2020
ms.topic: quickstart
ms.openlocfilehash: cc15b9fe75913ca47a14001a27e1e3c37d9ea71a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "84899395"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-go"></a>빠른 시작: Go를 사용하여 첫 번째 Resource Graph 쿼리 실행

Azure Resource Graph를 사용하는 첫 번째 단계는 Go에 필요한 패키지가 설치되었는지 확인하는 것입니다. 이 빠른 시작에서는 Go 설치에 패키지를 추가하는 과정을 안내합니다.

이 과정이 끝나면 Go 설치에 패키지가 추가되고 첫 번째 Resource Graph 쿼리를 실행할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="add-the-resource-graph-package"></a>Resource Graph 패키지 추가

Go를 사용하도록 설정하여 Azure Resource Graph를 쿼리하려면 해당 패키지를 추가해야 합니다. 이 패키지는 [Windows 10의 bash](/windows/wsl/install-win10) 또는 로컬로 설치된 경우를 포함하여 Go를 사용할 수 있을 때마다 작동합니다.

1. 최신 Go가 설치되었는지 확인합니다(**1.14** 이상). 아직 설치되지 않았으면 [Golang.org](https://golang.org/dl/)에서 다운로드합니다.

1. 최신 Azure CLI가 설치되었는지 확인합니다(**2.5.1** 이상). 아직 설치되지 않았으면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

   > [!NOTE]
   > 다음 예제에서 `auth.NewAuthorizerFromCLI()` 메서드를 사용하도록 Go를 설정하려면 Azure CLI가 필요합니다. 다른 옵션에 대한 자세한 내용은 [Go용 Azure SDK - 추가 인증 정보](https://github.com/Azure/azure-sdk-for-go#more-authentication-details)를 참조하세요.

1. Azure CLI를 통해 인증을 수행합니다.

   ```azurecli
   az login
   ```

1. 선택한 Go 환경에서 Azure Resource Graph에 필요한 패키지를 설치합니다.

   ```bash
   # Add the Resource Graph package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="run-your-first-resource-graph-query"></a>첫 번째 Resource Graph 실행

Go 패키지가 선택한 환경에 추가되었으므로 간단한 Resource Graph 쿼리를 시도해 볼 수 있습니다. 쿼리는 각 리소스의 **이름** 및 **리소스 형식**과 함께 처음 5개 Azure 리소스를 반환합니다.

1. Go 애플리케이션을 만들고 `argQuery.go`로 다음 원본을 저장합니다.

   ```Go
   package main
   
   import (
       "fmt"
       "os"
       "context"
       "strconv"
       arg "github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph"
       "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
      // Get variables from command line arguments
      var query = os.Args[1]
      var subList = os.Args[2:]
   
      // Create and authorize a ResourceGraph client
      argClient := arg.New()
      authorizer, err := auth.NewAuthorizerFromCLI()
      if err == nil {
          argClient.Authorizer = authorizer
      } else {
          fmt.Printf(err.Error())
      }
   
      // Set options
      RequestOptions := arg.QueryRequestOptions {
          ResultFormat: "objectArray",
      }
   
      // Create the query request
      Request := arg.QueryRequest {
          Subscriptions: &subList,
          Query: &query,
          Options: &RequestOptions,
      }
   
      // Run the query and get the results
      var results, queryErr = argClient.Resources(context.Background(), Request)
      if queryErr == nil {
          fmt.Printf("Resources found: " + strconv.FormatInt(*results.TotalRecords, 10) + "\n")
          fmt.Printf("Results: " + fmt.Sprint(results.Data) + "\n")
      } else {
          fmt.Printf(queryErr.Error())
      }
   }
   ```

1. Go 애플리케이션을 빌드합니다.

   ```bash
   go build argQuery.go
   ```

1. 컴파일된 Go 애플리케이션을 사용하여 첫 번째 Azure Resource Graph 쿼리를 실행합니다. `<SubID>`를 사용자의 구독 ID로 바꿉니다.

   ```bash
   argQuery "Resources | project name, type | limit 5" "<SubID>"
   ```

   > [!NOTE]
   > 이 쿼리 예제에서는 `order by`와 같은 정렬 한정자를 제공하지 않으므로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 집합이 생성될 수 있습니다.

1. 첫 번째 매개 변수를 `argQuery`로 변경하고 **Name** 속성을 `order by`하도록 쿼리를 변경합니다. `<SubID>`를 사용자의 구독 ID로 바꿉니다.

   ```bash
   argQuery "Resources | project name, type | limit 5 | order by name asc" "<SubID>"
   ```

   > [!NOTE]
   > 첫 번째 쿼리와 마찬가지로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 집합이 생성될 수 있습니다. 쿼리 명령의 순서는 중요합니다. 이 예제에서 `order by`는 `limit` 뒤에 옵니다. 이 명령 순서는 먼저 쿼리 결과를 제한한 다음, 정렬합니다.

1. 첫 번째 매개 변수를 `argQuery`로 변경하고 **Name** 속성에 먼저 `order by`를 수행한 다음, 상위 5개 결과로 `limit`하도록 쿼리를 변경합니다. `<SubID>`를 사용자의 구독 ID로 바꿉니다.

   ```bash
   argQuery "Resources | project name, type | order by name asc | limit 5" "<SubID>"
   ```

최종 쿼리가 여러 번 실행될 때 환경이 전혀 변경되지 않는다고 가정하면 반환되는 결과는 **Name** 속성을 기준으로 일관되고 정렬되지만 여전히 상위 5개 결과로 제한됩니다.

## <a name="clean-up-resources"></a>리소스 정리

Go 환경에서 설치된 패키지를 제거하려면 다음 명령을 사용할 수 있습니다.

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Resource Graph 패키지를 Go 환경에 추가하고 첫 번째 쿼리를 실행했습니다. 리소스 그래프 언어에 대한 자세한 내용을 보려면 쿼리 언어 정보 페이지로 이동하세요.

> [!div class="nextstepaction"]
> [쿼리 언어에 대한 자세한 정보 가져오기](./concepts/query-language.md)