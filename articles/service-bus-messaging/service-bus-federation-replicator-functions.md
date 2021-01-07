---
title: 메시지 복제 태스크 및 응용 프로그램-Azure Service Bus | Microsoft Docs
description: 이 문서에서는를 사용 하 여 메시지 복제 태스크 및 응용 프로그램을 빌드하는 개요를 제공 Azure Functions
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 4db151f54a2ad236ba937b005ba6a1fd3edd5967
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657498"
---
# <a name="message-replication-tasks-and-applications"></a>메시지 복제 태스크 및 응용 프로그램

[메시지 복제 및 지역 간 페더레이션](service-bus-federation-overview.md) 문서에서 설명 하는 것 처럼 Service Bus 엔터티 쌍 간의 메시지 시퀀스와 Service Bus 간 및 기타 메시지 원본 및 대상 간의 복제는 일반적으로 Azure Functions에서 기울어집니다 됩니다.

[Azure Functions](../azure-functions/functions-overview.md) 는 [메시지 복제 및 페더레이션](service-bus-federation-overview.md) 태스크를 포함 하 여 서버 리스 응용 프로그램을 구성 하 고 실행 하기 위한 확장 가능 하 고 안정적인 실행 환경입니다.

이 개요에서는 이러한 응용 프로그램에 대 한 기본 제공 기능, 변환 작업에 맞게 조정 하 고 수정할 수 있는 코드 블록, Service Bus 및 기타 Azure 메시징 서비스와 이상적인 통합을 위해 Azure Functions 응용 프로그램을 구성 하는 방법을 Azure Functions 설명 합니다. 이 문서에서는 많은 세부 정보를 Azure Functions 설명서를 안내 합니다.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]
