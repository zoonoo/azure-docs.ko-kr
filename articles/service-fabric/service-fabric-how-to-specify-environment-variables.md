---
title: 서비스에 대 한 환경 변수 지정
description: Service Fabric에서 애플리케이션에 대한 환경 변수를 사용하는 방법을 보여 줍니다.
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: f4c4f2a1c140e3d0f181c4fd55482056f9f91b62
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614318"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Service Fabric에서 서비스에 대한 환경 변수를 지정하는 방법

이 문서에서는 Service Fabric에서 서비스 또는 컨테이너에 대한 환경 변수를 지정하는 방법을 보여 줍니다.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>서비스에 대한 환경 변수를 지정하기 위한 절차

이 예제에서는 컨테이너에 대한 환경 변수를 설정합니다. 이 문서에서는 애플리케이션 및 서비스 매니페스트가 이미 있다고 가정합니다.

1. ServiceManifest.xml 파일을 엽니다.
2. `CodePackage` 요소에서 각 환경 변수에 대해 새 `EnvironmentVariables` 요소 및 `EnvironmentVariable` 요소를 추가합니다.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   환경 변수는 애플리케이션 매니페스트에서 재정의할 수 있습니다.

3. 애플리케이션 매니페스트에서 환경 변수를 재정의하려면 `EnvironmentOverrides` 요소를 사용합니다.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Docker Compose를 사용 하 여 동적으로 환경 변수 지정

Service Fabric는 [배포에 Docker Compose를 사용](service-fabric-docker-compose.md#supported-compose-directives)하는 기능을 지원 합니다. 작성 파일은 셸에서 환경 변수를 원본으로 사용할 수 있습니다. 이 동작은 원하는 환경 값을 동적으로 대체 하는 데 사용할 수 있습니다.

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>다음 단계
이 문서에서 설명하는 핵심 개념 중 일부에 대한 자세한 내용은 [여러 환경에 대한 애플리케이션 관리](service-fabric-manage-multiple-environment-app-configuration.md) 문서를 참조하세요.

Visual Studio에서 사용할 수 있는 다른 앱 관리 기능에 대한 정보는 [Visual Studio에서 Service Fabric 애플리케이션 관리](service-fabric-manage-application-in-visual-studio.md)를 참조하세요.
