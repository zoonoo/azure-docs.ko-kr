---
title: Azure Cosmos DB에 대 한 보안 특성
description: Azure Cosmos DB를 평가 하는 것에 대 한 보안 특성에 대 한 검사 목록
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: ffeb60f5476a540e3da46a82c240b0dda9aa6be2
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480472"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Azure Cosmos DB에 대 한 보안 특성

이 문서에서는 Azure Cosmos DB에 내장 된 일반 보안 특성을 설명 합니다.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>예방

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| (예: 서버 쪽 암호화, 고객 관리 키를 사용 하 여 서버 쪽 암호화 및 기타 암호화 기능)는 미사용 데이터 암호화 | 예 | 기본적으로 모든 Cosmos DB 데이터베이스와 백업을 암호화 됩니다. 참조 [Azure Cosmos DB에서 데이터 암호화](database-encryption-at-rest.md)합니다. 고객 관리 키를 사용 하 여 서버 쪽 암호화는 지원 되지 않습니다. |
| (예: ExpressRoute 암호화 Vnet 암호화 및 VNet 대 VNet 암호화에서) 전송 중 암호화| 예 | 모든 Azure Cosmos DB 데이터 전송 시 암호화 됩니다. |
| 암호화 키 처리(CMK, BYOK 등)| 아닙니다. |  |
| 열 수준 암호화(Azure Data Services)| 예 | 테이블 API 프리미엄 에서만. 일부 Api는이 기능을 지원 합니다. 참조 [Azure Cosmos DB 소개: Table API](table-introduction.md)합니다. |
| API 호출 암호화| 예 | Azure Cosmos DB에 대 한 모든 연결이 HTTPS를 지원 합니다. Azure Cosmos DB는 또한 TLS 1.2 연결을 지원 하지만이 아직 적용 되지 않습니다. 고객 측에서 하위 수준 TLS 해제 하는 경우 Cosmos DB에 연결 하려면 유지할 수 있습니다.  |

## <a name="network-segmentation"></a>네트워크 구분

| 보안 특성 | 예/아니요 | 메모 |
|---|---|--|
| 서비스 엔드포인트 지원| 예 |  |
| vNET 삽입 지원| 예 | VNet 서비스 끝점을 사용 하 여 가상 네트워크 (VNet)의 특정 서브넷 에서만에서 액세스할 수 있도록 Azure Cosmos DB 계정을 구성할 수 있습니다. 또한 방화벽 규칙을 사용 하 여 VNet 대 한 액세스를 결합할 수 있습니다.  참조 [가상 네트워크에서 Azure Cosmos DB 액세스](vnet-service-endpoint.md)합니다. |
| 네트워크 격리 및 Firewalling 지원| 예 | 방화벽 지원 기능을 사용 하 여 Azure Cosmos 계정의 IP 주소, IP 주소 범위는 승인 된 집합에만 액세스를 허용 및/또는 클라우드 서비스를 구성할 수 있습니다. 참조 [Azure Cosmos DB의 IP 구성 방화벽](how-to-configure-firewall.md)합니다.|
| 강제 터널링 지원 | 예 | 가상 머신이 위치한 VNET의 클라이언트 쪽에서 구성할 수 있습니다.   |

## <a name="detection"></a>감지

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Azure 지원 (예: Log analytics, App insights)를 모니터링 합니다.| 예 | Azure Cosmos DB로 전송 되는 모든 요청이 로깅됩니다. [Azure 모니터링](../azure-monitor/overview.md), Azure 메트릭, Azure 감사 로깅 지원 됩니다.  MongoDB를 데이터 평면 요청의 쿼리 런타임 통계, 쿼리 텍스트에 해당 정보를 기록할 수 있습니다를 요청 합니다. 또한 경고를 설정할 수 있습니다. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| Authentication| 예 | 데이터베이스 계정 수준에서 예 데이터 평면 수준에서 Cosmos DB 리소스 토큰 및 액세스 키를 사용합니다. |
| 권한 부여| 예 | 마스터 키 (기본 및 보조)를 사용 하 여 Azure Cosmos 계정 및 리소스 토큰을 지원 합니다. 마스터 키를 사용 하 여 데이터에만 대 한 읽기 또는 읽기/쓰기를 얻을 수 있습니다. 리소스 토큰 문서 및 컨테이너와 같은 리소스에 대 한 제한 된 시간 액세스를 허용 합니다. |


## <a name="audit-trail"></a>감사 내역

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 컨트롤/관리 계획 로깅 및 감사| 예 | Vnet 방화벽 같은 계정 수준 작업에 대 한 azure 활동 로그 IAM 및 키 액세스 합니다. |
| 데이터 평면 로깅 및 감사 | 예 | 와 같은 컨테이너 수준 작업에 대 한 로깅을 모니터링 하는 진단 컨테이너를 프로 비전 처리량을 인덱싱 정책 및 문서에 대 한 CRUD 작업을 만듭니다. |

## <a name="configuration-management"></a>구성 관리

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 구성 관리 지원 (구성 등의 버전 관리 합니다.)| 아닙니다.  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Cosmos DB에 대 한 추가 보안 특성

| 보안 특성 | 예/아니요 | 메모|
|---|---|--|
| 교차 원본 자원 공유 (CORS) | 예 | 참조 [크로스-원본 자원 공유 (CORS)를 구성](how-to-configure-cross-origin-resource-sharing.md)합니다. |
