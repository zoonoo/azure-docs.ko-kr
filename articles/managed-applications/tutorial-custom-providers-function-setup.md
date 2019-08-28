---
title: Azure 사용자 지정 공급 기업을 위한 Azure Functions 설정
description: 이 자습서에서는 Azure Function을 만들어서 Azure 사용자 지정 공급 기업에서 작동하도록 설정하는 방법을 살펴봅니다.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7e4de43659db88bfd9aad40cc3b9f1753189bba
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799122"
---
# <a name="setup-azure-functions-for-azure-custom-providers"></a>Azure 사용자 지정 공급 기업을 위한 Azure Functions 설정

사용자 지정 공급 기업을 사용하면 Azure에서 워크플로를 사용자 지정할 수 있습니다. 사용자 지정 공급 기업은 Azure와 `endpoint` 사이의 계약입니다. 이 자습서에서는 Azure Function이 사용자 지정 공급 기업 `endpoint`로 작동하도록 설정하는 과정을 살펴봅니다.

이 자습서는 다음과 같은 단계로 나뉩니다.

- Azure Function 만들기
- Azure 테이블 바인딩 설치
- RESTful HTTP 메서드 업데이트
- Azure Resource Manager NuGet 패키지 추가

이 자습서는 다음 자습서를 기반으로 합니다.

- [Azure Portal을 통해 첫 번째 Azure Function 만들기](../azure-functions/functions-create-first-azure-function.md)

## <a name="creating-the-azure-function"></a>Azure Function 만들기

> [!NOTE]
> 이 자습서에서는 Azure Function을 사용하여 간단한 서비스 엔드포인트를 만들지만 사용자 지정 공급 기업은 원하는 공용 액세스 가능 `endpoint`를 사용할 수 있습니다. Azure Logic Apps, Azure API Management, Azure Web Apps는 몇 가지 훌륭한 대안입니다.

이 자습서를 시작하려면, [Azure Portal에서 첫 번째 Azure Function 만들기](../azure-functions/functions-create-first-azure-function.md) 자습서를 수행해야 합니다. 이 자습서에서는 Azure Portal에서 수정할 수 있는 .NET Core 웹후크 함수를 만듭니다.

## <a name="install-azure-table-bindings"></a>Azure 테이블 바인딩 설치

이 섹션에서는 Azure Table Storage 바인딩을 설치하는 빠른 단계를 수행합니다.

1. HttpTrigger의 `Integrate` 탭으로 이동합니다.
2. `+ New Input`을 클릭합니다.
3. `Azure Table Storage`를 선택합니다.
4. `Microsoft.Azure.WebJobs.Extensions.Storage`가 아직 설치되지 않았으면 설치합니다.
5. `Table parameter name`을 "tableStorage"로, `Table name`을 "myCustomResources"로 업데이트합니다.
6. 업데이트된 입력된 매개 변수를 저장합니다.

![사용자 지정 공급 기업 개요](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>RESTful HTTP 메서드 업데이트

이 섹션에서는 사용자 지정 공급 기업 RESTful 요청 메서드를 포함하도록 Azure Function을 설정하는 빠른 단계를 수행합니다.

1. HttpTrigger의 `Integrate` 탭으로 이동합니다.
2. `Selected HTTP methods`를 GET, POST, DELETE, PUT으로 업데이트합니다.

![사용자 지정 공급 기업 개요](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="modifying-the-csproj"></a>csproj 수정

> [!NOTE]
> csproj가 디렉터리에서 누락된 경우, 수동으로 추가할 수 있습니다. 또는 `Microsoft.Azure.WebJobs.Extensions.Storage` 확장이 함수에 설치되면 나타납니다.

다음으로, 유용한 NuGet 라이브러리를 포함하도록 csproj 파일을 업데이트하겠습니다. 그러면 사용자 지정 공급 기업에서 들어오는 요청의 구문 분석이 쉬워집니다. [포털에서 확장 추가](../azure-functions/install-update-binding-extensions-manual.md)의 단계에 따라 다음 패키지 참조를 포함하도록 csproj를 업데이트합니다.

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

샘플 csproj 파일:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Function이 Azure 사용자 지정 공급 기업 `endpoint`로 작동하도록 설정합니다. 다음 문서로 이동하면 RESTful 사용자 지정 공급 기업 `endpoint`를 작성하는 방법을 알아볼 수 있습니다.

- [자습서: RESTful 사용자 지정 공급 기업 엔드포인트 작성](./tutorial-custom-providers-function-authoring.md)
