---
title: Azure Cosmos DB에 대 한 Advanced Threat Protection
description: 미사용 데이터의 암호화 및 구현 방법을 Azure Cosmos DB 하는 방법에 대해 알아봅니다.
author: monhaber
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: v-mohabe
ms.custom: seodec18
ms.openlocfilehash: 02281a1cad9c7e6f9680441a699fa5d34558b890
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501495"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db"></a>Azure Cosmos DB에 대 한 Advanced Threat Protection

Azure Cosmos DB에 대 한 Advanced Threat Protection은 Azure Cosmos DB 계정에 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도를 감지 하는 추가 보안 인텔리전스 계층을 제공 합니다. 이 보호 계층을 사용 하면 보안 전문가 없이도 위협을 해결 하 고 중앙 보안 모니터링 시스템과 통합할 수 있습니다.

보안 경고는 활동의 비정상 현상이 발생할 때 트리거됩니다. 이러한 보안 경고는  [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합 되며, 의심 스러운 활동의 세부 정보와 위협을 조사 하 고 수정 하는 방법에 대 한 권장 사항을 포함 하 여 전자 메일을 통해 구독 관리자에 게 전송 됩니다.

> [!NOTE]
>
> * Azure Cosmos DB에 대 한 Advanced Threat Protection은 현재 SQL API에 대해서만 사용할 수 있습니다.
> * Azure Cosmos DB에 대 한 Advanced Threat Protection은 현재 Azure 정부 및 소 버린 클라우드 지역에서 사용할 수 있습니다.

보안 경고에 대 한 전체 조사 환경을 위해 모든 문서, 컨테이너 및 데이터베이스에 대 한 CRUD 작업을 포함 하 여 데이터베이스 자체에 대 한 작업을 기록 하는 [Azure Cosmos DB에서 진단 로깅을](https://docs.microsoft.com/azure/cosmos-db/logging)사용 하는 것이 좋습니다.

## <a name="set-up-advanced-threat-protection"></a>Advanced Threat Protection 설정

### <a name="set-up-atp-using-the-portal"></a>포털을 사용 하 여 ATP 설정

1. 에서  [https://portal.azure.com](https://portal.azure.com/)Azure Portal를 시작 합니다.

2. Azure Cosmos DB 계정의 **설정** 메뉴에서 **고급 보안**을 선택 합니다.

    ![ATP 설정](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3.  **고급 보안** 구성 블레이드에서 다음을 수행 합니다.

    * **Advanced Threat Protection** 옵션을 클릭 하 여 **켜기**로 설정 합니다.
    *  **** 저장 을 클릭 하 여 새로운 또는 업데이트 된 Advanced Threat Protection 정책을 저장 합니다.   

### <a name="set-up-atp-using-rest-api"></a>REST API를 사용 하 여 ATP 설정

Rest API 명령을 사용 하 여 특정 Azure Cosmos DB 계정에 대 한 Advanced Threat Protection 설정을 만들거나 업데이트 하거나 가져옵니다.

* [Advanced Threat Protection-만들기](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Advanced Threat Protection-Get](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 ATP 설정

다음 PowerShell cmdlet을 사용 합니다.

* [Advanced Threat Protection 사용](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Advanced Threat Protection 가져오기](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Advanced Threat Protection 사용 안 함](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

## <a name="manage-atp-security-alerts"></a>ATP 보안 경고 관리

Azure Cosmos DB 작업에 대 한 예외가 발생할 경우 의심 스러운 보안 이벤트에 대 한 정보를 사용 하 여 보안 경고가 트리거됩니다. 

 Azure Security Center에서 현재 [보안 경고](../security-center/security-center-alerts-overview.md)를 검토 하 고 관리할 수 있습니다.  가능한 원인 및 권장 조치를 확인 하 여 잠재적인 위협을 조사 하 고 완화 하는 [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) 에서 특정 경고를 클릭 합니다. 다음 이미지는 Security Center에서 제공 하는 경고 정보의 예를 보여 줍니다.

 ![위협 세부 정보](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

또한 경고 세부 정보 및 권장 조치를 사용 하 여 전자 메일 알림이 전송 됩니다. 다음 그림은 경고 전자 메일의 예를 보여 줍니다.

 ![경고 정보](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>ATP 경고 Cosmos DB

 Azure Cosmos DB 계정을 모니터링할 때 생성 되는 경고 목록을 보려면 Security Center 설명서의 [Cosmos DB 경고](../security-center/security-center-alerts-data-services.md#cosmos-db) 섹션을 참조 하십시오.

## <a name="next-steps"></a>다음 단계

*  [Azure Cosmos DB의 진단 로깅](https://docs.microsoft.com/azure/cosmos-db/logging#turn-on-logging-in-the-azure-portal) 에 대해 자세히 알아보세요.
*  [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 에 대 한 자세한 정보