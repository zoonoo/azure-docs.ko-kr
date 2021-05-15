---
title: Azure Data Factory에 대한 Azure Private Link
description: Azure Data Factory에서 Azure Private Link가 작동하는 방법에 대해 알아봅니다.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: f84d7d7a02b75723f78cfbed9ee23e19ebea9a15
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109481682"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Data Factory에 대한 Azure Private Link

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure Private Link를 사용하면 프라이빗 엔드포인트를 통해 Azure의 다양한 PaaS(Platform as a Service) 배포에 연결할 수 있습니다. 프라이빗 엔드포인트는 특정 가상 네트워크 및 서브넷 내의 개인 IP 주소입니다. Private Link 기능을 지원하는 PaaS 배포 목록은 [Private Link 설명서](../private-link/index.yml)를 참조하세요. 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>고객 네트워크와 Azure Data Factory 간의 보안 통신 
Azure 가상 네트워크를 클라우드에서 네트워크의 논리적 표현으로 설정할 수 있습니다. 이렇게 하면 다음과 같은 이점이 있습니다.
* 공용 네트워크의 공격으로부터 Azure 리소스를 보호하는 데 도움이 됩니다.
* 네트워크와 Data Factory가 서로 안전하게 통신하도록 합니다. 

IPsec(인터넷 프로토콜 보안) VPN(사이트 간) 연결 또는 Azure ExpressRoute(개인 피어링) 연결을 설정하여 온-프레미스 네트워크를 가상 네트워크에 연결할 수도 있습니다. 

온-프레미스 컴퓨터 또는 가상 네트워크의 가상 머신에 자체 호스팅 통합 런타임을 설치할 수도 있습니다. 이렇게 하면 다음을 수행할 수 있습니다.
* 클라우드 데이터 저장소와 개인 네트워크의 데이터 저장소 간에 복사 활동을 실행합니다.
* 온-프레미스 네트워크 또는 Azure 가상 네트워크의 컴퓨팅 리소스에 대해 변환 활동을 디스패치할 수 있습니다. 

다음 표에 표시된 것처럼 Azure Data Factory와 고객 가상 네트워크 간에는 여러 가지 통신 채널이 필요합니다.

| 도메인 | 포트 | Description |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Data Factory 작성 및 모니터링에 필요한 컨트롤 플레인입니다. |
| `*.{region}.datafactory.azure.net` | 443 | 자체 호스팅 통합 런타임에서 Data Factory 서비스에 연결하는 데 필요합니다. |
| `*.servicebus.windows.net` | 443 | 대화형 작성을 위해 자체 호스팅 통합 런타임에 필요합니다. |
| `download.microsoft.com` | 443 | 업데이트를 다운로드하기 위해 자체 호스팅 통합 런타임에서 필요합니다. |

Azure Data Factory용 Private Link 지원을 통해 다음을 수행할 수 있습니다.
* 가상 네트워크에 프라이빗 엔드포인트를 만듭니다.
* 특정 데이터 팩터리 인스턴스에 대한 프라이빗 연결을 사용하도록 설정합니다. 

Azure Data Factory 서비스에 대한 통신은 Private Link를 통해 이동하여 안전한 프라이빗 연결을 제공합니다. 

![Azure Data Factory 아키텍처에 대한 Private Link 다이어그램.](./media/data-factory-private-link/private-link-architecture.png)

위의 각 통신 채널에 대해 Private Link 서비스를 사용하도록 설정하면 다음과 같은 기능을 제공합니다.
- **지원되는 사항**:
   - 모든 아웃바운드 통신을 차단하더라도 가상 네트워크의 데이터 팩터리를 작성하고 모니터링할 수 있습니다.
   - 자체 호스팅 통합 런타임과 Azure Data Factory 서비스 사이의 명령 통신은 프라이빗 네트워크 환경에서 안전하게 수행될 수 있습니다. 자체 호스팅 통합 런타임과 Azure Data Factory 서비스 사이의 트래픽은 Private Link를 통과합니다. 
- **현재 지원되지 않는 사항**:
   - 연결 테스트, 폴더 목록 및 테이블 목록 찾아보기, 스키마 가져오기, 데이터 미리 보기 등 자체 호스팅 통합 런타임을 사용하는 대화형 작성은 Private Link를 통과합니다.
   - 자동 업데이트를 사용하도록 설정하면 Microsoft 다운로드 센터에서 자동으로 다운로드할 수 있는 자체 호스팅 통합 런타임의 새 버전은 현재 지원되지 않습니다.

   > [!NOTE]
   > 현재 지원되지 않는 기능을 사용하려면 가상 네트워크 또는 회사 방화벽에서 이전에 언급한 도메인 및 포트를 구성해야 합니다. 

   > [!NOTE]
   > 프라이빗 엔드포인트를 통해 Azure Data Factory에 연결하는 것은 데이터 팩터리의 자체 호스팅 통합 런타임에만 적용됩니다. Synapse에서는 지원되지 않습니다.

> [!WARNING]
> 연결된 서비스를 생성할 때 자격 증명이 Azure 키 자격 증명 모음에 저장되어 있는지 확인합니다. 그렇지 않으면 Azure Data Factory에서 Private Link를 사용하도록 설정할 때 자격 증명이 작동하지 않습니다.

## <a name="dns-changes-for-private-endpoints"></a>프라이빗 엔드포인트용 DNS 변경
프라이빗 엔드포인트를 생성하면 Data Factory에 대한 DNS CNAME 리소스 레코드가 접두사 'private link'를 포함하는 하위 도메인의 별칭으로 업데이트됩니다. 또한 기본적으로 프라이빗 엔드포인트에 대한 DNS A 리소스 레코드를 사용하여 'privatelink' 하위 도메인에 해당하는 [프라이빗 DNS 영역](../dns/private-dns-overview.md)을 만듭니다.

프라이빗 엔드포인트를 사용하여 VNet 외부에서 데이터 팩터리 엔드포인트 URL을 확인하면 데이터 팩터리 서비스의 퍼블릭 엔드포인트로 확인됩니다. 프라이빗 엔드포인트를 호스트하는 VNet에서 확인되면 스토리지 엔드포인트 URL은 프라이빗 엔드포인트의 IP 주소로 확인됩니다.

위의 예에서 프라이빗 엔드포인트를 호스팅하는 VNet 외부에서 해결되면 Data Factory 'DataFactory A'에 대한 DNS 리소스 레코드는 다음과 같습니다.

| Name | Type | 값 |
| ---------- | -------- | --------------- |
| DataFactoryA.{지역}.datafactory.azure.net | CNAME   | DataFactoryA.{지역}.privatelink.datafactory.azure.net |
| DataFactoryA.{지역}.privatelink.datafactory.azure.net | CNAME   | < 데이터 팩터리 서비스 퍼블릭 엔드포인트 > |
| < 데이터 팩터리 서비스 퍼블릭 엔드포인트 >  | A | < 데이터 팩터리 서비스 퍼블릭 IP 주소 > |

프라이빗 엔드포인트를 호스팅하는 VNet에서 확인되면 DataFactoryA에 대한 DNS 리소스 레코드는 다음과 같습니다.

| Name | Type | 값 |
| ---------- | -------- | --------------- |
| DataFactoryA.{지역}.datafactory.azure.net | CNAME   | DataFactoryA.{지역}.privatelink.datafactory.azure.net |
| DataFactoryA.{지역}.privatelink.datafactory.azure.net   | A | < 프라이빗 엔드포인트 IP 주소 > |

네트워크에서 사용자 지정 DNS 서버를 사용하는 경우 클라이언트는 Data Factory 엔드포인트의 FQDN을 프라이빗 엔드포인트 IP 주소로 확인할 수 있어야 합니다. 프라이빗 링크 하위 도메인을 VNet의 프라이빗 DNS 영역에 위임하도록 DNS 서버를 구성하거나 프라이빗 엔드포인트 IP 주소를 사용하여 'DataFactoryA.{지역}.privatelink.datafactory.azure.net'에 대한 A 레코드를 구성해야 합니다.

프라이빗 엔드포인트 지원 목적으로 자체 DNS 서버를 구성하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
- [Azure 가상 네트워크의 리소스 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [프라이빗 엔드포인트에 대한 DNS 구성](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-private-link-for-azure-data-factory"></a>Azure Data Factory에 대한 Private Link 설정
[Azure Portal](../private-link/create-private-endpoint-portal.md)을 사용하여 프라이빗 엔드포인트를 만들 수 있습니다.

퍼블릭 엔드포인트 또는 프라이빗 엔드포인트를 통해 Azure Data Factory에 자체 호스팅 통합 런타임을 연결할지 여부를 선택할 수 있습니다. 

![자체 호스팅 통합 런타임에 대한 공용 액세스를 차단하는 스크린샷.](./media/data-factory-private-link/disable-public-access-shir.png)


Azure Portal에서 Azure Data Factory로 이동하여 다음과 같이 프라이빗 엔드포인트를 만들 수도 있습니다.

![프라이빗 엔드포인트를 만들기 위한 "프라이빗 엔드포인트 연결" 창의 스크린샷.](./media/data-factory-private-link/create-private-endpoint.png)

**리소스** 단계에서 **Microsoft.Datafactory/factories** 를 **리소스 종류** 로 선택합니다. 자체 호스팅 통합 런타임과 Azure Data Factory 서비스 간의 명령 통신용 프라이빗 엔드포인트를 만들려면 **대상 하위 리소스** 로 **datafactory** 를 선택합니다.

![리소스를 선택하기 위한 "프라이빗 엔드포인트 연결" 창의 스크린샷.](./media/data-factory-private-link/private-endpoint-resource.png)

> [!NOTE]
> 공용 네트워크 액세스 비활성화는 Azure Integration Runtime 및 SSIS(SQL Server Integration Services) Integration Runtime이 아닌 자체 호스팅 통합 런타임에만 적용할 수 있습니다.

가상 네트워크에서 데이터 팩터리를 작성하고 모니터링하기 위한 프라이빗 엔드포인트를 만들려면 **대상 하위 리소스** 로 **포털** 을 선택합니다.

> [!NOTE]
> 포털에 대한 프라이빗 엔드포인트를 만든 후에도 공용 네트워크를 통해 Azure Data Factory 포털에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory UI를 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-portal.md)
- [Azure Data Factory 소개](introduction.md)
- [Azure Data Factory에서 시각적 작성](author-visually.md)
