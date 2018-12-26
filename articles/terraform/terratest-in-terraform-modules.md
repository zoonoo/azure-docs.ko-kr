---
title: Terratest를 사용하여 Azure에서 Terraform 모듈 테스트
description: Terratest를 사용하여 Terraform 모듈을 테스트하는 방법을 알아보세요.
services: terraform
ms.service: terraform
keywords: terraform, devops, 저장소 계정, azure, terratest, 단위 테스트, 통합 테스트
author: JunyiYi
manager: jeconnoc
ms.author: junyi
ms.topic: tutorial
ms.date: 10/19/2018
ms.openlocfilehash: 7feee063c7b311934f7d157a9dff62d803a041b0
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638714"
---
# <a name="test-terraform-modules-in-azure-using-terratest"></a>Terratest를 사용하여 Azure에서 Terraform 모듈 테스트

Terraform 모듈은 재사용 가능하고 구성 가능하며 테스트 가능한 구성 요소를 만드는 데 사용합니다. "코드 제공 인프라" 환경에서 캡슐화를 통합합니다.

다른 소프트웨어 구성 요소와 마찬가지로 품질 보증은 Terraform 모듈에서 중요한 역할을 합니다. 그러나 Terraform 모듈에서 단위 테스트 및 통합 테스트를 작성하는 방법을 설명하는 문서는 거의 없습니다. 이 자습서에서는 [Azure Terraform 모듈](https://registry.terraform.io/browse?provider=azurerm)을 빌드하는 동안 적용한 테스트 인프라 및 모범 사례를 소개합니다.

가장 인기 있는 테스트 인프라를 모두 고려한 후, [Terratest](https://github.com/gruntwork-io/terratest)를 사용하기로 선택했습니다. Terratest는 Go 라이브러리로 구현됩니다. 특정 가상 머신에 HTTP 요청 및 SSH를 적용하는 등, 일반적인 인프라 테스트 작업에 도우미 함수 및 패턴 컬렉션을 제공합니다. Terratest의 몇 가지 주요 이점은 다음과 같습니다.

- **인프라를 확인할 수 있는 편리한 도우미를 제공합니다.** 이 기능은 실제 환경에서 실제 인프라를 확인하려는 경우에 유용합니다.
- **폴더 구조가 명확하게 구성되어 있습니다.** 테스트 사례는 명확하게 구성되며 [표준 Terraform 모듈 폴더 구조](https://www.terraform.io/docs/modules/create.html#standard-module-structure)를 따릅니다.
- **모든 테스트 사례가 Go로 작성되었습니다.** 대부분 Terraform 개발자가 이미 Go 개발자이므로, Terratest를 사용하면 다른 프로그래밍 언어를 학습할 필요성을 없애줍니다. 또한 Terratest에서 테스트 사례를 실행하는 데 필요한 유일한 종속성은 Go와 Terraform입니다.
- **이 인프라는 확장성이 매우 뛰어납니다.** Azure 특정 기능 등, Terratest 이외에 추가 기능을 확장하는 것이 어렵지 않습니다.

## <a name="prerequisites"></a>필수 조건

이 실습 안내서는 플랫폼 독립적으로, Windows, Linux 또는 MacOS에서 실행할 수 있습니다. 설치를 계속하려면 다음 소프트웨어가 필요합니다.

- **Go 프로그래밍 언어**: Terraform 테스트 사례가 [Go](https://golang.org/dl/)로 작성되어 있습니다.
- **dep**: [dep](https://github.com/golang/dep#installation)는 Go의 종속성 관리 도구입니다.
- **Azure CLI**: [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)는 Azure 리소스를 관리하기 위한 명령줄 도구입니다. (Terraform은 서비스 주체 또는 [Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html)를 통한 Azure 인증을 지원합니다.)
- **mage**: [mage 실행 파일](https://github.com/magefile/mage/releases)을 사용하여 Terratest 사례 실행을 간소화하는 방법에 대해 알아보려고 합니다. 

## <a name="create-a-static-webpage-module"></a>정적 웹 페이지 모듈 만들기

이 자습서에서는 단일 HTML 파일을 Azure Storage Blob에 업로드하여 정적 웹 페이지를 프로비저닝하는 Terraform 모듈을 만듭니다. 이 모듈을 통해 세계 각지의 사용자는 모듈이 반환하는 URL을 통해 이 웹 페이지에 액세스할 수 있게 됩니다.

> [!NOTE]
> 이 섹션에서 설명된 모든 파일은 [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) 아래에 만들어져야 합니다.

먼저, GoPath의 `src` 폴더에 `staticwebpage`라는 이름의 새 폴더를 만듭니다. 이 자습서의 전체 폴더 구조는 아래 자세히 설명되어 있습니다. (별표(`(*)`)가 있는 파일은 이 섹션의 주요 중점 사항입니다.)

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

정적 웹 페이지 모듈은 세 가지 입력을 수신하며 `./variables.tf`에 나와 있습니다.

```hcl
variable "location" {
  description = "The Azure region in which all resources will be created."
}

variable "website_name" {
  description = "The website name which will be used to create a bunch of related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static homepage HTML in your local filesystem."
  default     = "index.html"
}
```

앞서 언급된 것처럼, 이 모듈은 `./outputs.tf`에 표시된 URL도 출력합니다.

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

이를 통해 이 모듈의 기본 논리에 대해 알 수 있습니다. 전체적으로 4개의 리소스를 프로비저닝합니다.
- `website_name` 입력에 `-staging-rg`가 추가된 이름의 리소스 그룹
- `website_name` 입력에 `data001`이 추가된 이름의 저장소 계정 하지만 저장소 계정의 이름 제한 사항을 준수하기 위해 이 모듈은 모든 특수 문자를 제거하고 전체 이름을 소문자로 표시합니다.
- 고정 이름의 컨테이너 `wwwroot`가 위의 저장소 계정에 생성되었습니다.
- 단일 HTML 파일을 `html_path` 입력에서 읽어 `wwwroot/index.html`로 업로드됩니다.

정적 웹 페이지 모듈 논리가 `./main.tf`에 구현되었습니다.

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = "${var.location}"
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = "${azurerm_resource_group.main.name}"
  location                 = "${azurerm_resource_group.main.location}"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = "${azurerm_resource_group.main.name}"
  storage_account_name  = "${azurerm_storage_account.main.name}"
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = "${azurerm_resource_group.main.name}"
  storage_account_name   = "${azurerm_storage_account.main.name}"
  storage_container_name = "${azurerm_storage_container.main.name}"
  source                 = "${var.html_path}"
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>단위 테스트

Terratest는 일반적으로 실제 환경에서 실제 리소스를 프로비저닝하는 통합 테스트용으로 설계된 도구입니다. 경우에 따라 이러한 작업은 특히 프로비저닝할 리소스가 엄청나게 많을 때 매우 커질 수 있습니다. 이전 섹션에 설명된 저장소 계정 명명 변환 논리는 좋은 예입니다. 실제로 모든 리소스를 프로비저닝하지 않고, 단지 명명 변환 논리가 올바른지 확인하길 원할 수도 있습니다.

Terratest의 유연성 덕분에, 단위 테스트를 사용하여 쉽게 확인할 수 있습니다. 단위 테스트는 로컬 실행 테스트 사례(여전히 인터넷 액세스가 필요함)로서, `terraform init` 및 `terraform plan` 명령 실행만으로 단위 테스트 사례는 `terraform plan`의 출력을 구문 분석하고 비교할 특성 값을 찾습니다.

이 섹션의 나머지 부분에서는 저장소 계정 명명 변환 논리가 올바른지 확인하기 위해 Terratest를 사용하여 단위 테스트를 구현하는 방법에 대해 설명합니다. 별표(`(*)`)가 있는 파일에 대해서만 관심을 가질 것입니다.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

먼저, 빈 HTML 파일 `./test/fixtures/storage-account-name/empty.html`은 단지 자리 표시자입니다.

`./test/fixtures/storage-account-name/main.tf` 파일은 테스트 사례 스켈레톤입니다. 하나의 입력 `website_name`을 수신하며, 이 입력은 단위 테스트의 입력이기도 합니다. 해당 논리는 여기에 표시됩니다.

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = "${var.website_name}"
  html_path    = "empty.html"
}
```

마지막으로, 주요 구성 요소는 단위 테스트 `./test/storage_account_name_unit_test.go`의 구현입니다.

Go 개발자라면 `*testing.T` 유형의 인수를 수락하여 클래식 Go 테스트 함수의 서명과 일치하다는 것을 알게 될 것입니다.

단위 테스트 본문에는 변수 `testCases`에 정의된 총 5개의 사례가 있습니다(키는 입력, 값은 예상 출력). 각 단위 테스트 사례에 대해 먼저 테스트 픽스쳐 폴더(`./test/fixtures/storage-account-name/`)를 타게팅한 `terraform init`를 실행합니다. 

그런 다음, 특정 테스트 사례 입력이 있는 `terraform plan` 명령(`tfOptions`의 `website_name` 정의 참조)은 결과를 `./test/fixtures/storage-account-name/terraform.tfplan`에 저장합니다(전체 폴더 구조에 나와 있지 않음).

다음으로, 이 결과 파일은 공식 Terraform 계획 파서를 사용하여 코드 판독 가능한 구조로 구문 분석됩니다.

이제 우리는 관심이 있는 특성(이 사례에서는 `azurerm_storage_account`의 `name`)을 살펴보고 이들을 예상 출력과 비교할 것입니다.

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions.Vars, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

단위 테스트를 실행하려면 명령줄에서 다음 단계를 완료해야 합니다.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

약 1분 후에 일반적인 Go 테스트 결과가 나타납니다.

### <a name="integration-test"></a>통합 테스트

단위 테스트와 달리, 통합형 관점에서 실제 환경에 리소스를 프로비저닝하려면 통합 테스트가 필요합니다. Terratest는 이러한 작업에서 우수합니다. 또한 Terraform 모듈의 모범 사례에서도 일부 통합형 예제를 포함한 `examples` 폴더를 권장하기 때문에, 이러한 샘플을 통합 테스트로 테스트하는 것은 어떨까요? 이 섹션에서는 각 별표(`(*)`)가 있는 세 개의 파일을 중점적으로 살펴보겠습니다.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

먼저 샘플로 시작하겠습니다. 새 샘플 폴더(`hello-world/`)가 `./examples/` 폴더에 만들어집니다. 여기에서는 업로드할 간단한 HTML 페이지를 제공합니다. `./examples/hello-world/index.html`

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demostrate Terratest.</p>
</body>
</html>
```

Terraform 샘플(`./examples/hello-world/main.tf`)은 단위 테스트에 표시된 것과 유사하지만, 단 하나의 큰 차이점은 이름이 `homepage`인 업로드된 HTML의 URL도 출력한다는 점입니다.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = "${var.website_name}"
}

output "homepage" {
  value = "${module.staticwebpage.homepage_url}"
}
```

Terratest 및 클래식 Go 테스트 함수가 통합 테스트 파일 `./test/hello_world_example_test.go`에 다시 나타납니다.

단위 테스트와는 달리 통합 테스트는 Azure에서 실제 리소스를 만들며, 이것이 이름 충돌이 발생하지 않도록 주의해야 하는 이유입니다. (저장소 계정 이름과 같은 전역적으로 고유한 이름에 특히 주의하세요.) 따라서, 테스트 논리의 첫 단계는 TerraTest에서 제공하는 `UniqueId()` 함수를 사용하는 임의 `websiteName`을 생성하는 것입니다. 이 함수는 수문자, 대문자 또는 숫자를 포함한 임의 이름을 생성합니다. `tfOptions`는 모든 Terraform 명령이 `./examples/hello-world/` 폴더를 타게팅하며 `website_name`가 임의 `websiteName`으로 설정됩니다.

그런 다음, `terraform init`, `terraform apply` 및 `terraform output`이 하나씩 실행됩니다. 우리는 HTTP Get 상태 코드를 `200`과 비교하고 HTML 콘텐츠에서 일부 키워드를 찾아 `terraform output`에서 반환한 출력 `homepage` URL로 HTML이 업로드될 수 있도록 Terratest에서 제공한 다른 도우미 함수 `HttpGetWithCustomValidation()`을 사용했습니다. 마지막으로, `terraform destroy`는 Go의 `defer` 기능을 활용하여 실행되도록 "약속"되어 있습니다.

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demostrate Terratest.")
    })
}
```

통합 테스트를 실행하려면 명령줄에서 다음 단계를 완료해야 합니다.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

약 2분 후에 일반적인 Go 테스트 결과가 나타납니다. 물론, 다음을 실행하여 통합 테스트와 단위 테스트를 둘 다 실행할 수도 있습니다.

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

알 수 있듯이 통합 테스트는 단위 테스트보다 더 오래 걸립니다(1개의 통합 사례에 2분, 5개의 단위 테스트에 1분). 하지만 단위 테스트 사용 시기와 통합 테스트 활용 시기는 사용자가 결정해야 합니다. 일반적으로 Terraform HCL 함수를 사용하는 복잡한 논리에는 단위 테스트를 사용하지만, 사용자의 통합형 관점에서는 통합 테스트를 사용하는 것이 좋습니다.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>mage를 사용하여 Terratest 사례 실행 단순화 

앞에서 설명한 것처럼, 셸에서 테스트 사례를 실행하는 것은 다른 디렉터리로 이동하여 다른 명령을 실행해야 하므로 쉬운 작업이 아닙니다. 프로젝트에 빌드 시스템을 도입하는 것도 이러한 이유 때문입니다. 이 섹션에서는 Go 빌드 시스템 mage를 사용하여 작업을 수행하려고 합니다.

mage에는 프로젝트의 루트 디렉토리에 `magefile.go`만 있으면 됩니다(다음 그림에서 `(+)`로 표시).

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

`./magefile.go`의 한 가지 예제는 다음과 같습니다. Go로 작성된 빌드 스크립트에서 5개의 빌드 단계를 구현했습니다.
- `Clean`: 이 단계는 테스트 실행 중에 생성된/임시 폴더를 모두 제거합니다.
- `Format`: 이 단계는 `terraform fmt` 및 `go fmt`를 실행하여 코드 베이스의 서식을 지정합니다.
- `Unit`: 이 단계는 `./test/` 폴더에 있는 모든 단위 테스트(함수 이름 표기 규칙 `TestUT_*` 사용)를 실행합니다.
- `Integration`: 단위 테스트 대신, 통합 테스트(`TestIT_*`)를 실행한다는 것을 제외하고 `Unit`과 유사합니다.
- `Full`: 이 단계는 `Clean`, `Format`, `Unit', and `Integration`을 순서대로 실행합니다.

```go
// +build mage

// Build script to format and run tests of a Terraform module project.
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// Default target when execute `mage` in shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build/test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

이전 실행 단계와 마찬가지로 다음 명령을 사용하여 전체 테스트 도구 모음을 사용할 수 있습니다.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only requied when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage$ mage
```

인스턴스 `mage unit` 또는 `mage clean`에 모든 mage 단계를 사용하여 자유롭게 마지막 명령줄을 대체할 수 있습니다. 이제 아직 많은 명령줄이 있다고 생각할 수 있으며, magefile에 `az login`뿐만 아니라 `dep` 명령을 포함하는 것이 좋습니다. 하지만 여기에서는 코드를 표시하지 않습니다. mage를 사용하면 하나 더 나아가 Go 패키지 시스템을 사용하여 단계를 공유할 수 있다는 것입니다. 따라서 공통된 구현을 참조하고 종속성을 선언하는 것만으로도 모든 모듈 전반에서 magefile을 단순화할 수 있습니다(`mg.Deps()`).

> [!NOTE]
> **옵션: 인수 테스트를 실행하도록 서비스 주체 환경 변수 설정**
> 
> 테스트 전에 `az login`을 실행하는 대신, 서비스 주체 환경 변수를 설정하여 Azure 인증을 실행할 수 있습니다. Terraform은 [환경 변수 이름 목록](https://www.terraform.io/docs/providers/azurerm/index.html#testing)을 게시합니다. (이러한 환경 변수 중 처음 4개만 필요합니다.) 또한, Terraform은 [이러한 환경 변수 값을 획득](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)하는 방법을 설명하는 세부적인 지침도 게시합니다.

## <a name="next-steps"></a>다음 단계

Terratest에 대한 자세한 내용은 [GitHub 페이지](https://github.com/gruntwork-io/terratest)를 참조하세요. [GitHub 페이지](https://github.com/magefile/mage) 및 [홈 페이지](https://magefile.org/)에서 mage에 대한 일부 유용한 정보를 찾을 수도 있습니다.
