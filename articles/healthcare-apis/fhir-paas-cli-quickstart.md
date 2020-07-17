---
title: '빠른 시작: Azure CLI를 사용하여 Azure API for FHIR 배포'
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Azure에서 Azure API for FHIR을 배포하는 방법을 알아봅니다.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: mihansen
ms.openlocfilehash: 3eca478c3417810bfa227f55427294517247e083
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "84820044"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure API for FHIR 배포

이 빠른 시작에서는 Azure CLI를 사용하여 Azure에서 Azure API for FHIR을 배포하는 방법을 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-healthcareapis-extension"></a>HealthcareAPI 확장 추가

```azurecli-interactive
az extension add --name healthcareapis
```

HealthcareAPI에 대한 명령 목록을 가져옵니다.

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>Azure Resource 그룹 만들기

Azure API for FHIR을 포함할 리소스 그룹의 이름을 선택하고 만듭니다.

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>Azure API for FHIR 배포

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>Fetch FHIR API 기능 문

다음을 사용하여 FHIR API에서 기능 문을 가져옵니다.

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 다음 단계에 따라 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작 가이드에서는 Azure API for FHIR을 구독에 배포했습니다. Azure API for FHIR에서 추가 설정을 설정하려면 추가 설정 방법 가이드로 이동합니다.

>[!div class="nextstepaction"]
>[Azure API for FHIR의 추가 설정](azure-api-for-fhir-additional-settings.md)
