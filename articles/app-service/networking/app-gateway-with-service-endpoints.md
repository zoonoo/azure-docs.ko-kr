---
title: 서비스 끝점과의 애플리케이션 게이트웨이 통합 - Azure 앱 서비스 | 마이크로 소프트 문서
description: 응용 프로그램 게이트웨이가 서비스 끝점으로 보안된 Azure 앱 서비스와 통합되는 방법을 설명합니다.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 5e32baa10e98f0f57a861f8cebfb7506ad615631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980063"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>서비스 엔드포인트와의 애플리케이션 게이트웨이 통합
Azure 응용 프로그램 게이트웨이와의 통합에 대해 약간 다른 구성을 필요로 하는 앱 서비스의 세 가지 변형이 있습니다. 다중 테넌트, ILB(내부 로드 밸러블러) 앱 서비스 환경(ASE) 및 외부 ASE라고도 하는 일반 앱 서비스가 변형됩니다. 이 문서에서는 앱 서비스(다중 테넌트)로 구성하는 방법을 살펴보고 ILB 및 외부 ASE에 대한 고려 사항에 대해 설명합니다.

## <a name="integration-with-app-service-multi-tenant"></a>앱 서비스와의 통합(다중 테넌트)
앱 서비스(다중 테넌트)에는 공용 인터넷이 엔드포인트를 향합니다. [서비스 끝점을](../../virtual-network/virtual-network-service-endpoints-overview.md) 사용하면 Azure 가상 네트워크 내의 특정 서브넷에서만 트래픽을 허용하고 다른 모든 것을 차단할 수 있습니다. 다음 시나리오에서는 이 기능을 사용하여 앱 서비스 인스턴스가 특정 응용 프로그램 게이트웨이 인스턴스에서만 트래픽을 수신할 수 있도록 합니다.

![앱 서비스와의 애플리케이션 게이트웨이 통합](./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png)

이 구성에는 앱 서비스 및 응용 프로그램 게이트웨이를 만드는 것 외에 두 부분이 있습니다. 첫 번째 부분은 응용 프로그램 게이트웨이가 배포되는 가상 네트워크의 서브넷에서 서비스 끝점을 사용하도록 설정하는 것입니다. 서비스 끝점은 앱 서비스를 향해 서브넷을 떠나는 모든 네트워크 트래픽이 특정 서브넷 ID로 태그가 지정되도록 합니다. 두 번째 부분은 특정 웹 앱의 액세스 제한을 설정하여 이 특정 서브넷 ID로 태그가 지정된 트래픽만 허용되도록 하는 것입니다. 기본 설정에 따라 다른 도구를 사용하여 구성할 수 있습니다.

## <a name="using-azure-portal"></a>Azure Portal 사용
Azure 포털에서는 네 단계를 수행하여 설정을 프로비전하고 구성합니다. 기존 리소스가 있는 경우 첫 번째 단계를 건너뛸 수 있습니다.
1. 앱 서비스 설명서의 빠른 시작 중 하나를 사용하여 앱 서비스 만들기(예: [.Net Core 빠른 시작)](../../app-service/app-service-web-get-started-dotnet.md)
2. [포털 빠른 시작을](../../application-gateway/quick-create-portal.md)사용하여 응용 프로그램 게이트웨이를 만들지만 백 엔드 대상 추가 섹션을 건너뜁니다.
3. [응용 프로그램 게이트웨이에서 앱 서비스를 백 엔드로](../../application-gateway/configure-web-app-portal.md)구성하지만 액세스 제한 섹션을 건너뜁니다.
4. 마지막으로 [서비스 끝점을 사용하여 액세스 제한을](../../app-service/app-service-ip-restrictions.md#service-endpoints)만듭니다.

이제 응용 프로그램 게이트웨이를 통해 앱 서비스에 액세스할 수 있지만 앱 서비스에 직접 액세스하려고 하면 웹 사이트가 중지되었음을 나타내는 403 HTTP 오류가 나타납니다.

![앱 서비스와의 애플리케이션 게이트웨이 통합](./media/app-gateway-with-service-endpoints/web-site-stopped.png)

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용
[리소스 관리자 배포 템플릿은][template-app-gateway-app-service-complete] 전체 시나리오를 프로비전합니다. 시나리오는 서비스 끝점및 액세스 제한으로 잠긴 앱 서비스 인스턴스로 구성되며 응용 프로그램 게이트웨이에서만 트래픽을 수신합니다. 템플릿에는 리소스 이름에 추가된 많은 스마트 기본값과 고유한 접두사가 포함되어 있습니다. 이를 재정의하려면 리포지토리를 복제하거나 템플릿을 다운로드하여 편집해야 합니다. 

템플릿을 적용하려면 템플릿 설명에 있는 Azure에 배포 단추를 사용하거나 적절한 PowerShell/CLI를 사용할 수 있습니다.

## <a name="using-azure-command-line-interface"></a>Azure 명령줄 인터페이스 사용
[Azure CLI 샘플은](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) 응용 프로그램 게이트웨이에서만 트래픽을 수신하도록 서비스 끝점 및 액세스 제한으로 잠긴 앱 서비스를 프로비전합니다. 기존 응용 프로그램 게이트웨이에서 기존 앱 서비스에 대한 트래픽을 격리해야 하는 경우 다음 명령으로 충분합니다.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

기본 구성에서 명령은 서브넷의 서비스 끝점 구성 설정과 앱 서비스의 액세스 제한을 모두 보장합니다.

## <a name="considerations-for-ilb-ase"></a>ILB ASE에 대한 고려 사항
ILB ASE는 인터넷에 노출되지 않으며 인스턴스와 응용 프로그램 게이트웨이 간의 트래픽은 이미 가상 네트워크에 격리되어 있습니다. 다음 [방법 가이드는](../environment/integrate-with-application-gateway.md) ILB ASE를 구성하고 Azure 포털을 사용하는 응용 프로그램 게이트웨이와 통합합니다. 

응용 프로그램 게이트웨이 서브넷의 트래픽만 ASE에 도달하도록 하려면 ASE의 모든 웹 앱에 영향을 주는 NSG(네트워크 보안 그룹)를 구성할 수 있습니다. NSG의 경우 서브넷 IP 범위와 선택적으로 포트(80/443)를 지정할 수 있습니다. ASE가 올바르게 작동하도록 [필요한 NSG 규칙을](../environment/network-info.md#network-security-groups) 재정의하지 않도록 합니다.

개별 웹 앱으로 트래픽을 격리하려면 서비스 끝점이 ASE에서 작동하지 않으므로 IP 기반 액세스 제한을 사용해야 합니다. IP 주소는 응용 프로그램 게이트웨이 인스턴스의 개인 IP여야 합니다.

## <a name="considerations-for-external-ase"></a>외부 ASE에 대한 고려 사항
외부 ASE에는 다중 테넌트 앱 서비스와 같은 공용 로드 밸런서가 있습니다. 서비스 끝점은 ASE에서 작동하지 않으며 응용 프로그램 게이트웨이 인스턴스의 공용 IP를 사용하여 IP 기반 액세스 제한을 사용해야 하는 이유입니다. Azure 포털을 사용하여 외부 ASE를 만들려면 이 [빠른 시작을](../environment/create-external-ase.md) 따를 수 있습니다.

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "전체 시나리오를 위한 Azure 리소스 관리자 템플릿"

## <a name="considerations-for-kuduscm-site"></a>쿠두/scm 사이트에 대한 고려 사항
kudu라고도 하는 scm 사이트는 모든 웹 앱에 대해 존재하는 관리자 사이트입니다. scm 사이트를 프록시로 되돌릴 수 없으며 개별 IP 주소 또는 특정 서브넷으로 프록시를 잠글 수도 있습니다.

기본 사이트와 동일한 액세스 제한을 사용하려는 경우 다음 명령을 사용하여 설정을 상속할 수 있습니다.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

scm 사이트에 대한 개별 액세스 제한을 설정하려면 아래와 같이 --scm-site 플래그를 사용하여 액세스 제한을 추가할 수 있습니다.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>다음 단계
앱 서비스 환경에 대한 자세한 내용은 [앱 서비스 환경 설명서를](https://docs.microsoft.com/azure/app-service/environment)참조하십시오.

웹 앱을 더욱 안전하게 보호하기 위해 응용 프로그램 게이트웨이의 웹 응용 프로그램 방화벽에 대한 정보는 [Azure 웹 응용 프로그램 방화벽 설명서에서](../../web-application-firewall/ag/ag-overview.md)찾을 수 있습니다.