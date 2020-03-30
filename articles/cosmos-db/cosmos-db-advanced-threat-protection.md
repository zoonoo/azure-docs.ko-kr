---
title: Azure 코스모스 DB를 위한 고급 위협 보호
description: Azure Cosmos DB가 미사용 데이터의 암호화를 제공하는 방법과 이를 구현하는 방법에 대해 알아봅니다.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: bcc1c6ffe7cdec4aed325a67969235ae993a5109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614841"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Azure 코스모스 DB에 대한 고급 위협 보호(미리 보기)

Azure Cosmos DB에 대한 고급 위협 보호는 Azure Cosmos DB 계정에 액세스하거나 악용하려는 특이하고 잠재적으로 유해한 시도를 감지하는 추가 보안 인텔리전스 계층을 제공합니다. 이 보호 계층을 사용하면 보안 전문가가 없어도 위협을 해결하고 중앙 보안 모니터링 시스템과 통합할 수 있습니다.

보안 경고는 활동의 이상 징후가 발생할 때 트리거됩니다. 이러한 보안 경고는 [Azure Security Center와](https://azure.microsoft.com/services/security-center/)통합되며 의심스러운 활동에 대한 세부 정보와 위협을 조사하고 해결하는 방법에 대한 권장 사항과 함께 구독 관리자에게 전자 메일을 통해 전송됩니다.

> [!NOTE]
>
> * Azure Cosmos DB에 대한 고급 위협 보호는 현재 SQL API에서만 사용할 수 있습니다.
> * Azure Cosmos DB에 대한 고급 위협 보호는 현재 Azure 정부 및 주권 클라우드 지역에서 사용할 수 없습니다.

보안 경고에 대한 전체 조사 환경을 위해 모든 문서, 컨테이너 및 데이터베이스에서 CRUD 작업을 포함하여 데이터베이스 자체에서 작업을 기록하는 [Azure Cosmos DB에서 진단 로깅을](https://docs.microsoft.com/azure/cosmos-db/logging)사용하도록 설정하는 것이 좋습니다.

## <a name="threat-types"></a>위협 유형

Azure Cosmos DB에 대한 고급 위협 보호는 데이터베이스에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 나타내는 비정상적인 활동을 검색합니다. 현재 다음 경고를 트리거할 수 있습니다.

- **비정상적인 위치에서의 액세스**: 이 경고는 누군가가 비정상적인 지리적 위치에서 Azure Cosmos DB 끝점에 연결한 Azure Cosmos 계정에 대한 액세스 패턴이 변경될 때 트리거됩니다. 경우에 따라 경고는 새 응용 프로그램 또는 개발자의 유지 관리 작업을 의미하는 합법적인 작업을 검색합니다. 다른 경우에는 경고가 이전 직원, 외부 공격자 등의 악의적인 작업을 감지합니다.

- **비정상적인 데이터 추출**: 클라이언트가 Azure Cosmos DB 계정에서 비정상적인 양의 데이터를 추출할 때 이 경고가 트리거됩니다. 이는 계정에 저장된 모든 데이터를 외부 데이터 저장소로 전송하기 위해 수행되는 일부 데이터 유출의 증상일 수 있습니다.

## <a name="set-up-advanced-threat-protection"></a>고급 위협 보호 설정

### <a name="set-up-atp-using-the-portal"></a>포털을 사용하여 ATP 설정

1. 에서 [https://portal.azure.com](https://portal.azure.com/)Azure 포털을 시작합니다.

2. Azure Cosmos DB 계정에서 **설정** 메뉴에서 **고급 보안을**선택합니다.

    ![ATP 설정](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. 고급 **보안** 구성 블레이드에서:

    * 고급 **위협 보호** 옵션을 클릭하여 **ON으로**설정합니다.
    * **저장**을 클릭하여 새로운 또는 업데이트된 Advanced Threat Protection 정책을 저장합니다.   

### <a name="set-up-atp-using-rest-api"></a>REST API를 사용하여 ATP 설정

나머지 API 명령을 사용하여 특정 Azure Cosmos DB 계정에 대한 고급 위협 보호 설정을 만들거나 업데이트하거나 가져옵니다.

* [고급 위협 보호 - 생성](https://go.microsoft.com/fwlink/?linkid=2099745)
* [고급 위협 보호 - 받기](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Azure PowerShell을 사용하여 ATP 설정

다음 PowerShell cmdlet을 사용합니다.

* [Advanced Threat Protection을 사용하도록 설정](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [고급 위협 보호](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [고급 위협 보호 비활성화](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 사용

Azure 리소스 관리자 템플릿을 사용하여 고급 위협 보호가 활성화된 Cosmos DB를 설정합니다.
자세한 내용은 [고급 위협 보호를 사용하여 CosmosDB 계정 만들기를](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)참조하십시오.

### <a name="using-azure-policy"></a>Azure 정책 사용

Azure 정책을 사용하여 Cosmos DB에 대한 고급 위협 보호를 사용하도록 설정합니다.

1. Azure 정책 - 정의 페이지를 시작하고 Cosmos DB 정책에 **대한 고급 위협 보호 배포를** **검색합니다.**

    ![정책 검색](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. **CosmosDB** 정책에 대한 고급 위협 보호 배포를 클릭한 다음 **에 할당을**클릭합니다.

    ![구독 또는 그룹 선택](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. **Scope** 필드에서 세 개의 점을 클릭하고 Azure 구독 또는 리소스 그룹을 선택한 다음 **선택**을 클릭합니다.

    ![정책 정의 페이지](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. 다른 매개 변수를 입력하고 **에 할당을**클릭합니다.

## <a name="manage-atp-security-alerts"></a>ATP 보안 경고 관리

Azure Cosmos DB 활동 이상이 발생하면 의심스러운 보안 이벤트에 대한 정보로 보안 경고가 트리거됩니다. 

 Azure 보안 센터에서 현재 보안 경고를 검토하고 관리할 수 [있습니다.](../security-center/security-center-alerts-overview.md)  [보안 센터에서](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) 특정 경고를 클릭하여 잠재적인 위협을 조사하고 완화하는 가능한 원인과 권장 조치를 확인합니다. 다음 이미지는 보안 센터에서 제공하는 경고 세부 정보의 예를 보여 주며 있습니다.

 ![위협 세부 정보](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

경고 세부 정보 및 권장 작업과 함께 전자 메일 알림도 전송됩니다. 다음 이미지는 경고 전자 메일의 예를 보여 주며 있습니다.

 ![경고 세부 정보](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>코스모스 DB ATP 경고

 Azure Cosmos DB 계정을 모니터링할 때 생성된 경고 목록을 보려면 Azure 보안 센터 설명서의 [Cosmos DB 경고](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos) 섹션을 참조하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure 코스모스 DB의 진단 로깅에](cosmosdb-monitor-resource-logs.md) 대해 자세히 알아보기
* [Azure 보안 센터에](https://docs.microsoft.com/azure/security-center/security-center-intro) 대해 자세히 알아보기
