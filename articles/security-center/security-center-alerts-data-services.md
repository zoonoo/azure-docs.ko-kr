---
title: Azure Security Center에서 데이터 서비스에 대 한 위협 감지 | Microsoft Docs
description: 이 항목에서는 Azure Security Center에서 사용할 수 있는 데이터 서비스 경고를 제공 합니다.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/24/2019
ms.author: v-mohabe
ms.openlocfilehash: 8812ac325e6bfd5ee019f6ddd6bf86c846ed5c10
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782457"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Azure Security Center에서 데이터 서비스에 대 한 위협 감지

 Security Center는 데이터 저장소 서비스의 로그를 분석 하 고 데이터 리소스에 대 한 위협이 감지 되 면 경고를 트리거합니다. 이 항목에서는 다음 서비스에 대해 Security Center에서 생성 하는 경고를 나열 합니다.

* [Azure SQL Database 및 SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Cosmos DB](#cosmos-db)

## Azure SQL Database 및 SQL Data Warehouse<a name="data-sql"></a>

SQL 위협 감지는 비정상적인 활동을 감지 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 합니다. Security Center SQL 감사 로그를 분석 하 고 SQL 엔진에서 기본적으로 실행 됩니다.

|알림|Description|
|---|---|
|**SQL 삽입에 대 한 취약성**|응용 프로그램이 데이터베이스에서 잘못 된 SQL 문을 생성 했습니다. 이는 SQL 삽입 공격에 대 한 가능한 취약점을 나타낼 수 있습니다. 잘못된 문 생성에 대한 두 가지 가능한 이유가 있습니다. 즉, 응용 프로그램 코드의 결함으로 잘못 된 SQL 문을 생성 했습니다. 또는 응용 프로그램 코드 또는 저장 프로시저는 SQL 삽입에 악용 될 수 있는 잘못 된 SQL 문을 생성할 때 사용자 입력을 삭제 하지 않았습니다.|
|**잠재적인 SQL 삽입**|SQL 삽입에 취약 한 식별 된 응용 프로그램에 대해 활성 익스플로잇이 발생 했습니다. 즉 공격자가 취약 한 응용 프로그램 코드 또는 저장 프로시저를 사용 하 여 악성 SQL 문을 삽입 하려고 합니다.|
|**비정상적인 위치에서 액세스**|다른 사람이 비정상적인 지리적 위치에서 SQL server에 로그온 한 SQL server에 대 한 액세스 패턴이 변경 되었습니다. 일부 경우에서 경고는 합법적인 작업(새 애플리케이션 또는 개발자 유지 관리)을 검색합니다. 하고 악의적인 작업(퇴사 직원, 외부 공격자)을 감지하기도 합니다.|
|**익숙하지 않은 사용자의 액세스**|SQL server에 대 한 액세스 패턴이 변경 되었으며, 누군가가 비정상적인 보안 주체 (SQL 사용자)를 사용 하 여 SQL server에 로그온 했습니다. 경고는 합법적인 작업(새 애플리케이션, 개발자 유지 관리)을 감지하기도 하고 악의적인 작업(퇴사 직원, 외부 공격자)을 감지하기도 합니다. 하고 악의적인 작업(퇴사 직원, 외부 공격자)을 감지하기도 합니다.|
|**잠재적으로 위험한 응용 프로그램에서 액세스**|잠재적으로 유해한 응용 프로그램이 데이터베이스에 액세스 하는 데 사용 되었습니다. 일부 경우에서 경고는 작업의 침투 테스트를 감지합니다. 다른 경우에서 경고는 일반적인 공격 도구를 사용하는 공격을 감지합니다.|
|**무차별 암호 대입 SQL 자격 증명**|다른 자격 증명을 사용 하 여 실패 한 로그인 수가 비정상적으로 많습니다. 일부 경우에서 경고는 작업의 침투 테스트를 감지합니다. 다른 경우에서 경고는 무차별 암호 대입 공격(brute force attack)을 검색합니다.|

SQL 위협 검색 경고에 대 한 자세한 내용은[Azure SQL Database 위협 감지](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)를 참조 하 고 위협 검색 경고 섹션을 검토 하십시오. 또한 일어나는지를 통해 악성 SQL 작업 검색을 사용 하 여 공격을 검색 하는 Security Center 방법의 예를 확인 하는 [Azure Security Center 방법](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) 에 대해 알아봅니다.

## Azure Storage<a name="azure-storage"></a>

>[!NOTE]
> Azure Storage에 대 한 Advanced Threat Protection은 현재 Blob Storage 에서만 사용할 수 있습니다.

Azure Storage를 위한 Advanced Threat Protection은 스토리지 계정에 대한 비정상적이며 잠재적으로 유해한 액세스 또는 악용 시도를 탐지하는 추가 보안 인텔리전스 계층을 제공합니다. 이 보호 계층을 사용 하면 보안 전문가로 서는 안 되며 보안 모니터링 시스템을 관리할 필요 없이 위협을 해결할 수 있습니다.

Security Center는 Blob 저장소에 대 한 읽기, 쓰기 및 삭제 요청에 대 한 진단 로그를 분석 하 여 위협을 감지 하 고, 활동의 비정상 현상이 발생 하면 경고를 트리거합니다. 자세한 내용은 [스토리지 분석 로깅을 구성](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging) 하려면을 참조 하세요.

> [!div class="mx-tableFixed"]

|알림|Description|
|---|---|
|**비정상적인 위치 액세스 변칙**|샘플링 된 네트워크 트래픽 분석은 배포의 리소스에서 발생 하는 비정상적인 RDP (보내는 원격 데스크톱 프로토콜) 통신을 검색 했습니다. 이 작업은이 환경에서 비정상으로 간주 되며 리소스가 손상 되었으며 현재 외부 RDP 끝점의 무작위 사용을 위해 사용 되 고 있음을 나타낼 수 있습니다. 이러한 유형의 활동으로 인해 외부 엔터티가 IP에 악성 플래그를 설정할 수 있습니다.|
|**응용 프로그램 액세스 변칙**|비정상적인 응용 프로그램이이 저장소 계정에 액세스 했음을 나타냅니다. 공격자가 새 응용 프로그램을 사용 하 여 저장소 계정에 액세스 하는 것이 원인일 수 있습니다.|
|**익명 액세스 변칙**|저장소 계정에 대 한 액세스 패턴이 변경 되었음을 나타냅니다. 예를 들어이 계정에 대 한 최근 액세스 패턴과 달리, 계정은 인증 없이 익명으로 액세스 됩니다. 공격자가 blob 저장소를 보유 하는 컨테이너에 대 한 공용 읽기 액세스를 악용 하는 것이 가능한 원인입니다.|
|**이상**|은 (익명화 프록시)의 활성 종료 노드인 것으로 알려진 IP 주소에서이 계정이 성공적으로 액세스 되었음을 나타냅니다. 이 경고의 심각도는 사용 된 인증 유형 (있는 경우) 및이가 이러한 액세스의 첫 번째 사례 인지를 고려 합니다. 공격자가를 사용 하 여 저장소 계정에 액세스 했거나 합법적인 사용자가 자신을 사용 하 여 저장소 계정에 액세스 했을 수 있습니다.|
|**데이터 Exfiltration 이상**|이 저장소 컨테이너의 최근 작업과 비교할 때 비정상적으로 많은 양의 데이터가 추출 되었음을 나타냅니다. 공격자가 blob 저장소를 보유 하는 컨테이너에서 많은 양의 데이터를 추출 하는 것이 원인일 수 있습니다.|
|**예기치 않은 삭제 비정상**|저장소 계정에서 예기치 않은 삭제 작업이 하나 이상 발생 했음을 나타냅니다 .이 계정에 대 한 최근 작업과 비교해 볼 수 있습니다. 공격자가 저장소 계정에서 데이터를 삭제 한 경우에 발생할 수 있습니다.|
|**Azure 클라우드 서비스 패키지 업로드**|Azure 클라우드 서비스 패키지 (.cspkg 파일)가이 계정의 최근 활동과 비교 하 여 비정상적인 방식으로 저장소 계정에 업로드 되었음을 나타냅니다. 공격자가 저장소 계정에서 Azure 클라우드 서비스로 악성 코드를 배포 하기 위해 준비 하는 것이 원인일 수 있습니다.|
|**권한 액세스 변칙**|이 저장소 컨테이너의 액세스 권한이 비정상으로 변경 되었음을 나타냅니다. 잠재적 원인은 공격자가 보안 상태를 약화 시키거나 지 속성을 얻기 위해 컨테이너 권한을 변경 했기 때문입니다.|
|**검사 액세스 변칙**|저장소 계정에 대 한 액세스 권한이이 계정의 최근 작업과 비교 하 여 비정상적인 방식으로 검사 되었음을 나타냅니다. 공격자가 향후 공격을 위해 정찰을 수행 했기 때문일 수 있습니다.|
|**데이터 탐색 변칙**|저장소 계정의 blob 또는 컨테이너가이 계정의 최근 활동과 비교 하 여 비정상적인 방식으로 열거 되었음을 나타냅니다. 공격자가 향후 공격을 위해 정찰을 수행 했기 때문일 수 있습니다.|

>[!NOTE]
>Azure Storage에 대 한 Advanced Threat Protection은 현재 Azure 정부 및 소 버린 클라우드 지역에서 사용할 수 없습니다.

저장소에 대 한 경고에 대 한 자세한 내용은 [Azure Storage 고급 위협 방지](../storage/common/storage-advanced-threat-protection.md) 문서를 참조 하 고 보호 경고 섹션을 검토 하세요.

## Cosmos DB<a name="cosmos-db"></a>

다음 경고는 Azure Cosmos DB 계정을 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도로 생성 됩니다.

|알림|Description|
|---|---|
|**비정상적인 위치에서 액세스**|Cosmos DB 계정에 대 한 액세스 패턴이 변경 되었음을 나타냅니다. 누군가가 최근 활동에 비해 익숙하지 않은 IP 주소에서이 계정에 액세스 했습니다. 공격자가 Cosmos DB 계정에 액세스 했거나 합법적인 사용자가 새롭고 비정상적인 지리적 위치에서 Cosmos DB 계정에 액세스 했습니다. 예를 들어 원격에서 새 응용 프로그램 또는 개발자 유지 관리를 수행 합니다.|
|**비정상적인 데이터 반출**|Cosmos DB 계정에서 데이터 추출 패턴이 변경 되었음을 나타냅니다. 누군가가 최근 활동에 비해 비정상적인 양의 데이터를 추출 했습니다. 공격자가 Cosmos DB 데이터베이스에서 많은 양의 데이터를 추출 했습니다. 예: 데이터의 데이터 전송/누출, 무단 전송 또는 합법적인 사용자 또는 응용 프로그램이 컨테이너에서 비정상적으로 많은 양의 데이터를 추출 했습니다. 예: 유지 관리 백업 작업.|

자세한 내용은 [Azure Cosmos DB에 대 한 Advanced Threat Protection](../cosmos-db/cosmos-db-advanced-threat-protection.md)을 참조 하세요.