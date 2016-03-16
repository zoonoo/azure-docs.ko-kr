<properties 
   pageTitle="가상 네트워크 크로스-프레미스 연결에 대한 VPN 게이트웨이 정보 | Microsoft Azure"
   description="하이브리드 구성을 위한 크로스-프레미스 연결에 사용할 수 있는 VPN 게이트웨이에 대해 알아봅니다. 이 문서에서는 게이트웨이 SKU(기본, 표준 및 고성능), VPN 게이트웨이와 Express 경로의 공존 구성, 게이트웨이 라우팅 유형(정적, 동적, 정책 기반, 경로 기반), 클래식 및 리소스 관리자 배포 모델 모두에 대한 가상 네트워크 연결을 위한 게이트웨이 요구 사항 등을 알아봅니다."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/19/2016"
   ms.author="cherylmc" />

# VPN 게이트웨이 정보

VPN 게이트웨이는 가상 네트워크와 온-프레미스 위치 간에 네트워크 트래픽을 보내는 데 사용됩니다. 또한 Azure 내의 여러 가상 네트워크 간(VNet 간)에 트래픽을 보내는 데에도 사용됩니다. 게이트웨이를 만들 때 고려해야 할 몇 가지 요소가 있습니다.

- 사용할 게이트웨이 SKU
- 연결에 적합한 VPN 유형
- VPN 장치(연결에 필요한 경우)

**배포 모델 정보**

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]
 

## 게이트웨이 SKU

[AZURE.INCLUDE [vpn-gateway-table-sku](../../includes/vpn-gateway-table-sku-include.md)]

## VPN 게이트웨이 유형

두 가지 VPN 유형이 있습니다.

- **정책 기반:** 클래식 배포 모델에서는 정책 기반 게이트웨이를 *정적 게이트웨이*라고 합니다. 이름이 변경되었지만 정적 게이트웨이의 기능은 변경되지 않았습니다. 이 유형의 게이트웨이는 정책 기반 VPN을 지원합니다. 정책 기반 VPN은 온-프레미스 네트워크와 Azure VNet 간의 주소 접두사의 조합에 따라 트래픽 선택기를 사용하여 IPsec 터널을 통해 패킷을 전달합니다. 트래픽 선택기 또는 정책은 일반적으로 VPN 구성에서 액세스 목록으로 정의됩니다.
 
- **경로 기반:** 클래식 배포 모델에서는 경로 기반 게이트웨이를 *동적 게이트웨이*라고 합니다. 이름이 변경되었지만 동적 게이트웨이의 기능은 변경되지 않았습니다. 경로 기반 게이트웨이는 경로 기반 VPN을 구현합니다. 경로 기반 VPN은 IP 전달 또는 라우팅 테이블에서 “경로"를 사용하여 패킷을 해당 VPN 터널 인터페이스에 전달합니다. 그런 다음 터널 인터페이스는 터널로 들어오는 터널에서 나가는 패킷을 암호화하거나 암호 해독합니다. 경로 기반 VPN에 대한 정책 또는 트래픽 선택기는 임의 또는 와일드카드로 구성됩니다.

지점 및 사이트 간, VNet 간 등의 일부 연결은 특정 VPN 유형에서만 작동합니다. 만들려는 연결 시나리오에 해당하는 게이트웨이 요구 사항이 이 문서에 나와 있습니다.

VPN 장치에는 구성 제한 사항도 있습니다. VPN 게이트웨이 만들 때 연결에 필요한 VPN 유형을 선택합니다. 이때 사용하려는 VPN 장치에서도 해당 라우팅 유형을 지원하는지 확인해야 합니다. 자세한 내용은 [VPN 장치 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요.

예를 들어 사이트 간 연결을 지점 및 사이트 간 연결과 동시에 사용하려면 경로 기반 VPN 게이트웨이를 구성해야 합니다. 사이트 간 연결은 정책 기반 게이트웨이에서 작동하지만 지점 및 사이트 간 연결에는 경로 기반 게이트웨이 유형이 필요합니다. 두 연결에서 동일한 게이트웨이를 사용하므로 두 구성을 모두 지원하는 게이트웨이 유형을 선택해야 합니다. 또한 사용하는 VPN 장치에서도 경로 기반 구성을 지원해야 합니다.


## 게이트웨이 요구 사항


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## 다음 단계

구성에 대한 VPN 장치를 선택합니다. [VPN 장치 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요.





 

<!---HONumber=AcomDC_0302_2016-->