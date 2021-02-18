---
title: Azure Monitor에서 IT 서비스 관리 커넥터-보안 내보내기
description: 이 문서에서는 itsm 제품/서비스를 Azure Monitor의 보안 내보내기와 연결 하 여 ITSM 작업 항목을 중앙에서 모니터링 하 고 관리 하는 방법을 보여 줍니다.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 6b273b864c8329547ef91eb7063a064e0f5cb77d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620874"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>보안 내보내기를 사용 하 여 Azure를 ITSM 도구에 연결

이 문서에서는 보안 내보내기를 사용 하 여 ITSM (IT Service Management) 제품 또는 서비스 간 연결을 구성 하는 방법을 보여 줍니다.

보안 내보내기는 [ITSMC (IT 서비스 관리 커넥터](../platform/itsmc-overview.md)의 업데이트 된 버전입니다. 두 버전 모두 Azure Monitor에서 경고를 보낼 때 ITSM 도구에서 작업 항목을 만들 수 있습니다. 이 기능에는 메트릭, 로그 및 활동 로그 경고가 포함 됩니다.

ITSMC는 사용자 이름 및 암호 자격 증명을 사용 합니다. 보안 내보내기는 Azure Active Directory (Azure AD)를 사용 하기 때문에 더 강력한 인증이 있습니다. Azure AD는 Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스입니다. 사용자가 로그인 하 고 내부 또는 외부 리소스에 액세스할 수 있습니다. ITSM에서 Azure AD를 사용 하면 외부 시스템으로 보낸 azure 경고 (Azure AD 응용 프로그램 ID를 통해)를 식별할 수 있습니다.

> [!NOTE]
> 보안 내보내기를 사용 하 여 Azure를 ITSM 도구에 연결 하는 기능은 미리 보기 상태입니다.

## <a name="secure-export-architecture"></a>보안 내보내기 아키텍처

보안 내보내기 아키텍처에는 다음과 같은 새로운 기능이 도입 되었습니다.

* **새 작업 그룹**: itsm가 사용 하는 itsm 작업 그룹 대신 보안 Webhook 작업 그룹을 통해 itsm 도구에 경고를 보냅니다.
* **AZURE ad 인증**: 인증은 사용자 이름/암호 자격 증명 대신 azure ad를 통해 수행 됩니다.

## <a name="secure-export-data-flow"></a>보안 내보내기 데이터 흐름

보안 내보내기 데이터 흐름의 단계는 다음과 같습니다.

1. Azure Monitor는 보안 내보내기를 사용 하도록 구성 된 경고를 보냅니다.
2. 경고 페이로드는 보안 Webhook 작업을 통해 ITSM 도구에 전송 됩니다.
3. Itsm 응용 프로그램은 ITSM 도구를 시작할 수 있는 권한이 있는 경우 Azure AD를 사용 하 여 확인 합니다.
4. 경고에 권한이 부여 되 면 응용 프로그램은 다음을 수행 합니다.
   
   1. ITSM 도구에서 작업 항목 (예: 인시던트)을 만듭니다.
   2. CI (구성 항목)의 ID를 CMDB (고객 관리 데이터베이스)에 바인딩합니다.

![ITSM 도구가 Azure A D, Azure 경고 및 작업 그룹과 통신 하는 방법을 보여 주는 다이어그램입니다.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>보안 내보내기의 이점

통합의 주요 이점은 다음과 같습니다.

* **향상 된 인증**: Azure AD는 itsmc에서 자주 발생 하는 시간 제한 없이 더 안전한 인증을 제공 합니다.
* **ITSM 도구에서 해결 된 경고**: 메트릭 경고는 "발생" 및 "해결 됨" 상태를 구현 합니다. 조건을 충족 하는 경우 경고 상태는 "발생"입니다. 조건이 더 이상 충족 되지 않을 경우 경고 상태는 "해결 됨"입니다. ITSMC에서는 경고를 자동으로 해결할 수 없습니다. 보안 내보내기를 사용 하면 해결 됨 상태가 ITSM 도구로 흘러 자동으로 업데이트 됩니다.
* **[일반적인 경고 스키마](./alerts-common-schema.md)**: itsmc에서 경고 페이로드의 스키마는 경고 유형에 따라 달라 집니다. 보안 내보내기에는 모든 경고 유형에 대 한 공통 스키마가 있습니다. 이 공통 스키마에는 모든 경고 유형에 대 한 CI가 포함 되어 있습니다. 모든 경고 유형은 CI를 CMDB에 바인딩할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 경고에서 ITSM 작업 항목 만들기](../platform/itsmc-overview.md)
