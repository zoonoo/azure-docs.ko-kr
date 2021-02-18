---
title: IT 서비스 관리 커넥터를 사용 하 여 Cherwell 연결
description: 이 문서에서는 ITSMC 작업 항목을 중앙에서 모니터링 하 고 관리 하기 위해 Azure Monitor에서 ITSMC (IT 서비스 관리 커넥터)로 Cherwell 하는 방법에 대 한 정보를 제공 합니다.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 6d9d8136ef0936927af238726e30c7feed7d0b83
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100618019"
---
# <a name="connect-cherwell-with-it-service-management-connector"></a>IT 서비스 관리 커넥터를 사용 하 여 Cherwell 연결

이 문서에서는 작업 항목을 중앙에서 관리 하기 위해 Log Analytics에서 Cherwell 인스턴스와 IT 서비스 관리 커넥터 (ITSMC) 간의 연결을 구성 하는 방법에 대 한 정보를 제공 합니다.

> [!NOTE]
> 2020 년 10 월 1 일-Cherwell ITSM와 Azure Alert의 통합은 더 이상 새 고객에 대해 사용 하도록 설정 되지 않습니다. 새 ITSM 연결은 지원 되지 않습니다.
> 기존 ITSM 연결이 지원 됩니다.

다음 섹션에서는 Cherwell 제품을 Azure의 ITSMC에 연결하는 방법을 자세히 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 필수 조건이 충족되는지 확인합니다.

- ITSMC가 설치되어 있습니다. 추가 정보: [IT 서비스 관리 커넥터 솔루션 추가](./itsmc-definition.md#add-it-service-management-connector).
- 클라이언트 ID가 생성되었습니다. 추가 정보: [Cherwell용 클라이언트 ID 생성](#generate-client-id-for-cherwell).
- 사용자 역할:  관리자.

## <a name="connection-procedure"></a>연결 절차

다음 절차에 따라 Cherwell 연결을 만듭니다.

1. Azure Portal에서 **모든 리소스** 로 이동하여 **ServiceDesk(YourWorkspaceName)** 를 찾습니다.

2. **작업 영역 데이터 원본** 에서 **ITSM 연결** 을 클릭합니다.
    ![새 연결](/media/itsmc-overview/add-new-itsm-connection.png)

3. 왼쪽 창의 맨 위에 있는 **추가** 를 클릭합니다.

4. 다음 표에 설명된 대로 정보를 제공하고 **확인** 을 클릭하여 연결을 만듭니다.

> [!NOTE]
> 이러한 모든 매개 변수는 필수입니다.

| **필드** | **설명** |
| --- | --- |
| **연결 이름**   | ITSMC에 연결하려는 Cherwell 인스턴스의 이름을 입력합니다.  이 이름은 나중에 이 ITSM의 작업 항목을 구성하고 자세한 로그 분석을 확인할 때 사용합니다. |
| **파트너 유형**   | **Cherwell** 을 선택합니다. |
| **사용자 이름**   | ITSMC에 연결할 수 있는 Cherwell 사용자 이름을 입력합니다. |
| **암호**   | 이 사용자 이름과 관련된 암호를 입력합니다. **참고:** 사용자 이름 및 암호는 인증 토큰 생성에만 사용되며 ITSMC 서비스에는 저장되지 않습니다.|
| **서버 URL**   | ITSMC에 연결하려는 Cherwell 인스턴스의 URL을 입력합니다. |
| **클라이언트 ID**   | Cherwell 인스턴스에서 생성한 이 연결을 인증하기 위한 클라이언트 ID를 입력합니다.   |
| **데이터 동기화 범위**   | ITSMC를 통해 동기화할 Cherwell 작업 항목을 선택합니다.  이러한 작업 항목을 로그 분석으로 가져옵니다.   **옵션:**  인시던트, 변경 요청. |
| **데이터 동기화** | 데이터를 원하는 이전 일 수를 입력합니다. **최대 제한**: 120일. |
| **ITSM 솔루션에서 새 구성 항목 만들기** | ITSM 제품에서 구성 항목을 만들려는 경우 이 옵션을 선택합니다. 이 옵션을 선택하면 ITSMC는 지원되는 ITSM 시스템에서 영향을 받는 CI를 구성 항목으로 만듭니다(존재하지 않는 CI의 경우). **기본**: 사용하지 않도록 설정됩니다. |

![Cherwell 연결](media/itsmc-connections-cherwell/itsm-connections-cherwell-latest.png)

**성공적으로 연결 및 동기화된 경우**:

- 이 Cherwell 인스턴스에서 선택한 작업 항목을 Azure **Log Analytics** 로 가져옵니다. IT Service Management Connector 타일에서 이러한 작업 항목에 대한 요약을 볼 수 있습니다.

- Log Analytics 경고 또는 로그 레코드에서, 또는 이 Cherwell 인스턴스의 Azure 경고에서 인시던트를 만들 수 있습니다.

자세한 정보: [Azure Alerts에서 ITSM 작업 항목 만들기](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)

### <a name="generate-client-id-for-cherwell"></a>Cherwell용 클라이언트 ID 생성

Cherwell용 클라이언트 ID/키를 생성하려면 다음 절차를 수행합니다.

1. Cherwell 인스턴스에 관리자로 로그인합니다.
2. **보안** > **REST API 클라이언트 설정 편집** 을 클릭합니다.
3. **새 클라이언트 만들기** > **클라이언트 암호** 를 선택합니다.

    ![Cherwell 사용자 ID](media/itsmc-connections-cherwell/itsmc-cherwell-client-id.png)

## <a name="next-steps"></a>다음 단계

* [ITSM 커넥터 개요](itsmc-overview.md)
* [Azure 경고에서 ITSM 작업 항목 만들기](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [ITSM 커넥터의 문제 해결](./itsmc-resync-servicenow.md)