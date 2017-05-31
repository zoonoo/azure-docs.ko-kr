---
title: "미사용 데이터베이스 암호화 - Azure Cosmos DB | Microsoft Docs"
description: "Azure Cosmos DB가 모든 데이터의 기본 암호화를 제공하는 방법을 알아봅니다."
services: cosmosdb
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/31/2017
ms.author: voellm
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8248b429cf9b7fdfc709a68c4d1e88b7b660020b
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---

# <a name="azure-cosmos-db-database-encryption-at-rest"></a>미사용 Azure Cosmos DB 데이터베이스 암호화

미사용 암호화는 일반적으로 반도체 드라이브(SSD) 및 하드 디스크(HDD)와 같은 비휘발성 저장소 장치에서 데이터를 암호화하는 것을 말합니다.  Azure Cosmos DB는 주 데이터베이스를 SSD에 저장하고 미디어 첨부 파일 및 백업을 일반적으로 HDD가 지원되는 Azure Blob에 저장합니다.  Azure Cosmos DB에 대한 미사용 암호화가 릴리스됨에 따라 이제 모든 데이터베이스, 미디어 첨부 파일 및 백업이 암호화됩니다.  즉, 이제 데이터는 전송 중(네트워크를 통해)과 미사용 시(비휘발성 저장소) 암호화되므로 종단 간 암호화가 가능합니다.

Microsoft는 PaaS 서비스로, Azure Cosmos DB의 사용 용이성을 높이기 위해 열심히 노력해 왔습니다.  이러한 노력의 일환으로, Azure Cosmos DB에 저장된 모든 사용자 데이터는 사용자가 조치를 취하지 않아도 미사용 시 및 전송 시 암호화됩니다.  또한 미사용 암호화가 기본적으로 "설정" 상태라는 것도 이러한 노력 중 하나입니다.  이를 끄거나 켜는 제어 기능은 없으며 [가용성 및 성능 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/)를 지속적으로 충족하면서 이 기능을 제공했습니다.

## <a name="how-does-encryption-at-rest-work"></a>미사용 암호화 작동 방식

미사용 암호화는 보안 키 저장소 시스템, 암호화된 네트워크 및 암호화 API를 비롯한 수많은 보안 기술을 사용하여 구현되었습니다.  아래 다이어그램은 암호화된 데이터의 저장소와 키 관리가 어떻게 구분되는지를 보여 줍니다.  데이터를 암호 해독하고 처리하는 시스템은 키를 관리하는 시스템과 통신해야 합니다.

![디자인 다이어그램](./media/documentdb-nosql-database-encryption-at-rest/design-diagram.png)

사용자 요청의 기본 흐름은 다음과 같습니다.
- 사용자 데이터베이스 계정은 이미 준비되어 있고 저장소 키는 관리 서비스 리소스 공급자(RP)에 대한 요청을 통해 검색됩니다.
- 사용자는 HTTPS/보안 전송을 통해 Azure Cosmos DB에 대한 연결을 만듭니다(SDK가 세부 정보를 추상화함).
- 사용자는 이전에 만든 보안 연결을 통해 저장할 JSON 문서를 보냅니다.
- JSON 문서는 사용자가 인덱싱을 해제하지 않는다면, 인덱싱됩니다.
- JSON 문서와 인덱스 데이터 모두 보안 저장소에 기록됩니다.
- 데이터는 보안 저장소에서 주기적으로 읽어오고 Azure Encrypted Blob Store에 백업됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="q-how-much-more-does-azure-storage-cost-if-sse-is-enabled"></a>Q: SSE를 사용하는 경우 Azure Storage 비용은 얼마나 늘어나나요?
A: 추가 비용은 없습니다.

### <a name="q-who-manages-the-encryption-keys"></a>Q: 암호화 키는 누가 관리하나요?
A: 키는 Microsoft에서 관리합니다.

### <a name="q-how-often-are-encryption-keys-rotated"></a>Q: 얼마나 자주 암호화 키가 순환되나요?
A: Microsoft에는 DocumentDB에서 따르는 일련의 내부 지침이 있습니다.  특정 지침이 게시되지는 않지만, Microsoft는 내부 지침의 하위 집합이며 개발자에게 유용한 모범 사례를 포함하는 SDL(Security Development Lifecycle)[https://www.microsoft.com/sdl/default.aspx]을 게시합니다.

### <a name="q-can-i-use-my-own-encryption-keys"></a>Q: 나만의 암호화 키를 사용할 수 있나요?
A: Azure Cosmos DB는 PaaS 서비스로, Microsoft는 서비스의 사용 용이성을 높이기 위해 열심히 노력해 왔습니다.  이 질문이 PCI-DSS와 같은 규정 준수를 충족시키기 위한 프록시 질문으로 자주 제기되는 것을 확인했습니다.  이 기능을 구축하는 과정에서 준수 감사자와 협력하여 Azure Cosmos DB를 사용하는 고객이 키를 직접 관리할 필요없이 요구 사항을 충족할 수 있도록 했습니다.
이러한 이유로 현재 사용자에게 직접 키를 관리하는 부담을 주는 옵션을 제공합니다.


### <a name="q-what-regions-have-encryption-turned-on"></a>Q: 암호화가 설정된 지역은 어디인가요?
A: 모든 Azure Cosmos DB 지역에서 모든 사용자 데이터에 대해 암호화가 켜져 있습니다.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>Q: 암호화는 성능 대기 시간 및 처리량 SLA에 영향을 주나요?
A: 이제 기존의 모든 계정과 새 계정에 대해 미사용 암호화가 사용되므로 성능 SLA에 대한 영향이나 변경 내용은 없습니다.  최신 보장 내용을 보려면 [성능 SLA 페이지](https://azure.microsoft.com/support/legal/sla/documentdb)를 참조하세요.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>Q: 로컬 에뮬레이터가 미사용 암호화를 지원하나요?
A: 에뮬레이터는 독립 실행형 개발/테스트 도구이며 관리되는 DocumentDB 서비스에서 사용되는 키 관리 서비스를 사용하지 않습니다. 중요한 에뮬레이터 테스트 데이터를 저장할 드라이브에서 BitLocker를 사용하도록 설정하는 것이 좋습니다. 에뮬레이터는 기본 데이터 디렉터리 변경[https://docs.microsoft.com/azure/documentdb/documentdb-nosql-local-emulator] 및 잘 알려진 위치 사용을 지원합니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB 보안 및 최신 개선 사항에 대한 개요는 [Azure Cosmos DB 데이터베이스 보안](documentdb-nosql-database-security.md)을 참조하세요.

Microsoft 인증서에 대한 자세한 내용은 [Azure 보안 센터](https://azure.microsoft.com/en-us/support/trust-center/)를 참조하세요.

