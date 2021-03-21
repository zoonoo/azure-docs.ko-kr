---
title: Azure Instance Metadata Service (IMDS)를 사용 하 여 부하 분산 장치 메타 데이터 검색
titleSuffix: Azure Load Balancer
description: Azure Instance Metadata Service를 사용 하 여 부하 분산 장치 메타 데이터를 검색 하는 방법을 배웁니다.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 9ec217cefb05929ed6f5c7395df5e68891e823ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739783"
---
# <a name="retrieve-load-balancer-metadata-using-the-azure-instance-metadata-service-imds"></a>Azure Instance Metadata Service (IMDS)를 사용 하 여 부하 분산 장치 메타 데이터 검색

## <a name="prerequisites"></a>필수 구성 요소

* 요청에 대 한 [최신 API 버전](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#supported-api-versions) 을 사용 합니다.

## <a name="sample-request-and-response"></a>예제 요청 및 응답
> [!IMPORTANT]
> 이 예에서는 프록시를 무시 합니다. IMDS를 쿼리할 때 프록시를 무시 **해야** 합니다. 자세한 내용은 [프록시](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#proxies)를 참조 하세요.
### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01" | ConvertTo-Json
```
> [!NOTE]
> -NoProxy 매개 변수는 PowerShell 6.0에서 도입 되었습니다. 이전 버전의 PowerShell을 사용 하는 경우 요청 본문에서-NoProxy를 제거 하 고 IMDS 정보를 검색 하는 동안 프록시를 사용 하 고 있지 않은지 확인 합니다. [여기](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service?tabs=windows#proxies)를 참조하세요.
> 
### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H "Metadata:true" --noproxy "*" "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01"
```

---
### <a name="sample-response"></a>샘플 응답

```json
{
   "loadbalancer": {
    "publicIpAddresses":[
      {
         "frontendIpAddress":"51.0.0.1",
         "privateIpAddress":"10.1.0.4"
      }
   ],
   "inboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frontendIpAddress":"2603:10e1:100:2::1:1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
   ],
   "outboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frotendIpAddress":"2603:10e1:100:2::1:1",
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
    ]
   }
}

```

## <a name="next-steps"></a>다음 단계
[일반적인 오류 코드 및 문제 해결 단계](troubleshoot-load-balancer-imds.md)

[Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md) 에 대 한 자세한 정보

[인스턴스의 모든 메타 데이터 검색](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#access-azure-instance-metadata-service)

[표준 부하 분산 장치 배포](quickstart-load-balancer-standard-public-portal.md)

