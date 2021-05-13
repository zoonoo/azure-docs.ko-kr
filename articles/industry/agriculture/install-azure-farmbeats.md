---
title: Azure FarmBeats 설치
description: 이 문서는 Azure 구독에서 Azure FarmBeats를 설치하는 방법을 설명합니다.
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 5863dcc20fb13f1bb203c68ad168655371130601
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674561"
---
# <a name="install-azure-farmbeats"></a>Azure FarmBeats 설치

이 문서는 Azure 구독에서 Azure FarmBeats를 설치하는 방법을 설명합니다.

Azure FarmBeats는 Azure Marketplace에서 사용할 수 있는 B2B(Business-to-Business) 제품입니다. 이를 통해 공급자 간 농업 데이터 세트를 집계하고 실행 가능한 인사이트를 생성할 수 있습니다. Azure FarmBeats는 융합된 데이터 세트를 기반으로 AI(인공 지능) 또는 ML(기계 학습) 모델을 구축하도록 해 이와 같이 할 수 있습니다. Azure FarmBeats의 두 가지 기본 구성 요소는 다음과 같습니다.

- **Datahub**: 여러 공급자의 다양한 농업 데이터 세트를 집계, 정규화 및 컨텍스트화할 수 있도록 하는 API 계층입니다.

- **가속기**: Datahub를 바탕으로 구축된 웹 애플리케이션입니다. 모델 개발 및 시각화를 바로 시작합니다. 가속기는 Azure FarmBeats API를 활용하여 수집된 센서 데이터를 차트로 시각화하고 모델 출력을 지도로 시각화하는 방법을 보여 줍니다.

## <a name="general-information"></a>일반 정보

### <a name="components-installed"></a>설치된 구성 요소

Azure FarmBeats를 설치하면 Azure 구독에서 다음 리소스가 프로비저닝됩니다.

| 설치된 Azure 리소스  | Azure FarmBeats 구성 요소  |
|---------|---------|
| Application Insights   |      Datahub 및 가속기      |
| App Service     |     Datahub 및 가속기     |
| App Service 계획   | Datahub 및 가속기  |
| API 연결    |  Datahub       |
| Azure Cache for Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Datahub 및 가속기      |
| Azure Batch 계정    | Datahub   |
| Azure Key Vault |  Datahub 및 가속기        |
| Azure Maps 계정       |     액셀러레이터    |
| 이벤트 허브 네임스페이스    |     Datahub      |
| 논리 앱      |  Datahub       |
| 스토리지 계정      |     Datahub 및 가속기      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>발생하는 비용

Azure FarmBeats의 비용은 기본 Azure 서비스 비용의 집계입니다. Azure 서비스에 대한 가격 책정 정보는 [가격 계산기](https://azure.microsoft.com/pricing/calculator)를 사용하여 계산할 수 있습니다. 총 설치의 실제 비용은 사용량에 따라 달라집니다. 두 구성 요소에 대한 안정 상태 비용은 다음과 같습니다.

- Datahub - 일별 $10 미만
- 가속기 - 일별 $2 미만

### <a name="regions-supported"></a>지원되는 지역

현재 Azure FarmBeats는 다음 지역의 퍼블릭 클라우드 환경에서 지원됩니다.

- 오스트레일리아 동부
- 미국 중부
- 미국 동부
- 미국 동부 2
- 미국 서부
- 미국 서부 2
- 북유럽
- 서유럽
- 동아시아
- 동남아시아

### <a name="time-taken"></a>소요 시간

준비 및 설치를 포함하여 Azure FarmBeats의 전체 설정에는 한 시간이 채 걸리지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure FarmBeats의 실제 설치를 시작하기 전에 다음 단계를 완료해야 합니다.

### <a name="verify-permissions"></a>권한 확인

Azure FarmBeats를 설치하려면 Azure 테넌트에서 다음 권한이 필요합니다.

- 테넌트 - AAD 앱 작성자
- 구독 - 소유자
- FarmBeats가 설치되는 리소스 그룹 - 소유자

처음 두 가지 권한은 [AAD 애플리케이션을 만드는 데](#create-an-aad-application) 필요합니다. 필요한 경우 적절한 권한을 가진 사용자를 가져와 AAD 애플리케이션을 만들 수 있습니다.

마켓플레이스에서 FarmBeats 설치를 실행하는 사람은 FarmBeats가 설치되는 리소스 그룹의 소유자여야 합니다. 구독 소유자의 경우 이는 리소스 그룹을 만들 때 자동으로 발생합니다. 다른 사용자의 경우 리소스 그룹을 미리 만들고 나를 리소스 그룹의 소유자로 지정하도록 구독 소유자에게 요청하세요.

[Azure 역할 기반 액세스 제어](../../role-based-access-control/check-access.md)의 지침에 따라 Azure Portal에서 액세스 권한을 확인할 수 있습니다.

### <a name="decide-subscription-and-region"></a>구독 및 지역 결정

Azure 구독 ID와 Azure FarmBeats를 설치하려는 지역이 필요합니다. [지원되는 지역](#regions-supported) 섹션에 나열된 지역 중 하나를 선택합니다.

**Azure 구독 ID** 와 **Azure 지역** 을 적어 둡니다.

### <a name="create-an-aad-application"></a>AAD 애플리케이션 만들기

Azure FarmBeats를 사용하려면 Azure Active Directory 애플리케이션을 만들고 등록해야 합니다. AAD 생성 스크립트를 성공적으로 실행하려면 다음 권한이 필요합니다.

- 테넌트 - AAD 앱 작성자
- 구독 - 소유자

PowerShell 환경을 사용하여 Cloud Shell 인스턴스에서 다음 단계를 실행합니다. 맨 처음에는 사용자에게 구독을 선택하고 스토리지 계정을 만들도록 요청하는 메시지가 표시됩니다. 지시에 따라 설정을 완료합니다.

1. [AAD 앱 생성기 스크립트](https://aka.ms/FarmBeatsAADScript)를 다운로드합니다.

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. 기본적으로 이 파일은 홈 디렉터리에 다운로드됩니다. 홈 디렉터리로 이동합니다.

    ```azurepowershell-interactive
        cd
    ```

3. AAD 스크립트를 실행합니다.

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. 이 스크립트는 다음 세 가지 입력을 요청합니다.

    - **FarmBeats 웹 사이트 이름**: FarmBeats 웹 애플리케이션에 대한 고유한 URL 접두사입니다. 접두사를 이미 사용하는 경우 스크립트에서 오류가 발생합니다. 설치되면 FarmBeats 배포는 https://\<FarmBeats-website-name>.azurewebsites.net에서 액세스할 수 있고, Swagger API는 https://\<FarmBeats-website-name>-api.azurewebsites.net에 있습니다.

    - **Azure 로그인 ID**: FarmBeats의 관리자로 추가하려는 사용자의 Azure 로그인 ID를 제공합니다. 그러면 이 사용자는 FarmBeats 웹 애플리케이션에 액세스하는 액세스 권한을 다른 사용자에게 부여할 수 있습니다. 로그인 ID의 일반적인 형식은 john.doe@domain.com입니다. Azure UPN도 지원됩니다.

    - **구독 ID**: Azure FarmBeats를 설치하려는 구독의 ID입니다.

5. AAD 스크립트는 실행하는 데 2분 정도 걸리며 동일한 디렉터리의 json 파일 뿐만 아니라 화면에 값을 출력합니다. 다른 사람이 스크립트를 실행하도록 한 경우 이 출력을 공유해 달라고 요청하세요.

### <a name="create-sentinel-account"></a>Sentinel 계정 만들기

Azure FarmBeats를 설치하면 유럽 우주국의 [Sentinel-2](https://scihub.copernicus.eu/) 위성 미션에서 위성 영상을 가져올 수 있습니다. 이 설정을 구성하려면 Sentinel 계정이 필요합니다.

다음 단계에 따라 Sentinel에서 무료 계정을 만듭니다.

1. 공식 [등록](https://aka.ms/SentinelRegistration) 페이지로 이동합니다.
2. 필요한 세부 정보(이름, 성, 사용자 이름, 암호 및 메일 ID)를 입력하고 양식을 완성합니다.
3. 확인 링크는 등록된 메일 ID로 전송됩니다. 메일에 제공된 링크를 선택하고 확인을 완료합니다.

등록 프로세스가 완료됩니다. 확인이 완료되면 **Sentinel 사용자 이름** 과 **Sentinel 암호** 를 기록해 둡니다.

## <a name="install"></a>설치

이제 FarmBeats를 설치할 준비가 되었습니다. 아래 단계에 따라 설치를 시작하세요.

1. Azure Portal에 로그인합니다. 오른쪽 위 모서리에서 사용자의 계정을 선택하고 Azure FarmBeats를 설치할 Azure AD 테넌트로 전환합니다.

2. 포털 내의 Azure Marketplace로 이동하고 Marketplace에서 **Azure FarmBeats** 를 검색합니다.

3. Azure FarmBeats 개요가 표시된 새 창이 표시됩니다. **만들기** 를 선택합니다.

4. 새 창이 나타납니다. Azure FarmBeats를 설치하려는 올바른 구독, 리소스 그룹, 위치를 선택하여 등록 프로세스를 완료합니다.

5. **FarmBeats 서비스 경고** 섹션에 Azure FarmBeats와 관련된 모든 서비스 경고를 받을 메일 주소를 입력합니다. 페이지 아래쪽에서 **다음** 을 선택하여 **종속성** 탭으로 이동합니다.

    ![기본 사항 탭](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. [AAD 스크립트](#create-an-aad-application)의 출력에서 AAD 애플리케이션 섹션의 입력으로 개별 항목을 복사합니다.

7. Sentinel 계정 섹션에서 [Sentinel 계정](#create-sentinel-account) 사용자 이름과 암호를 입력합니다. **다음** 을 선택하여 **검토 + 만들기** 탭으로 이동합니다.

    ![종속성 탭](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. 입력한 세부 정보의 유효성이 검사되면 **확인** 을 선택합니다. 사용 약관 페이지가 나타납니다. 약관을 검토하고 **만들기** 를 선택하여 설치를 시작합니다. 설치 진행 상황을 확인할 수 있는 페이지로 리디렉션됩니다.

설치가 완료되면 설치를 확인하고 설치 중에 입력한 웹 사이트 이름(https://\<FarmBeats-website-name>.azurewebsites.net)으로 이동하여 FarmBeats 포털 사용을 시작할 수 있습니다. 팜을 만들 수 있는 옵션이 포함된 FarmBeats 사용자 인터페이스가 표시되어야 합니다.

**Datahub** 는 https://\<FarmBeats-website-name>-api.azurewebsites.net/swagger에서 찾을 수 있습니다. 여기에서 다양한 FarmBeats API 개체를 보고 API에 대한 REST 작업을 수행합니다.

## <a name="upgrade"></a>업그레이드

FarmBeats를 최신 버전으로 업그레이드하려면 PowerShell 환경을 사용하여 Cloud Shell 인스턴스에서 다음 단계를 실행합니다. 사용자가 FarmBeats가 설치된 구독의 소유자여야 합니다.

맨 처음에는 사용자에게 구독을 선택하고 스토리지 계정을 만들도록 요청하는 메시지가 표시됩니다. 지시에 따라 설정을 완료합니다.

1. [업그레이드 스크립트](https://aka.ms/FarmBeatsUpgradeScript)를 다운로드합니다.

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. 기본적으로 이 파일은 홈 디렉터리에 다운로드됩니다. 홈 디렉터리로 이동합니다.

    ```azurepowershell-interactive
        cd
    ```

3. 업그레이드 스크립트를 실행합니다.

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

input.json 파일의 경로는 선택 사항입니다. 지정하지 않으면 스크립트에서 필요한 모든 입력을 요청합니다. 업그레이드는 약 30분 이내에 완료되어야 합니다.

## <a name="uninstall"></a>제거

Azure FarmBeats Datahub 또는 가속기를 제거하려면 다음 단계를 완료하세요.

1. Azure Portal에 로그인하고 해당 구성 요소가 설치된 **리소스 그룹을 삭제** 합니다.

2. Azure Active Directory로 이동하여 Azure FarmBeats 설치에 연결된 **Azure AD 애플리케이션을 삭제** 합니다.

## <a name="next-steps"></a>다음 단계

Azure 구독에서 Azure FarmBeats를 설치하는 방법을 배웠습니다. 이제 Azure FarmBeats 인스턴스에 [사용자를 추가](manage-users-in-azure-farmbeats.md#manage-users)하는 방법에 대해 알아봅니다.