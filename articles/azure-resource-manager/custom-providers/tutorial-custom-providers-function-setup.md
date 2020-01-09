---
title: Azure Functions 설정
description: 이 자습서에서는 Azure 함수 앱을 만들어서 Azure 사용자 지정 공급자에서 작동하도록 설정하는 방법을 살펴봅니다.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: b6984bfdf7ec078d02999bccf77f3592cfbcab27
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75648680"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Azure 사용자 지정 공급자를 위한 Azure Functions 설정

사용자 지정 공급자는 Azure와 엔드포인트 사이의 계약입니다. 사용자 지정 공급자를 사용하면 Azure에서 워크플로를 변경할 수 있습니다. 이 자습서에서는 사용자 지정 공급자 엔드포인트로 작동하도록 Azure 함수 앱을 설정하는 방법을 보여줍니다.

## <a name="create-the-azure-function-app"></a>Azure 함수 앱 만들기

> [!NOTE]
> 이 자습서에서는 Azure 함수 앱을 사용하는 간단한 서비스 엔드포인트를 만듭니다. 그러나 사용자 지정 공급자는 공개적으로 액세스할 수 있는 엔드포인트를 사용할 수 있습니다. 대안에는 Azure Logic Apps, Azure API Management 및 Azure App Service의 Web Apps 기능이 포함됩니다.

이 자습서를 시작하려면 [Azure Portal에서 첫 번째 Azure 함수 앱 만들기](../../azure-functions/functions-create-first-azure-function.md) 자습서를 먼저 수행해야 합니다. 이 자습서에서는 Azure Portal에서 수정할 수 있는 .NET Core 웹후크 함수를 만듭니다. 또한 현재 자습서의 기반이 됩니다.

## <a name="install-azure-table-storage-bindings"></a>Azure Table Storage 바인딩 설치

Azure Table Storage 바인딩을 설치하려면 다음을 수행합니다.

1. HttpTrigger의 **통합** 탭으로 이동합니다.
1. **+ 새 입력**을 선택합니다.
1. **Azure Table Storage**를 선택합니다.
1. 아직 설치되지 않은 경우 Microsoft.Azure.WebJobs.Extensions.Storage 확장을 설치합니다.
1. **테이블 매개 변수 이름** 상자에 **tableStorage**를 입력합니다.
1. **테이블 이름** 상자에 **myCustomResources**를 입력합니다.
1. **저장**을 선택하여 업데이트된 입력 매개 변수를 저장합니다.

![테이블 바인딩을 보여주는 사용자 지정 공급자 개요](./media/create-custom-provider/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>RESTful HTTP 메서드 업데이트

사용자 지정 공급자 RESTful 요청 메서드를 포함하도록 Azure 함수를 설정하려면 다음을 수행합니다.

1. HttpTrigger의 **통합** 탭으로 이동합니다.
1. **선택한 HTTP 메서드**에서 **GET**, **POST**, **DELETE** 및 **PUT**을 선택합니다.

![HTTP 메서드를 보여주는 사용자 지정 공급자 개요](./media/create-custom-provider/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Azure Resource Manager NuGet 패키지 추가

> [!NOTE]
> C# 프로젝트 파일이 프로젝트 디렉터리에 없으면 수동으로 추가할 수 있습니다. 또는 Microsoft.Azure.WebJobs.Extensions.Storage 확장이 함수 앱에 설치된 후에 표시됩니다.

다음으로, 유용한 NuGet 라이브러리를 포함하도록 C# 프로젝트 파일을 업데이트합니다. 이러한 라이브러리를 사용하면 사용자 지정 공급자로부터 들어오는 요청을 더 쉽게 구문 분석할 수 있습니다. [포털에서 확장을 추가하는](../../azure-functions/install-update-binding-extensions-manual.md) 단계에 따라 다음 패키지 참조를 포함하도록 C# 프로젝트를 업데이트합니다.

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

다음 XML 요소는 C# 프로젝트 파일의 예제입니다.

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

이 자습서에서는 Azure 사용자 지정 공급자 엔드포인트로 작동하도록 Azure 함수 앱을 설정합니다.

RESTful 사용자 지정 공급자 엔드포인트를 작성하는 방법을 알아보려면 [자습서: RESTful 사용자 지정 공급자 엔드포인트 작성](./tutorial-custom-providers-function-authoring.md)을 참조하세요.

