---
title: 이벤트 복제 태스크 및 응용 프로그램-Azure Event Hubs | Microsoft Docs
description: 이 문서에서는를 사용 하 여 이벤트 복제 작업 및 응용 프로그램을 빌드하는 개요를 제공 Azure Functions
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: a65815c7da400af8b5b6d46358e6bca6edbd7543
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97663645"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>Azure Functions를 사용 하는 이벤트 복제 태스크 및 응용 프로그램

> [!TIP]
> 이벤트의 페이로드를 고려해 야 하 고 변환, 집계, 보강 또는 줄여야 하는 모든 상태 저장 복제 작업의 경우 Azure Functions 대신 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 를 사용 합니다.

[이벤트 복제 및 지역 간 페더레이션](event-hubs-federation-overview.md) 문서에서 설명 하는 것 처럼 Event Hubs 쌍 간 이벤트 스트림의 상태 비저장 복제와 Event Hubs 간의 이벤트 스트림 원본 및 대상 간의 상태를 Azure Functions 기울어집니다.

[Azure Functions](../azure-functions/functions-overview.md) 는 이벤트 복제 및 페더레이션 태스크를 포함 하 여 서버 리스 응용 프로그램을 구성 하 고 실행 하기 위한 확장성과 안정성이 뛰어난 실행 환경입니다.

이 개요에서는 이러한 응용 프로그램에 대 한 기본 제공 기능, 변환 작업에 맞게 조정 하 고 수정할 수 있는 코드 블록, Event Hubs 및 기타 Azure 메시징 서비스와 이상적인 통합을 위해 Azure Functions 응용 프로그램을 구성 하는 방법을 Azure Functions 설명 합니다. 이 문서에서는 많은 세부 정보를 Azure Functions 설명서를 안내 합니다.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









