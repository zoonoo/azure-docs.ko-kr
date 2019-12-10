---
title: Azure FarmBeats 배포
description: 이 문서에서는 Azure FarmBeats를 배포 하는 방법을 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 27bf62cb328273db1f7bdd44117853b00feca9ae
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941579"
---
# <a name="deploy-azure-farmbeats"></a>Azure FarmBeats 배포

이 문서에서는 Azure FarmBeats를 설정 하는 방법을 설명 합니다.

Azure FarmBeats는 Azure 클라우드, 원격 분석 데이터 수집 및 집계에 대 한 원활한 프로 비전 및 센서 장치 연결을 가능 하 게 하는 데이터 기반 경작에 대 한 업계 고유의 확장 가능한 솔루션입니다. Azure FarmBeats는 카메라, 드 론 및 토양 센서와 같은 다양 한 센서를 포함 합니다. Azure FarmBeats를 사용 하면 클라우드에서 장치를 관리할 수 있습니다. 여기에는 IoT로 준비 된 장치용 Azure의 인프라 및 서비스 관리 및 확장 가능한 웹 및 모바일 앱을 사용 하 여 시각화, 경고 및 통찰력 제공이 포함 됩니다.

> [!NOTE]
> Azure FarmBeats는 공용 클라우드 환경 에서만 지원 됩니다. 자세한 내용은 [공용 클라우드 란?](https://azure.microsoft.com/overview/what-is-a-public-cloud/)을 참조 하세요.

Azure FarmBeats에는 다음과 같은 두 가지 구성 요소가 있습니다.

- **Datahub**: Azure FarmBeats의 플랫폼 계층으로, 기존 또는 새로운 데이터 파이프라인에서 데이터를 작성, 저장, 처리 하 고 정보를 그릴 수 있습니다. 이 플랫폼 계층은 농업 데이터 파이프라인과 모델을 실행 하 고 빌드하는 데 유용 합니다.

- **Accelerator**: 미리 만든 농업 모델을 사용 하 여 azure FarmBeats의 기능을 설명 하는 기본 제공 응용 프로그램을 포함 하는 azure FarmBeats의 솔루션 계층입니다. 이 솔루션 계층을 사용 하면 팜 경계를 만들고 팜 경계의 컨텍스트 내에서 농업 데이터의 정보를 그릴 수 있습니다.

Azure FarmBeats 빠른 배포는 한 시간 이내에 소요 됩니다. Datahub 및 Accelerator에 대 한 비용은 사용량에 따라 다릅니다.

## <a name="deployed-resources"></a>배포 된 리소스

Azure FarmBeats 배포는 구독 내에 다음 리소스를 만듭니다.

| 연속적인. 번호  | 리소스 이름  | FarmBeats 구성 요소  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  Datahub       |
|2  |    Application Insights      |     Datahub/Accelerator     |
|3  |Azure Cache for Redis   |Datahub   |
|4  |       Azure Key Vault    |  Datahub/Accelerator        |
|5  |    Azure Time Series Insights       |     Datahub      |
|6 |      Azure 이벤트 허브 네임 스페이스    |  Datahub       |
|7  |    Azure Data Factory V2       |     Datahub/Accelerator      |
|8  |Batch 계정    |Datahub   |
|9  |       Storage 계정     |  Datahub/Accelerator        |
|10  |    논리 앱        |     Datahub      |
|11  |    API 연결        |     Datahub      |
|12|      Azure App Service      |  Datahub/Accelerator       |
|13 |    앱 서비스 계획        |     Datahub/Accelerator      |
|14 |Azure Maps 계정     |액셀러레이터    |

Azure FarmBeats는 Azure Portal에서 직접 액세스할 수 있는 Azure Marketplace에서 다운로드할 수 있습니다.  

## <a name="create-an-azure-farmbeats-offer-in-azure-marketplace"></a>Azure Marketplace에서 Azure FarmBeats 제품 만들기

Azure Marketplace에서 Azure FarmBeats 제품을 만들려면 다음을 수행 합니다.

1. Azure Portal에 로그인 하 고 오른쪽 위에서 계정을 선택한 다음 Azure FarmBeats를 배포 하려는 Azure Active Directory (Azure AD) 테 넌 트로 전환 합니다.
1. Azure FarmBeats는 Azure Marketplace에서 사용할 수 있습니다. **Azure Marketplace** 페이지에서 **지금 가져오기**를 선택 합니다.
1. **만들기**를 선택합니다.

> [!NOTE]
> Azure Marketplace의 제품 완료는 설치의 일부일 뿐입니다. Azure 구독에서 Azure FarmBeats 배포를 완료 하려면 다음 섹션의 지침을 따르세요.

## <a name="prepare"></a>준비

Azure FarmBeats를 배포 하려면 먼저 다음 권한이 필요 합니다.

- **테 넌 트**: 읽기 권한
- **구독**: 참가자 또는 소유자
- **리소스 그룹**: 소유자

## <a name="before-you-begin"></a>시작하기 전에

배포를 시작 하기 전에 다음 필수 구성 요소를 준비 해야 합니다.

- 센티널 계정
- Azure AD 앱 등록

## <a name="create-a-sentinel-account"></a>센티널 계정 만들기    

센티널이 있는 계정은 공식 웹 사이트에서 장치에 대 한 센티널 위성 이미지를 다운로드 하는 데 도움이 됩니다. 무료 계정을 만들려면 다음을 수행 합니다.

1. [센티널 계정 등록 페이지로](https://scihub.copernicus.eu/dhus/#/self-registration)이동 합니다.
1. 등록 양식에서 이름, 성, 사용자 이름, 암호 및 전자 메일 주소를 제공 합니다.

확인을 위해 등록 된 전자 메일 주소로 확인 전자 메일이 전송 됩니다. 링크를 선택 하 여 전자 메일 주소를 확인 합니다. 등록 프로세스가 완료 되었습니다.

## <a name="create-an-azure-ad-app-registration"></a>Azure AD 앱 등록 만들기

Azure FarmBeats에 대 한 인증 및 권한 부여는 Azure AD 앱을 등록 해야 합니다. 다음 두 가지 방법 중 하나로 만들 수 있습니다.

* **옵션 1**: 필수 테 넌 트, 구독 및 리소스 그룹 액세스 권한이 있는 경우 설치 관리자에서 자동으로 등록을 만들 수 있습니다. 이러한 권한이 있으면 ["input. json 파일 준비"](#prepare-the-inputjson-file) 섹션을 계속 진행 합니다.

* **옵션 2**: Azure FarmBeats를 배포 하기 전에 등록을 수동으로 만들고 구성할 수 있습니다. 구독 내에서 Azure AD 앱 등록을 만들고 구성 하는 데 필요한 권한이 없는 경우이 방법을 사용 하는 것이 좋습니다. 관리자에 게 [사용자 지정 스크립트](https://aka.ms/FarmBeatsAADScript)를 사용 하도록 요청 합니다 .이 스크립트는 IT 관리자가 Azure Portal에서 Azure AD 앱 등록을 자동으로 생성 하 고 구성 하는 데 도움이 됩니다. PowerShell 환경을 사용 하 여이 사용자 지정 스크립트를 실행 하는 출력으로 IT 관리자는 Azure AD 응용 프로그램 클라이언트 ID 및 암호 암호를 공유 해야 합니다. 이러한 값을 기록해 둡니다.

    Azure AD 앱 등록 스크립트를 실행 하려면 다음을 수행 합니다.

    1. [스크립트](https://aka.ms/FarmBeatsAADScript)를 다운로드 합니다.
    1. Azure Portal에 로그인 하 고 구독과 Azure AD 테 넌 트를 선택 합니다.
    1. Azure Portal 맨 위에 있는 도구 모음에서 Cloud Shell를 시작 합니다.

        ![프로젝트 FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

    1. 첫 번째 사용자 인 경우 저장소 계정 및 Microsoft Azure 파일 공유를 만들 구독을 선택 하 라는 메시지가 표시 됩니다. **스토리지 만들기**를 선택합니다.
    1. 처음에는 사용자에 게 셸 경험, Bash 또는 PowerShell을 선택할 수도 있습니다. PowerShell을 선택 합니다.
    1. 1 단계에서 Cloud Shell 스크립트를 업로드 하 고 업로드 된 파일의 위치를 확인 합니다.

        > [!NOTE]
        > 기본적으로 파일은 홈 디렉터리에 업로드 됩니다.

        다음 스크립트를 사용합니다.

        ```azurepowershell-interactive
        ./create_aad_script.ps1
        ```
    1. Azure FarmBeats를 배포 하는 사람과 공유할 Azure AD 응용 프로그램 ID 및 클라이언트 암호를 기록해 둡니다.

### <a name="prepare-the-inputjson-file"></a>Input. json 파일 준비

설치 과정에서 다음과 같이 *input. json* 파일을 만듭니다.

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":" dummy-test-acc1",
    "acceleratorWebsiteName":" dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

이 파일은 Azure Cloud Shell 하는 입력 파일입니다. 여기에는 설치 중에 값이 사용 되는 매개 변수가 포함 됩니다. JSON 파일의 모든 매개 변수를 적절 한 값으로 바꾸거나 제거 해야 합니다. 제거 되 면 설치 관리자가 설치 하는 동안 메시지를 표시 합니다.

파일을 준비 하기 전에 다음 표의 매개 변수를 검토 합니다.

|명령 | 설명|
|--- | ---|
|sku  | Azure FarmBeats의 구성 요소 중 하나 또는 둘 다를 다운로드할 수 있는 옵션을 제공 합니다. 다운로드할 구성 요소를 지정 합니다. Datahub만 설치 하려면 "onlydatabhub"를 사용 합니다. Datahub 및 Accelerator를 설치 하려면 "both"를 사용 합니다.|
|subscriptionId | Azure FarmBeats를 설치 하기 위한 구독을 지정 합니다.|
|datahubResourceGroup| Datahub 리소스의 리소스 그룹 이름입니다.|
|location |리소스를 만들려는 위치입니다.|
|acceleratorWebsiteName |Datahub 인스턴스의 이름을의 고유 URL 접두사입니다.|
|acceleratorResourceGroup  | 액셀러레이터 키 웹 사이트의 이름을 위한 고유한 URL 접두사입니다.|
|datahubWebsiteName  | Datahub 웹 사이트의 이름을로 하는 고유한 URL 접두사입니다. |
|sentinelUsername | 로그인 할 사용자 이름입니다 (예: `https://scihub.copernicus.eu/dhus/#/self-registration`).|
|notificationEmailAddress  | Datahub 인스턴스 내에서 구성 하는 경고에 대 한 알림을 받을 전자 메일 주소입니다.|
|updateIfExists| 필드 기존 Azure FarmBeats 인스턴스를 업그레이드 하려는 경우에만 *입력 된 json* 파일 내에 포함할 매개 변수입니다. 업그레이드의 경우 리소스 그룹 이름 및 위치와 같은 기타 세부 정보는 동일 해야 합니다.|
|aadAppClientId | 필드 Azure AD 앱이 이미 있는 경우에만 *입력. json* 파일 내에 포함할 매개 변수입니다.  |
|aadAppClientSecret  | 필드 Azure AD 앱이 이미 있는 경우에만 *입력. json* 파일 내에 포함할 매개 변수입니다.|

## <a name="deploy-the-app-within-cloud-shell"></a>Cloud Shell 내에 앱 배포

앞에서 설명한 marketplace 워크플로의 일부로, 하나의 리소스 그룹을 만들고 "사용 조건" 계약에 서명 해야 하며,이는 실제 배포의 일부로 다시 검토할 수 있습니다. Bash 환경을 사용 하 여 Cloud Shell 브라우저 기반 명령줄 인터페이스를 통해 앱을 배포할 수 있습니다. Cloud Shell를 통해 배포 하려면 다음 섹션을 계속 진행 합니다.

> [!NOTE]
> 비활성 Cloud Shell 세션은 20 분 후에 만료 됩니다. 이 시간 내에 배포를 완료 해 보세요.

1. Azure Portal에 로그인 하 고 사용 하려는 구독 및 Azure 테 넌 트를 선택 합니다.
1. Azure Portal 맨 위에 있는 도구 모음에서 Cloud Shell를 시작 합니다.
1. Cloud Shell를 처음으로 사용 하는 경우에는 구독을 선택 하 여 저장소 계정 및 Azure Files 공유를 만들어야 한다는 메시지가 표시 됩니다.
1. **저장소 만들기**를 선택 합니다.  
1. 환경에 대해 **Bash** (PowerShell 아님)를 선택 합니다.

## <a name="deployment-scenario-1"></a>배포 시나리오 1

"옵션 1"의 앞에서 설명한이 시나리오에서 설치 관리자는 Azure AD 앱 등록을 자동으로 만듭니다. 다음을 수행 하 여 FarmBeats를 배포 합니다.

1. 다음 샘플 JSON 템플릿을 복사 하 여 *input. json*으로 저장 합니다. 로컬 컴퓨터의 파일 경로를 기록해 두어야 합니다.

    ```json
    {  
       "sku":"both", 
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
       "datahubResourceGroup":"dummy-test-dh1", 
       "location":"eastus2", 
       "datahubWebsiteName":"dummy-test-dh1", 
       "acceleratorResourceGroup":" dummy-test-acc1",   
       "acceleratorWebsiteName":" dummy-test-acc1", 
       "sentinelUsername":"dummy-dev", 
       "notificationEmailAddress":" dummy@microsoft.com", 
       "updateIfExists":true 
    }
    ```

1. Azure Cloud Shell를 엽니다. 인증에 성공 하면 **업로드** 단추 (다음 이미지에 강조 표시 됨)를 선택 하 고 *입력. json* 파일을 Cloud Shell 저장소에 업로드 합니다.  

    ![프로젝트 FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

1. Cloud Shell에서 홈 디렉터리로 이동 합니다. 기본적으로 디렉터리 이름은 */home/\<username >* 입니다.
1. Cloud Shell에서 다음 명령을 입력 합니다. *입력. json* 파일에 대 한 경로를 수정한 후 enter 키를 선택 해야 합니다.

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     설치 관리자가 모든 종속성을 자동으로 다운로드 하 고 배포자을 빌드합니다. Azure FarmBeats 사용 조건에 동의 하 라는 메시지가 표시 됩니다.

     - 동의 하는 경우 **Y** 를 입력 하 여 다음 단계를 진행 합니다.
     - 동의 하지 않는 경우 **N**을 입력 하면 배포가 종료 됩니다.

1. 프롬프트에서 배포에 대 한 액세스 토큰을 입력 합니다. 생성 된 코드를 복사 하 고 Azure 자격 증명을 사용 하 여 [장치 로그인 페이지](https://microsoft.com/devicelogin) 에 로그인 합니다.

    > [!NOTE]
    > 토큰은 60 분 후에 만료 됩니다. 만료 되 면 배포 명령을 다시 입력 하 여를 다시 시작할 수 있습니다.

1. 프롬프트에서 센티널 계정 암호를 입력 합니다.

   설치 관리자는 배포의 유효성을 검사 하 고 시작 하며,이는 약 20 분 정도 걸릴 수 있습니다.

   배포가 성공적으로 완료 되 면 다음 출력 링크를 받게 됩니다.

    - **Datahub URL**: Azure FarmBeats api를 시도 하기 위한 Swagger 링크입니다.
    - **ACCELERATOR URL**: Azure FarmBeats 스마트 팜 가속기 탐색을 위한 사용자 인터페이스입니다.
    - **배포자 로그 파일**: 배포 중에 생성 되는 로그 파일입니다. 필요한 경우 문제를 해결 하는 데 사용할 수 있습니다.

## <a name="deployment-scenario-2"></a>배포 시나리오 2

"옵션 2"에서 앞서 설명한이 시나리오에서는 다음을 수행 하 여 기존 Azure Active Directory 앱 등록을 사용 하 여 FarmBeats를 배포 합니다.

1. 다음 JSON 파일을 복사 하 여 Azure 애플리케이션 클라이언트 ID와 암호를 포함 하는 json 파일을 *입력* 하 고 저장 합니다. 로컬 컴퓨터의 파일 경로를 기록해 두어야 합니다.

    ```json
   {

   "sku":"both",
   "subscriptionId":"daxx9xxx-d18f-4xxc-9c21-5xx3exxxxx45",
   "datahubResourceGroup":"dummy-test-dh1",   
   "location":"westus2",   
   "datahubWebsiteName":"dummy-test-dh1",   
   "acceleratorResourceGroup":"dummy-test-acc1",   
   "acceleratorWebsiteName":"dummy-test-acc1",   
   "sentinelUsername":"dummy-dev",
   "notificationEmailAddress":"dummyuser@org1.com",
   "updateIfExists": true,
   "aadAppClientId": "lmtlemlemylmylkmerkywmkm823",
   "aadAppClientSecret": "Kxxxqxxxxu*kxcxxx3Yxxu5xx/db[xxx"

   }
   ```

1. Azure Cloud Shell 다시로 이동 하 고 성공적으로 인증 되었습니다.
1. **업로드** 단추 (다음 이미지에 강조 표시 됨)를 선택 하 고 *입력. json* 파일을 Cloud Shell 저장소에 업로드 합니다.

    ![프로젝트 FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

1. Cloud Shell에서 홈 디렉터리로 이동 합니다. 기본적으로 디렉터리 이름은 */home/\<username >* 입니다.
1. Cloud Shell에서 다음 명령을 입력 합니다. *입력. json* 파일에 대 한 경로를 수정한 후 enter 키를 선택 해야 합니다.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

     설치 관리자가 모든 종속성을 자동으로 다운로드 하 고 배포자을 빌드합니다. Azure FarmBeats 사용 조건에 동의 하 라는 메시지가 표시 됩니다.

     - 동의 하는 경우 **Y** 를 입력 하 여 다음 단계를 진행 합니다.
     - 동의 하지 않는 경우 **N**을 입력 하면 배포가 종료 됩니다.

1. 프롬프트에서 배포에 대 한 액세스 토큰을 입력 합니다. 생성 된 코드를 복사 하 고 Azure 자격 증명을 사용 하 여 [장치 로그인 페이지](https://microsoft.com/devicelogin) 에 로그인 합니다.

   설치 관리자는 배포의 유효성을 검사 하 고 시작 하며,이는 약 20 분 정도 걸릴 수 있습니다.

   배포가 성공적으로 완료 되 면 다음 출력 링크를 받게 됩니다.

    - **Datahub URL**: Azure FarmBeats api를 시도 하기 위한 Swagger 링크입니다.
    - **ACCELERATOR URL**: Azure FarmBeats 스마트 팜 가속기 탐색을 위한 사용자 인터페이스입니다.
    - **배포자 로그 파일**: 배포 중에 생성 되는 로그 파일입니다. 필요한 경우 문제를 해결 하는 데 사용할 수 있습니다.

문제가 발생 하는 경우 [문제 해결](troubleshoot-project-farmbeats.md)을 검토 합니다.


## <a name="validate-the-deployment"></a>배포 유효성 검사

### <a name="datahub"></a>Datahub

Datahub 설치가 완료 되 면 https://>\<해당 하는 형식으로 Swagger 인터페이스를 통해 Azure FarmBeats Api에 액세스 하는 URL을 받게 됩니다.

1. Swagger를 통해 로그인 하려면 브라우저에서 URL을 복사 하 여 붙여 넣습니다.
2. Azure Portal 자격 증명으로 로그인 합니다.
3. Swagger를 확인 하 고 Azure FarmBeats Api에서 모든 REST 작업을 수행할 수 있습니다. 이는 Azure FarmBeats의 성공적인 배포를 나타냅니다.

### <a name="accelerator"></a>액셀러레이터

가속기 설치가 완료 되 면 Azure FarmBeats 사용자 인터페이스에 액세스 하기 위한 URL을 https://\<가속기-웹 사이트 이름 >.

1. Accelerator에서 로그인 하려면 브라우저에서 URL을 복사 하 여 붙여 넣습니다.
1. Azure Portal 자격 증명으로 로그인 합니다.

## <a name="upgrade"></a>업그레이드

업그레이드 지침은 처음 설치할 때와 유사 합니다. 다음을 수행합니다.

1. Azure Portal에 로그인 한 다음 원하는 구독과 Azure 테 넌 트를 선택 합니다.
1. Azure Portal의 위쪽 탐색에서 Cloud Shell를 엽니다.

   ![프로젝트 FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

1. 왼쪽의 **환경** 드롭다운 목록에서 **Bash**를 선택 합니다.
1. 필요한 경우 *input. json* 파일을 변경한 다음 Cloud Shell에 업로드 합니다. 예를 들어 수신 하려는 알림에 대 한 전자 메일 주소를 업데이트할 수 있습니다.
1. Cloud Shell에 다음 두 명령을 입력 하거나 붙여 넣습니다. *Input. json* 파일 경로를 수정한 후 Enter 키를 선택 해야 합니다.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

   설치 관리자는 런타임에 필요한 입력을 자동으로 표시 합니다.

1. 배포에 대 한 액세스 토큰을 입력 합니다. 생성 된 코드를 복사 하 고 Azure 자격 증명을 사용 하 여 [장치 로그인 페이지](https://microsoft.com/devicelogin) 에 로그인 합니다.
1. 센티널 암호를 입력 합니다.

   이제 설치 관리자는 리소스의 유효성을 검사 하 고 리소스 만들기를 시작 합니다 .이는 약 20 분 정도 걸릴 수 있습니다.

   배포가 성공적으로 완료 되 면 다음 출력 링크를 받게 됩니다.
    - **Datahub URL**: Azure FarmBeats api를 시도 하기 위한 Swagger 링크입니다.
    - **ACCELERATOR URL**: Azure FarmBeats 스마트 팜 가속기 탐색을 위한 사용자 인터페이스입니다.
    - **배포자 로그 파일**: 배포 중에 생성 되는 로그 파일입니다. 필요한 경우 문제를 해결 하는 데 사용할 수 있습니다.

> [!NOTE]
> 나중에 사용 하기 위해 앞의 값을 기록해 둡니다.


## <a name="uninstall"></a>제거

현재 설치 관리자를 사용 하 여 Azure FarmBeats의 자동 제거를 지원 하지 않습니다. Datahub 또는 Accelerator를 제거 하려면 Azure Portal에서 이러한 구성 요소가 설치 된 리소스 그룹을 삭제 하거나 리소스를 수동으로 삭제 합니다.

예를 들어 두 개의 다른 리소스 그룹에 Datahub와 Accelerator를 배포한 경우 다음과 같이 해당 리소스 그룹을 삭제할 수 있습니다.

1. Azure 포털에 로그인합니다.
1. 오른쪽 위에서 계정을 선택 하 고 Azure FarmBeats를 배포 하려는 Azure AD 테 넌 트로 전환 합니다.

   > [!NOTE]
   > Datahub는 가속기가 제대로 작동 하는 데 필요 합니다. 액셀러레이터 키를 제거 하지 않으면 Datahub를 제거 하지 않는 것이 좋습니다.

1. **리소스 그룹**을 선택 하 고 삭제할 datahub 또는 Accelerator 리소스 그룹의 이름을 입력 합니다.
1. 해당 리소스 그룹 이름을 선택합니다. 이름을 다시 입력 하 여 확인 한 다음 **삭제** 를 선택 하 여 리소스 그룹 및 모든 해당 기본 리소스를 제거 합니다.
   또는 권장 하지 않는 메서드인 각 리소스를 수동으로 삭제할 수 있습니다.
1. Datahub를 삭제 하거나 제거 하려면 Azure에서 직접 리소스 그룹으로 이동 하 여 해당 위치에서 리소스 그룹을 삭제 합니다.

## <a name="next-steps"></a>다음 단계

Azure FarmBeats를 배포 하는 방법을 알아보았습니다. 다음으로 [FarmBeats 팜을 만드는](manage-farms.md#create-farms)방법을 알아봅니다.
