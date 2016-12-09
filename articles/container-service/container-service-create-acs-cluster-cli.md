---
title: "CLI를 사용하여 Azure Container Service 클러스터 배포 | Microsoft Docs"
description: "Azure CLI 2.0 미리 보기를 사용하여 Azure Container Service 클러스터 배포"
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 855f0fe77bd55f6ec0dacad4bc28603ac1c6979c
ms.openlocfilehash: c4a513686433e802f27f78de60e8b7fca21b4634


---
# <a name="using-the-azure-cli-20-preview-to-create-an-azure-container-service-cluster"></a>Azure CLI 2.0 미리 보기를 사용하여 Azure Container Service 클러스터 만들기

Azure Container Service 클러스터를 만들려면 다음이 필요합니다.
* Azure 계정([무료 평가판 받기](https://azure.microsoft.com/pricing/free-trial/))
* [Azure CLI v. 2.0(미리 보기)](https://github.com/Azure/azure-cli#installation)가 설치됨
* Azure 계정에 로그인됨(아래 참조)

## <a name="log-in-to-your-account"></a>계정에 로그인
```azurecli
az login 
```
이 [링크](https://login.microsoftonline.com/common/oauth2/deviceauth)로 이동하여 CLI에 제공된 장치 코드로 인증을 받아야 합니다.

![명령 입력](media/container-service-create-acs-cluster-cli/login.png)

![만들기](media/container-service-create-acs-cluster-cli/login-browser.png)


## <a name="create-a-resource-group"></a>리소스 그룹 만들기
```azurecli
az resource group create -n acsrg1 -l "westus"
```

![이미지 리소스 그룹 만들기](media/container-service-create-acs-cluster-cli/rg-create.png)

## <a name="list-of-available-azure-container-service-cli-commands"></a>사용 가능한 Azure Container Service CLI 명령 목록

```azurecli
az acs -h
```

![ACS 명령 사용법](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

## <a name="create-an-azure-container-service-cluster"></a>Azure Container Service 클러스터 만들기

*CLI의 ACS create 사용법*

```azurecli
az acs create -h
```
컨테이너 서비스의 이름, 이전 단계에서 만든 리소스 그룹 및 고유한 DNS 이름은 필수입니다. 해당 스위치를 사용하여 덮어쓰지 않는 한, 다른 입력은 기본값으로 설정됩니다(도움말을 포함하는 다음 화면 스냅숏 참조).
![이미지 ACS create 도움말](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

*기본값을 사용하여 ACS create를 빠르게 진행합니다. SSH 키가 없으면 두 번째 명령을 사용합니다. 이 두 번째 create 명령과 --generate-ssh-keys 스위치를 사용하면 하나 만들어집니다.*

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

*dns-prefix(-d 스위치)가 고유한지 확인하세요. 오류가 발생하는 경우 고유 문자열을 사용해서 다시 시도하세요.*

앞의 명령을 입력한 후 클러스터가 만들어질 때까지 10분 정도 기다립니다.

![이미지 ACS create](media/container-service-create-acs-cluster-cli/cluster-create.png)

## <a name="list-acs-clusters"></a>ACS 클러스터 나열 

### <a name="under-a-subscription"></a>구독에서

```azurecli
az acs list --output table
```

### <a name="in-a-specific-resource-group"></a>특정 리소스 그룹에서

```azurecli
az acs list -g acsrg1 --output table
```

![이미지 ACS list](media/container-service-create-acs-cluster-cli/acs-list.png)


## <a name="display-details-of-a-container-service-cluster"></a>컨테이너 서비스 클러스터 세부 정보 표시

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![이미지 ACS list](media/container-service-create-acs-cluster-cli/acs-show.png)


## <a name="scale-the-acs-cluster"></a>ACS 클러스터 크기 조정
*확장 및 축소 모두 허용됩니다. 매개 변수 new-agent-count는 ACS 클러스터의 새 에이전트 수입니다.*

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![이미지 ACS scale](media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>컨테이너 서비스 클러스터 삭제
```
az acs delete -g acsrg1 -n acs-cluster 
```
*이 delete 명령을 실행해도 컨테이너 서비스를 만드는 동안 생성된 모든 리소스(네트워크 및 저장소)가 삭제되는 것은 아닙니다. 모든 리소스를 삭제하려면 리소스 그룹당 단일 ACS 클러스터를 만든 후 acs 클러스터가 더 이상 필요하지 않을 때 리소스 그룹이 저절로 삭제되도록 하여 모든 관련 리소스가 삭제되고 요금이 부과되지 않도록 하는 것이 좋습니다.*



<!--HONumber=Nov16_HO3-->


