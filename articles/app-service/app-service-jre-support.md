---
title: Windows Java 런타임 지원에 대 한 azure App Service
description: 이 항목에서는 Windows에서 Java 런타임 설명은 Azure App Service에 대 한 지원 제공합니다.
author: bmitchell287
ms.author: brendm; joshuapa
ms.date: 3/22/2019
ms.topic: article
ms.service: app-service
manager: dougE
ms.openlocfilehash: ab0e9b9f272108745b629d2cb284f6c5ab52c76f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58523042"
---
# <a name="java-runtime-statement-of-support-for-app-service-on-windows"></a>Windows의 App Service에 대 한 지원 Java 런타임 정보

## <a name="jdk-versions-and-maintenance"></a>JDK 버전 및 유지 관리

Azure에서 지원되는 JDK(Java Development Kit)는 [Azul Systems](https://www.azul.com/)를 통해 제공하는 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)입니다.

주 버전 업데이트는 Windows에 대 한 Azure App Service에서 새 런타임 옵션을 통해 제공 됩니다. 고객은 App Service 배포를 구성하여 최신 버전의 Java로 업데이트해야 하며, 주 업데이트를 테스트하고 요구 사항을 충족하도록 관리할 책임이 있습니다.

지원되는 JDK는 매년 분기마다 1월, 4월, 7월, 10월에 자동으로 패치됩니다.

## <a name="security-updates"></a>보안 업데이트

Azul Systems에서 주요 보안 취약점에 대한 패치 및 수정 사항을 출시하는 즉시 고객에게 제공됩니다. [NIST Common Vulnerability Scoring System 버전 2](https://nvd.nist.gov/cvss.cfm)에서 기본 점수 9.0 이상을 받으면 "주요" 취약점으로 정의됩니다.

## <a name="deprecation-and-retirement"></a>사용 중단 및 사용 중지

지원되는 Java 런타임이 폐기되는 경우 폐기 예정인 런타임을 사용하는 Azure 개발자에게는 적어도 런타임 폐기 6개월 전에 사용 중단 알림이 제공됩니다.

## <a name="local-development"></a>로컬 개발

개발자는 [Azul의 다운로드 사이트](https://www.azul.com/downloads/azure-only/zulu/)에서 로컬 개발용 Azul Zulu Enterprise JDK의 프로덕션 버전을 다운로드할 수 있습니다.

## <a name="development-support"></a>개발 지원

에 대 한 제품 지원 합니다 [Azul Zulu JDK Azure 지원](https://www.azul.com/downloads/azure-only/zulu/) Microsoft를 통해 사용할 수 있는 경우 Azure에 대 한 개발 또는 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 사용 하 여를 [Azure 지원 계획을 정규화 된](https://azure.microsoft.com/support/plans/)합니다.

## <a name="runtime-support"></a>런타임 지원

개발자는 [정규화된 지원 계획](https://azure.microsoft.com/support/plans/)이 있는 경우 Azure 지원을 통해 Azul Zulu JDK 관련 [문제를 제기](/azure/azure-supportability/how-to-create-azure-support-request)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 항목에서는 Windows에서 Java 런타임 설명은 Azure App Service에 대 한 지원 제공합니다.
- Azure App Service 참조를 사용 하 여 웹 응용 프로그램 호스팅에 대해 자세히 알아보려면 [App Service 개요](overview.md)합니다.
- Azure 개발에 대 한 Java에 대 한 정보를 참조 하세요. [Java 개발자 센터에 대 한 Azure](https://docs.microsoft.com/java/azure/?view=azure-java-stable)합니다.
