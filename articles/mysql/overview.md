---
title: "MySQL용 Azure Database 관계형 데이터베이스 서비스 개요 | Microsoft Docs"
description: "MySQL용 Azure Database 관계형 데이터베이스 서비스 개요입니다."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c8d75b0ce8bba4d0c065f86c199538b4a51f4bee
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---


# <a name="what-is-azure-database-for-mysql-service-introduction"></a>MySQL용 Azure Database란? 서비스 소개
MySQL용 Azure Database는 [MySQL 커뮤니티 버전](https://www.mysql.com/products/community/) 데이터베이스 엔진을 기반으로 하는 Microsoft 클라우드의 관계형 데이터베이스 서비스입니다.  MySQL용 Azure Database는 다음과 같은 기능을 제공합니다.

- 여러 서비스 수준에서 예측 가능한 성능
- 응용 프로그램 가동 중지 시간 없는 동적 확장성
- 기본 제공되는 고가용성
- 데이터 보호

이러한 기능에는 인증이 필요하지 않고 추가 비용 없이 제공됩니다. 그러면 가상 컴퓨터와 인프라를 관리하는 데 귀중한 시간과 리소스를 할당하는 대신 빠른 앱 개발에 집중하고 시장 출시 시간을 단축할 수 있습니다. 또한 오픈 소스 도구 및 선택한 플랫폼을 사용하여 응용 프로그램을 개발하고 새로운 기술을 배울 필요 없이 비즈니스에서 요구하는 속도 및 효율성을 계속 제공할 수 있습니다.

![MySQL용 Azure Database 개념 다이어그램](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

이 문서에서는 세부 정보를 찾는 링크를 통해 성능, 확장성 및 관리 효율성과 관련된 MySQL용 Azure Database의 핵심 개념과 기능을 소개합니다. 이러한 빠른 시작을 참조하여 다음 항목을 시작하세요.
- [Azure Portal을 사용한 MySQL용 Azure 데이터베이스 서버 만들기](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure CLI를 사용한 MySQL용 Azure 데이터베이스 서버 만들기](quickstart-create-mysql-server-database-using-azure-cli.md)

일련의 Azure CLI 샘플은 다음을 참조하세요.
- [MySQL용 Azure Database에 대한 Azure CLI 샘플](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>가동 중지 시간 없이 성능 및 규모 조정
MySQL용 Azure Database는 기본 및 표준 서비스 계층을 제공합니다. 각 계층은 경량급에서 중량급까지 데이터베이스 워크로드를 지원하기 위해 다양한 성능 및 기능을 제공합니다. 한 달에 몇 달러로 작은 데이터베이스에 첫 번째 앱을 빌드하고 가동 중지 시간 없이 서비스 계층을 솔루션의 요구에 맞게 확장하도록 변경할 수 있습니다. 동적 확장성을 사용하면 데이터베이스가 빠르게 변화하는 리소스 요구 사항에 투명하게 대응할 수 있습니다. 필요할 경우 필요한 리소스에 대해서만 요금을 지불합니다.

## <a name="monitoring-and-alerting"></a>모니터링 및 경고
확장 및 축소할 때 마우스 오른쪽 단추 클릭이 정지하는 것을 어떻게 알 수 있습니까? 계산 단위에 따라 성능 등급을 함께 결합한 기본 제공 성능 모니터링 및 경고 기능을 사용합니다. 이러한 기능을 사용하면 현재 또는 프로젝트의 성능 요구에 기반하여 확장 또는 축소의 영향을 신속하게 평가할 수 있습니다. 자세한 내용은 [개념: 서비스 계층](concepts-service-tiers.md)을 참조하세요.

## <a name="keep-your-app-and-business-running"></a>앱 및 비즈니스 운영 유지
Azure의 업계 선도적인 99.99% 가용성 SLA(서비스 수준 약정)를 Microsoft에서 관리되는 전 세계 데이터 센터 네트워크의 지원을 받아 앱을 연중 무휴(24/7)로 실행할 수 있습니다. 다른 방법으로 구입 또는 설계, 구축 및 관리해야 할 수도 있는 기본 제공 보안, 내결함성 및 데이터 보호를 모든 MySQL 서버용 Azure Database에서 활용합니다. MySQL용 Azure Database에서 특정 시점 복원을 사용하여 서버를 최대 35일 전의 상태로 복원할 수 있습니다.

## <a name="secure-your-data"></a>데이터 보호
Azure Database 서비스는 MySQL용 Azure Database가 액세스를 제한하고, 미사용 및 사용 중인 데이터를 보호하고, 작업을 모니터링하는 데 도움이 되는 기능을 사용하여 보관하도록 데이터 보안을 유지해왔습니다. Azure의 플랫폼 보안에 대한 자세한 내용을 보려면 [Azure 보안 센터](https://www.microsoft.com/en-us/TrustCenter/Security/default.aspx)를 방문하세요.

## <a name="next-steps"></a>다음 단계
이제 MySQL용 Azure Database에 대한 소개를 읽고 질문 "MySQL용 Azure Database란?"에 답변했으므로 다음에 대한 준비가 되었습니다.
- 비용 비교 및 계산기는 가격 책정 페이지를 참조하세요. [가격 책정](https://azure.microsoft.com/pricing/details/mysql/)
- 첫 번째 서버를 만들어서 시작합니다. [Azure Portal을 사용한 MySQL용 Azure 데이터베이스 서버 만들기](quickstart-create-mysql-server-database-using-azure-portal.md)
- Python, PHP, Ruby, C\#, Java, Node.js로 첫 번째 앱 빌드: [MySQL용 Azure Database에 연결하는 데 사용되는 연결 라이브러리](concepts-connection-libraries.md)

