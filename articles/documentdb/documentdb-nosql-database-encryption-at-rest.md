---
title: "미사용 NoSQL 데이터베이스 암호화 - Azure DocumentDB | Microsoft Docs"
description: "Azure DocumentDB가 모든 NoSQL 데이터에 대해 기본 암호화를 어떻게 제공하는지 알아봅니다."
services: documentdb
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/31/2017
ms.author: voellm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: 9c97f76d581935cb85f33b0016c192ded3b6f880
ms.contentlocale: ko-kr
ms.lasthandoff: 04/28/2017


---

# <a name="documentdb-nosql-database-encryption-at-rest"></a>미사용 DocumentDB NoSQL 데이터베이스 암호화

미사용 암호화는 일반적으로 반도체 드라이브(SSD) 및 하드 디스크(HDD)와 같은 비휘발성 저장소 장치에서 데이터를 암호화하는 것을 말합니다.  DocumentDB는 주 데이터베이스를 SSD에 저장하고 미디어 첨부 및 백업을 일반적으로 HDD가 지원되는 Azure Blob에 저장합니다.  DocumentDB에 대한 미사용 암호화가 릴리스됨에 따라 이제 모든 데이터베이스, 미디어 첨부 및 백업이 암호화됩니다.  즉, 이제 데이터는 전송 중(네트워크를 통해)과 미사용 시(비휘발성 저장소) 암호화되므로 종단 간 암호화가 가능합니다.

Microsoft는 PaaS 서비스로, DocumentDB의 사용 용이성을 높이기 위해 열심히 노력해 왔습니다.  이러한 노력의 일환으로, DocumentDB에 저장된 모든 사용자 데이터는 사용자가 조치를 취하지 않아도 미사용 시 및 전송 시 암호화됩니다.  또한 미사용 암호화가 기본적으로 "설정" 상태라는 것도 이러한 노력 중 하나입니다.  이를 끄거나 켜는 제어 기능은 없으며 [가용성 및 성능 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/)를 지속적으로 충족하면서 이 기능을 제공했습니다.

## <a name="how-does-encryption-at-rest-work"></a>미사용 암호화 작동 방식

미사용 암호화는 보안 키 저장소 시스템, 암호화된 네트워크 및 암호화 API를 비롯한 수많은 보안 기술을 사용하여 구현되었습니다.  아래 다이어그램은 암호화된 데이터의 저장소와 키 관리가 어떻게 구분되는지를 보여 줍니다.  데이터를 암호 해독하고 처리하는 시스템은 키를 관리하는 시스템과 통신해야 합니다.

![디자인 다이어그램](./media/documentdb-nosql-database-encryption-at-rest/design-diagram.png)

사용자 요청의 기본 흐름은 다음과 같습니다.
- 사용자 데이터베이스 계정은 이미 준비되어 있고 저장소 키는 관리 서비스 리소스 공급자(RP)에 대한 요청을 통해 검색됩니다.
- 사용자는 HTTPS/보안 전송을 통해 DocumentDB로의 연결을 만듭니다(SDK가 세부 정보를 추상화).
- 사용자는 이전에 만든 보안 연결을 통해 저장할 JSON 문서를 보냅니다.
- JSON 문서는 사용자가 인덱싱을 해제하지 않는다면, 인덱싱됩니다.
- JSON 문서와 인덱스 데이터 모두 보안 저장소에 기록됩니다.
- 데이터는 보안 저장소에서 주기적으로 읽어오고 Azure Encrypted Blob Store에 백업됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="q-how-much-more-does-azure-storage-cost-if-sse-is-enabled"></a>Q: SSE를 사용하는 경우 Azure Storage 비용은 얼마나 늘어나나요?
A: 추가 비용은 없습니다.

### <a name="q-who-manages-the-encryption-keys"></a>Q: 암호화 키는 누가 관리하나요?
A: 키는 Microsoft에서 관리합니다.

### <a name="q-can-i-use-my-own-encryption-keys"></a>Q: 나만의 암호화 키를 사용할 수 있나요?
A: DocumentDB는 PaaS 서비스로, Microsoft는 서비스의 사용 용이성을 높이기 위해 열심히 노력해 왔습니다.  이 질문이 PCI-DSS와 같은 규정 준수를 충족시키기 위한 프록시 질문으로 자주 제기되는 것을 확인했습니다.  이 기능을 구축하는 과정에서 준수 감사자와 협력하여 DocumentDB를 사용하는 고객이 키를 직접 관리할 필요없이 요구 사항을 충족할 수 있도록 했습니다.
이러한 이유로 현재 사용자에게 직접 키를 관리하는 부담을 주는 옵션을 제공합니다.

### <a name="q-what-regions-have-encryption-turned-on"></a>Q: 암호화가 설정된 지역은 어디인가요?
A: 모든 DocumentDB 지역의 모든 사용자 데이터에 대해 암호화가 설정되었습니다.

## <a name="next-steps"></a>다음 단계

DocumentDB 보안 및 최신 개선 사항에 대한 개요는 [DocumentDB NoSQL 데이터베이스 보안](documentdb-nosql-database-security.md)을 참조하세요.

Microsoft 인증서에 대한 자세한 내용은 [Azure 보안 센터](https://azure.microsoft.com/en-us/support/trust-center/)를 참조하세요.

