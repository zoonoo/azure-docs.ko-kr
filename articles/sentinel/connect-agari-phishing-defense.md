---
title: Azure 센티널에 Agari 피싱 방어 및 브랜드 보호 솔루션 연결 | Microsoft Docs
description: Agari 피싱 방어 및 브랜드 보호 커넥터를 사용 하 여 Azure 센티널로 로그를 가져오는 방법에 대해 알아봅니다. 통합 문서에서 Agari 데이터를 보고, 경고를 생성 하 고, 조사를 개선 합니다.
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
ms.openlocfilehash: ba37b2280ba4d7138f4ed652b7b330bcaf7b9935
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566877"
---
# <a name="connect-your-agari-phishing-defense-and-brand-protection-solutions-to-azure-sentinel"></a>Azure 센티널에 Agari 피싱 방어 및 브랜드 보호 솔루션 연결

> [!IMPORTANT]
> Agari 피싱 방어 및 브랜드 보호 커넥터는 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

Agari 피싱 방어 및 브랜드 보호 커넥터를 사용 하면 사용자가 통합 문서에서 데이터를 보고, 데이터를 쿼리하고, 사용자 지정 경고를 만들고, 조사를 개선 하는 데 통합할 수 있도록 브랜드 보호 및 피싱 방어 솔루션의 로그를 Azure 센티널에 쉽게 연결할 수 있습니다. Agari의 솔루션은 Azure Functions 및 REST API를 사용 하 여 Azure 센티널과 통합 됩니다.

또한 브랜드 보호 및 피싱 응답 고객은 보안 Graph API을 통해 위협 인텔리전스 공유를 활용할 수 있습니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

Agari의 피싱 방어 및 브랜드 보호 솔루션을 Azure 센티널에 연결 하려면 다음이 필요 합니다.

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한입니다.

- 작업 영역에 대 한 공유 키에 대 한 읽기 권한입니다. [작업 영역 키에 대해 자세히 알아보세요](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

- Azure Functions에 대 한 읽기 및 쓰기 권한이 함수 앱을 만듭니다. [Azure Functions에 대해 자세히 알아보세요](../azure-functions/index.yml).

- Agari **클라이언트 ID** 및 **비밀 키** 가 있는지 확인 합니다 (모든 agari 솔루션에서 동일). 지침은 [Agari 개발자 사이트](https://developers.agari.com/agari-platform/docs/quick-start) 를 참조 하십시오.

## <a name="configure-and-connect-agari-solutions"></a>Agari 솔루션 구성 및 연결 

Agari 솔루션은 Azure 함수 앱를 사용 하 여 Azure 센티널에 직접 로그를 통합 하 고 내보낼 수 있습니다.

> [!NOTE]
> 이 커넥터는 Azure Functions를 사용 하 여 Agari의 솔루션에 연결 하 여 해당 로그를 Azure 센티널로 끌어옵니다. 이로 인해 데이터 수집 비용이 추가로 발생할 수 있습니다. 자세한 내용은 [Azure Functions 가격 책정](https://azure.microsoft.com/pricing/details/functions/) 페이지를 확인 하세요.

1. **Agari API 자격 증명을 수집 합니다.** 

    Agari **클라이언트 ID** 및 **비밀 키** 가 있는지 확인 합니다. 지침은 [Agari 개발자 사이트](https://developers.agari.com/agari-platform/docs/quick-start#generate-api-credentials)에서 찾을 수 있습니다.

1. **필드 보안 Graph API를 사용 하도록 설정 합니다.** 

    Agari 함수 앱를 사용 하 여 보안 Graph API를 통해 센티널과 위협 인텔리전스를 공유할 수 있습니다. 이 기능을 사용 하려면 [센티널 위협 인텔리전스 플랫폼 커넥터](connect-threat-intelligence.md) 를 사용 하도록 설정 하 고 Azure Active Directory에 [응용 프로그램을 등록](/graph/auth-register-app-v2) 해야 합니다.

    이 프로세스는 **graph 테 넌 트 ID**, **그래프 클라이언트 id** 및 **graph 클라이언트 암호** 와 같은 함수 앱를 배포할 때 사용할 수 있는 세 가지 정보를 제공 합니다.

1. **커넥터 및 연결 된 Azure 함수 앱를 배포 합니다.** 

    1. Azure 센티널 포털에서 **데이터 커넥터** 를 선택 합니다. **Agari 피싱 방어 및 브랜드 보호 (미리 보기)** 를 선택한 다음 **커넥터 페이지를 엽니다**.

    1. **구성** 아래에서 Azure 센티널 **작업 영역 ID** 및 **기본 키** 를 복사 하 고 남겨 둡니다.

    1. **Azure에 배포를** 선택 합니다. 단추를 찾으려면 아래로 스크롤해야 할 수도 있습니다.

    1. **사용자 지정 배포** 화면이 표시 됩니다.

        - Agari **클라이언트 ID** 및 **클라이언트 암호** (비밀 키)를 입력 합니다.

        - 복사 하 여 보관 한 Azure 센티널 **작업 영역 ID** 및 **작업 영역 키** (기본 키)를 입력 합니다.

        - 활성 구독이 있는 Agari 솔루션에 대해 **True** 또는 **False** 를 선택 합니다.

        - 보안 Graph API를 사용 하 여 IoCs와 Azure 센티널을 공유 하는 Azure 애플리케이션를 만든 경우 **보안 그래프 공유 사용** 에 대해 **True** 를 선택 하 고 **graph 테 넌 트 id**, **그래프 클라이언트 id** 및 **graph 클라이언트 암호** 를 입력 합니다.

    1. **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료 되 면 **만들기** 를 클릭 합니다.

1. **함수 앱에 필요한 사용 권한을 할당 합니다.**

    Agari 커넥터는 환경 변수를 사용 하 여 로그 액세스 타임 스탬프를 저장 합니다. 응용 프로그램이이 변수에 쓰도록 하려면 시스템 할당 id에 사용 권한을 할당 해야 합니다.

    1. Azure Portal에서 **함수 앱** 로 이동 합니다.

    1. **함수 앱** 블레이드의 목록에서 함수 앱를 선택 하 고 함수 앱의 탐색 메뉴에서 **설정** 아래에서 **id** 를 선택 합니다.

    1. **시스템 할당 됨** 탭에서 **상태** 를 **켜기** 로 설정 합니다. 

    1. **저장** 을 선택 하면 **Azure 역할 할당** 단추가 표시 됩니다. 이 단추를 클릭하십시오.

    1. **Azure 역할 할당** 화면에서 **역할 할당 추가** 를 선택 합니다. **범위** 를 **구독** 으로 설정 하 고 **구독** 드롭다운에서 구독을 선택한 다음 **역할** 을 **앱 구성 데이터 소유자** 로 설정 합니다. 

    1. **저장** 을 선택합니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 다음 표에서 *Customlogs* 의 **로그** 에 데이터가 나타납니다. 

- `agari_apdtc_log_CL`
- `agari_apdpolicy_log_CL`
- `agari_bpalerts_log_CL`

Agari 솔루션 데이터를 쿼리하려면 쿼리 창에서 위의 테이블 이름 중 하나를 입력 합니다.

몇 가지 유용한 샘플 쿼리는 커넥터 페이지의 **다음 단계** 탭을 참조 하세요.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics 나타날 때까지 최대 20 분이 걸릴 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 센티널에 Agari 피싱 방어 및 브랜드 보호 솔루션을 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대 한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아보세요.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
