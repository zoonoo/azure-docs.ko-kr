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
ms.openlocfilehash: ef573817927cf732da3426d802f8f26e2e9cd4ec
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91399026"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Azure CLI를 사용 하 여 피어 링 서비스 연결을 등록 합니다.

Azure 피어 링 서비스는 Microsoft 365, Dynamics 365, SaaS (software as a Service) 서비스, Azure 또는 공용 인터넷을 통해 액세스할 수 있는 Microsoft 서비스와 같은 Microsoft 클라우드 서비스에 대 한 고객의 연결을 향상 시키는 네트워킹 서비스입니다. 이 문서에서는 Azure CLI를 사용 하 여 피어 링 서비스 연결을 등록 하는 방법에 대해 알아봅니다.

Azure 구독이 없는 경우 지금 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치 하 고 사용 하도록 선택 하는 경우이 문서에 Azure CLI 버전 2.0.28 이상이 필요 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소 

다음이 있어야 합니다.

### <a name="azure-account"></a>Azure 계정

유효한 활성 Microsoft Azure 계정이 있어야 합니다. 이 계정은 Peering Service 연결을 설정하는 데 필요합니다. Peering Service는 Azure 구독 내에 있는 리소스입니다.

### <a name="connectivity-provider"></a>연결 공급자

인터넷 서비스 공급자 또는 인터넷 교환 파트너와 협력하여 Peering Service를 얻어 네트워크를 Microsoft 네트워크와 연결할 수 있습니다.

연결 공급자가 Microsoft와 파트너 관계를 맺고 있는지 확인합니다.

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Azure 계정에 로그인 하 고 구독을 선택 합니다.

구성을 시작하려면, Azure 계정에 로그인합니다. Cloud Shell **사용해 보기** 옵션을 사용 하면 자동으로 로그인 됩니다. 다음 예제를 사용 하 여 연결을 지원 합니다.

```azurecli-interactive
az login
```

계정에 대한 구독을 확인합니다.

```azurecli-interactive
az account list
```

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
