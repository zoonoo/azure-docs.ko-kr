---
title: Azure Security Center에 GCP 계정 연결
description: Azure Security Center에서 GCP 리소스 모니터링
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c4020bc5e96ff35fa2d04a872216e43abbba5323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328312"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>Azure Security Center에 GCP 계정 연결

클라우드 워크 로드가 일반적으로 여러 클라우드 플랫폼을 사용 하는 경우 클라우드 보안 서비스는 동일한 작업을 수행 해야 합니다.

Azure Security Center는 Azure, Amazon Web Services (AWS) 및 Google Cloud Platform (GCP)에서 워크 로드를 보호 합니다.

GCP 계정을 Security Center에 등록 하 고 GCP 보안 명령과 Azure Security Center를 통합 합니다. 따라서 Security Center은 다음을 제공 하기 위해 이러한 클라우드 환경 모두에서 가시성과 보호를 제공 합니다.

- 보안 오류 감지
- Security Center 권장 사항 및 GCP 보안 명령 센터의 결과를 보여주는 단일 보기
- Security Center의 보안 점수 계산에 GCP 리소스 통합
- CI 표준을 기반으로 하는 GCP 보안 명령 센터 권장 사항을 Security Center의 규정 준수 대시보드에 통합

아래 스크린샷에서는 Security Center의 개요 대시보드에 표시 되는 GCP 프로젝트를 볼 수 있습니다.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="Security Center 개요 대시보드에 나열 된 GCP 프로젝트 3 개" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|미리 보기|
|결정|[서버용 Azure Defender](defender-for-servers-introduction.md) 가 필요 합니다.|
|필요한 역할 및 사용 권한:|관련 Azure 구독에 대 한 **소유자** 또는 **참가자**|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/no-icon.png) 국가/소 버린 (US Gov, 중국 .Gov, 기타 .Gov)|
|||

## <a name="connect-your-gcp-account"></a>GCP 계정 연결

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>1단계. Security Health Analytics를 사용 하 여 GCP 보안 명령 센터 설정

조직의 모든 GCP 프로젝트에 대해 다음을 수행 해야 합니다.

1. [Gcp 설명서의 이러한 지침을](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup)사용 하 여 **Gcp 보안 명령 센터** 를 설정 합니다.
1. [GCP 설명서에서 이러한 지침을](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics)사용 하 여 **보안 상태 분석** 을 사용 하도록 설정 합니다.
1. 보안 명령 센터로 흐르는 데이터가 있는지 확인 합니다.

보안 구성에 GCP 환경을 연결 하는 방법에 대 한 지침은 Google의 보안 구성 권장 사항 사용에 대 한 권장 사항을 따릅니다. 통합은 Google 보안 명령 센터를 활용 하 고 청구에 영향을 줄 수 있는 추가 리소스를 사용 합니다.

처음으로 보안 상태 분석을 사용 하도록 설정 하는 경우 데이터를 사용 하는 데 몇 시간이 걸릴 수 있습니다.


### <a name="step-2-enable-gcp-security-command-center-api"></a>2단계. GCP 보안 명령 센터 API 사용

1. Google의 **클라우드 콘솔 API 라이브러리**에서 Azure Security Center에 연결 하려는 프로젝트를 선택 합니다.
1. API 라이브러리에서 **보안 명령 센터 API**를 찾아 선택 합니다.
1. API 페이지에서 **사용**을 선택 합니다.

[보안 명령 센터 API](https://cloud.google.com/security-command-center/docs/reference/rest/)에 대해 자세히 알아보세요.


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>3단계. 보안 구성 통합을 위한 전용 서비스 계정 만들기

1. **Gcp 콘솔**에서 Security Center에 연결 하려는 프로젝트를 선택 합니다.
1. **탐색 메뉴**의 **IAM & 관리** 옵션에서 **서비스 계정**을 선택 합니다.
1. **서비스 계정 만들기**를 선택 합니다.
1. 계정 이름을 입력 하 고 **만들기**를 선택 합니다.
1. **역할** 을 **Security Center 관리 뷰어로**지정 하 고 **계속**을 선택 합니다.
1. 사용자에 게 **이 서비스 계정에 대 한 액세스 권한 부여** 섹션은 선택 사항입니다. **완료**를 선택합니다.
1. 만든 서비스 계정의 **전자 메일 값** 을 복사 하 고 나중에 사용할 수 있도록 저장 합니다.
1. **탐색 메뉴**의 **iam & 관리** 옵션에서 **iam** 을 선택 합니다.
    1. 조직 수준으로 전환 합니다.
    1. **추가**를 선택 합니다.
    1. **새 구성원** 필드에 앞에서 복사한 **전자 메일 값** 을 붙여넣습니다.
    1. 역할을 **Security Center 관리 뷰어로** 지정 하 고 저장을 선택 합니다.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="관련 GCP 권한 설정":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>4단계. 전용 서비스 계정에 대 한 개인 키 만들기
1. 프로젝트 수준으로 전환 합니다.
1. **탐색 메뉴**의 **IAM & 관리** 옵션에서 **서비스 계정**을 선택 합니다.
1. 전용 서비스 계정을 열고 편집을 선택 합니다.
1. **키** 섹션에서 **키 추가** 를 선택한 다음 **새 키 만들기**를 선택 합니다.
1. 개인 키 만들기 화면에서 **JSON**을 선택 하 고 **만들기**를 선택 합니다.
1. 나중에 사용 하기 위해이 JSON 파일을 저장 합니다.


### <a name="step-5-connect-gcp-to-security-center"></a>5단계. Security Center에 GCP 연결 
1. Security Center의 메뉴에서 **클라우드 커넥터**를 선택 합니다.
1. GCP 계정 추가를 선택 합니다.
1. 온 보 딩 페이지에서 다음을 수행한 **후 다음을 선택 합니다**.
    1. 선택한 구독의 유효성을 검사 합니다.
    1. **표시 이름** 필드에 커넥터에 대 한 표시 이름을 입력 합니다.
    1. **조직 id** 필드에 조직의 id를 입력 합니다. 모르는 경우 [조직 만들기 및 관리](https://cloud.google.com/resource-manager/docs/creating-managing-organization)를 참조 하세요.
    1. **개인 키** 파일 상자에서 4 단계에서 다운로드 한 JSON 파일을 찾습니다 [. 전용 서비스 계정에 대 한 개인 키를 만듭니다](#step-4-create-a-private-key-for-the-dedicated-service-account).


### <a name="step-6-confirmation"></a>6단계. 확인

커넥터가 성공적으로 만들어지고 GCP 보안 명령 센터가 올바르게 구성 된 경우:

- GCP CI 표준은 Security Center의 규정 준수 대시보드에 표시 됩니다.
- GCP 리소스에 대 한 보안 권장 사항은 Security Center 포털에 표시 되 고, 등록을 완료 한 후 규정 준수 대시보드 5-10 분이 표시 됩니다.   :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="Security Center의 권장 사항 페이지에서 Gcp 리소스 및 권장 사항 페이지":::


## <a name="monitoring-your-gcp-resources"></a>GCP 리소스 모니터링

위와 같이 Azure Security Center의 보안 권장 사항 페이지에는 진정한 다중 클라우드 보기를 위해 Azure 및 AWS 리소스와 함께 GCP 리소스가 표시 됩니다.

리소스 유형별 리소스에 대 한 모든 활성 권장 사항을 보려면 Security Center의 asset inventory 페이지를 사용 하 여 관심 있는 GCP 리소스 종류를 필터링 합니다.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="GCP 옵션을 보여 주는 자산 인벤토리 페이지의 리소스 종류 필터"::: 


## <a name="next-steps"></a>다음 단계

GCP 계정 연결은 Azure Security Center에서 사용할 수 있는 다중 클라우드 환경의 일부입니다. 관련 정보는 다음 페이지를 참조 하세요.

- [Azure Security Center에 AWS 계정 연결](quickstart-onboard-aws.md)
