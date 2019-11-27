---
title: Azure FarmBeats 배포
description: FarmBeats를 배포 하는 방법을 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 0fc7eb6c3b582c4fc95d78397c4cb2820ebb4ea8
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534233"
---
# <a name="deploy-farmbeats"></a>FarmBeats 배포

이 문서에서는 Azure FarmBeats를 설정 하는 방법을 설명 합니다.

Azure FarmBeats는 Azure 클라우드, 원격 분석 데이터 수집 및 집계에 대 한 원활한 프로 비전 및 센서 장치 연결을 가능 하 게 하는 데이터 기반 경작에 대 한 업계 고유의 확장 가능한 솔루션입니다. Azure FarmBeats에는 카메라, 드 론, 토양 센서, 클라우드에서 장치 관리 등의 다양 한 센서가 있습니다. 여기에는 IoT ready (사물 인터넷) 장치에서 제공 하는 정보와 웹 및 모바일 앱에 대 한 Azure의 인프라 및 서비스가 포함 됩니다. 시각화, 경고 및 정보입니다.

> [!NOTE]
> Azure FarmBeats는 공용 클라우드 환경 에서만 지원 됩니다. 클라우드 환경에 대 한 자세한 내용은 [Azure](https://azure.microsoft.com/overview/what-is-a-public-cloud/)를 참조 하세요.

Azure FarmBeats에는 다음과 같은 두 가지 구성 요소가 있습니다.

- **데이터 허브** -데이터 허브는 기존 또는 새 데이터 파이프라인에서 데이터를 작성, 저장, 처리 및 정보를 그릴 수 있는 Azure FarmBeats의 플랫폼 계층입니다. 이 플랫폼 계층은 농업 데이터 파이프라인과 모델을 실행 하 고 빌드하는 데 유용 합니다.

- **Accelerator** -accelerator는 미리 만들어진 농업 모델을 사용 하 여 azure FarmBeats의 기능을 설명 하는 기본 제공 응용 프로그램을 포함 하는 azure FarmBeats의 솔루션 계층입니다. 이 솔루션 계층을 사용 하면 팜 경계를 만들고 팜 경계의 컨텍스트 내에서 농업 데이터의 정보를 그릴 수 있습니다.

Azure FarmBeats 빠른 배포는 한 시간 이내에 소요 됩니다. 데이터 허브 및 가속기 비용은 사용량에 따라 다릅니다.

## <a name="deployed-resources"></a>배포 된 리소스

Azure FarmBeats 배포는 구독 내에 아래 나열 된 리소스를 만듭니다.

|S.No  |리소스 이름  |Azure FarmBeats 구성 요소  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  데이터 허브       |
|2  |    Application Insights      |     데이터 허브/가속기     |
|3  |Azure Cache for Redis   |데이터 허브   |
|4  |       Azure KeyVault    |  데이터 허브/가속기        |
|5  |    Time Series Insights       |     데이터 허브      |
|6 |      EventHub 네임 스페이스    |  데이터 허브       |
|7  |    Azure Data Factory V2       |     데이터 허브/가속기      |
|8  |Batch 계정    |데이터 허브   |
|9  |       Storage 계정     |  데이터 허브/가속기        |
|10  |    논리 앱        |     데이터 허브      |
|11  |    API 연결        |     데이터 허브      |
|12|      App Service      |  데이터 허브/가속기       |
|13 |    앱 서비스 계획        |     데이터 허브/가속기      |
|14 |Azure Maps 계정     |액셀러레이터    |
|15 |       Time Series Insights      |  데이터 허브     |

Azure FarmBeats는 Azure Marketplace에서 다운로드할 수 있습니다. Azure Portal에서 직접 액세스할 수 있습니다.  

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>Marketplace에서 Azure FarmBeats 제품 만들기

다음 단계를 사용 하 여 marketplace에서 Azure FarmBeats 제품을 만듭니다.

1. Azure Portal에 로그인 하 고 오른쪽 위 모서리에서 사용자의 계정을 선택 하 고 Microsoft Azure FarmBeats를 배포 하려는 Azure AD 테 넌 트로 전환 합니다.
2. Azure FarmBeats는 Azure Marketplace에서 사용할 수 있습니다. Marketplace 페이지에서 "지금 가져오기"를 선택 합니다.
3. 만들기를 선택 하 고 다음 정보를 입력 합니다.
    - 구독 이름입니다.
    - 기존 리소스 그룹 이름 (빈 리소스 그룹에만 해당) 또는 Azure FarmBeats를 배포 하기 위한 새 리소스 그룹을 만듭니다. 이후 섹션에서이 리소스 그룹을 기록해 둡니다.
4. Azure FarmBeats를 설치 하려는 지역입니다. 현재 Azure FarmBeats는 미국 중부, 유럽 서부, 미국 동부 2, 북부 유럽, 미국 서 부, 동남 아시아, 미국 동부, 오스트레일리아 동부, 미국 서 부 2 지역에서 지원 됩니다.
5. **확인**을 선택합니다.
사용 약관 페이지가 나타납니다. 표준 마켓플레이스 용어를 검토 하거나 하이퍼링크를 선택 하 여 사용 약관을 검토 합니다.
6. **닫기**를 선택 하 고 "동의 함" 확인란을 선택 하 고 **만들기**를 선택 합니다.
7. 이제 marketplace에서 Azure FarmBeats의 EULA (최종 사용자 사용권 계약)에 성공적으로 서명 했습니다.  
7. 배포를 계속 하려면이 가이드의 다음 단계를 수행 합니다.

> [!NOTE]
> Azure Marketplace에서 제품이 완료 되 면 설치의 일부만 완료 됩니다. Azure 구독에서 Azure FarmBeats의 배포를 완료 하려면 아래 지침을 따르세요.

## <a name="prepare"></a>준비

Azure FarmBeats를 배포 하려면 다음 권한이 필요 합니다.

- 테 넌 트: 읽기 권한
- 구독: 참가자 또는 소유자
- 리소스 그룹: 소유자

## <a name="before-you-begin"></a>시작하기 전에

배포를 시작 하기 전에 다음이 있는지 확인 합니다.

- 센티널 계정
- AD (Azure Active Directory) 앱 등록

## <a name="create-a-sentinel-account"></a>센티널 계정 만들기    

센티널이 있는 계정은 공식 웹 사이트에서 장치에 대 한 센티널 위성 이미지를 다운로드 하는 데 도움이 됩니다. 무료 계정을 만들려면 다음 단계를 따르세요.

[https://editor.swagger.io](https://scihub.copernicus.eu/dhus/#/self-registration ) 로 이동합니다. 등록 페이지에서 이름, 성, 사용자 이름, 암호 및 전자 메일을 입력 합니다.
확인을 위해 등록 된 전자 메일 주소로 확인 전자 메일이 전송 됩니다. 링크를 선택 하 고 확인 합니다. 등록 프로세스가 완료 되었습니다.

## <a name="create-azure-ad-app-registration"></a>Azure AD 앱 등록 만들기

Azure FarmBeats의 인증 및 권한 부여에 대 한 Azure active directory 응용 프로그램 등록이 있어야 합니다.

- 사례 1: 설치 관리자에서 자동으로 만들 수 있습니다 (필수 테 넌 트, 구독 및 리소스 그룹 액세스 권한이 있는 경우).
- 사례 2: Azure FarmBeats를 배포 하기 전에 만들고 구성할 수 있습니다 (수동 단계 필요).

**사례 1**:: AAD 앱 등록을 만들기 위한 액세스 권한이 있는 경우이 단계를 건너뛰고 설치 관리자가 앱 등록을 만들도록 할 수 있습니다. 다음 섹션인 [input. json 파일 준비](#prepare-input-json-file) 를 계속 진행 하세요.

구독이 이미 있는 경우 다음 절차로 바로 이동할 수 있습니다.

**사례 2**: 구독 내에서 Azure AD 앱 등록을 만들고 구성할 수 있는 충분 한 권한이 없는 경우이 방법이 기본 설정 된 단계입니다. 관리자에 게 [사용자 지정 스크립트](https://aka.ms/FarmBeatsAADScript)를 사용 하도록 요청 합니다 .이 스크립트는 IT 관리자가 Azure Portal에서 Azure AD 앱 등록을 자동으로 생성 하 고 구성 하는 데 도움이 됩니다. PowerShell 환경을 사용 하 여이 사용자 지정 스크립트를 실행 하는 출력으로 IT 관리자는 Azure Active Directory 응용 프로그램 클라이언트 ID와 암호 암호를 공유 해야 합니다. 이러한 값을 기록해 둡니다.

다음 단계를 사용 하 여 Azure AD 응용 프로그램 등록 스크립트를 실행 합니다.

1. [스크립트](https://aka.ms/FarmBeatsAADScript)를 다운로드 합니다.
2. Azure Portal에 로그인 하 고 구독 및 AD 테 넌 트를 선택 합니다.
3. Azure Portal의 위쪽 탐색 모음에서 Cloud Shell을 시작합니다.

    ![프로젝트 팜 비트](./media/prepare-for-deployment/navigation-bar-1.png)


4. 첫 번째 사용자에 게 저장소 계정 및 Microsoft Azure 파일 공유를 만들 구독을 선택 하 라는 메시지가 표시 됩니다. **스토리지 만들기**를 선택합니다.
5. 처음에는 사용자에 게 기본 셸 환경 Bash 또는 PowerShell을 선택 하 라는 메시지가 표시 됩니다. PowerShell을 선택 합니다.
6. 1 단계에서 Cloud Shell 스크립트를 업로드 하 고 업로드 된 파일의 위치를 확인 합니다.

    > [!NOTE]
    > 기본적으로이 파일은 홈 디렉터리에 업로드 됩니다.

    다음 스크립트를 사용합니다.

    ```azurepowershell-interactive
    ./create_aad_script.ps1
    ```
7. Azure FarmBeats를 배포 하는 사람과 공유할 Azure AD 응용 프로그램 ID 및 클라이언트 암호를 기록해 둡니다.

### <a name="prepare-input-json-file"></a>입력 Json 파일 준비

설치 과정에서 다음과 같이 입력 합니다. json 파일을 만듭니다.

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

이 파일은 설치 중에 사용 되는 값을 가진 매개 변수 및 Azure Cloud Shell 하는 입력 파일입니다. Json의 모든 매개 변수를 적절 한 값으로 바꾸거나 제거 해야 합니다. 제거 하는 경우 설치 프로그램에서 설치 중에 메시지를 표시 합니다.


파일을 준비 하기 전에 매개 변수를 검토 합니다.

|명령 | 설명|
|--- | ---|
|sku  | Azure FarmBeats의 구성 요소 중 하나 또는 둘 모두를 다운로드할 수 있는 옵션을 제공 합니다. 다운로드할 구성 요소를 지정 합니다. 데이터 허브만 설치 하려면 "onlydatabhub"를 사용 합니다. 데이터 허브 및 가속기를 설치 하려면 "both"를 사용 합니다.|
|subscriptionId | Azure FarmBeats를 설치 하기 위한 구독을 지정 합니다.|
|datahubResourceGroup| 데이터 허브 리소스의 리소스 그룹 이름|
|location |리소스를 만들려는 위치입니다.|
|acceleratorWebsiteName |데이터 허브의 이름을 위한 고유한 URL 접두사|
|acceleratorResourceGroup  | 액셀러레이터 웹 사이트의 이름을 위한 고유한 URL 접두사입니다.|
|datahubWebsiteName  | 데이터 허브 웹 사이트의 이름을 위한 UUnique URL 접두사입니다. |
|sentinelUsername | 로그인 할 사용자 이름: https://scihub.copernicus.eu/dhus/#/self-registration.|
|notificationEmailAddress  | 데이터 허브 내에서 구성 하는 경고에 대 한 알림을 받을 전자 메일 주소입니다.|
|updateIfExists|필드 기존 Azure FarmBeats 인스턴스를 업그레이드 하려는 경우에만 Input. Json에 포함 될 매개 변수입니다. 업그레이드의 경우 기타 세부 정보 (예:)입니다. 리소스 그룹 이름, 위치 등은 동일 해야 합니다.|
|aadAppClientId | [**선택 사항**] Azure AD 앱이 이미 있는 경우에만 Input. Json에 포함 될 매개 변수입니다.  |
|aadAppClientSecret  | [**선택 사항**] Azure AD 앱이 이미 있는 경우에만 Input. Json에 포함 될 매개 변수입니다.|

## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>Cloud Shell 브라우저 기반 명령줄 내에 배포

위의 marketplace 워크플로의 일부로, 하나의 리소스 그룹을 만들고 최종 사용자 사용권 계약에 서명 해야 합니다. 그러면 실제 배포의 일부로 다시 검토할 수 있습니다. 배포는 Bash 환경을 사용 하 여 Azure Cloud Shell (브라우저 기반 명령줄)를 통해 수행할 수 있습니다. Cloud Shell를 통해 배포 하려면 다음 섹션을 계속 진행 하세요.

> [!NOTE]
> 비활성 Cloud Shell 세션은 20 분 후에 만료 됩니다. 이 시간 내에 배포를 완료 해 보세요.

1. Azure Portal에 로그인 하 고 원하는 구독과 AD 테 넌 트를 선택 합니다.
2. Azure Portal의 위쪽 탐색 모음에서 Cloud Shell을 시작합니다.
3. Cloud Shell를 처음 사용 하는 경우 저장소 계정 및 Microsoft Azure 파일 공유를 만들 구독을 선택 하 라는 메시지가 표시 됩니다.
4. **저장소 만들기**를 선택 합니다.  

환경을 Bash (PowerShell 아님)로 선택 합니다.

## <a name="deployment-scenario-1"></a>배포 시나리오 1

설치 관리자가 Azure AD 앱 등록을 만듭니다 (위의 사례 1).

1. 다음 템플릿을 복사 하 고 이름을 input. json으로 이름을로 입력 합니다.  
샘플 JSON 입력:

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

2. 파일을 저장 하 고 로컬 컴퓨터에 있는 경로를 적어 둡니다.
3. Azure Cloud Shell으로 이동 하 고 인증을 완료 한 후 업로드 (아래 이미지의 강조 표시 된 아이콘 참조)를 선택 하 고 입력. json 파일을 Cloud Shell 저장소에 업로드 합니다.  

    ![프로젝트 팜 비트](./media/prepare-for-deployment/bash-2-1.png)

4. Cloud shell의 홈 디렉터리로 이동 합니다. 기본적으로/home/<username>
5. Cloud Shell에 다음 명령을 입력 하거나 붙여 넣습니다. 입력 경로를 수정 해야 합니다. Json 파일을 입력 하 고 enter 키를 누릅니다.

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     설치 관리자가 모든 종속성을 자동으로 다운로드 하 고 배포자을 빌드합니다. Azure FarmBeats EULA (최종 사용자 사용권 계약)에 동의 하 라는 메시지가 표시 됩니다.

     - 동의 하면 ' Y '를 입력 하 고 다음 단계를 진행 합니다.
     - 조건에 동의 하지 않는 경우 ' N '을 입력 하면 배포가 종료 됩니다.

6. 그런 다음 배포에 대 한 액세스 토큰을 입력 하 라는 메시지가 표시 됩니다. 생성 된 코드를 복사 하 고 Azure 자격 증명을 사용 하 여 https://microsoft.com/devicelogin 로그인 합니다.

    > [!NOTE]
    > 토큰은 60 분 후에 만료 됩니다. 만료 되 면 배포 명령을 다시 입력 하 여 다시 시작할 수 있습니다.

7. 메시지가 표시 되 면 센티널 계정 암호를 입력 합니다.
8. 이제 설치 관리자는 유효성을 검사 하 고 배포를 시작 합니다 .이는 약 20 분 정도 걸릴 수 있습니다.
9. 배포가 성공적으로 완료 되 면 아래 출력 링크를 받게 됩니다.

 - **데이터 허브 URL**: Swagger 링크를 통해 Azure FarmBeats api를 사용해 보세요.
 - **ACCELERATOR URL**: Azure FarmBeats 스마트 팜 가속기를 탐색 하기 위한 사용자 인터페이스입니다.
 - **배포자 로그 파일**-배포 중에 생성 되는 로그 파일입니다. 필요한 경우 문제를 해결 하는 데 사용할 수 있습니다.

## <a name="deployment-scenario-2"></a>배포 시나리오 2

기존 Azure Active Directory 앱 등록이 배포 하는 데 사용 됩니다 (위의 사례 2).

1. 다음 JSON 파일을 복사 하 여 Azure 애플리케이션 클라이언트 ID와 암호를 포함 하는 json 파일을 저장 합니다.

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

나머지 단계를 수행 합니다.

2. 로컬 컴퓨터의 입력. json 파일에 대 한 경로를 적어 둡니다.
3. Azure Cloud Shell으로 다시 이동 하 여 성공적으로 인증 되 면 업로드 단추 (아래 이미지의 강조 표시 된 아이콘 참조)를 선택 하 고 입력 된 json 파일을 Cloud Shell 저장소에 업로드 합니다.

    ![프로젝트 팜 비트](./media/prepare-for-deployment/bash-2-1.png)

4. Cloud shell의 홈 디렉터리로 이동 합니다. 기본적으로/home/<username>
5. Cloud Shell에 다음 명령을 입력 하거나 붙여 넣습니다. 입력 경로를 수정 해야 합니다. Json 파일을 입력 하 고 enter 키를 누릅니다.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

화면의 지침을 따릅니다.

6. 이 스크립트는 모든 종속성을 자동으로 다운로드 하 고 배포자를 작성 합니다.
7. Azure FarmBeats EULA (최종 사용자 사용권 계약)를 읽고 동의 하 라는 메시지가 표시 됩니다.

    - 동의 하면 ' Y '를 입력 하 고 다음 단계를 계속 합니다.
    - 조건에 동의 하지 않고 배포가 종료 되는 경우 ' N '을 입력 합니다.

8. 배포에 대 한 액세스 토큰을 입력 하 라는 메시지가 표시 됩니다. 생성 된 코드를 복사 하 고 Azure 자격 증명을 사용 하 여 https://microsoft.com/devicelogin 에 로그인 합니다.
9. 이제 설치 관리자에서 리소스 만들기의 유효성을 검사 하 고 시작 합니다 .이는 약 20 분 정도 걸릴 수 있습니다. 이 시간 동안 Cloud Shell에서 세션을 활성 상태로 유지 합니다.
10. 배포가 성공적으로 완료 되 면 아래 출력 링크를 받게 됩니다.

 - **데이터 허브 URL**: Swagger 링크를 통해 Azure FarmBeats api를 사용해 보세요.
 - **ACCELERATOR URL**: Azure FarmBeats Accelerator를 탐색 하기 위한 사용자 인터페이스입니다.
 - **배포자 로그 파일**: 배포 중에 생성 되는 로그 파일입니다. 필요한 경우 문제를 해결 하는 데 사용할 수 있습니다.

문제가 발생 하는 경우 [문제 해결](troubleshoot-project-farmbeats.md)을 검토 합니다.


## <a name="validate-deployment"></a>배포 유효성 검사

### <a name="data-hub"></a>데이터 허브

데이터 허브 설치가 완료 되 면 다음 형식으로 Swagger 인터페이스를 통해 Azure FarmBeats Api에 액세스 하는 URL을 받게 됩니다. https://\<해당 합니다. 웹 사이트-이름 >.

1. Swagger를 통해 로그인 하려면 브라우저에서 URL을 복사 하 여 붙여 넣습니다.
2. Azure Portal 자격 증명으로 로그인 합니다.
3. 온전성 테스트 (선택 사항)

     - 성공적인 배포에 대 한 출력으로 받은 데이터 허브 링크를 사용 하 여 Swagger 포털에 성공적으로 로그인 할 수 있습니다.
     - 확장 형식 API 가져오기-"사용해 보기/Execute"를 선택 합니다.
     - 403 "권한이 없는 사용자"와 같은 예외가 아닌 서버 응답 코드 200을 수신 해야 합니다.

### <a name="accelerator"></a>액셀러레이터

가속기 설치가 완료 되 면 다음 형식으로 Azure FarmBeats 사용자 인터페이스에 액세스 하는 URL을 받게 됩니다. https://\<Accelerator-websites-이름 >

1. Accelerator에서 로그인 하려면 브라우저에서 URL을 복사 하 여 붙여 넣습니다.
2. Azure Portal 자격 증명으로 로그인 합니다.

## <a name="upgrade"></a>업그레이드

업그레이드 단계는 처음 설치할 때와 비슷합니다. 다음 단계를 수행하세요.

1. Azure Portal에 로그인 하 고 원하는 구독과 AD 테 넌 트를 선택 합니다.
2. Azure Portal의 위쪽 탐색 모음에서 Cloud Shell을 시작합니다.

   ![프로젝트 팜 비트](./media/prepare-for-deployment/navigation-bar-1.png)

3. 셸의 왼쪽에 있는 드롭다운에서 환경을 "Bash"로 선택 합니다.
4. 필요한 경우에만 입력 된 json 파일을 변경 하 고 Azure Cloud Shell 업로드 합니다. 예를 들어 수신 하려는 알림에 대 한 전자 메일 주소를 업데이트할 수 있습니다.
5. Azure Cloud Shell에 입력 json 파일을 업로드 합니다.
6. Cloud Shell에 다음 두 명령을 입력 하거나 붙여 넣습니다. 입력. json 파일의 경로를 수정 하 고 enter 키를 누릅니다.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
화면의 지침을 따릅니다.

7. 설치 관리자는 런타임에 필요한 입력을 자동으로 표시 합니다.
8. 배포에 대 한 액세스 토큰을 입력 합니다. 생성 된 코드를 복사 하 고 Azure 자격 증명을 사용 하 여 https://microsoft.com/devicelogin 에 로그인 합니다.
9. 센티널 암호
10. 이제 설치 관리자는 리소스의 유효성을 검사 하 고 리소스 만들기를 시작 합니다 .이는 약 20 분 정도 걸릴 수 있습니다.
11. 배포가 성공적으로 완료 되 면 아래 출력 링크를 받게 됩니다.
 - **데이터 허브 URL**: Swagger 링크를 통해 Azure FarmBeats api를 사용해 보세요.
 - **ACCELERATOR URL**: Azure FarmBeats Accelerator를 탐색 하기 위한 사용자 인터페이스입니다.
 - **배포자 로그 파일**: 배포 하는 동안 로그를 저장 합니다. 문제를 해결 하는 데 사용할 수 있습니다.

> [!NOTE]
> 나중에 사용 하기 위해 위의 값을 기록해 둡니다.


## <a name="uninstall"></a>제거

현재 설치 관리자를 사용 하 여 Azure FarmBeats의 자동 제거를 지원 하지 않습니다. 데이터 허브 또는 가속기를 제거 하려면 Azure Portal에서 이러한 구성 요소가 설치 된 리소스 그룹을 삭제 하거나 리소스를 수동으로 삭제 합니다.

예를 들어 두 개의 다른 리소스 그룹에 데이터 허브 및 가속기를 배포한 경우 다음과 같이 해당 리소스 그룹을 삭제 합니다.

1. Azure Portal에 로그인합니다.
2. 오른쪽 위 모서리에서 사용자의 계정을 선택 하 고 Azure FarmBeats를 배포 하려는 원하는 Azure AD 테 넌 트로 전환 합니다.

   > [!NOTE]
   > 데이터 허브는 가속기가 제대로 작동 하는 데 필요 합니다. 가속기를 제거 하지 않고 데이터 허브를 제거 하지 않는 것이 좋습니다.

3. 리소스 그룹을 선택 하 고 삭제 하려는 데이터 허브 또는 액셀러레이터 리소스 그룹의 이름을 입력 합니다.
4. 해당 리소스 그룹 이름을 선택합니다. 이름을 다시 입력 하 여 다시 확인 하 고 삭제를 선택 하 여 리소스 그룹 및 모든 기본 리소스를 제거 합니다.
5. 또는 각 리소스를 수동으로 삭제할 수 있습니다 .이는 권장 되지 않습니다.
7. 데이터 허브를 삭제/제거 하려면 Azure에서 직접 리소스 그룹으로 이동 하 여 해당 위치에서 리소스 그룹을 삭제 합니다.

## <a name="next-steps"></a>다음 단계

Azure FarmBeats를 배포 했습니다. 이제 [팜을 만드는](manage-farms.md#create-farms)방법을 알아봅니다.
