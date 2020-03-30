---
title: Azure FarmBeats 설치
description: 이 문서에서는 Azure 구독에 Azure FarmBeats를 설치하는 방법을 설명합니다.
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 0761db6b73c6fcfeb1ef6fda729a68c9644bbc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479562"
---
# <a name="install-azure-farmbeats"></a>Azure FarmBeats 설치

이 문서에서는 Azure 구독에 Azure FarmBeats를 설치하는 방법을 설명합니다.

Azure FarmBeats는 Azure Marketplace에서 사용할 수 있는 B2B(Business-to-Business) 제품입니다. 이를 통해 공급자 간에 농업 데이터 세트를 집계하고 실행 가능한 통찰력을 생성할 수 있습니다. Azure FarmBeats는 융합된 데이터 집합을 기반으로 인공 지능(AI) 또는 기계 학습(ML) 모델을 빌드할 수 있도록 하여 이 작업을 수행합니다. Azure FarmBeats의 두 가지 주요 구성 요소는 다음과 같습니다.

- **Datahub**: 다양한 공급자에 걸쳐 다양한 농업 데이터 세트의 집계, 정규화 및 컨텍스트화를 가능하게 하는 API 계층입니다.

- **액셀러레이터**: Datahub 를 기반으로 구축된 웹 응용 프로그램입니다. 모델 개발 및 시각화를 시작합니다. 가속기는 Azure FarmBeats API를 사용하여 수집된 센서 데이터의 시각화를 차트로 시연하고 모델 출력을 맵으로 시각화합니다.

## <a name="general-information"></a>일반 정보

### <a name="components-installed"></a>설치된 구성 요소

Azure FarmBeats를 설치하면 Azure 구독에서 다음 리소스가 프로비전됩니다.

| 설치된 Azure 리소스  | Azure 팜비츠 구성 요소  |
|---------|---------|
| 애플리케이션 정보   |      데이터 허브 & 액셀러레이터      |
| App Service     |     데이터 허브 & 액셀러레이터     |
| App Service 계획   | 데이터 허브 & 액셀러레이터  |
| API 연결    |  Datahub       |
| Azure Cache for Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     데이터 허브 & 액셀러레이터      |
| Azure Batch 계정    | Datahub   |
| Azure Key Vault |  데이터 허브 & 액셀러레이터        |
| Azure 지도 계정       |     액셀러레이터    |
| 이벤트 허브 네임스페이스    |     Datahub      |
| 논리 앱      |  Datahub       |
| 스토리지 계정      |     데이터 허브 & 액셀러레이터      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>발생한 비용

Azure FarmBeats의 비용은 기본 Azure 서비스의 비용의 집계입니다. Azure 서비스에 대한 가격 정보는 [가격 계산기를](https://azure.microsoft.com/pricing/calculator)사용하여 계산할 수 있습니다. 총 설치의 실제 비용은 사용량에 따라 달라집니다. 두 구성 요소에 대한 정상 상태 비용은 다음과 입니다.

- 데이터 허브 - 하루에 $10 미만
- 가속기 - 하루에 $2 미만

### <a name="regions-supported"></a>지원되는 지역

현재 Azure FarmBeats는 다음 리전의 퍼블릭 클라우드 환경에서 지원됩니다.

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

### <a name="time-taken"></a>걸린 시간

준비 및 설치를 포함하여 Azure FarmBeats의 전체 설정에는 1시간 미만이 소요됩니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure FarmBeats의 실제 설치를 시작하기 전에 다음 단계를 완료해야 합니다.

### <a name="verify-permissions"></a>사용 권한 확인

Azure FarmBeats를 설치하려면 Azure 테넌트에서 다음 권한이 필요합니다.

- 테넌트 - AAD 앱 작성자
- 구독 - 소유자
- FarmBeats가 설치되는 리소스 그룹 - 소유자

[AAD 응용 프로그램](#create-an-aad-application) 단계를 만드는 데 처음 두 개의 사용 권한이 필요합니다. 필요한 경우 AAD 응용 프로그램을 만들 수 있는 적절한 권한을 가진 사람을 얻을 수 있습니다.

마켓플레이스에서 FarmBeats 설치를 실행하는 사람은 FarmBeats가 설치되는 리소스 그룹의 소유자여야 합니다. 구독 소유자의 경우 리소스 그룹을 만들 때 자동으로 발생합니다. 다른 사용자의 경우 리소스 그룹을 미리 만들고 구독 소유자에게 리소스 그룹의 소유자로 설정하도록 요청하십시오.

[역할 기반 액세스 제어에](https://docs.microsoft.com/azure/role-based-access-control/check-access)대한 지침에 따라 Azure 포털에서 액세스 권한을 확인할 수 있습니다.

### <a name="decide-subscription-and-region"></a>구독 및 지역 결정

Azure 구독 ID와 Azure FarmBeats를 설치하려는 지역이 필요합니다. [지원](#regions-supported) 지역 섹션에 나열된 지역 중 하나를 선택합니다.

**Azure 구독 ID** 및 Azure **지역을**기록합니다.

### <a name="create-an-aad-application"></a>AAD 응용 프로그램 만들기

Azure FarmBeats에는 Azure Active Directory 응용 프로그램 만들기 및 등록이 필요합니다. AAD 생성 스크립트를 성공적으로 실행하려면 다음 권한이 필요합니다.

- 테넌트 - AAD 앱 작성자
- 구독 - 소유자

PowerShell 환경을 사용하여 클라우드 셸 인스턴스에서 다음 단계를 실행합니다. 처음 사용자에게 구독을 선택하고 저장소 계정을 만들라는 메시지가 표시됩니다. 지시에 따라 설정을 완료합니다.

1. [AAD 앱 생성기 스크립트](https://aka.ms/FarmBeatsAADScript) 다운로드

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. 기본적으로 파일은 홈 디렉토리에 다운로드됩니다. 디렉터리로 이동합니다.

    ```azurepowershell-interactive
        cd
    ```

3. AAD 스크립트 실행

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. 스크립트는 다음과 같은 세 가지 입력을 요청합니다.

    - **FarmBeats 웹 사이트 이름**: FarmBeats 웹 응용 프로그램에 대한 고유 URL 접두사입니다. 접두사를 이미 가져온 경우 스크립트에 오류가 발생합니다. 설치되면 FarmBeats 배포는 https://\<FarmBeats-웹 사이트 이름>.azurewebsite.net에서 액세스 할 수 있으며\<swagger API는 https:// FarmBeats 웹 사이트 이름>-api.azurewebsites.net

    - **Azure 로그인 ID**: FarmBeats의 관리자로 추가하려는 사용자에게 Azure 로그인 ID를 제공합니다. 그런 다음 이 사용자는 다른 사용자에게 FarmBeats 웹 응용 프로그램에 액세스할 수 있는 권한을 부여할 수 있습니다. 로그인 ID는 일반적으로 폼입니다. john.doe@domain.com Azure UPN도 지원됩니다.

    - **구독 ID**: Azure FarmBeats를 설치하려는 구독의 ID입니다.

5. AAD 스크립트를 실행하는 데 약 2분이 걸리며 동일한 디렉터리에서 json 파일뿐만 아니라 화면에서 값을 출력합니다. 다른 사람이 스크립트를 실행한 경우 이 출력을 사용자와 공유하도록 요청합니다.

### <a name="create-sentinel-account"></a>센티넬 계정 만들기

Azure FarmBeats 설정을 사용하면 농장을 위한 유럽 우주국의 [Sentinel-2](https://scihub.copernicus.eu/) 위성 임무에서 위성 이미지를 얻을 수 있습니다. 이 설정을 구성하려면 Sentinel 계정이 필요합니다.

센티넬을 사용하여 무료 계정을 만들려면 다음 단계를 따르세요.

1. 공식 [가입](https://aka.ms/SentinelRegistration) 페이지로 이동합니다.
2. 필요한 세부 정보(이름, 성, 사용자 이름, 암호 및 이메일 ID)를 제공하고 양식을 작성합니다.
3. 확인 링크가 등록된 이메일 ID로 전송됩니다. 이메일에 제공된 링크를 선택하고 확인을 완료합니다.

등록 절차가 완료되었습니다. 확인이 완료되면 **Sentinel 사용자 이름과** **Sentinel 암호를**기록합니다.

## <a name="install"></a>설치

이제 FarmBeats를 설치할 준비가 되었습니다. 설치를 시작하려면 아래 단계에 따라 다음단계를 따르십시오.

1. Azure Portal에 로그인합니다. 오른쪽 상단 모서리에서 계정을 선택하고 Azure FarmBeats를 설치하려는 Azure AD 테넌트로 전환합니다.

2. 포털 내에서 Azure 마켓플레이스로 이동하여 마켓플레이스에서 **Azure FarmBeats를** 검색합니다.

3. Azure FarmBeats의 개요가 있는 새 창이 나타납니다. **만들기**를 선택합니다.

4. 새 창이 나타납니다. Azure FarmBeats를 설치할 올바른 구독, 리소스 그룹 및 위치를 선택하여 등록 프로세스를 완료합니다.

5. **FarmBeats 서비스** 경고 섹션에서 Azure FarmBeats와 관련된 서비스 경고를 수신해야 하는 전자 메일 주소를 제공합니다. 페이지 하단에서 **다음을** 선택하여 **종속성** 탭으로 이동합니다.

    ![기본 탭](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. [AAD 스크립트](#create-an-aad-application) 출력에서 AAD 응용 프로그램 섹션의 입력으로 개별 항목을 복사합니다.

7. 센티넬 계정 섹션에 [센티넬 계정](#create-sentinel-account) 사용자 이름과 암호를 입력합니다. **다음으로** 이동하여 **검토 + 만들기** 탭으로 이동합니다.

    ![종속성 탭](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. 입력한 세부 정보가 유효성을 검사하면 **확인을**선택합니다. 이용 약관 페이지가 나타납니다. 용어를 검토하고 설치를 시작하려면 **만들기를** 선택합니다. 설치 진행 상황을 따를 수 있는 페이지로 리디렉션됩니다.

설치가 완료되면 설치를 확인하고 설치 중에 제공한 웹 사이트 이름인 https://\<FarmBeats-웹 사이트 이름>.azurewebsite.net으로 이동하여 FarmBeats 포털을 사용할 수 있습니다. 팜을 만드는 옵션과 함께 FarmBeats 사용자 인터페이스가 표시 됩니다.

**데이터 허브는** https://\<FarmBeats-웹 사이트 이름>-api.azurewebsite.net/swagger에서 찾을 수 있습니다. 여기에서 다른 FarmBeats API 개체를 보고 API에서 REST 작업을 수행합니다.

## <a name="upgrade"></a>업그레이드

FarmBeats를 최신 버전으로 업그레이드하려면 PowerShell 환경을 사용하여 클라우드 셸 인스턴스에서 다음 단계를 실행합니다. 사용자는 FarmBeats가 설치된 구독의 소유자여야 합니다.

처음 사용자에게 구독을 선택하고 저장소 계정을 만들라는 메시지가 표시됩니다. 지시에 따라 설정을 완료합니다.

1. 업그레이드 [스크립트](https://aka.ms/FarmBeatsUpgradeScript) 다운로드

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. 기본적으로 파일은 홈 디렉토리에 다운로드됩니다. 디렉터리로 이동합니다.

    ```azurepowershell-interactive
        cd
    ```

3. 업그레이드 스크립트 실행

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

input.json 파일에 대한 경로는 선택 사항입니다. 지정하지 않으면 스크립트에서 필요한 모든 입력을 요청합니다. 업그레이드는 약 30분 안에 완료되어야 합니다.

## <a name="uninstall"></a>제거

Azure FarmBeats Datahub 또는 가속기를 제거하려면 다음 단계를 완료하십시오.

1. Azure 포털에 로그인하고 이러한 구성 요소가 설치된 **리소스 그룹을 삭제합니다.**

2. Azure Active Director &y로 이동하여 Azure FarmBeats 설치에 연결된 **Azure AD 응용 프로그램을 삭제합니다.**

## <a name="next-steps"></a>다음 단계

Azure 구독에 Azure FarmBeats를 설치하는 방법을 배웠습니다. 이제 Azure FarmBeats 인스턴스에 [사용자를 추가하는](manage-users-in-azure-farmbeats.md#manage-users) 방법을 알아봅니다.
