---
title: Azure Service Fabric Mesh 애플리케이션 비밀 관리 | Microsoft Docs
description: 애플리케이션 비밀을 안전하게 생성하고 배포할 수 있도록 Service Fabric Mesh 애플리케이션을 관리합니다.
services: service-fabric-mesh
keywords: secrets
author: aljo-microsoft
ms.author: aljo
ms.date: 4/2/2019
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chackdan
ms.openlocfilehash: c2548ea3cf892ebe1a56cbb0909bfa5d5e805acf
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503305"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Service Fabric Mesh 애플리케이션 비밀 관리
Service Fabric Mesh에서는 비밀을 Azure 리소스로 지원합니다. Service Fabric Mesh 비밀은 스토리지 연결 문자열, 암호, 안전하게 저장하고 전송되어야 하는 기타 값 등 중요한 텍스트 정보일 수 있습니다. 이 문서에서는 Service Fabric 보안 스토리지 서비스를 사용하여 비밀을 배포하고 유지 관리하는 방법을 보여줍니다.

Mesh 애플리케이션 비밀은 다음으로 이루어져 있습니다.
* **비밀** 리소스는 텍스트 비밀을 저장하는 컨테이너입니다. **비밀** 리소스 내에 포함된 비밀은 안전하게 저장되고 전송됩니다.
* 하나 이상의 **비밀/값** 리소스는 **비밀** 리소스 컨테이너에 저장됩니다. 각 **비밀/값** 리소스는 버전 번호로 구분됩니다. **비밀/값** 리소스의 버전을 수정할 수 없고 새 버전을 추가할 수는 있습니다.

비밀을 관리하는 작업은 다음 단계로 구성됩니다.
1. 메시를 선언 **비밀** SecretsStoreRef contentType 정의와 inlinedValue 종류를 사용 하 여 Azure 리소스 모델 YAML 또는 JSON 파일에는 리소스입니다.
2. 메시를 선언 **비밀/값** 에 저장 되는 Azure 리소스 모델 YAML 또는 JSON 파일에 리소스를 **비밀** (1 단계)에서 리소스입니다.
3. Mesh 비밀 값을 참조하도록 Mesh 애플리케이션을 수정합니다.
4. 비밀 값을 사용하도록 Mesh 애플리케이션을 배포하거나 롤링 업그레이드합니다.
5. 보안 스토리지 서비스 수명 주기 관리에 대해 Azure "az" CLI 명령을 사용합니다.

## <a name="declare-a-mesh-secrets-resource"></a>Mesh 비밀 리소스를 선언합니다.
메시 비밀 리소스는 Azure 리소스 모델 JSON 또는 YAML 파일 inlinedValue 종류 정의 사용 하 여 선언 됩니다. Mesh 비밀 리소스는 보안 스토리지 서비스 제공 비밀을 지원합니다. 
>
JSON 파일에서 Mesh 비밀 리소스를 선언하는 방법의 예제는 다음과 같습니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    }
  ]
}
```
YAML 파일에서 Mesh 비밀 리소스를 선언하는 방법의 예제는 다음과 같습니다.
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="declare-mesh-secretsvalues-resources"></a>Mesh 비밀/값 리소스 선언
Mesh 비밀/값 리소스에는 이전 단계에서 정의된 Mesh 비밀 리소스에 대한 종속성이 있습니다.

"resources" 섹션의 "value:"와 "name:" 필드 간의 관계와 관련하여 콜론으로 구분된 "name:" 문자열의 두 번째 부분은 비밀에 사용된 버전 번호입니다. 콜론 앞의 이름은 종속성이 있는 Mesh 비밀 값과 일치해야 합니다. 예를 들어 ```name: mysecret:1.0``` 요소의 경우 버전 번호는 1.0이고 ```mysecret```이라는 이름은 이전에 정의된 ```"value": "mysecret"```과 일치해야 합니다.

>
JSON 파일에서 Mesh 비밀/값 리소스를 선언하는 방법의 예제는 다음과 같습니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx/2019.02.28",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/sfbpHttpsCertificate.pfx"
      ],
      "properties": {
        "value": "[parameters('sfbpHttpsCertificate')]"
      }
    }
  ],
}
```
YAML 파일에서 Mesh 비밀/값 리소스를 선언하는 방법의 예제는 다음과 같습니다.
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          Secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
            - name: mysecret:1.0
            description: My Mesh Application Secret Value
            secret_type: value
            content_type: text/plain
            value: "P@ssw0rd#1234"
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Mesh 비밀 값을 참조하도록 Mesh 애플리케이션을 수정합니다.
보안 스토리지 서비스 비밀 값을 사용하려면 Service Fabric Mesh 애플리케이션은 다음과 같은 두 문자열에 주의해야 합니다.
1. Microsoft.ServiceFabricMesh/Secrets.name에는 파일의 이름이 포함되고 일반 텍스트인 비밀 값이 포함됩니다.
2. Windows 또는 Linux 환경 변수 "Fabric_SettingPath"에는 액세스 가능한 보안 스토리지 서비스 비밀 값을 포함하는 파일에 대한 디렉터리 경로가 포함됩니다. 이 Windows 호스트 Mesh 애플리케이션의 경우 "C:\Settings"이고, Linux 호스트 Mesh 애플리케이션의 경우 "/var/settings"입니다.

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>비밀 값을 사용하도록 Mesh 애플리케이션에 대한 롤링 업그레이드를 배포하거나 사용합니다.
비밀 및/또는 버전이 있는 비밀/값을 만드는 작업은 리소스 모델 선언 배포로 제한됩니다. 이러한 리소스를 만드는 유일한 방법은 다음과 같이 **az mesh deployment** 명령을 사용하여 리소스 모델 JSON 또는 YAML 파일을 전달하는 것입니다.

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>보안 스토리지 서비스 수명 주기 관리에 대한 Azure CLI 명령

### <a name="create-a-new-secrets-resource"></a>새 비밀 리소스 만들기
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
**template-file** 또는 **template-uri** 중 하나를 전달합니다(둘 다는 안 됨).

예를 들면 다음과 같습니다.
- az mesh deployment create --c:\MyMeshTemplates\SecretTemplate1.txt
- az mesh deployment create --https:\//www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>비밀 표시
비밀(값은 안 됨)의 설명을 반환합니다.
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>비밀 삭제

- Mesh 애플리케이션에서 참조하는 동안 비밀을 삭제할 수 없습니다.
- 비밀 리소스를 삭제하면 모든 비밀/리소스 버전을 삭제합니다.
  ```azurecli-interactive
  az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
  ```

### <a name="list-secrets-in-subscription"></a>구독에서 비밀 나열
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>리소스 그룹에서 비밀 나열
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>비밀의 모든 버전 나열
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>비밀 버전 값 표시
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>비밀 버전 값 삭제
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>다음 단계 
Service Fabric Mesh에 대한 자세한 내용은 다음 개요를 참조하세요.
- [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)
