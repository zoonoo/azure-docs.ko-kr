---
title: Azure Database for PostgreSQL 관계형 데이터베이스 서비스 개요
description: PostgreSQL 관계형 데이터베이스 서비스에 대한 Azure Docs 개요를 제공합니다.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/14/2018
ms.openlocfilehash: c1e90e9e39bb9ff698b2e53a2b7cf02340e1111f
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685857"
---
# <a name="what-is-azure-database-for-postgresql"></a>PostgreSQL용 Azure Database란?

Azure Database for PostgreSQL은 커뮤니티 버전의 오픈 소스 [PostgreSQL](https://www.postgresql.org/) 데이터베이스 엔진 9.5, 9.6 및 10 버전을 기반으로 하여 개발자용으로 빌드된 Microsoft 클라우드의 관계형 데이터베이스 서비스입니다. PostgreSQL용 Azure Database는 다음과 같은 기능을 제공합니다.

- 추가 비용 없이 기본 제공되는 고가용성
- 예측 가능한 성능, 포괄적인 종량제 가격 책정 사용
- 몇 초 이내 필요에 따라 크기 조정
- 중요한 미사용 및 이동 데이터 보호
- 최대 35일 동안 자동 백업 및 특정 시점 복원
- 엔터프라이즈급 보안 및 규정 준수

이러한 모든 기능에는 인증이 필요하지 않고 추가 비용 없이 제공됩니다. 이러한 기능을 사용하면 가상 머신과 인프라를 관리하는 데 귀중한 시간과 리소스를 할당하는 대신 빠른 응용 프로그램 개발에 집중하고 시장 출시 시간을 단축할 수 있습니다. 또한 원하는 오픈 소스 도구 및 플랫폼을 사용하여 응용 프로그램을 계속 개발하고, 새로운 기술을 익히지 않고도 비즈니스 요구 사항을 빠르고 효율적으로 제공할 수 있습니다. 

이 문서에서는 성능, 확장성 및 관리 효율성과 관련된 PostgreSQL용 Azure Database의 핵심 개념과 기능을 소개합니다. 이러한 빠른 시작을 참조하여 다음 항목을 시작하세요.

- [Azure Portal을 사용하여 PostgreSQL용 Azure Database 만들기](quickstart-create-server-database-portal.md)
- [Azure CLI를 사용하여 PostgreSQL용 Azure Database 만들기](quickstart-create-server-database-azure-cli.md)

일련의 Azure CLI 샘플은 다음을 참조하세요.

- [PostgreSQL용 Azure Database에 대한 Azure CLI 샘플](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>몇 초 이내 성능 및 규모 조정
Azure Database for PostgreSQL 서비스는 기본, 범용 및 메모리 최적화의 세 가지 가격 책정 계층을 제공합니다. 각 계층에서는 데이터베이스 작업을 지원하는 다양한 리소스 기능을 제공합니다. 한 달에 몇 달러로 작은 데이터베이스에 첫 번째 앱을 빌드하고 솔루션의 요구에 맞게 규모를 조정할 수 있습니다. 동적 확장성을 사용하면 데이터베이스가 빠르게 변화하는 리소스 요구 사항에 투명하게 대응할 수 있습니다. 필요할 경우 필요한 리소스에 대해서만 요금을 지불합니다. 자세한 내용은  [가격 책정 계층](concepts-pricing-tiers.md)을 참조하세요.

## <a name="monitoring-and-alerting"></a>모니터링 및 경고
기능을 사용하거나 해제하는 시기는 어떻게 결정하나요? 기본 제공 Azure 모니터링 및 경고 기능을 사용합니다. 이러한 도구를 사용하면 현재 또는 예상되는 성능 또는 저장소 요구 사항에 따라 크기를 확장 또는 축소함으로써 발생하는 효과를 신속하게 평가할 수 있습니다. 자세한 내용은 [경고](howto-alert-on-metric.md)를 참조하세요.

## <a name="keep-your-app-and-business-running"></a>앱 및 비즈니스 운영 유지
Azure의 업계 선도적인 99.99% 가용성 SLA(서비스 수준 약정)를 Microsoft에서 관리되는 전 세계 데이터 센터 네트워크의 지원을 받아 앱을 연중 무휴(24/7)로 실행할 수 있습니다. 모든 PostgreSQL 서버용 Azure Database에서 다른 방법으로 구입 또는 설계, 구축 및 관리해야 하는 기본 제공 보안, 내결함성 및 데이터 보호를 활용합니다. Azure Database for PostgreSQL을 사용하면 각 가격 책정 계층에서 해당 방식으로 시작하고 실행하고 계속하는 데 사용할 수 있는 일단의 포괄적인 비즈니스 연속성 기능 및 옵션을 제공합니다. [특정 시점 복원](howto-restore-server-portal.md)을 사용하면 데이터베이스를 최대 35일 전의 상태로 복원할 수 있습니다. 또한 데이터베이스를 호스팅하는 데이터 센터에서 중단이 발생하면 최신 백업의 지역 중복 복사본에서 데이터베이스를 복원할 수 있습니다.

## <a name="secure-your-data"></a>데이터 보호
Azure Database 서비스는 PostgreSQL용 Azure Database가 액세스를 제한하고, 미사용 및 사용 중인 데이터를 보호하고, 작업을 모니터링하는 데 도움이 되는 기능을 사용하여 보관하도록 데이터 보안을 유지해왔습니다. Azure의 플랫폼 보안에 대한 자세한 내용을 보려면 [Azure 보안 센터](https://www.microsoft.com/en-us/trustcenter/security)를 방문하세요.

PostgreSQL용 Azure 데이터베이스 서비스는 미사용 데이터에 대해 저장소 암호화를 사용합니다. 백업을 포함한 데이터는 디스크에서 암호화됩니다. 단, 쿼리를 실행하는 동안 엔진에서 만든 임시 파일은 예외입니다. 서비스는 Azure Storage 암호화에 포함된 AES 256비트 암호화를 사용하며 키는 시스템에서 관리합니다. 저장소 암호화는 항상 켜져 있고 해제할 수 없습니다.

기본적으로 PostgreSQL용 Azure 데이터베이스 서비스는 네트워크를 통해 이동하는 데이터에 대해 [SSL 연결 보안](./concepts-ssl-connection-security.md)을 요구하도록 구성됩니다. 데이터베이스 서버와 클라이언트 응용 프로그램 간 SSL 연결을 적용하면 서버와 응용 프로그램 간 데이터 스트림을 암호화함으로써 “메시지 가로채기(man in the middle)” 공격으로부터 보호할 수 있습니다. 필요에 따라 클라이언트 응용 프로그램이 SSL 연결을 지원하지 않을 경우 데이터베이스 서비스에 연결하기 위해 SSL을 요구하지 않도록 설정할 수 있습니다.

## <a name="contacts"></a>연락처
Azure Database for PostgreSQL 작업에 대해 궁금한 점이나 제안하고 싶은 의견이 있으면 Azure Database for PostgreSQL 팀([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com))으로 이메일을 보내주세요. 이는 기술 지원 별칭이 아닙니다.

또한 문의의 다음 사항을 적절히 고려해 주세요.
- Azure 고객 지원팀에 문의하려면 [Azure Portal에서 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.
- 계정 관련 문제를 해결하려면 Azure Portal에서 [지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출합니다.
- 피드백을 제공하거나 새 기능을 요청하려면 [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)를 통해 항목을 만드세요.

## <a name="next-steps"></a>다음 단계
- 비용 비교 및 계산기는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/postgresql/)를 참조하세요.
- [첫 번째 PostgreSQL용 Azure Database를 만들](./quickstart-create-server-database-portal.md)어서 시작합니다.
- Python, PHP, Ruby, C\#, Java, Node.js에서 첫 번째 앱 빌드: [연결 라이브러리](./concepts-connection-libraries.md)
