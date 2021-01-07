---
title: Azure CLI를 사용 하 여 피어 링 서비스 미리 보기 연결 등록
description: Azure CLI를 사용 하 여 피어 링 서비스 연결을 등록 하는 방법에 대해 알아봅니다.
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: e7b696ba052b2aca9e14628327c07275845607ad
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540589"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Azure CLI를 사용 하 여 피어 링 서비스 연결을 등록 합니다.

Azure Peering Service는 Microsoft 365, Dynamics 365, SaaS(Software as a Service) 서비스, Azure 또는 공용 인터넷을 통해 액세스할 수 있는 Microsoft 서비스와 같은 Microsoft 클라우드 서비스에 대한 고객의 연결을 향상시키는 네트워킹 서비스입니다. 이 문서에서는 Azure CLI를 사용 하 여 피어 링 서비스 연결을 등록 하는 방법에 대해 알아봅니다.

- 이 문서에는 Azure CLI 버전 2.0.28 이상이 필요 합니다. [az version](/cli/azure/reference-index#az_version)을 실행하여 설치된 버전과 종속 라이브러리를 찾습니다. 최신 버전으로 업그레이드하려면 [az upgrade](/cli/azure/reference-index#az_upgrade)를 실행합니다.

## <a name="prerequisites"></a>필수 구성 요소 

다음이 있어야 합니다.

### <a name="azure-account"></a>Azure 계정

유효한 활성 Microsoft Azure 계정이 있어야 합니다. 이 계정은 Peering Service 연결을 설정하는 데 필요합니다. Peering Service는 Azure 구독 내에 있는 리소스입니다.

### <a name="connectivity-provider"></a>연결 공급자

인터넷 서비스 공급자 또는 인터넷 교환 파트너와 협력하여 Peering Service를 얻어 네트워크를 Microsoft 네트워크와 연결할 수 있습니다.

연결 공급자가 Microsoft와 파트너 관계를 맺고 있는지 확인합니다.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- 이 문서에는 Azure CLI 버전 2.0.28 이상이 필요 합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

### <a name="1-select-your-subscription"></a>1. 구독을 선택 합니다.

피어 링 서비스 연결을 등록 하려는 구독을 선택 합니다.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

리소스 그룹이 아직 없는 경우 피어 링 서비스 연결을 등록 하기 전에 먼저 리소스 그룹을 만들어야 합니다. 다음 명령을 실행하여 리소스 그룹을 만들 수 있습니다.

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2. 리소스 공급자 및 기능 플래그를 사용 하 여 구독을 등록 합니다.

Azure CLI를 사용 하 여 피어 링 서비스 연결을 등록 하는 단계를 진행 하기 전에 Azure CLI를 사용 하 여 리소스 공급자 및 기능 플래그를 사용 하 여 구독을 등록 합니다. Azure CLI 명령은 다음과 같이 지정 됩니다.

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3. 피어 링 서비스 연결을 등록 합니다.

Azure CLI를 통해 다음 명령 집합을 사용 하 여 피어 링 서비스 연결을 등록 합니다. 이 예제에서는 myPeeringService 라는 피어 링 서비스 연결을 등록 합니다.

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4. 접두사를 등록 합니다.

Azure CLI를 통해 다음 명령을 실행 하 여 연결 공급자가 제공 하는 접두사를 등록 합니다. 이 예제에서는 myPrefix라는 접두사를 등록합니다.

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>다음 단계

- 피어 링 서비스 연결에 대해 자세히 알아보려면 [피어 링 서비스 연결](connection.md)을 참조 하세요.
- Peering Service 연결 원격 분석에 대해 알아보려면 [Peering Service 연결 원격 분석](connection-telemetry.md)을 참조하세요.
- 원격 분석을 측정하려면 [연결 원격 분석 측정](measure-connection-telemetry.md)을 참조하세요.
- Azure PowerShell을 사용하여 연결을 등록하려면 [Peering Service 연결 등록 - Azure PowerShell](powershell.md)을 참조하세요.
- Azure Portal를 사용 하 여 연결을 등록 하려면 [피어 링 서비스 연결 등록-Azure Portal](azure-portal.md)을 참조 하세요.
