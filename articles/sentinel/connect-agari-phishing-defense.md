---
title: Azure Sentinel에 Agari 피싱 방어 및 브랜드 보호 솔루션 연결 | Microsoft Docs
description: Agari 피싱 방어 및 브랜드 보호 커넥터를 사용하여 Azure Sentinel로 로그를 가져오는 방법에 대해 알아봅니다. 통합 문서에서 Agari 데이터를 보고, 경고를 만들고, 조사를 개선합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: a60a0291d6669b2a9115dffa8e0d4d63fae4a440
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724441"
---
# <a name="connect-your-agari-phishing-defense-and-brand-protection-solutions-to-azure-sentinel"></a>Azure Sentinel에 Agari 피싱 방어 및 브랜드 보호 솔루션 연결

> [!IMPORTANT]
> Agari 피싱 방어 및 브랜드 보호 커넥터는 현재 **PREVIEW** 상태입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Agari 피싱 방어 및 브랜드 보호 커넥터를 사용하면 사용자가 통합 문서에서 데이터를 보고, 데이터를 쿼리하여 사용자 지정 경고를 만들고, 조사를 개선하기 위해 데이터를 통합할 수 있도록 브랜드 보호 및 피싱 방어 솔루션의 로그를 Azure Sentinel에 쉽게 연결할 수 있습니다. Agari의 솔루션은 Azure Functions 및 REST API를 사용하여 Azure Sentinel과 통합됩니다.

또한 브랜드 보호 및 피싱 응답 고객은 Security Graph API를 통해 위협 인텔리전스 공유를 활용할 수 있습니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="prerequisites"></a>사전 요구 사항

Agari의 피싱 방어 및 브랜드 보호 솔루션을 Azure Sentinel에 연결하려면 다음이 필요합니다.

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한입니다.

- 작업 영역 공유 키에 대한 읽기 권한. [작업 영역 키에 대해 자세히 알아보세요.](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)

- 함수 앱을 작성하기 위한 Azure Functions에 대한 읽기 및 쓰기 권한. [Azure Functions에 대해 자세히 알아보세요](../azure-functions/index.yml).

- Agari **클라이언트 ID** 및 **비밀 키**(모든 Agari 솔루션에서 동일)가 있는지 확인합니다. [Agari 개발자 사이트](https://developers.agari.com/agari-platform/docs/quick-start)의 지침을 참조하세요.

## <a name="configure-and-connect-agari-solutions"></a>Agari 솔루션 구성 및 연결 

Agari 솔루션은 Azure 함수 앱을 사용하여 Azure Sentinel에 직접 로그를 통합하고 내보낼 수 있습니다.

> [!NOTE]
> 이 커넥터는 Azure Functions를 사용하여 Agari의 솔루션에 연결하여 해당 로그를 Azure Sentinel로 끌어옵니다. 이로 인해 데이터 수집 비용이 추가로 발생할 수 있습니다. 자세한 내용은 [Azure Functions 가격 책정](https://azure.microsoft.com/pricing/details/functions/) 페이지를 확인하세요.

1. **Agari API 자격 증명을 수집합니다.** 

    Agari **클라이언트 ID** 및 **비밀 키** 가 있는지 확인합니다. 지침은 [Agari 개발자 사이트](https://developers.agari.com/agari-platform/docs/quick-start#generate-api-credentials)에서 찾을 수 있습니다.

1. **(선택사항) Security Graph API를 사용하도록 설정합니다.** 

    Agari 함수 앱을 사용하여 Security Graph API를 통해 Azure Sentinel과 위협 인텔리전스를 공유할 수 있습니다. 이 기능을 사용하려면 [Sentinel 위협 인텔리전스 플랫폼 커넥터](connect-threat-intelligence.md)를 사용하도록 설정하고 Azure Active Directory에 [애플리케이션을 등록](/graph/auth-register-app-v2)해야 합니다.

    이 프로세스는 아래의 함수 앱을 배포할 때 사용할 세 가지 정보(**Graph 테넌트 ID**, **Graph 클라이언트 ID** 및 **Graph 클라이언트 비밀**)를 제공합니다.

1. **커넥터 및 연결된 Azure 함수 앱을 배포합니다.** 

    1. Azure Sentinel 포털에서 **데이터 커넥터** 를 선택합니다. **Agari 피싱 방어 및 브랜드 보호(미리 보기)** 를 선택한 다음 **커넥터 페이지 열기** 를 선택합니다.

    1. **구성** 에서 Azure Sentinel **작업 영역 ID** 와 **기본 키** 를 복사하여 옆에 붙여넣습니다.

    1. **Azure에 배포** 를 선택합니다. (단추를 찾으려면 아래로 스크롤해야 할 수도 있습니다.)

    1. **사용자 지정 배포** 화면이 나타납니다.

        - Agari **클라이언트 ID** 및 **클라이언트 비밀**(비밀 키)를 입력합니다.

        - 복사하여 따로 보관한 Azure Sentinel **작업 영역 ID** 및 **작업 영역 키**(기본 키)를 입력합니다.

        - 활성 구독이 있는 Agari 솔루션에 대해 **True** 또는 **False** 를 선택합니다.

        - Security Graph API를 사용하여 Azure Sentinel과 IoC를 공유할 Azure 애플리케이션을 만든 경우 **Security Graph 공유 사용** 에 대해 **True** 를 선택하고 **Graph 테넌트 ID**, **Graph 클라이언트 ID**, **Graph 클라이언트 비밀** 를 입력합니다.

    1. **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기** 를 클릭합니다.

1. **함수 앱에 필요한 권한 할당:**

    Agari 커넥터는 환경 변수를 사용하여 로그 액세스 타임스탬프를 저장합니다. 애플리케이션이 이 변수에 쓰려면 시스템 할당 ID에 사용 권한을 할당해야 합니다.

    1. Azure Portal에서 **함수 앱** 으로 이동합니다.

    1. **함수 앱** 블레이드의 목록에서 함수 앱을 선택한 다음 함수 앱 탐색 메뉴의 **설정** 에서 **ID** 를 선택합니다.

    1. **시스템 할당** 탭에서 **상태** 를 **켜기** 로 설정합니다. 

    1. **저장** 을 선택하면 **Azure 역할 할당** 단추가 나타납니다. 이 단추를 클릭하십시오.

    1. **Azure 역할 할당** 화면에서 **역할 할당 추가** 를 선택합니다. **범위** 를 **구독** 으로 설정하고, **구독** 드롭다운에서 구독을 선택하고, **역할** 을 **앱 구성 데이터 소유자** 로 설정합니다. 

    1. **저장** 을 선택합니다.

## <a name="find-your-data"></a>데이터 찾기

성공적으로 연결되면 다음 표의 *CustomLogs* 아래의 **Logs** 에 데이터가 나타납니다. 

- `agari_apdtc_log_CL`
- `agari_apdpolicy_log_CL`
- `agari_bpalerts_log_CL`

Agari 솔루션 데이터를 쿼리하려면 쿼리 창에 위의 테이블 이름 중 하나를 입력합니다.

몇 가지 유용한 샘플 쿼리를 보려면 커넥터 페이지에서 **다음 단계** 탭을 참조하세요.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 최대 20분 가량 소요될 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel에 Agari 피싱 방어 및 브랜드 보호 솔루션을 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.