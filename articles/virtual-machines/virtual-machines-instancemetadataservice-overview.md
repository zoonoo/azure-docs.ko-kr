---
title: "Azure 인스턴스 메타데이터 서비스 개요 | Microsoft Docs"
description: "VM의 계산, 네트워크 및 예정된 유지 관리 이벤트에 대한 정보를 가져오는 RESTful 인터페이스입니다."
services: virtual-machines-windows, virtual-machines-linux,virtual-machines-scale-sets, cloud-services
documentationcenter: 
author: harijay
manager: timlt
editor: 
tags: 
ms.service: azure-instancemetadataservice
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: harijay
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: b5c0268e1a0ebfcb33781678a367090cad865907
ms.lasthandoff: 04/11/2017

---

# <a name="azures-instance-metadata-service---preview"></a>Azure의 인스턴스 메타데이터 서비스 -- 미리 보기

> [!NOTE] 
> 사용 약관에 동의하게 되면 미리 보기를 사용할 수 있습니다. 자세한 내용은 [Microsoft Azure Preview용 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

인스턴스 메타데이터 서비스는 실행 중인 가상 컴퓨터 인스턴스에 대한 정보를 제공합니다. 이 정보는 Azure에서 인스턴스를 관리하고 구성하는 데 사용할 수 있습니다. Azure의 인스턴스 메타데이터 서비스는 새 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN)를 통해 생성된 모든 IaaS VM에서 사용할 수 있는 RESTful 끝점입니다. 이 끝점은 VM 내부에서만 액세스할 수 있는 잘 알려진 라우팅이 불가능한 IP 주소(*169.254.169.254*) 에서 사용할 수 있습니다. 이 서비스는 가상 컴퓨터 인스턴스에 관해 중요한 데이터(예: 네트워크 구성 및 예정된 유지 관리 이벤트 등)를 제공합니다. 자세한 내용은 [메타데이터 범주](#instance-metadata-data-categories)를 참조하세요.

### <a name="important-information"></a>중요 정보
현재 이 서비스는 **미리 보기** 상태이며 가상 컴퓨터 인스턴스 및 예정된 유지 관리 이벤트와 관련된 정보를 호스트합니다. 서비스는 계속 업데이트를 수신하며 이 페이지는 사용 가능한 특정 [데이터 범주](#instance-metadata-data-categories)를 반영합니다.


## <a name="service-availability"></a>서비스 가용성
현재 미리 보기는 Azure의 **미국 서부 중앙** 지역에서 사용할 수 있습니다. 다음 테이블에는 서비스 미리 보기가 제공되는 지역이 업데이트됩니다.
인스턴스 메타데이터 서비스를 체험하려면 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN) 또는 [Azure Portal](http://portal.azure.com)에서 VM을 만들고 예제 섹션의 예제에 따라 메타데이터 서비스에 액세스하세요. 

인스턴스 메타데이터 서비스 미리 보기가 제공되는 지역|
------------------------------------------------------------|
미국 중서부 |



## <a name="retrieving-instance-metadata"></a>인스턴스 메타데이터 검색 

인스턴스 메타데이터는 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN)를 사용하여 생성/관리되는 VM을 실행하는 데 사용할 수 있습니다. 가상 컴퓨터 인스턴스의 모든 데이터 범주를 액세스하려면 다음 URI를 사용합니다.

```
 curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```

모든 인스턴스 메타데이터의 기본 출력은 JSON 형식(콘텐츠 형식 응용 프로그램/JSON)입니다.

## <a name="usage-examples"></a>사용 예제
다음은 인스턴스 메타데이터 서비스에 대한 예제 및 사용 의미 체계입니다.

### <a name="versioning"></a>버전 관리 
인스턴스 메타데이터 서비스에는 버전이 있습니다. 버전은 필수이며 현재 미리 보기 버전은 2017-03-01입니다.


```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```

새로운 버전이 추가되더라도 스크립트가 특정 데이터 형식에 종속성이 있는 경우 이전 버전에 여전히 액세스할 수 있습니다. 현재 미리 보기 버전은 서비스가 일반 공급되면 사용하지 못할 수 있습니다.


### <a name="data-output"></a>데이터 출력
기본적으로 인스턴스 메타데이터는 JSON 형식(content type=application/json)으로 데이터를 반환합니다. 다른 노드 요소는 해당되는 다른 기본 형식으로 데이터를 반환합니다. 다음 테이블은 데이터 형식에 대한 간단한 참조입니다. 

요소 | 기본 데이터 형식 | 다른 형식
--------|---------------------|--------------
/instance | json : | 텍스트
/scheduledevents | json : | 없음

텍스트 형식의 경우 요청 URL에 format=text를 사용합니다. 예: 

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01&format=text"
```
### <a name="security"></a>보안
인스턴스 메타데이터 끝점은 라우팅이 불가능한 169.254.169.254 IP 주소에서 실행 중인 가상 컴퓨터 인스턴스 내부에서만 액세스할 수 있습니다. 또한 **X-Forwarded-For 헤더**가 포함된 모든 요청은 메타데이터 서비스에 의해 거부됩니다. 또한 실제 요청이 의도치 않은 리디렉션의 일환이 아니라 직접적으로 의도된 것이라는 것을 확인하기 위해 전송되는 요청에 **Metadata:true** 헤더가 포함되어야 합니다. 
### <a name="error"></a>오류
찾을 수 없는 데이터 요소 또는 형식이 잘못된 요청이 있으면 인스턴스 메타데이터 서비스는 표준 HTTP 오류를 반환하며, 다음은 반환 코드의 몇 가지 예입니다. 

HTTP 반환 코드 | 이유
----------------|-------
200 | 확인
400 | 잘못된 요청, 헤더 누락, 전달 -H Metadata:true
404 | 찾을 수 없음, 요청된 요소가 없음 
405 | 메서드가 지원되지 않음 
429 | 요청이 너무 많음, 현재 쿼리가 초당 5개 까지만 지원됨

### <a name="examples"></a>예
#### <a name="retrieving-the-network-information"></a>네트워크 정보 검색 

**요청**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-03-01"

```
**응답**
> [!NOTE] 
> 응답은 JSON 문자열입니다. 다음 출력은 [jq](https://stedolan.github.io/jq/)와 같은 유틸리티를 사용하여 JSON 형식으로 만들어졌습니다.
>

```
{
  "interface": [
    {
      "ipv4": {
        "ipaddress": [
          {
            "ipaddress": "10.0.0.4",
            "publicip": "<>.<>.<>.<>"
          }
        ],
        "subnet": [
          {
            "address": "10.0.0.0",
            "dnsservers": [
              {
                "ipaddress": "10.0.0.2"
              },
              {
                "ipaddress": "10.0.0.3"
              }
            ],
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipaddress": []
      },
      "mac": "000D3A00FA89"
    }
  ]
}
```

#### <a name="retrieving-public-ip-address"></a>공용 IP 주소 검색

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipaddress/0/publicip?api-version=2017-03-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>인스턴스에 대한 모든 메타데이터 검색

**요청**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```
**응답**
> [!NOTE]
> 응답은 JSON 문자열입니다. 다음 출력은 [jq](https://stedolan.github.io/jq/)와 같은 유틸리티를 사용하여 JSON 형식으로 만들어졌습니다.
>

```
{
"compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipaddress": [
            {
              "ipaddress": "10.0.0.4",
              "publicip": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.0.0",
              "dnsservers": [
                {
                  "ipaddress": "10.0.0.2"
                },
                {
                  "ipaddress": "10.0.0.3"
                }
              ],
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipaddress": []
        },
        "mac": "000D3A00FA89"
      }
    ]
  }
}

```
#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Windows 가상 컴퓨터에서 메타데이터 검색

인스턴스 메타데이터는 Powershell 유틸리티 curl을 통해 Windows에서 검색할 수 있습니다. Powershell 프롬프트에서 다음 명령을 실행합니다. 

**요청**
```
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-03-01 | select -ExpandProperty Content
```
**응답**
>[!NOTE]
> 응답은 JSON 문자열입니다. 다음 출력은 ConvertTo-Json Powershell 유틸리티를 사용하여 JSON 형식으로 만들어졌습니다.
> 

```
{
    "compute":  {
                    "location":  "CentralUSEUAP",
                    "name":  "SQLTest",
                    "offer":  "SQL2016SP1-WS2016",
                    "osType":  "Windows",
                    "platformFaultDomain":  "0",
                    "platformUpdateDomain":  "0",
                    "publisher":  "MicrosoftSQLServer",
                    "sku":  "Enterprise",
                    "version":  "13.0.400110",
                    "vmId":  "453945c8-3923-4366-b2d3-ea4c80e9b70e",
                    "vmSize":  "Standard_DS2"
                },
    "network":  {
                    "interface":  [
                                      "@{ipv4=; ipv6=; mac=002248020E1E}"
                                  ]
                }
}
```


## <a name="instance-metadata-data-categories"></a>인스턴스 메타데이터 데이터 범주
다음 테이블에는 인스턴스 메타데이터를 통해 사용할 수 있는 모든 데이터 범주가 나열되어 있습니다.

Data | 설명
-----|------------
location | VM을 실행하는 Azure 지역 
name | VM의 이름 
제품 | VM 이미지에 대한 제품 정보, 이 값은 Azure 이미지 갤러리에서 배포된 이미지에만 있습니다. 
publisher | VM 이미지 게시자
sku | VM 이미지에 해당하는 SKU  
버전 | VM 이미지의 버전 
osType | Linux 또는or Windows 
platformUpdateDomain |  VM을 실행 중인 [업데이트 도메인](virtual-machines-windows-manage-availability.md) 
platformFaultDomain | VM을 실행 중인 [장애 도메인](virtual-machines-windows-manage-availability.md)
vmId | VM에 대한 고유 식별자, 자세한 내용은 [여기](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)에서 참조
vmSize | [VM 크기](virtual-machines-windows-sizes.md)
ipv4/ipaddress | VM의 로컬 IP 주소 
ipv4/publicip | 인스턴스에 대한 공용 IP 주소 
subnet/address | 서브넷의 주소 
subnet/dnsservers/ipaddress1 | 기본 DNS 서버
subnet/dnsservers/ipaddress2 | 보조 DNS 서버
subnet/prefix | 서브넷 접두사, 예:24
ipv6/ipaddress | VM의 IPv6 주소
mac | VM MAC 주소 
scheduledevents | [scheduledevents](virtual-machines-scheduled-events.md) 참조



## <a name="example-scenarios-for-usage"></a>사용법을 위한 예제 시나리오  

### <a name="tracking-vm-running-on-azure"></a>Azure에서 실행 중인 VM 추적 

서비스 공급자는 소프트웨어를 실행 중인 VM의 수를 추적하거나 VM의 고유성을 추적해야 하는 에이전트가 있어야 합니다. VM의 고유 ID를 가져오려면 인스턴스 메타데이터 서비스의 vmId 필드를 사용합니다. 

**요청**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-03-01&format=text"
```
**응답**
```
5c08b38e-4d57-4c23-ac45-aca61037f084

```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>장애/업데이트 도메인 기반 컨테이너, 데이터 파티션 배치 

특정 시나리오의 경우 다양한 복제본 배치가 매우 중요합니다. 예를 들어 [HDFS 복제본 배치](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) 또는 [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/)를 통한 컨테이너 배치를 위해서는 VM을 실행 중인 platformFaultDomain 및 platformUpdateDomain을 알아야 합니다. 인스턴스 메타데이터를 통해 이러한 데이터 요소를 직접 쿼리할 수 있습니다.

**요청**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-03-01&format=text" 
```
**응답**
```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>지원 사례 중 VM에 대한 자세한 정보 얻기 

서비스 공급자는 지원 요청을 받을 수 있으며 이 때 VM에 대해 자세한 정보를 알아야 하는 경우가 있습니다. 고객에게 계산 메타데이터를 공유하도록 요청하면 지원 전문가가 Azure에서 VM의 종류에 대해 알 수 있도록 기본 정보가 제공될 수 있습니다. 

**요청**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-03-01"
```
**응답**
> [!NOTE] 
> 응답은 JSON 문자열입니다. 다음 출력은 [jq](https://stedolan.github.io/jq/)와 같은 유틸리티를 사용하여 JSON 형식으로 만들어졌습니다.
>

```
{
"compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }

}
```

## <a name="faq"></a>FAQ
1. 오류 400 잘못된 요청, 필요한 메타데이터 헤더가 지정되지 않음 오류가 발생했습니다. 무슨 의미인가요?
   * 인스턴스 메타데이터 서비스에서는 Metadata:true 헤더를 요청에 포함시켜 전달해야 합니다. REST 호출의 헤더를 전달하면 인스턴스 메타데이터 서비스에 액세스가 허용됩니다. 
2. VM에 대한 계산 정보를 구할 수 없는 이유가 무엇인가요?
   * 현재 인스턴스 메타데이터 서비스는 Azure Resource Manager가 생성한 인스턴스만 지원하며 Cloud Services VM에 대한 지원은 나중에 추가될 수 있습니다. 
3. 잠시 전에 Azure Resource Manager를 통해 내 가상 컴퓨터를 만들었습니다. 계산 메타데이터 정보가 왜 표시되지 않나요?
   * 2016년 9월 이후에 생성된 VM의 경우 계산 메타데이터를 표시하려면 [태그](../azure-resource-manager/resource-group-using-tags.md)를 추가하세요. 이전 VM(2016년 9월 전에 생성된)의 경우 메타데이터를 새로 고치도록 VM에 확장 또는 데이터 디스크를 추가/제거하세요.
4. 오류 500 - 내부 서버 오류가 발생하는 이유가 무엇인가요?
   * 현재 인스턴스 메타데이터 미리 보기는 미국 서부 중앙 지역에만 제공됩니다. VM을 지원되는 지역에 배포하세요.  
4. 추가 질문/의견은 어디에 공유하나요?
   * 의견은 feedback.azure.com에서 알려주세요.
    
## <a name="next-steps"></a>다음 단계

[scheduledevents]에 대한 자세한 정보(virtual-machines-scheduled-events.md)

