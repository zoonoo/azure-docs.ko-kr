---
title: Azure Security Center에서 데이터 서비스에 대 한 검색 위협 | Microsoft Docs
description: 이 항목에서는 Azure Security Center에서 사용할 수 있는 데이터 서비스 경고를 표시합니다.
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
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 87cfd2769e473d26c2dcae1b7b418f6fb1739915
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626283"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Azure Security Center에서 데이터 서비스에 대 한 위협 검색

 Security Center는 데이터 저장소 서비스의 로그를 분석 하 고 데이터 리소스에 위협이 감지 되 면 경고를 트리거합니다. 이 항목에서는 다음 서비스에 대 한 Security Center를 생성 하는 경고를 나열 합니다.

* [Azure SQL Database 및 SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)

## Azure SQL Database 및 SQL Data Warehouse <a name="data-sql"></a>

SQL 위협 감지는 비정상적이 고 잠재적으로 해로운를 나타내는 비정상적인 활동을 감지는 데이터베이스 액세스 또는 악용 하려고 시도 합니다. Security Center는 SQL 감사 로그를 분석 하 고 SQL 엔진의 고유 하 게 실행 합니다.

|경고|Description|
|---|---|
|**SQL 삽입에 대 한 취약점**|응용 프로그램 데이터베이스에 잘못 된 SQL 문을 생성 했습니다. 이 SQL 주입 공격에 대 한 가능한 취약점을 나타낼 수 있습니다. 잘못된 문 생성에 대한 두 가지 가능한 이유가 있습니다. 응용 프로그램 코드에서 결함을 잘못 된 SQL 문을 생성 합니다. 또는 응용 프로그램 코드 또는 저장된 프로시저 SQL 삽입에 악용 될 수 있는 잘못 된 SQL 문을 생성할 때 사용자 입력을 삭제 하지 않았습니다.|
|**잠재적인 SQL 삽입**|SQL 주입 공격에 취약 한 식별 된 응용 프로그램에 대 한 활성 악용 발생 했습니다. 이 공격자가 취약 한 응용 프로그램 코드를 사용 하 여 악성 SQL 문을 삽입 하려고 하거나 저장 프로시저를 의미 합니다.|
|**비정상적인 위치에서 액세스**|사용자가 로그온 한 위치 SQL server에 비정상적인 지리적 위치에서 SQL server에 대 한 액세스 패턴이 변경이 되었습니다. 일부 경우에서 경고는 합법적인 작업(새 애플리케이션 또는 개발자 유지 관리)을 검색합니다. 하고 악의적인 작업(퇴사 직원, 외부 공격자)을 감지하기도 합니다.|
|**알 수 없는 보안 주체에서 액세스**|SQL server에 대 한 액세스 패턴이 변경 된-누군가가 비정상적인 보안 주체 (SQL 사용자)를 사용 하 여 SQL server에 로그온 합니다. 경고는 합법적인 작업(새 애플리케이션, 개발자 유지 관리)을 감지하기도 하고 악의적인 작업(퇴사 직원, 외부 공격자)을 감지하기도 합니다. 하고 악의적인 작업(퇴사 직원, 외부 공격자)을 감지하기도 합니다.|
|**잠재적으로 위험한 응용 프로그램에서 액세스**|응용 프로그램을 잠재적으로 위험한 데이터베이스 액세스에 사용 되었습니다. 일부 경우에서 경고는 작업의 침투 테스트를 감지합니다. 다른 경우에서 경고는 일반적인 공격 도구를 사용하는 공격을 감지합니다.|
|**SQL 자격 증명 (brute force)**|비정상적인 많은 수의 다른 자격 증명을 사용 하 여 실패 한 로그인이 발생 했습니다. 일부 경우에서 경고는 작업의 침투 테스트를 감지합니다. 다른 경우에서 경고는 무차별 암호 대입 공격(brute force attack)을 검색합니다.|

SQL 위협 감지 경고 참조에 대 한 자세한 내용은[Azure SQL Database 위협 감지](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview), 위협 검색 경고 섹션을 검토 합니다. 도 참조 하세요 [방법을 Azure Security Center는 Cyberattack 표시를 통해](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) Security Center가 공격을 검색할 악성 SQL 작업 검색을 사용 하는 방법의 예제를 보려면.

## Azure Storage<a name="azure-storage"></a>

>[!NOTE]
> Azure Storage에 대 한 advanced Threat Protection은 현재 Blob 저장소에 대 한 합니다. 

Azure Storage를 위한 Advanced Threat Protection은 스토리지 계정에 대한 비정상적이며 잠재적으로 유해한 액세스 또는 악용 시도를 탐지하는 추가 보안 인텔리전스 계층을 제공합니다. 이 수준의 보호 기능을 사용 하면 보안 전문가 되어 보안 모니터링 시스템을 관리할 필요 없이 위협을 해결 하기 할 수 합니다.

Security Center는 읽기, 쓰기 및 위협 검색, Blob storage로 삭제 요청의 진단 로그를 분석 및 변칙 활동에서 발생 하는 경우 경고를 트리거합니다. 자세한 내용은를 참조 하세요 [저장소 분석 로깅을 구성](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging) 자세한 내용은 합니다.

> [!div class="mx-tableFixed"]

|경고|Description|
|---|---|
|**비정상적인 위치 액세스 이상**|샘플링 된 네트워크 트래픽 분석 리소스 배포에서 원래 비정상적으로 나가는 원격 데스크톱 프로토콜 (RDP) 통신이 검색 되었습니다. 이 작업은이 환경에 대해 비정상적으로 간주 및 리소스 이용 당하는 이제는 무작위 외부 RDP 엔드포인트를 나타낼 수 있습니다. 이러한 유형의 활동으로 인해 외부 엔터티가 IP에 악성 플래그를 설정할 수 있습니다.|
|**응용 프로그램 액세스 이상**|비정상적인 응용 프로그램에이 저장소 계정 액세스를 나타냅니다. 잠재적 원인이 될 수는 공격자가 새 응용 프로그램을 사용 하 여 저장소 계정에 액세스 하는 합니다.|
|**익명 액세스 이상**|저장소 계정에 대 한 액세스 패턴이 변경 중임을 나타냅니다. 예를 들어 계정에 액세스 익명으로 (인증 없이)는 예기치 않은이 계정에 최근 액세스 패턴과 비교 합니다. 잠재적 원인이 될 수는 공격자가 보유 개의 저장소 blob는 컨테이너를 공용 읽기 액세스를 악용에 있습니다.|
|**데이터 반출 변칙**|이 저장소 컨테이너에서 최근 작업에 비해 비정상적으로 많은 양의 데이터를 추출 된 나타냅니다. 잠재적 원인이 될 수는 공격자가 추출 하는 많은 양의 데이터를 보유 개의 저장소 blob는 컨테이너에서.|
|**예기치 않은 삭제 이상**|이 계정에 최근 작업과 비교 하 여 저장소 계정에서 하나 이상의 예기치 않은 삭제 작업이 발생 했음을 나타냅니다. 잠재적 원인이 될 수는 공격자가 저장소 계정에서 데이터를 삭제 했습니다.|
|**Azure 클라우드 서비스 패키지 업로드**|Azure 클라우드 서비스 패키지 (.cspkg 파일)를이 계정에 대 한 최근 활동에 비해 비정상적인 방식으로 저장소 계정에 업로드 된 있는지를 나타냅니다. 잠재적 원인이 될 수는 공격자가 악성 코드 저장소 계정에서 Azure 클라우드 서비스를 배포 하려면 준비 중 되었습니다.|
|**권한 액세스 이상**|이 저장소 컨테이너의 액세스 권한을 비정상적인 방식으로 바뀐 것을 나타냅니다. 잠재적 원인은 공격자 컨테이너 권한을 해당 보안 상태가 약화 또는 지 속성에 대 한 변경 된 것입니다.|
|**검사 액세스 이상**|저장소 계정의 액세스 권한을이 계정에 대 한 최근 활동에 비해 비정상적인 방식으로 확인 되는 것을 나타냅니다. 잠재적 원인은 공격자가 향후 공격에 대 한 정찰을 수행 했는지 보여 줍니다.|
|**데이터 탐색 이상**|Blob 또는 컨테이너를 저장소 계정에이 계정에 대 한 최근 활동에 비해 비정상적인 방식으로 열거를 나타냅니다. 잠재적 원인은 공격자가 향후 공격에 대 한 정찰을 수행 했는지 보여 줍니다.|

>[!NOTE]
>Azure Storage에 대 한 advanced Threat Protection은 현재 Azure government 및 소 버린 클라우드 지역에서 지원 되지.

저장소에 대 한 경고에 대 한 자세한 내용은 참조는 [Azure Storage에 대 한 Advanced Threat Protection](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection) 기사 및 보호 경고 섹션을 검토 합니다.
