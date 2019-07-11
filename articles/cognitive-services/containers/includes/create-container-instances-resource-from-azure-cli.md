---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
description: Azure CLI에서 Azure 컨테이너 인스턴스 리소스를 만드는 방법에 알아봅니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 5e7a3d849f726ae4dbbd559d541464404e427775
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717116"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Azure CLI에서 Azure 컨테이너 인스턴스 리소스 만들기

다음 YAML Azure 컨테이너 인스턴스 리소스를 정의합니다. 복사한 내용을 붙여 새 파일을 명명 된 `my-aci.yaml` 고유한 설명이 포함 된 값을 바꿉니다. [템플릿 형식] 참조 [템플릿-포맷 한 후] 유효한 YAML에 대 한 합니다. 참조를 [컨테이너 리포지토리 및 이미지][repositories-and-images] 사용 가능한 이미지 이름 및 해당 리포지토리의 해당 합니다.

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials:
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
> 모든 위치에 동일한 CPU 및 메모리 가용성입니다. 참조 된 [위치 및 리소스][location-to-resource] 위치와 OS 별로 컨테이너에 대 한 사용 가능한 리소스를 나열 하는 테이블입니다.

용으로 만든 YAML 파일을 사용 하겠습니다 합니다 [ `az container create` ][azure-container-create] 명령입니다. Azure CLI에서 실행 합니다 `az container create` 명령을 대체는 `<resource-group>` 고유의 합니다. 또한는 YAML 내의 값을 보호 하기 위한 배포 참조할 [값을 보호][secure-values]합니다.

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

명령의 출력은 `Running...` 유효한 지, 잠시 후 출력으로 변경 새로 만든된 ACI 리소스를 나타내는 JSON 문자열입니다. 컨테이너 이미지 가능성이 이상 사용할 수 없습니다 잠시 중이지만 리소스가 배포 됩니다.

> [!TIP]
> 주의 하 여 공개 미리 보기 Azure Cognitive 서비스 제품의 위치는 YAML 위치와 일치 하도록 적절 하 게 조정 하는 데 필요한 됩니다.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values