---
title: Azure Data Factory에 대한 Azure Private Link
description: Azure Data Factory에서 Azure 개인 링크의 작동 방식에 대해 알아봅니다.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 9e4d686f582a202dbc543620c7bf73dc4e7adb22
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389181"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Data Factory에 대한 Azure Private Link

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure 개인 링크를 사용 하 여 개인 끝점을 통해 Azure에서 다양 한 PaaS (platform as a service) 배포에 연결할 수 있습니다. 개인 끝점은 특정 가상 네트워크 및 서브넷의 개인 IP 주소입니다. 개인 링크 기능을 지 원하는 PaaS 배포 목록은 [개인 링크 설명서](../private-link/index.yml)를 참조 하세요. 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>고객 네트워크와 Azure Data Factory 간의 보안 통신 
Azure 가상 네트워크를 클라우드에서 네트워크의 논리적 표현으로 설정할 수 있습니다. 이렇게 하면 다음과 같은 이점이 있습니다.
* 공용 네트워크의 공격 으로부터 Azure 리소스를 보호 하는 데 도움이 됩니다.
* 네트워크와 Data Factory는 서로 안전 하 게 통신할 수 있습니다. 

IPsec (인터넷 프로토콜 보안) VPN (사이트 간) 연결 또는 Azure Express 경로 (개인 피어 링) 연결을 설정 하 여 온-프레미스 네트워크를 가상 네트워크에 연결할 수도 있습니다. 

또한 온-프레미스 컴퓨터 또는 가상 네트워크의 가상 컴퓨터에 자체 호스팅 통합 런타임을 설치할 수 있습니다. 이렇게 하면 다음을 수행할 수 있습니다.
* 클라우드 데이터 저장소와 개인 네트워크의 데이터 저장소 간에 복사 작업을 실행 합니다.
* 온-프레미스 네트워크 또는 Azure 가상 네트워크에서 계산 리소스에 대 한 변환 작업을 디스패치합니다. 

다음 표에 표시 된 것 처럼 Azure Data Factory와 고객 가상 네트워크 사이에 몇 가지 통신 채널이 필요 합니다.

| 도메인 | 포트 | Description |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Data Factory 작성 및 모니터링에 필요한 제어 평면입니다. |
| `*.{region}.datafactory.azure.net` | 443 | 자체 호스팅 통합 런타임에서 Data Factory 서비스에 연결하는 데 필요합니다. |
| `*.servicebus.windows.net` | 443 | 대화형 작성을 위해 자체 호스팅 통합 런타임에 필요합니다. |
| `download.microsoft.com` | 443 | 업데이트를 다운로드하기 위해 자체 호스팅 통합 런타임에서 필요합니다. |

Azure Data Factory에 대 한 개인 링크를 지 원하는 경우 다음을 수행할 수 있습니다.
* 가상 네트워크에서 개인 끝점을 만듭니다.
* 특정 data factory 인스턴스에 대 한 개인 연결을 사용 하도록 설정 합니다. 

Azure Data Factory 서비스에 대 한 통신은 개인 링크를 통해 이동 하 여 안전한 개인 연결을 제공 합니다. 

![Azure Data Factory 아키텍처에 대 한 개인 링크 다이어그램](./media/data-factory-private-link/private-link-architecture.png)

위의 각 통신 채널에 대해 개인 링크 서비스를 사용 하도록 설정 하면 다음과 같은 기능을 제공 합니다.
- **지원 됨**:
   - 모든 아웃 바운드 통신을 차단 하는 경우에도 가상 네트워크에서 데이터 팩터리를 작성 하 고 모니터링할 수 있습니다.
   - 자체 호스팅 통합 런타임과 Azure Data Factory 서비스 간의 명령 통신은 개인 네트워크 환경에서 안전 하 게 수행할 수 있습니다. 자체 호스팅 통합 런타임과 Azure Data Factory 서비스 간의 트래픽은 개인 링크를 통해 이동 합니다. 
- **현재 지원 되지 않음**:
   - 연결 테스트, 폴더 목록 및 테이블 목록 찾아보기, 스키마 가져오기 및 데이터 미리 보기와 같은 자체 호스팅 통합 런타임을 사용 하는 대화형 작성은 개인 링크를 통해 이동 합니다.
   - 자동 업데이트를 사용 하도록 설정 하는 경우 자체 호스팅 통합 런타임의 새 버전이 Microsoft 다운로드 센터에서 자동으로 다운로드 될 수 있습니다.

   > [!NOTE]
   > 현재 지원 되지 않는 기능의 경우에도 이전에 언급 한 도메인 및 포트를 가상 네트워크 또는 회사 방화벽에서 구성 해야 합니다. 

   > [!NOTE]
   > 개인 끝점을 통해 Azure Data Factory에 연결 하는 것은 Data Factory의 자체 호스팅 통합 런타임에만 적용 됩니다. Synapse에서 지원 되지 않습니다.

> [!WARNING]
> 연결 된 서비스를 만들 때 자격 증명이 Azure key vault에 저장 되어 있는지 확인 합니다. 그렇지 않으면 Azure Data Factory에서 개인 링크를 사용 하도록 설정 하는 경우 자격 증명이 작동 하지 않습니다.

## <a name="dns-changes-for-private-endpoints"></a>전용 끝점에 대 한 DNS 변경
개인 끝점을 만들 때 Data Factory에 대 한 DNS CNAME 리소스 레코드는 접두사가 ' privatelink ' 인 하위 도메인의 별칭으로 업데이트 됩니다. 또한 기본적으로 개인 끝점에 대 한 DNS A 리소스 레코드를 사용 하 여 ' privatelink ' 하위 도메인에 해당 하는 [개인 DNS 영역](../dns/private-dns-overview.md)을 만듭니다.

개인 끝점을 사용 하 여 VNet 외부에서 데이터 팩터리 끝점 URL을 확인 하면 data factory 서비스의 공용 끝점으로 확인 됩니다. 개인 끝점을 호스트 하는 VNet에서 확인 되 면 저장소 끝점 URL은 개인 끝점의 IP 주소로 확인 됩니다.

위의 예에서는 개인 끝점을 호스트 하는 VNet 외부에서 확인 되는 Data Factory ' DataFactoryA '에 대 한 DNS 리소스 레코드는 다음과 같습니다.

| Name | Type | 값 |
| ---------- | -------- | --------------- |
| DataFactoryA. {region}. datafactory | CNAME   | DataFactoryA. {region}. privatelink. datafactory. |
| DataFactoryA. {region}. privatelink. datafactory. | CNAME   | < data factory 서비스 공용 끝점 > |
| < data factory 서비스 공용 끝점 >  | A | < data factory 서비스 공용 IP 주소 > |

DataFactoryA에 대 한 DNS 리소스 레코드는 개인 끝점을 호스트 하는 VNet에서 확인 되는 경우 다음과 같습니다.

| Name | Type | 값 |
| ---------- | -------- | --------------- |
| DataFactoryA. {region}. datafactory | CNAME   | DataFactoryA. {region}. privatelink. datafactory. |
| DataFactoryA. {region}. privatelink. datafactory.   | A | < 개인 끝점 IP 주소 > |

네트워크에서 사용자 지정 DNS 서버를 사용 하는 경우 클라이언트는 개인 끝점 IP 주소에 대 한 Data Factory 끝점의 FQDN을 확인할 수 있어야 합니다. 개인 링크 하위 도메인을 VNet의 개인 DNS 영역에 위임 하도록 DNS 서버를 구성 하거나 ' DataFactoryA '에 대 한 A 레코드를 구성 해야 합니다. 개인 끝점 IP 주소를 사용 하는 {region}. privatelink. datafactory.

전용 끝점을 지원 하기 위해 자체 DNS 서버를 구성 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.
- [Azure 가상 네트워크의 리소스 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [전용 끝점에 대 한 DNS 구성](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-private-link-for-azure-data-factory"></a>Azure Data Factory에 대 한 개인 링크 설정
[Azure Portal를](../private-link/create-private-endpoint-portal.md)사용 하 여 개인 끝점을 만들 수 있습니다.

공용 끝점 또는 개인 끝점을 통해 Azure Data Factory에 자체 호스팅 통합 런타임을 연결할지 여부를 선택할 수 있습니다. 

![자체 호스팅 Integration Runtime에 대 한 공용 액세스를 차단 하는 스크린샷](./media/data-factory-private-link/disable-public-access-shir.png)


Azure Portal에서 Azure 데이터 팩터리로 이동 하 여 다음과 같이 개인 끝점을 만들 수도 있습니다.

![개인 끝점을 만들기 위한 "개인 끝점 연결" 창의 스크린샷](./media/data-factory-private-link/create-private-endpoint.png)

**리소스** 단계에서 **Datafactory/factory** 를 **리소스 유형** 으로 선택 합니다. 자체 호스팅 통합 런타임과 Azure Data Factory 서비스 간의 명령 통신용 개인 끝점을 만들려면 **대상 하위 리소스로** **datafactory** 를 선택 합니다.

![리소스를 선택 하기 위한 "개인 끝점 연결" 창의 스크린샷](./media/data-factory-private-link/private-endpoint-resource.png)

> [!NOTE]
> 공용 네트워크 액세스를 사용 하지 않도록 설정 하는 것은 자체 호스팅 통합 런타임에서만 적용 되며 SSIS (Azure Integration Runtime 및 SQL Server Integration Services) Integration Runtime에만 적용 됩니다.

가상 네트워크에서 데이터 팩터리를 작성 하 고 모니터링 하기 위한 개인 끝점을 만들려면 **대상 하위 리소스로** **포털** 을 선택 합니다.

> [!NOTE]
> 포털에 대 한 개인 끝점을 만든 후에도 공용 네트워크를 통해 Azure Data Factory 포털에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory UI를 사용 하 여 데이터 팩터리 만들기](quickstart-create-data-factory-portal.md)
- [Azure Data Factory 소개](introduction.md)
- [Azure Data Factory에서 시각적 작성](author-visually.md)