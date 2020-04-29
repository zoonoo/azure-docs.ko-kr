---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
description: Azure CLI에서 Azure container instance 리소스를 만드는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e3542b976921aa45794d62cad9517984c8348ce3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80875132"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Azure CLI에서 Azure Container Instance 리소스 만들기

아래 YAML은 Azure Container Instance 리소스를 정의 합니다. 콘텐츠를 복사 하 여 라는 `my-aci.yaml` 새 파일에 붙여넣고 주석 처리 된 값을 사용자 고유의 값으로 바꿉니다. 유효한 YAML의 [템플릿 형식을][template-format] 참조 하십시오. 사용 가능한 이미지 이름 및 해당 리포지토리의 [컨테이너 리포지토리 및 이미지][repositories-and-images] 를 참조 하세요. 컨테이너 인스턴스에 대 한 YAML 참조에 대 한 자세한 내용은 [Yaml 참조: Azure Container Instances][aci-yaml-ref]를 참조 하세요.

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials: # This is required when pulling a non-public image
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> 모든 위치에서 CPU 및 메모리 가용성이 동일 하지는 않습니다. 위치 및 OS에 따라 컨테이너에 대해 사용 가능한 리소스 목록을 보려면 [위치 및 리소스][location-to-resource] 표를 참조 하세요.

[`az container create`][azure-container-create] 명령에 대해 만든 yaml 파일을 사용 합니다. Azure CLI에서 `az container create` 명령을 실행 하 여를 `<resource-group>` 사용자 고유의으로 바꿉니다. 또한 YAML 배포 내에서 값을 보호 하기 위해 [보안 값][secure-values]을 참조 합니다.

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

명령의 출력은 올바른 경우에 `Running...` 는 출력을 새로 만든 ACI 리소스를 나타내는 JSON 문자열로 변경 합니다. 컨테이너 이미지는 잠시 동안 사용 하지 못할 수도 있지만 이제 리소스가 배포 됩니다.

> [!TIP]
> YAML은 위치와 일치 하도록 적절히 조정 해야 하므로 공개 미리 보기 Azure 인지 서비스 제공의 위치에 주의 하세요.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
