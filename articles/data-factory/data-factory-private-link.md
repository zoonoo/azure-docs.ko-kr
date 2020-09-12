---
title: Azure Data Factory에 대 한 Azure 개인 링크
description: Azure Data Factory에서 Azure 개인 링크 작동에 대해 알아봅니다.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 164a1005d9382711874b644e14b23d2154d613a0
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596025"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Data Factory에 대 한 Azure 개인 링크

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Private Link를 사용하면 프라이빗 엔드포인트를 통해 Azure의 다양한 PaaS 서비스에 연결할 수 있습니다. 개인 링크 기능을 지 원하는 PaaS 서비스 목록은 [개인 링크 설명서 페이지](https://docs.microsoft.com/azure/private-link/)를 참조 하세요. 개인 끝점은 특정 가상 네트워크 및 서브넷의 개인 IP 주소입니다.

## <a name="secure-communication-between-customer-network-and-azure-data-factory-service"></a>고객 네트워크와 Azure Data Factory 서비스 간의 보안 통신
공용 네트워크의 공격 으로부터 Azure 리소스를 보호 하거나 서로 안전 하 게 통신할 수 있도록 하려면 클라우드에서 네트워크의 논리적 표현으로 Azure Virtual Network를 설정할 수 있습니다. IPSec VPN (사이트 간) 또는 Express 경로 (개인 피어 링)를 설정 하 여 온-프레미스 네트워크를 가상 네트워크에 연결할 수도 있습니다. 자체 호스트 된 Integration Runtime 온-프레미스 컴퓨터 또는 가상 Virtual Network 컴퓨터에 설치 하 여 클라우드 데이터 저장소와 개인 네트워크의 데이터 저장소 간에 또는 온-프레미스 네트워크 또는 Azure 가상 네트워크의 계산 리소스에 대 한 변환 작업을 디스패치 하는 복사 작업을 실행할 수 있습니다. 

Data Factory와 고객 가상 네트워크 사이에는 몇 가지 통신 채널이 필요 합니다.


| **도메인** | **포트** | **설명** |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | 제어 평면. Data Factory 작성 및 모니터링에 필요 합니다. |
| `*.{region}.datafactory.azure.net` | 443 | Data Factory 서비스에 연결 하기 위해 자체 호스팅 Integration Runtime에 필요 합니다. |
| `*.servicebus.windows.net` | 443 | 대화형 작성을 위해 자체 호스팅 Integration Runtime에 필요 합니다. |
| `download.microsoft.com` | 443 | 업데이트를 다운로드 하는 자체 호스팅 Integration Runtime에 필요 합니다. |


Azure Data Factory에 대 한 Azure 개인 링크를 지 원하는 경우 가상 네트워크에서 개인 끝점 (PE)을 만들고 특정 Azure Data Factory에 대 한 개인 연결을 사용 하도록 설정할 수 있습니다. Azure Data Factory 서비스에 대 한 통신은 안전한 개인 연결을 제공 하는 Azure 개인 링크를 통해 이동 합니다. 그리고 더 안전 하 게 리소스를 보호 하는 방법을 제공 하는 가상 네트워크 또는 회사 방화벽에서 위의 도메인 및 포트를 구성할 필요가 없습니다.  

![Azure Data Factory 개인 링크 아키텍처](./media/data-factory-private-link/private-link-architecture.png)

위에서 설명한 각 통신 채널에 대해 개인 링크 서비스를 사용 하도록 설정 하는 이점은 다음과 같습니다.
- 되지 모든 아웃 바운드 통신을 차단 하는 경우에도 가상 네트워크에서 Azure Data Factory의 제작 및 모니터링을 수행할 수 있습니다.
- 되지 자체 호스팅 Integration Runtime와 Azure Data Factory 서비스 간의 명령 통신은 개인 네트워크 환경에서 안전 하 게 수행할 수 있습니다. 자체 호스팅 Integration Runtime와 Azure Data Factory 서비스 간의 트래픽은 개인 링크를 통해 이동 합니다. 
- (현재 지원 되지 않음) 자체 호스팅 Integration Runtime를 사용 하는 대화형 작성은 연결 테스트, 폴더 목록 및 테이블 목록 찾아보기, 스키마 가져오기 및 데이터 미리 보기와 같은 개인 링크를 통해 이동 합니다.
- (현재 지원 되지 않음) 자동 업데이트를 사용 하는 경우 다운로드 센터에서 자체 호스트 된 Integration Runtime의 새 버전을 자동으로 다운로드할 수 있습니다.

> [!NOTE]
> 현재 지원 되지 않는 기능의 경우 가상 네트워크 또는 회사 방화벽에서 위의 도메인 및 포트를 구성 해야 합니다. 

> [!WARNING]
> 연결 된 서비스를 만들 때 자격 증명이 Azure Key Vault에 저장 되어 있는지 확인 합니다. 그렇지 않으면 Azure Data Factory에서 개인 링크 서비스를 사용 하도록 설정 하는 경우 작동 하지 않습니다.

## <a name="how-to-set-up-private-link-for-azure-data-factory"></a>Azure Data Factory에 대 한 개인 링크를 설정 하는 방법
프라이빗 엔드포인트는 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 만들 수 있습니다.

[포털](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)


Azure Portal에서 Azure Data Factory로 이동 하 여 개인 끝점 (PE)을 만들 수도 있습니다.

![프라이빗 엔드포인트 만들기](./media/data-factory-private-link/create-private-endpoint.png)


이 Azure Data Factory에 대 한 공용 액세스를 차단 하 고 개인 링크만을 통해서만 액세스를 허용 하려는 경우 Azure Portal에서 Azure Data Factory의 네트워크 액세스를 사용 하지 않도록 설정할 수 있습니다.

![프라이빗 엔드포인트 만들기](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> 공용 네트워크 액세스를 사용 하지 않도록 설정 하는 것은 자체 호스팅 Integration Runtime에만 적용 되 고 Azure Integration Runtime 및 SSIS Integration Runtime에는 적용 되지 않습니다.

> [!NOTE]
> 사용자는 공용 네트워크 액세스를 사용 하지 않도록 설정한 후에도 공용 네트워크를 통해 Azure Data Factory 포털에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory UI를 사용 하 여 데이터 팩터리 만들기](quickstart-create-data-factory-portal.md)

- [Azure Data Factory 소개](introduction.md)

- [Azure Data Factory에서 시각적 작성](author-visually.md)

