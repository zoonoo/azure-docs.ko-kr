---
title: 제안 만들기 또는 수정-Azure Marketplace
description: 새 API를 만들거나 기존 제안을 업데이트 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/29/2020
ms.openlocfilehash: dba47073a6676b17c091af048f583ca3e7712999
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420229"
---
# <a name="create-or-modify-an-offer"></a>제안 만들기 또는 수정

> [!NOTE]
> Cloud 파트너 포털 Api는와 통합 되며 파트너 센터에서 계속 작업 합니다. 전환에는 작은 변화가 도입 됩니다. [CLOUD 파트너 포털 API 참조](./cloud-partner-portal-api-overview.md) 에 나열 된 변경 내용을 검토 하 여 파트너 센터로 전환 된 후 코드가 계속 작동 하는지 확인 합니다. CPP Api는 파트너 센터로 전환 하기 전에 이미 통합 된 기존 제품에만 사용 해야 합니다. 새 제품은 파트너 센터 제출 Api를 사용 해야 합니다.

이 호출은 게시자 네임스페이스 내에서 특정 제안을 업데이트하거나 새 제안을 만듭니다.

  `PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 매개 변수

|  **이름**         |  **설명**                      |  **데이터 형식**  |
|  --------         |  ----------------                     |  -------------  |
| publisherId       |  게시자 식별자입니다(예: `contoso`). |   String |
| offerId           |  제안 식별자입니다.                     |   String        |
| api-version       |  최신 버전의 API입니다.            |   Date           |
|  |  |  |

## <a name="header"></a>헤더

|  **이름**        |  **값**               |
|  ---------       |  ----------              | 
| 콘텐츠 형식     | `application/json`       |
| 권한 부여    | `Bearer YOUR_TOKEN`      |
|  |  |

## <a name="body-example"></a>본문 예제

다음 예제에서는 offerID가 `contosovirtualmachine`인 제안을 만듭니다.

### <a name="request"></a>요청

``` json
  {
      "publisherId": "contoso",
      "offerTypeId": "microsoft-azure-virtualmachines",
      "id": "contosovirtualmachine",
      "offerTypeVersions": {
          "microsoft-azure-virtualmachines": 87,
          "microsoft-azure-marketplace": 39
      },
      "definition": {
          "displayText": "Contoso Virtual Machine Offer",
          "offer": {
          "microsoft-azure-marketplace.title": "Contoso App",
          "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
          "microsoft-azure-marketplace.allowedSubscriptions": ["59160c40-2e25-4dcf-a2fd-6514cb08bf08"],
          "microsoft-azure-marketplace.usefulLinks": [{
              "linkTitle": "Contoso App for Azure",
              "linkUrl": "https://azuremarketplace.microsoft.com"
          }],
          "microsoft-azure-marketplace.categoryMap": [
                {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                        "visualization-and-reporting"
                    ]
                },
                {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning":[
                        "bot-services",
                        "cognitive-services",
                        "other"
                    ]
                }
            ],
          "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
          "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.heroLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/c46ec74d-d214-4fb5-9082-3cee55200eba.png?sv=2014-02-14&sr=b&sig=RfDvjowFGpP4WZGAHylbF2CuXwO2NXOrwycrXEJvJI4%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.screenshots": [],
          "microsoft-azure-marketplace.videos": [],
          "microsoft-azure-marketplace.leadDestination": "None",
          "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
          "microsoft-azure-marketplace.termsOfUse": "Terms of use",
          "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
          "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.supportContactName": "Jon Doe",
          "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.publicAzureSupportUrl": "",
          "microsoft-azure-marketplace.fairfaxSupportUrl": ""
      },
      "plans": 
      [
          {
              "planId": "contososkuidentifier",
              "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
              "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
              "microsoft-azure-virtualmachines.cloudAvailability": ["PublicAzure"],
              "virtualMachinePricing": {
                  "isByol": true,
                  "freeTrialDurationInMonths": 0
              },
              "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
              "microsoft-azure-virtualmachines.windowsOSType": "Other",
              "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
              "microsoft-azure-virtualmachines.recommendedVMSizes": ["a0-basic", "a0-standard", "a1-basic", "a1-standard", "a2-basic", "a2-standard"],
              "microsoft-azure-virtualmachines.openPorts": [],
              "microsoft-azure-virtualmachines.vmImages": 
              {
                  "1.0.1": 
                  {
                      "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                      "lunVhdDetails": []
                  }
              },
              "regions": ["AZ"]
          }
          ]
      },
      "eTag": "W/\"datetime'2017-06-07T06%3A15%3A40.4771399Z'\"",
      "version": 5
  }
```

### <a name="response"></a>응답

``` json
 {
         "offerTypeId": "microsoft-azure-virtualmachines",
         "publisherId": "contoso",
         "status": "neverPublished",
         "id": "contosovirtualmachine",
         "version": 1,
         "definition": {
         "displayText": "Contoso Virtual Machine Offer",
         "offer": 
         {
             "microsoft-azure-marketplace-testdrive.videos": [],
             "microsoft-azure-marketplace.title": "Contoso App",
             "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
             "microsoft-azure-marketplace.allowedSubscriptions": 
             [
                 "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
             ],
             "microsoft-azure-marketplace.usefulLinks": 
             [
                 {
                     "linkTitle": "Contoso App for Azure",
                     "linkUrl": "https://azuremarketplace.microsoft.com"
                 }
             ],
             "microsoft-azure-marketplace.categoryMap":
             [
                 {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                    "visualization-and-reporting"
                    ]
                 },
                 {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning": [
                    "bot-services",
                    "cognitive-services",
                    "other"
                    ]
                 }
             ],
             "microsoft-azure-marketplace.smallLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/8affcd28-60a5-4839-adf8-c560e069fd61.png?sv=2014-02-14&sr=b&sig=nGErAgn%2BDUecrX892wcmk32kh0MHgIZeJ5jcKyY%2Fuew%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.mediumLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/39550bca-1110-432c-9ea9-e12b3a2288cd.png?sv=2014-02-14&sr=b&sig=4X0hlkXYtuZOmcYq%2BsbYVZz3k5k26kngcFX6yBAJjNI%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.largeLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/ce3576e3-df12-4074-b0a3-0b8d3f329df1.png?sv=2014-02-14&sr=b&sig=mFhtCUQh%2FbFz10nlIWbqsz6jq5MBZ0M%2F5cIREE9P6V0%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.wideLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/476d6edd-12d3-4414-9def-d2970c4a9de4.png?sv=2014-02-14&sr=b&sig=pg4MDSZjAb8w8D%2FrQ9RT%2BodpynSy%2FlYOvpx0yeam2Bw%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.heroLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/46c85b7b-4438-4e0d-8218-24fb5651727a.png?sv=2014-02-14&sr=b&sig=wIsCOO5%2BDj8NsLVSwwzwTgogF71oA7Q1XjKhNB1ni5g%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.screenshots": [],
             "microsoft-azure-marketplace.videos": [],
             "microsoft-azure-marketplace.leadDestination": "None",
             "microsoft-azure-marketplace.tableLeadConfiguration": {},
             "microsoft-azure-marketplace.blobLeadConfiguration": {},
             "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
             "microsoft-azure-marketplace.crmLeadConfiguration": {},
             "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
             "microsoft-azure-marketplace.marketoLeadConfiguration": {},
             "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
             "microsoft-azure-marketplace.termsOfUse": "Terms of use",
             "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
             "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.supportContactName": "Jon Doe",
             "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.publicAzureSupportUrl": "",
             "microsoft-azure-marketplace.fairfaxSupportUrl": ""
         },
         "plans": 
         [
             {
                 "planId": "contososkuidentifier",
                 "microsoft-azure-virtualmachines.skuTitle": "Contoso App (Old Title)",
                 "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                 "microsoft-azure-virtualmachines.cloudAvailability": 
                 [
                     "PublicAzure"
                 ],
                 "microsoft-azure-virtualmachines.certificationsFairfax": [],
                 "virtualMachinePricing": {
                     "isByol": true,
                     "freeTrialDurationInMonths": 0
                 },
                 "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                 "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                 "microsoft-azure-virtualmachines.recommendedVMSizes": 
                 [
                     "a0-basic",
                     "a0-standard",
                     "a1-basic",
                     "a1-standard",
                     "a2-basic",
                     "a2-standard"
                 ],
                 "microsoft-azure-virtualmachines.openPorts": [],
                 "microsoft-azure-virtualmachines.vmImages": 
                 {
                     "1.0.1": 
                     {
                         "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                         "lunVhdDetails": []
                     }
                 },
                 "regions":
                 [
                     "AZ"
                 ]
             } 
         ]
     },
     "changedTime": "2018-03-28T22:27:13.8363879Z"
 }
```

> [!NOTE]
> 이 제안을 수정하려면 위 요청에 **If-Match** 헤더 집합을 추가합니다. 위와 동일한 요청 본문을 사용하지만 필요에 따라 값을 수정합니다. 

### <a name="response-status-codes"></a>응답 상태 코드

| **코드**  |  **설명**                                                                            |
| --------  |  ---------------                                                                            |
|  200      | `OK`. 요청이 성공적으로 처리되었으며 제안이 수정되었습니다.           |
|  201      | `Created`. 요청이 성공적으로 처리되었으며 제안이 만들어졌습니다.   |
|  400      | `Bad/Malformed request`. 오류 응답 본문이 자세한 정보를 제공할 수 있습니다.            |
|  403      | `Forbidden`. 클라이언트에서 요청된 네임스페이스에 액세스할 수 없습니다.                     |
|  404      | `Not found`. 클라이언트에서 참조한 엔터티가 존재하지 않습니다.                           |
|  412      | 요청자가 요청에 지정한 사전 조건 중 하나를 서버가 충족하지 않습니다. 클라이언트가 요청과 함께 전송된 ETAG를 확인해야 합니다. |
|  |  |

## <a name="uploading-artifacts"></a>아티팩트 업로드 중

위 예제와 같이 이미지, 로고 등의 아티팩트를 웹의 액세스 가능 위치에 업로드한 후 각각을 PUT 요청에 URI로 포함하여 공유해야 합니다. 시스템은 이러한 파일이 Azure Marketplace 저장소에 존재 하지 않는 것을 감지 하 고 이러한 파일을 저장소로 다운로드 합니다.  결과적으로 이후 GET 요청에서 이러한 파일에 대 한 Azure Marketplace 서비스 URL을 반환 한다는 것을 알 수 있습니다.

## <a name="categories-and-industries"></a>범주 및 산업

새 제품을 만들 때 marketplace에서 제품에 대 한 범주를 지정 해야 합니다. 필요에 따라 일부 제공 유형에 대해 산업을 지정할 수도 있습니다. 제안 유형에 따라 다음 표의 특정 키 값을 사용 하 여 제품에 적용 가능한 범주/산업을 제공 합니다.

### <a name="azure-marketplace-categories"></a>Azure Marketplace 범주

이러한 범주 및 해당 키는 Azure 앱, Virtual Machines, 핵심 Virtual Machines, 컨테이너, 컨테이너 앱, IoT Edge 모듈 및 SaaS 제품 유형에 적용 됩니다. 굵은 기울임꼴 (예: ***분석***)의 항목은 범주 이며 표준 텍스트 항목 (예: 데이터-정보)은 하위 범주입니다. 공백 또는 대문자 표시를 변경 하지 않고 정확한 키 값을 사용 합니다.

| 범주 | SaaS 키 | Azure 앱 키 | 가상 컴퓨터, 컨테이너, 컨테이너 앱, IoT Edge 모듈, 코어 가상 컴퓨터 키 |
| --- | --- | --- | --- |
| ***분석*** | ***analytics*** | ***분석-azure-앱*** | ***분석-amp*** |
| 데이터 정보 | 데이터-정보 | 데이터-정보 | 데이터-정보 |
| 데이터 분석 | 데이터 분석 | 데이터 분석 | 데이터 분석 |
| 빅 데이터 | 빅 데이터 | 데이터 데이터 | 빅 데이터 |
| 예측 분석 | 예측-분석 | 예측-분석 | 예측-분석 |
| 실시간/스트리밍 분석 | 실시간 스트리밍-분석 | 실시간 스트리밍-분석 | 실시간 스트리밍-분석 |
| 기타 | 기타 | 기타-분석 | 기타 |
| ***AI + Machine Learning*** | ***ArtificialIntelligence*** | ***ai + 기계 학습*** | ***ai + 기계 학습*** |
| Bot 서비스 | bot-서비스 | bot-서비스 | bot-서비스 |
| Cognitive Services | 인지-서비스 | 인지-서비스 | 인지-서비스 |
| ML 서비스 | ml-서비스 | ml-서비스 | ml-서비스 |
| 자동화된 ML | 자동-ml | 자동-ml | 자동-ml |
| 비즈니스/로보틱 프로세스 자동화 | 비즈니스-로보틱-프로세스-자동화 | 비즈니스-로보틱-프로세스-자동화 | 비즈니스-로보틱-프로세스-자동화 |
| 데이터 Labelling | 데이터-labelling | 데이터-labelling | 데이터-labelling |
| 데이터 준비 | 데이터-준비 | 데이터-준비 | 데이터-준비 |
| 지식 마이닝 | 지식-마이닝 | 지식-마이닝 | 지식-마이닝 |
| ML 작업 | ml-작업 | ml-작업 | ml-작업 |
| 기타 | 기타-AI-기계 학습 | 기타 | 기타 |
| ***블록체인*** | ***blockchain*** | ***blockchain*** | ***blockchain*** |
| 앱 가속기 | 앱-가속기 | 앱-가속기 | 앱-가속기 |
| 단일 노드 원장 | 단일 노드 원장 | 단일 노드 원장 | 단일 노드 원장 |
| 다중 노드 원장 | 다중 노드 원장 | 다중 노드 원장 | 다중 노드 원장 |
| 도구 | 도구 | 도구 | 도구 |
| 기타 | 기타 | 기타 | 기타 |
| ***컴퓨팅*** | ***계산-saas*** | ***계산-azure-앱*** | ***계산*** |
| 응용 프로그램 인프라 | appInfra | appInfrastructure | 응용 프로그램-인프라 |
| 운영 체제 | clientOS | clientOS | 운영 체제 |
| 캐시 | 캐시 | 캐시 | 캐시 |
| 기타 | 기타-계산 | 기타-계산 | 기타 |
| ***컨테이너*** | ***컨테이너가*** | ***컨테이너가*** | ***컨테이너가*** |
| 컨테이너 앱 | 컨테이너-앱 | 컨테이너-앱 | 컨테이너-앱 |
| 컨테이너 이미지 | 컨테이너-이미지 | 컨테이너-이미지 | 컨테이너-이미지 |
| 컨테이너 시작 | 시작-컨테이너 포함 | 시작-컨테이너 포함 | 시작-컨테이너 포함 |
| 기타 | 기타 | 기타 | 기타 |
| ***데이터베이스*** | ***데이터베이스-saas*** | ***database*** | ***데이터베이스인*** |
| NoSQL 데이터베이스 | nosql-데이터베이스 | nosql-데이터베이스 | nosql-데이터베이스 |
| 관계형 데이터베이스 | 관계형 데이터베이스 | 관계형 데이터베이스 | 관계형 데이터베이스 |
| 원장/Blockchain 데이터베이스 | 원장 blockchain-데이터베이스 | 원장 blockchain-데이터베이스 | 원장 blockchain-데이터베이스 |
| 데이터 레이크 | 데이터-레이크 | 데이터-레이크 | 데이터-레이크 |
| 데이터 웨어하우스 | 데이터 웨어하우스 | 데이터 웨어하우스 | 데이터 웨어하우스 |
| 기타 | 기타-데이터베이스 | 기타-데이터베이스 | 기타 |
| ***개발자 도구*** | ***개발자-saas*** | ***개발자-도구-azure-앱*** | ***개발자-도구*** |
| 도구 | 도구-개발자-도구 | 도구-개발자-도구 | 도구-개발자-도구 |
| 스크립트 | 스크립트 | 스크립트 | 스크립트 |
| 개발자 서비스 | devService | devService | 개발자-서비스 |
| 기타 | 기타-개발자-도구 | 기타-개발자-도구 | 기타 |
| ***DevOps*** | ***devops*** | ***devops*** | ***devops*** |
| 기타 | 기타 | 기타 | 기타 |
| ***ID*** | ***identity*** | ***identity*** | ***identity*** |
| 액세스 관리 | 액세스 관리 | 액세스 관리 | 액세스 관리 |
| 기타 | 기타 | 기타 | 기타 |
| ***통합*** | ***통합할*** | ***통합할*** | ***통합할*** |
| 메시징 | 메시징 | 메시징 | 메시징 |
| 기타 | 기타 | 기타 | 기타 |
| ***사물 인터넷*** | ***IoT*** | ***사물 인터넷-azure-앱*** | ***사물 인터넷*** |
| IoT 핵심 서비스 | 해당 없음 | iot-핵심 서비스 | iot-핵심 서비스 |
| IoT Edge 모듈 | 해당 없음 | iot-에 지-모듈 | iot-에 지-모듈 |
| IoT 솔루션 | iot-솔루션 | iot-솔루션 | iot-솔루션 |
| 데이터 분석 & 시각화 | 데이터 분석 및 시각화 | 데이터 분석 및 시각화 | 데이터 분석 및 시각화 |
| IoT 연결 | iot-연결 | iot-연결 | iot-연결 |
| 기타 | 기타-사물 인터넷 | 기타-사물 인터넷 | 기타 |
| ***IT & 관리 도구*** | ***ITandAdministration*** | ***it-및 관리-도구-azure-앱*** | ***it 및 관리-도구*** |
| 관리 솔루션 | 관리-솔루션 | 관리-솔루션 | 관리-솔루션 |
| 비즈니스 애플리케이션 | businessApplication | businessApplication | 비즈니스 응용 프로그램 |
| 기타 | 기타-관리-도구 | 기타-관리-도구 | 기타 |
| ***모니터링 & 진단*** | ***모니터링-및 진단*** | ***모니터링-및 진단*** | ***모니터링-및 진단*** |
| 기타 | 기타 | 기타 | 기타 |
| ***미디어*** | ***media*** | ***media*** | ***media*** |
| Media Services | media-services | media-services | media-services |
| Content Protection | content-protection | content-protection | content-protection |
| 라이브 & 주문형 스트리밍 | 주문형 라이브 스트리밍 | 주문형 라이브 스트리밍 | 주문형 라이브 스트리밍 |
| 기타 | 기타 | 기타 | 기타 |
| ***마이그레이션*** | ***마이그레이션이나*** | ***마이그레이션이나*** | ***마이그레이션이나*** |
| 데이터 마이그레이션 | 데이터 마이그레이션 | 데이터 마이그레이션 | 데이터 마이그레이션 |
| 기타 | 기타 | 기타 | 기타 |
| ***Mixed Reality*** | ***mixed-reality*** | ***mixed-reality*** | ***mixed-reality*** |
| 기타 | 기타 | 기타 | 기타 |
| ***네트워킹*** | ***lan*** | ***lan*** | ***lan*** |
| 어플라이언스 관리자 | 어플라이언스-관리자 | 어플라이언스-관리자 | 어플라이언스-관리자 |
| 연결 | 연결 | 연결 | 연결 |
| 방화벽 | 방화벽 | 방화벽 | 방화벽 |
| 부하 분산 장치 | 부하 분산 장치 | 부하 분산 장치 | 부하 분산 장치 |
| 기타 | 기타 | 기타 | 기타 |
| ***보안*** | ***보안*** | ***보안*** | ***보안*** |
| ID 및 액세스 관리 | id 및 액세스 관리 | id 및 액세스 관리 | id 및 액세스 관리 |
| Threat Protection | 위협 방지 | 위협 방지 | 위협 방지 |
| Information Protection | 정보-보호 | 정보-보호 | 정보-보호 |
| 기타 | 기타 | 기타 | 기타 |
| ***스토리지*** | ***저장소-saas*** | ***저장소-azure-앱*** | ***storage*** |
| 백업 & 복구 | 백업(backup) | 백업(backup) | 백업 및 복구 |
| 엔터프라이즈 하이브리드 저장소 | 엔터프라이즈급-하이브리드 저장소 | 엔터프라이즈급-하이브리드 저장소 | 엔터프라이즈급-하이브리드 저장소 |
| 파일 공유 | 파일 공유 | 파일 공유 | 파일 공유 |
| 데이터 수명 주기 관리 | 데이터 수명 주기-관리 | 데이터 수명 주기-관리 | 데이터 수명 주기-관리 |
| 기타 | 기타-저장소 | 기타-저장소 | 기타 |
| ***Web*** | ***web*** | ***web*** | ***web*** |
| 블로그 & CMSs | 블로그-및-cmss | 블로그-및-cmss | 블로그-및-cmss |
| 스타터 Web Apps | starter-웹-앱 | starter-웹-앱 | starter-웹-앱 |
| 전자 | 전자 | 전자 | 전자 |
| Web Apps 프레임 워크 | 웹 앱-프레임 워크 | 웹 앱-프레임 워크 | 웹 앱-프레임 워크 |
| Web Apps | 웹 앱 | 웹 앱 | 웹 앱 |
| 기타 | 기타 | 기타 | 기타 |
||||

### <a name="microsoft-appsource-categories"></a>Microsoft AppSource 범주

이러한 범주 및 해당 키는 SaaS, PowerBI 앱, Dynamics 365 business central, Dynamics 365 for customer engagement 및 작업 제공 유형에 대 한 Dynamics 365에 적용 됩니다. 굵은 기울임꼴 (예: ***분석***)의 항목은 범주 이며 표준 텍스트 항목 (예: 고급 분석)은 하위 범주입니다. 공백 또는 대문자 표시를 변경 하지 않고 정확한 키 값을 사용 합니다.

| 범주 | SaaS 키 | Dynamics 365 business central, Dynamics 365 for customer engagement, 작업 키에 대 한 Dynamics 365 | PowerBI 앱 키 |
| --- | --- | --- | --- |
| ***분석*** | ***analytics*** | ***분석*** | ***분석*** |
| 고급 분석 | 고급-분석 | 고급-분석 | 고급-분석 |
| 시각화 & 보고 | 시각화-보고 | 시각화-보고 | 시각화-보고 |
| 기타 | 기타 | 기타-분석 | 기타-분석 |
| ***AI + Machine Learning*** | ***ArtificialIntelligence*** | ***ai와 기계 학습-dynamics*** | ***ai와 기계 학습-appsource*** |
| 비즈니스 AI | 비즈니스 ai | 비즈니스 ai | 비즈니스 ai |
| Bot 앱 | 봇-앱 | 봇-앱 | 봇-앱 |
| 기타 | 기타-ai-기계 학습 | 기타-ai-기계 학습 | 기타-ai-기계 학습 |
| ***협업*** | ***협업*** | ***협업*** | ***작업할*** |
| 담당자 & 담당자 | 연락처-사람 | 연락처-사람 | 연락처-사용자 |
| 모임 관리 | 모임 관리 | 모임 관리 | 모임 관리 |
| 사이트 디자인 & 관리 | 사이트-디자인-관리 | 사이트-디자인-관리 | 사이트 디자인 및 관리 |
| 작업 & 프로젝트 관리 | 작업-프로젝트 관리 | 작업-프로젝트 관리 | 작업 및 프로젝트 관리 |
| 음성 & 비디오 회의 | 음성-비디오-회의 | 음성-비디오-회의 | 음성 및 비디오 회의 |
| 기타 | 기타-공동 작업 | 기타-공동 작업 | 기타 |
| ***규정 준수 & 법적*** | ***정한*** | ***정한*** | ***규정 준수 및 법적 고 지*** |
| 세금 & 감사 | 세금 감사 | 세금 감사 | 세금 및 감사 |
| 법적 정보 | 법적 정보 | 법적 정보 | legal |
| 데이터, 거 버 넌 스 & 개인 정보 | 데이터-거 버 넌 스-개인 정보 | 데이터-거 버 넌 스-개인 정보 | 데이터-거 버 넌 스 및 개인 정보 |
| 상태 & 안전 | 상태-안전 | 상태-안전 | 상태 및 안전 |
| 기타 | 기타 규정 준수-법적 | 기타 규정 준수-법적 | 기타 |
| ***Customer Service*** | ***CustomerService*** | ***CustomerService*** | ***고객 서비스*** |
| 연락처 센터 | 연락처-센터 | 연락처-센터 | 연락처-센터 |
| Face 서비스 | 얼굴 대 얼굴 서비스 | 얼굴 대 얼굴 서비스 | 얼굴 대 얼굴 서비스 |
| Office & Employee Service 백업 | 사무실-직원-서비스 | 사무실-직원-서비스 | 사무실 및 직원-서비스 |
| 기술 자료 & 사례 관리 | 기술 항목-관리 | 기술 항목-관리 | 지식과 사례 관리 |
| 소셜 미디어 & Omnichannel Engagement | omnichannel-engagement | omnichannel-engagement | 소셜-미디어 및-omnichannel |
| 기타 | 기타-고객-서비스 | 기타-고객-서비스 | 기타 |
| ***재무*** | ***재무*** | ***재무*** | ***부서*** |
| 회계 | 회계 | 회계 | 회계 |
| 자산 관리 | 자산 관리 | 자산 관리 | 자산 관리 |
| 분석, 통합 & 보고 | 분석-통합-보고 | 분석-통합-보고 | 분석-통합 및 보고 |
| 크레딧 & 컬렉션 | 크레딧-컬렉션 | 크레딧-컬렉션 | 크레딧 및 컬렉션 |
| 규정 준수 & 위험 관리 | 규정 준수-위험 관리 | 규정 준수-위험 관리 | 규정 준수 및 위험 관리 |
| 기타 | 기타-재무 | 기타-재무 | 기타 |
| ***Human Resources*** | ***HumanResources*** | ***HumanResources*** | ***인적 자원*** |
| 인재 획득 | 인재-취득 | 인재-취득 | 인재-취득 |
| 인재 관리 | 인재-관리 | 인재-관리 | 인재-관리 |
| HR 작업 | hr-작업 | hr-작업 | hr-작업 |
| 직원 계획 & 분석 | 직원-계획-분석 | 직원-계획-분석 | 직원-계획 및 분석 |
| 기타 | 기타-인적 자원 | 기타-인적 자원 | 기타 |
| ***사물 인터넷*** | ***IoT*** | ***사물 인터넷*** | ***사물 인터넷-appsource*** |
| Asset Management & 작업 | 자산 관리-작업 | 자산 관리-작업 | 자산 관리-및 작업 |
| 연결 된 제품 | 연결 됨-제품 | 연결 됨-제품 | 연결 됨-제품 |
| 인텔리전트 공급망 | intelligent 공급망 | intelligent 공급망 | intelligent 공급망 |
| 예측 유지 관리 | 예측 유지 관리 | 예측 유지 관리 | 예측 유지 관리 |
| 원격 모니터링 | 원격 모니터링 | 원격 모니터링 | 원격 모니터링 |
| 안전 & 보안 | 안전-보안 | 안전-보안 | 안전 및 보안 |
| 스마트 인프라 & 리소스 | 스마트 인프라-리소스 | 스마트 인프라-리소스 | 스마트 인프라 및 리소스 |
| 차량 & 이동성 | 차량-이동성 | 차량-이동성 | 차량 및 이동성 |
| 기타 | 기타-사물 인터넷 | 기타-사물 인터넷 | 기타 |
| ***IT & 관리 도구*** | ***ITandAdministration*** | ***ITandAdministration*** | ***it 및 관리-도구*** |
| 관리 솔루션 | 관리-솔루션 | 관리-솔루션 | 관리-솔루션 |
| 비즈니스 애플리케이션 | businessApplication | businessApplication | 비즈니스 응용 프로그램 |
| 기타 | 기타-관리-도구 | 기타-관리-도구 | 기타 |
| ***마케팅*** | ***마케팅*** | ***마케팅*** | ***마케팅*** |
| 광고가 | 광고가 | 광고가 | 광고가 |
| 분석 | 분석-마케팅 | 분석-마케팅 | 분석-마케팅 |
| 캠페인 관리 & 자동화 | 캠페인-관리-자동화 | 캠페인-관리-자동화 | 캠페인 관리-및-자동화 |
| 전자 메일 마케팅 | 전자 메일-마케팅 | 전자 메일-마케팅 | 전자 메일-마케팅 |
| L2-이벤트 & 리소스 관리 | 이벤트-리소스 관리 | 이벤트-리소스 관리 | 이벤트 및 리소스 관리 |
| & 분석 연구 | 연구 분석 | 연구 분석 | 연구 분석 |
| 소셜 미디어 | 소셜-미디어 | 소셜-미디어 | 소셜-미디어 |
| 기타 | 기타-마케팅 | 기타-마케팅 | 기타 |
| ***작업 & 공급망*** | ***OperationsSupplyChain*** | ***OperationsSupplyChain*** | ***작업 및 공급망*** |
| Asset & 프로덕션 관리 | 자산-프로덕션 관리 | 자산-프로덕션 관리 | 자산 및 프로덕션 관리 |
| 수요 예측 | 수요 예측 | 수요 예측 | 수요 예측 |
| 정보 관리 & 연결 | 정보-관리-연결 | 정보-관리-연결 | 정보 관리 및 연결 |
| 계획, 구매 & 보고 | 계획-구매-보고 | 계획-구매-보고 | 계획-구매 및 보고 |
| 품질 & 서비스 관리 | 품질-서비스 관리 | 품질-서비스 관리 | 품질 및 서비스 관리 |
| 판매 & 주문 관리 | 판매 주문 관리 | 판매 주문 관리 | 판매 및 주문 관리 |
| 교통 & 웨어하우스 관리 | 교통-웨어하우스-관리 | 교통-웨어하우스-관리 | 교통 및 웨어하우스 관리 |
| 기타 | 기타-작업-공급망 | 기타-작업-공급망 | 기타 |
| ***생산성*** | ***생산성*** | ***생산성*** | ***업무*** |
| 콘텐츠 생성 & 관리 | 콘텐츠 생성-관리 | 콘텐츠 생성-관리 | 콘텐츠 생성 및 관리 |
| 언어 & 번역 | 언어-번역 | 언어-번역 | 언어 및 번역 |
| 문서 관리 | 문서 관리 | 문서 관리 | 문서 관리 |
| 전자 메일 관리 | 전자 메일 관리 | 전자 메일 관리 | 전자 메일 관리 |
| 검색 & 참조 | 검색-참조 | 검색-참조 | 검색 및 참조 |
| 기타 | 기타-생산성 | 기타-생산성 | 기타 |
| 게이미피케이션 | 게이미피케이션 | 게이미피케이션 | gamification |
| ***Sales*** | ***Sales*** | ***Sales*** | ***Sales*** |
| 영업 | 영업 | 영업 | 영업 |
| 구성, 가격, 견적 (CPQ) | 구성-가격-견적 | 구성-가격-견적 | 구성-가격-견적 |
| 계약 관리 | 계약 관리 | 계약 관리 | 계약 관리 |
| CRM | crm | crm | crm |
| 전자 상거래 | 전자 상거래 | 전자 상거래 | 전자 상거래 |
| 비즈니스 데이터 보강 | 비즈니스 데이터-보강 | 비즈니스 데이터-보강 | 비즈니스 데이터-보강 |
| 판매 활성화 | 판매-활성화 | 판매-활성화 | 판매-활성화 |
| 기타 | 기타-판매 | 기타-판매 | 기타-판매 |
| ***지리적 위치*** | ***지리적*** | ***지리적*** | ***지리적*** |
| 지도 | maps | maps | maps |
| 뉴스 & 날씨 | 뉴스 및 날씨 | 뉴스 및 날씨 | 뉴스 및 날씨 |
| 기타 | 기타-지리적 위치 | 기타-지리적 위치 | 기타-지리적 위치 |
||||

### <a name="microsoft-appsource-industries"></a>Microsoft AppSource 산업

이러한 산업 및 해당 키는 SaaS, PowerBI 앱, Dynamics 365 business central, Dynamics 365 for customer engagement 및 작업 제공 유형에 대 한 Dynamics 365에 적용 됩니다. 굵은 기울임꼴 (예: ***자동차***)의 항목은 범주 이며 표준 텍스트 항목 (예: AutomotiveL2)은 하위 범주입니다. 공백 또는 대문자 표시를 변경 하지 않고 정확한 키 값을 사용 합니다.

| 업종 | SaaS, Dynamics 365 business central, Dynamics 365 for customer engagement, 작업 키에 대 한 Dynamics 365 | PowerBI 앱 키 |
| --- | --- | --- |
| ***자동차*** | ***자동차*** | ***사업*** |
| 자동차 | AutomotiveL2 | AutomotiveL2 |
| ***농업*** | ***농업*** | ***농업*** |
| 기타-분할 되지 않음 | 분할 되지 않은 농업 \_ | 기타-분할 되지 않음 |
| ***배포*** | ***배포*** | ***분산*** |
| 도매 | 도매 | 도매 |
| Parcel & 패키지 전달 | ParcelAndPackageShipping | parcel-및 패키지 전달 |
| ***Education*** | ***Education*** | ***교육과*** |
| 높은 교육 | HigherEducation | 높은 교육 |
| 기본 & 보조 교육/K-12 | PrimaryAndSecondaryEducationK12 | 기본-및 보조-교육 |
| 라이브러리 & Museums | LibrariesAndMuseums | 라이브러리-및-museums |
| ***금융 서비스*** | ***FinancialServices*** | ***금융 서비스*** |
| 은행 & 자본 시장 | BankingAndCapitalMarkets | 은행 및 자본-시장 |
| 보험 | 보험 | 보험 |
| ***정부*** | ***정부*** | ***자치*** |
| 방어 & 인텔리전스 | DefenseAndIntelligence | 방어 및 인텔리전스 |
| 공용 안전 & | PublicSafetyAndJustice | 공용 안전 성과 |
| Civilian 정부 | CivilianGovernment | civilian-정부 |
| ***의료*** | ***HealthCareandLifeSciences*** | ***보건*** |
| 상태 보험사 | HealthPayor | 상태-보험사 |
| 상태 제공자 | HealthProvider | 상태-공급자 |
| 제약 | 제약 | 제약 회사 |
| ***제조 & 리소스*** | ***제조업*** | ***제조 및 리소스*** |
| 화학 & Agrochemical | ChemicalAndAgrochemical | 화학 및 agrochemical |
| 불연속 제조 | DiscreteManufacturing | 불연속-제조 |
| 에너지 | 에너지 | energy |
| ***소매 & 소비자 상품*** | ***RetailandConsumerGoods*** | ***소매 및 소비자-상품*** |
| 소비자 상품 | ConsumerGoods | 소비자 상품 |
| 업체 | 업체 | 업체 |
| ***미디어 & 통신*** | ***MediaAndCommunications*** | ***미디어 및 통신*** |
| 미디어 & 엔터테인먼트 | MediaandEntertainment | 미디어 및 엔터테인먼트 |
| 전자 통신 | 전자 통신 | 통신 |
| ***전문 서비스*** | ***ProfessionalServices*** | ***전문 서비스*** |
| 법적 정보 | 법적 정보 | legal |
| 파트너 전문가 서비스 | PartnerProfessionalServices | 파트너-전문가-서비스 |
| ***아키텍처 & 생성*** | ***ArchitectureAndConstruction*** | ***아키텍처 및 생성*** |
| 기타-분할 되지 않음 | 분할 되지 않은 ArchitectureAndConstruction \_ | 기타-분할 되지 않음 |
| ***숙박 & 여행*** | ***HospitalityandTravel*** | ***숙박 및 여행*** |
|    호텔 & 레저 | HotelsAndLeisure | 호텔-및-레저 |
| 여행 및 교통 | TravelAndTransportation | 여행 및 교통 |
| 식당 & 음식 서비스 | RestaurantsAndFoodServices | 식당 및 음식-서비스 |
| ***기타 공공 부문 산업*** | ***OtherPublicSectorIndustries*** | ***기타-공공 부문-산업*** |
| 임업 & 낚 싯 | ForestryAndFishing | 임업 |
| 비영리 조직 | 비영리 조직 | 비영리 조직 |
| ***부동산*** | ***Realestate-us-sample*** | ***부동산*** |
| 기타-분할 되지 않음 | 분할 되지 않은 Realestate-us-sample \_ | 기타-분할 되지 않음 |
|||
