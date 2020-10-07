---
title: 개인 정보에 대 한 명명 된 엔터티
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: eedfe4f24797a9b564479b2f8d3f4d04b0751272
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779865"
---
> [!NOTE]
> 보호 된 상태 정보 (화)를 검색 하려면 `domain=phi` 매개 변수 및 모델 버전 이상을 사용 `2020-04-01` 합니다.
>
> 예를 들어 다음과 같습니다.
 
끝점에 요청을 보낼 때 다음 엔터티 범주가 반환 됩니다 `/v3.1-preview.2/entities/recognition/pii` .

| 범주   | 하위 범주 | 설명:                          | 시작 모델 버전 | 참고 |
|------------|-------------|--------------------------------------|------------------------|---|
| 사람     | N/A         | 사람의 이름입니다.  | `2019-10-01`  | 또한로 반환 `domain=phi` 됩니다. |
| PersonType | N/A         | 사용자가 보유 한 작업 유형 또는 역할 | `2020-02-01` | |
| PhoneNumber | N/A | 전화 번호 (미국과 EU 전화 번호만 해당). | `2019-10-01` | 또한로 반환 `domain=phi` 됩니다. |
|조직  | N/A | 회사, 정치적 그룹, 음악 밴드, 스포츠 클럽, 정부 기관, 공공 단체.  | `2019-10-01` | Nationalities 및 religions는이 엔터티 형식에 포함 되지 않습니다.  |
|조직 | 의료 | 의료 회사 및 그룹. | `2020-04-01` |  |
|조직 | 재고 교환 | 재고 교환 그룹. | `2020-04-01` |  |
| 조직 | 스포츠 | 스포츠 관련 조직. | `2020-04-01` |  |
| 주소 | N/A | 전체 우편 주소  | `2020-04-01` | 또한로 반환 `domain=phi` 됩니다. |
| EU GPS 좌표 | N/A | 유럽 연합 내의 위치에 대 한 GPS 좌표입니다.  | `2019-10-01` |  |
| 메일 | N/A | 전자 메일 주소. | `2019-10-01` | 또한로 반환 `domain=phi` 됩니다.   |
| URL | N/A | 웹 사이트에 대 한 Url입니다. | `2019-10-01` | 또한로 반환 `domain=phi` 됩니다. |
| IP | N/A | 네트워크 IP 주소. | `2019-10-01` | 또한로 반환 `domain=phi` 됩니다. |
| DateTime | N/A | 날짜 및 시간입니다. | `2019-10-01` |  | 
| DateTime | 날짜 | 일정 날짜 | `2019-10-01` | 또한로 반환 `domain=phi` 됩니다. |
| 수량 | N/A | 숫자 및 숫자 수량. | `2019-10-01` |  |
| 수량 | 나이 | 에이징을. | `2019-10-01` | | |

## <a name="azure-information"></a>Azure 정보

이 엔터티 범주는 인증 정보 및 연결 문자열을 포함 하 여 식별 가능한 Azure 정보를 포함 합니다. 모델 버전부터 사용 가능 `2019-10-01` 합니다. 매개 변수를 사용 하 여 반환 되지 않습니다 `domain=phi` .

| 하위 범주                           | 설명:                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB 인증 키             | Azure Cosmos DB 서버에 대 한 권한 부여 키입니다.                           |
| Azure IAAS 데이터베이스 연결 문자열 및 Azure SQL 연결 문자열 | Azure IaaS (infrastructure as a service) 데이터베이스에 대 한 연결 문자열 및 SQL 연결 문자열입니다. |
| Azure SQL 연결 문자열           | Azure SQL Database 데이터베이스에 대 한 연결 문자열입니다.                                |
| Azure IoT 연결 문자열           | Azure IoT에 대 한 연결 문자열입니다.                        |
| Azure 게시 설정 암호        | Azure 게시 설정에 대 한 암호입니다.                                        |
| Azure Redis Cache 연결 문자열   | Redis cache에 대 한 연결 문자열입니다.                             |
| Azure SAS                             | Azure SaaS (software as a service)에 대 한 연결 문자열입니다.                     |
| Azure Service Bus 연결 문자열   | Azure 서비스 버스에 대 한 연결 문자열입니다.                                 |
| 계정 키 Azure Storage             | Azure storage 계정에 대 한 계정 키입니다.                                   |
| Azure Storage 계정 키(일반)   | Azure 저장소 계정에 대 한 일반 계정 키입니다.                           |
| SQL Server 연결 문자열          | SQL Server를 실행 하는 컴퓨터에 대 한 연결 문자열입니다.                                         |

## <a name="identification"></a>식별

[!INCLUDE [supported identification entities](./identification-entities.md)]
