---
title: Azure 분석 서비스에서 장기 실행 작업을 위한 모범 사례 | 마이크로 소프트 문서
description: 이 문서에서는 장기 실행 작업에 대한 모범 사례를 설명합니다.
author: minewiskan
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 3f6b2194cc422a827bbc7a15c012173b3f814b52
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428109"
---
# <a name="best-practices-for-long-running-operations"></a>장기 실행 운영을 위한 모범 사례

Azure Analysis Services에서 *노드는* 서버 리소스가 실행 중인 호스트 가상 컴퓨터를 나타냅니다. 서버 리소스가 다른 노드로 이동하는 경우 장기 실행 쿼리, 새로 고침 작업 및 쿼리 확장 동기화와 같은 일부 작업이 실패할 수 있습니다. 이 시나리오의 일반적인 오류 메시지는 다음과 같습니다.

- "오래 실행되는 XMLA 요청을 찾는 동안 오류가 발생했습니다. 서비스 업그레이드 또는 서버 다시 시작으로 요청이 중단되었을 수 있습니다."
- 'ID를 가진<guid>작업'<database>' 서비스 오류로 인해 취소 되었습니다 (비활성) 메시지와 함께 '새로 고침 요청을 취소 이후 업데이트 없이 중단 되었습니다. 이것은 내부 서비스 문제입니다. 이 문제가 반복적으로 발생하면 작업을 다시 제출하거나 티켓을 제출하여 도움을 받으십시오."

장기 실행 작업이 중단될 수 있는 데는 여러 가지 이유가 있습니다. 예를 들어 다음과 같은 Azure의 업데이트가 있습니다. 
- 운영 체제 패치 
- 보안 업데이트
- Azure 분석 서비스 서비스 업데이트
- 서비스 패브릭 업데이트. 서비스 패브릭은 Azure 분석 서비스를 포함하여 여러 Microsoft 클라우드 서비스에서 사용하는 플랫폼 구성 요소입니다.

서비스에서 발생하는 업데이트 외에도 로드 분산으로 인해 노드 간에 서비스가 자연스럽게 이동됩니다. 노드 이동은 클라우드 서비스의 예상 부분입니다. Azure Analysis Services는 노드 이동으로 인한 영향을 최소화하려고 시도하지만 완전히 제거하는 것은 불가능합니다. 

노드 이동 외에도 발생할 수 있는 다른 오류가 있습니다. 예를 들어 데이터 원본 데이터베이스 시스템이 오프라인이거나 네트워크 연결이 끊어질 수 있습니다. 새로 고침 중에 파티션에 1,000만 개의 행이 있고 9백만 번째 행에서 오류가 발생하는 경우 실패 시점에 새로 고침을 다시 시작할 수 없습니다. 서비스는 처음부터 다시 시작해야 합니다. 

## <a name="refresh-rest-api"></a>나머지 API 새로 고침

서비스 중단은 데이터 새로 고침과 같은 장기 실행 작업에 어려울 수 있습니다. Azure 분석 서비스에는 서비스 중단으로 인한 부정적인 영향을 완화하는 데 도움이 되는 REST API가 포함되어 있습니다. 자세한 내용은 [REST API를 사용하여 비동기 새로 고침을](analysis-services-async-refresh.md)참조하십시오.
 
REST API 외에도 장기 실행 새로 고침 작업 중에 잠재적인 문제를 최소화하는 데 사용할 수 있는 다른 방법이 있습니다. 주요 목표는 처음부터 새로 고침 작업을 다시 시작하지 않고 단계적으로 커밋할 수 있는 작은 일괄 처리에서 새로 고침을 수행하는 것입니다. 
 
REST API를 사용하면 이러한 다시 시작할 수 있지만 파티션 생성 및 삭제의 유연성을 완전히 허용하지는 않습니다. 시나리오에 복잡한 데이터 관리 작업이 필요한 경우 솔루션에 논리에 일종의 일괄 처리 가 포함되어야 합니다. 예를 들어 트랜잭션을 사용하여 여러 개의 개별 일괄 처리로 데이터를 처리하면 처음부터 다시 시작하지 않고 중간 검사점에서 다시 시작하지 않아도 됩니다. 
 
## <a name="scale-out-query-replicas"></a>쿼리 복제본 확장

REST 또는 사용자 지정 논리를 사용하든 클라이언트 응용 프로그램 쿼리는 일괄 처리되는 동안 일관성 이 없거나 중간 결과를 반환할 수 있습니다. 처리가 진행되는 동안 클라이언트 응용 프로그램 쿼리에서 반환되는 일관된 데이터가 필요하고 모델 데이터가 중간 상태인 경우 읽기 전용 쿼리 복제본을 사용하여 [확장을](analysis-services-scale-out.md) 사용할 수 있습니다.

읽기 전용 쿼리 복제본을 사용하여 새로 고침이 일괄 처리로 수행되는 동안 클라이언트 응용 프로그램 사용자는 읽기 전용 복제본에서 데이터의 이전 스냅숏을 계속 쿼리할 수 있습니다. 새로 고침이 완료되면 동기화 작업을 수행하여 읽기 전용 복제본을 최신 상태로 만들 수 있습니다.


## <a name="next-steps"></a>다음 단계

[REST API를 사용한 비동기 새로 고침](analysis-services-async-refresh.md)  
[Azure Analysis Services 규모 확장](analysis-services-scale-out.md)  
[Analysis Services 고가용성](analysis-services-bcdr.md)  
[Azure 서비스에 대한 다시 시도 지침](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)   

