---
title: App Service Environment 네트워킹
description: App Service Environment 네트워킹 세부 정보
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0a1221a8de10fd18768a1a0f0ac08277dc2901d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735036"
---
# <a name="app-service-environment-networking"></a>App Service Environment 네트워킹

> [!NOTE]
> 이 문서는 ASE(App Service Environment) v3에 관한 내용임(미리 보기)
> 

ASE(App Service Environment)는 웹앱, api 앱 및 함수 앱을 호스트하는 Azure App Service의 단일 테넌트 배포입니다. ASE를 설치할 때 배포하려는 Azure VNet(Virtual Network)를 선택합니다. 모든 인바운드 및 아웃바운드 트래픽 애플리케이션은 사용자가 지정하는 VNet 내부에 있습니다.  

ASEv3는 두 개의 서브넷을 사용합니다.  하나의 서브넷은 인바운드 트래픽을 처리하는 프라이빗 엔드포인트에 사용됩니다. 기존 서브넷 또는 새 서브넷일 수 있습니다.  인바운드 서브넷은 프라이빗 엔드포인트 이외의 다른 용도로 사용할 수 있습니다. 아웃바운드 서브넷은 ASE에서 아웃바운드 트래픽에만 사용할 수 있습니다. 다른 어떤 것도 ASE 아웃바운드 서브넷으로 이동할 수 없습니다.

## <a name="addresses"></a>주소 
ASE는 생성 시 다음 주소를 갖습니다.

| 주소 형식 | description |
|--------------|-------------|
| 인바운드 주소 | 인바운드 주소는 ASE에서 사용하는 프라이빗 엔드포인트 주소입니다. |
| 아웃바운드 서브넷 | 아웃 바운드 서브넷은 ASE 서브넷이기도 합니다. 미리 보기 중 이 서브넷은 아웃 바운드 트래픽에만 사용됩니다. |
| Windows 아웃바운드 주소 | 이 ASE의 Windows 앱은 인터넷에 대한 아웃바운드 호출을 수행할 때 기본값으로 이 주소를 사용합니다. |
| Linux 아웃바운드 주소 | 이 ASE의 Linux 앱은 인터넷에 대한 아웃바운드 호출을 수행할 때 기본값으로 이 주소를 사용합니다. |

ASEv3는 ASE 포털의 **IP 주소** 부분에 있는 ASE에서 사용하는 주소에 대한 세부 정보를 포함합니다.

![ASE 주소 UI](./media/networking/networking-ip-addresses.png)

ASE에서 사용하는 프라이빗 엔드포인트를 삭제하면 ASE의 앱에 연결할 수 없습니다.  

ASE는 아웃바운드 서브넷의 주소를 사용하여 ASE에서 사용하는 인프라를 지원합니다. ASE에서 App Service 요금제를 높이면 더 많은 주소를 사용할 수 있습니다. ASE의 앱은 아웃바운드 서브넷에 전용 주소를 포함하지 않습니다. 앱에서 아웃바운드 서브넷의 앱에 사용되는 주소는 시간이 지남에 따라 변경됩니다.

## <a name="ports"></a>포트

ASE는 포트 80 및 443에서 애플리케이션 트래픽을 수신합니다.  ASE에 대한 인바운드 또는 아웃바운드 포트 요구 사항은 없습니다. 

## <a name="extra-configurations"></a>추가 구성

ASEv2와 달리 ASEv3에서는 NSG(네트워크 보안 그룹) 및 UDR(Route Tables)을 제한 없이 사용할 수 있도록 설정할 수 있습니다. 모든 아웃바운드 트래픽을 ASE에서 NVA 디바이스로 강제 터널링하려는 경우입니다. ASE로 들어오는 모든 인바운드 트래픽 앞에 WAF 디바이스를 배치할 수 있습니다. 

## <a name="dns"></a>DNS

ASE의 앱은 VNet이 구성된 DNS를 사용합니다. [App Service Environment 사용](./using.md#dns-configuration)의 지침에 따라 ASE를 가리키도록 DNS 서버를 구성합니다. 일부 앱에서 VNet이 구성된 것과 다른 DNS 서버를 사용하도록 하려면 WEBSITE_DNS_SERVER 및 WEBSITE_DNS_ALT_SERVER 앱 설정을 사용하여 앱별로 수동 설정할 수 있습니다. WEBSITE_DNS_ALT_SERVER 앱 설정은 보조 DNS 서버를 구성합니다. 보조 DNS 서버는 주 DNS 서버에서 응답이 없는 경우에만 사용됩니다. 

## <a name="preview-limitation"></a>미리 보기 제한 사항

ASEv3에서 사용할 수 없는 몇 가지 네트워킹 기능이 있습니다.  ASEv3에서 사용할 수 없는 항목은 다음과 같습니다.

• FTP • 원격 디버그 • 외부 부하 분산 장치 배포 • 컨테이너 배포를 위한 프라이빗 컨테이너 레지스트리에 액세스할 수 있는 기능 • 글로벌 피어링 Vnet에 대한 호출을 수행할 수 있는 기능 • 서비스 엔드포인트 또는 프라이빗 엔드포인트 보안 스토리지 계정으로 백업/복원할 수 있는 기능 • 서비스 엔드포인트 또는 프라이빗 엔드포인트 보안 KeyVault 계정에 앱 설정 KeyVault 참조를 포함할 수 있는 기능 • BYOS를 사용하는 기능 • 서비스 엔드포인트 또는 프라이빗 엔드포인트 보안 스토리지 계정에 대해 BYOS를 사용할 수 있는 기능 • 아웃바운드 트래픽에서 Network Watcher 또는 NSG 흐름 사용
    
    