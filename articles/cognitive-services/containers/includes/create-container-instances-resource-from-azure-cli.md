---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
description: Azure CLI에서 Azure 컨테이너 인스턴스 리소스를 만드는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e3542b976921aa45794d62cad9517984c8348ce3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875132"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Azure CLI에서 Azure 컨테이너 인스턴스 리소스 만들기

아래 YAML은 Azure 컨테이너 인스턴스 리소스를 정의합니다. 주석 처리된 값을 새 파일로 복사하여 `my-aci.yaml` 붙여넣습니다. 유효한 YAML에 대한 [템플릿 형식을][template-format] 참조하십시오. 사용 가능한 이미지 이름과 해당 리포지토리에 대한 [컨테이너 리포지토리 및 이미지를][repositories-and-images] 참조하십시오. 컨테이너 인스턴스에 대한 YAML 참조에 대한 자세한 내용은 [YAML 참조: Azure 컨테이너 인스턴스][aci-yaml-ref]를 참조하십시오.

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
> 모든 위치의 CPU 및 메모리 가용성이 동일하지는 않습니다. 위치 및 OS별 컨테이너에 사용할 수 있는 리소스 목록은 위치 [및 리소스][location-to-resource] 테이블을 참조하십시오.

명령에 대해 만든 YAML 파일에 의존합니다. [`az container create`][azure-container-create] Azure CLI에서 명령을 `az container create` 사용자 로 `<resource-group>` 대체하는 명령을 실행합니다. 또한 YAML 배포 내에서 값을 보호하려면 [보안 값을][secure-values]참조합니다.

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

명령의 출력은 `Running...` 유효한 경우, 언젠가 출력이 새로 생성된 ACI 리소스를 나타내는 JSON 문자열로 변경됩니다. 컨테이너 이미지는 잠시 동안 사용할 수 없지만 리소스가 배포됩니다.

> [!TIP]
> YAML은 위치에 맞게 조정해야 하므로 공개 미리 보기 Azure Cognitive Service 제품의 위치에 주의를 기울이세요.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
