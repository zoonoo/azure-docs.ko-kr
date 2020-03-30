---
title: Azure 가상 네트워크에서 기본 로드 밸런서를 사용 하 여 IPv6 듀얼 스택 응용 프로그램 배포 - 리소스 관리자 템플릿 (미리 보기)
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure 리소스 관리자 VM 템플릿을 사용하여 Azure 가상 네트워크에서 IPv6 이중 스택 응용 프로그램을 배포하는 방법을 보여 주며 있습니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/26/2019
ms.author: kumud
ms.openlocfilehash: b397c874045a89f5992aeadacfbbd4434a486977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70012829"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template-preview"></a>Azure에서 기본 로드 밸런서를 사용 하 여 IPv6 듀얼 스택 응용 프로그램을 배포 - 템플릿 (미리 보기)

이 문서에서는 적용되는 Azure 리소스 관리자 VM 템플릿의 일부와 함께 IPv6 구성 작업 목록을 제공합니다. 이 문서에서 설명한 템플릿을 사용하여 IPv4 및 IPv6 서브넷이 있는 듀얼 스택 가상 네트워크, 듀얼(IPv4 + IPv6) 프런트 엔드 구성이 있는 기본 로드 밸러저, VM을 포함하는 기본 로드 밸러서가 포함된 기본 로드 밸랜서가 포함된 이중 스택(IPv4 + IPv6) 응용 프로그램을 배포합니다. 이중 IP 구성, 네트워크 보안 그룹 및 공용 IP가 있는 NIC입니다.

표준 로드 밸런서를 사용하여 이중 스택(IPV4 + IPv6) 응용 프로그램을 배포하려면 [표준 로드 밸런서 - 템플릿을 사용하여 IPv6 이중 스택 응용 프로그램 배포를](ipv6-configure-standard-load-balancer-template-json.md)참조하십시오.

## <a name="required-configurations"></a>필수 구성

템플릿의 템플릿 섹션을 검색하여 템플릿이 발생할 위치를 확인합니다.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>가상 네트워크를 위한 IPv6 주소공간

추가할 템플릿 섹션:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6 가상 네트워크 주소 공간 내의 IPv6 서브넷

추가할 템플릿 섹션:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>NIC용 IPv6 구성

추가할 템플릿 섹션:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>IPv6 네트워크 보안 그룹(NSG) 규칙

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>조건부 구성

네트워크 가상 어플라이언스를 사용하는 경우 경로 테이블에 IPv6 경로를 추가합니다. 그렇지 않으면 이 구성은 선택 사항입니다.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>선택적 구성

### <a name="ipv6-internet-access-for-the-virtual-network"></a>가상 네트워크에 대한 IPv6 인터넷 액세스

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>IPv6 공용 IP 주소

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>부하 밸러블러용 IPv6 프런트 엔드

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>로드 밸러블러용 IPv6 백 엔드 주소 풀

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>들어오는 포트와 나가는 포트를 연결하는 IPv6 로드 밸러서 규칙

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>샘플 VM 템플릿 JSON
Azure 리소스 관리자 템플릿을 사용하여 Azure 가상 네트워크에서 기본 로드 밸런서가 있는 IPv6 이중 스택 응용 프로그램을 배포하려면 [샘플 템플릿을 보려면 여기를](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/)봅니다.

## <a name="next-steps"></a>다음 단계

공용 IP 주소, [네트워크 대역폭](https://azure.microsoft.com/pricing/details/bandwidth/)또는 [로드 밸런서에](https://azure.microsoft.com/pricing/details/load-balancer/)대한 가격 책정에 대한 세부 정보를 찾을 수 [있습니다.](https://azure.microsoft.com/pricing/details/ip-addresses/)
