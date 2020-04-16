---
title: Azure Integration Runtime IP 주소
description: 데이터 저장소에 대한 네트워크 액세스를 보호하기 위해 방화벽을 적절하게 구성하려면 인바운드 트래픽을 허용해야 하는 IP 주소를 알아봅니다.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: b0ba47ff28208bce1a6fa6ec300a261d788167de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415608"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Azure Integration Runtime IP 주소

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 통합 런타임에서 사용하는 IP 주소는 Azure 통합 런타임이 있는 지역에 따라 다릅니다. *모두* 동일한 지역에 있는 Azure 통합 런타임은 동일한 IP 주소 범위를 사용합니다.

> [!IMPORTANT]  
> 데이터 흐름은 현재 이러한 IP를 사용하지 않습니다. 
>
> 이러한 IP 범위를 데이터 이동, 파이프라인 및 외부 활동 실행에 사용할 수 있습니다. 이러한 IP 범위는 Azure 통합 런타임의 인바운드 액세스를 위해 데이터 저장소/NSG(네트워크 보안 그룹) / 방화벽의 허용 목록에 있는 화이트리스트에 사용할 수 있습니다. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure 통합 런타임 IP 주소: 특정 지역

리소스가 있는 특정 Azure 지역의 Azure 통합 런타임에 대해 나열된 IP 주소의 트래픽 허용:

|                | 지역              | IP 주소                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| 아시아           | 동아시아           | 20.189.104.128/25, </br>20.189.106.0/26, </br>13.75.39.112/28 |
| &nbsp;         | 동남아시아      | 20.43.128.128/25, </br>20.43.130.0/26, </br>40.78.236.176/28 |
| 오스트레일리아      | 오스트레일리아 동부      | 20.37.193.0/25,</br>20.37.193.128/26,</br>13.70.74.144/28    |
| &nbsp;         | 오스트레일리아 남동부 | 20.42.225.0/25,</br>20.42.225.128/26,</br>13.77.53.160/28    |
| 브라질         | 브라질 남부        | 191.235.224.128/25,</br>191.235.225.0/26,</br>191.233.205.160/28 |
| Canada         | 캐나다 중부      | 52.228.80.128/25,</br>52.228.81.0/26,</br>13.71.175.80/28    |
| 중국          | 중국 동부 2        | 40.73.172.48/28,</br>52.130.0.128/25,</br>52.130.1.0/26      |
| 유럽         | 북유럽        | 20.38.82.0/23,</br>20.38.80.192/26,</br>13.69.230.96/28      |
| &nbsp;         | 서유럽         | 40.74.26.0/23,</br>40.74.24.192/26,</br>13.69.67.192/28      |
| 프랑스         | 프랑스 중부      | 20.43.40.128/25,</br>20.43.41.0/26,</br>40.79.132.112/28     |
| 인도          | 인도 중부       | 52.140.104.128/25,</br>52.140.105.0/26,</br>20.43.121.48/28  |
| 일본          | 일본 동부          | 20.43.64.128/25,</br>20.43.65.0/26,</br>13.78.109.192/28     |
| 한국          | 한국 중부       | 20.41.64.128/25,</br>20.41.65.0/26,</br>52.231.20.64/28      |
| 남아프리카   | 남아프리카 북부  | 102.133.124.104/29,</br>102.133.216.128/25,</br>102.133.217.0/26 |
| United Kingdom | 영국 남부            | 51.104.24.128/25,</br>51.104.25.0/26,</br>51.104.9.32/28     |
| 미국  | 미국 중부          | 20.37.154.0/23,</br>20.37.156.0/26,</br>20.44.10.64/28       |
|                | 미국 동부             | 20.42.2.0/23,</br>20.42.4.0/26,</br>40.71.14.32/28           |
|                | 미국 동부2            | 20.41.2.0/23,</br>20.41.4.0/26,</br>20.44.17.80/28           |
|                | 미국 동부 2 EUAP      | 20.39.8.128/26,</br>20.39.8.96/27,</br>40.75.35.144/28       |
|                | 미국 중북부    | 40.80.185.0/24,</br>40.80.186.0/25,</br>52.162.111.48/28      |
|                | 미국 중남부    | 40.119.9.0/25,</br>40.119.9.128/26,</br>13.73.244.32/28      |
|                | 미국 중서부     | 52.150.137.128/25,</br>52.150.136.192/26,</br>13.71.199.0/28 |
|                | 미국 서부             | 40.82.250.0/23,</br>40.82.249.64/26,</br>13.86.219.208/28    |
|                | 미국 서부2            | 20.42.132.0/23,</br>20.42.129.64/26,</br>13.66.143.128/28    |
|                | US Gov 버지니아     | 52.127.45.96/28,</br>52.127.48.128/25,</br>52.127.49.0/26    |

## <a name="known-issue-with-azure-storage"></a>Azure 저장소에 알려진 문제

* Azure Storage 계정에 연결할 때 IP 네트워크 규칙은 저장소 계정과 동일한 리전에서 Azure 통합 런타임에서 시작된 요청에 영향을 주지 않습니다. 자세한 내용은 [이 문서를 참조하십시오.](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range) 

  대신 [Azure 저장소에 연결하는 동안 신뢰할 수 있는 서비스를](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)사용하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계

* [Azure Data Factory에서 데이터 이동을 위한 보안 고려 사항](data-movement-security-considerations.md)
