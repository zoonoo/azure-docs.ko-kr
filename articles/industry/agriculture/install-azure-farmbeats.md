---
title: Azure FarmBeats 설치
description: 이 문서에서는 azure 구독에 Azure FarmBeats을 설치 하는 방법을 설명 합니다.
author: usha-rathnavel
ms.topic: article
ms.date: 12/11/2019
ms.author: usrathna
ms.openlocfilehash: d1a6bdfb38431e18eb305b223ce8ee2467804052
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482453"
---
# <a name="install-azure-farmbeats"></a>Azure FarmBeats 설치

이 문서에서는 azure 구독에 Azure FarmBeats을 설치 하는 방법을 설명 합니다.

Azure FarmBeats는 Azure Marketplace에서 사용할 수 있는 b2b 제품입니다. 이를 통해 공급자의 농업 데이터 집합을 집계 하 고 실행 가능한 정보를 생성할 수 있습니다. Azure FarmBeats는 퓨즈 데이터 집합을 기반으로 AI (인공 지능) 또는 기계 학습 (ML) 모델을 빌드할 수 있도록 하 여이를 수행 합니다. Azure FarmBeats의 두 가지 주요 구성 요소는 다음과 같습니다.

- **Datahub**: 다양 한 공급자에 걸쳐 다양 한 농업 데이터 집합의 집계, 정규화 및 contextualization를 사용 하도록 설정 하는 API 계층입니다.

- **Accelerator**: datahub 위에 빌드되는 샘플 웹 응용 프로그램입니다. 모델 개발 및 시각화를 바로 시작 합니다. 액셀러레이터는 Azure FarmBeats Api를 사용 하 여 수집 센서 데이터를 차트로 시각화 하 고 모델 출력의 시각화를 지도로 보여 줍니다.

## <a name="before-you-start"></a>시작하기 전에
### <a name="components-installed"></a>설치 된 구성 요소

Azure FarmBeats를 설치 하는 경우 Azure 구독에 다음과 같은 리소스가 프로 비전 됩니다.

| 설치 된 Azure 리소스  | Azure FarmBeats 구성 요소  |
|---------|---------|
| Application Insights   |      Datahub & 액셀러레이터      |
| App Service     |     Datahub & 액셀러레이터     |
| App Service 계획   | Datahub & 액셀러레이터  |
| API 연결    |  Datahub       |
| Azure Cache for Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Datahub & 액셀러레이터      |
| Azure Batch 계정    | Datahub   |
| Azure Key Vault |  Datahub & 액셀러레이터        |
| Azure Maps 계정       |     액셀러레이터    |
| 이벤트 허브 네임 스페이스    |     Datahub      |
| 논리 앱      |  Datahub       |
| Storage 계정      |     Datahub & 액셀러레이터      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>발생 한 비용

Azure FarmBeats의 비용은 기본 Azure 서비스 비용의 집계입니다. [가격 계산기](https://azure.microsoft.com/pricing/calculator)를 사용 하 여 Azure 서비스에 대 한 가격 정보를 계산할 수 있습니다. 총 설치의 실제 비용은 사용량에 따라 달라 집니다. 두 구성 요소에 대 한 안정 된 상태 비용은 다음과 같습니다.

* Datahub-일별 $10 미만
* Accelerator-하루 $2 미만

### <a name="regions-supported"></a>지원 되는 지역

현재 Azure FarmBeats는 다음 지역의 공용 클라우드 환경에서 지원 됩니다.
* 오스트레일리아 동부
* 미국 중부
* 미국 동부
* 미국 동부 2
* 미국 서부
* 미국 서부 2
* 북유럽
* 서유럽
* 동남아시아

### <a name="time-taken"></a>걸린 시간

준비 및 설치를 포함 하 여 Azure FarmBeats의 전체 설정은 한 시간 이내에 소요 됩니다.

## <a name="prerequisites"></a>필수 조건    

Azure FarmBeats의 실제 설치를 시작 하기 전에 다음 단계를 완료 해야 합니다.

### <a name="create-sentinel-account"></a>센티널 계정 만들기
Azure FarmBeats 설치 프로그램을 사용 하 여 팜에 대해 유럽 공간 에이전시의 [센티널-2](https://scihub.copernicus.eu/) 위성 업무에서 무료 위성 이미지를 가져올 수 있습니다. 이 설정을 구성 하려면 센티널 계정이 필요 합니다.

센티널을 사용 하 여 무료 계정을 만들려면 다음 단계를 따르세요.

1. 공식 [등록](https://scihub.copernicus.eu/dhus/#/self-registration) 페이지로 이동 합니다.
2. 필요한 세부 정보 (이름, 성, 사용자 이름, 암호 및 전자 메일 ID)를 입력 하 고 양식을 완성 합니다.
3. 등록 된 전자 메일 ID로 확인 링크가 전송 됩니다. 전자 메일에 제공 된 링크를 선택 하 고 확인을 완료 합니다.

확인을 완료 하면 등록 프로세스가 완료 됩니다. **센티널 사용자 이름** 및 **센티널 암호**를 기록해 둡니다.

### <a name="decide-subscription-and-region"></a>구독 및 지역 결정

Azure FarmBeats를 설치 하려는 Azure 구독 ID 및 지역이 필요 합니다. [지원 되는 지역](#regions-supported) 섹션에 나열 된 지역 중 하나를 선택 합니다.

**Azure 구독 ID** 와 **azure 지역을**적어 둡니다.

### <a name="verify-permissions"></a>권한 확인

Azure FarmBeats를 설치 하려는 Azure 테 넌 트에서 충분 한 권한 및 사용 권한이 있는지 확인 해야 합니다.

[역할 기반 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/check-access) 에 대 한 지침에 따라 Azure Portal에서 액세스 권한을 확인할 수 있습니다.

Azure FarmBeats를 설치 하려면 다음 권한이 필요 합니다.
- 테 넌 트-읽기 액세스
- 구독-참가자 또는 소유자
- 리소스 그룹-소유자.

또한 Azure FarmBeats에는 응용 프로그램 등록 Azure Active Directory 필요 합니다. 필요한 Azure AD 설치를 완료 하는 방법에는 다음 두 가지가 있습니다.

**사례 1**: 설치 하려는 Azure 테 넌 트에서 **쓰기** 권한이 있습니다. 이 경우 설치 중에 AAD 앱 등록을 동적으로 만드는 데 필요한 권한이 있음을 의미 합니다.

[Marketplace 등록 완료](#complete-azure-marketplace-sign-up) 섹션으로 바로 진행할 수 있습니다.


**사례 2**: Azure 테 넌 트에서 **쓰기** 권한이 없습니다. 이 경우는 회사의 Azure 구독에 Azure FarmBeats를 설치 하려고 할 때 일반적 이며, 사용자가 소유 하는 리소스 그룹 으로만 **쓰기** 권한이 제한 됩니다.
이 경우 IT 관리자에 게 다음 단계를 수행 하 여 Azure Portal에서 Azure AD 앱 등록을 자동으로 생성 하 고 완료 하도록 요청 합니다.

1. [AAD 앱 생성기 스크립트](https://aka.ms/FarmBeatsAADScript) 를 다운로드 하 여 로컬 컴퓨터에 추출 합니다.
2. Azure Portal에 로그인 하 고 구독과 Azure AD 테 넌 트를 선택 합니다.
3. Azure Portal 맨 위에 있는 도구 모음에서 Cloud Shell를 시작 합니다.

    ![프로젝트 FarmBeats](./media/install-azure-farmbeats/navigation-bar-1.png)

4. PowerShell을 선호 하는 셸 환경으로 선택 합니다. 처음으로 사용자에 게 구독을 선택 하 고 저장소 계정을 만들도록 요청 하는 메시지가 표시 됩니다. 지시에 따라 설치를 완료 합니다.
5. 1 단계에서 Cloud Shell 스크립트를 업로드 하 고 업로드 된 파일의 위치를 확인 합니다.

    > [!NOTE]
    > 기본적으로 파일은 홈 디렉터리에 업로드 됩니다.

6. ' Cd ' 명령을 사용 하 여 홈 디렉터리로 이동 하 고 다음 스크립트를 실행 합니다.

      ```azurepowershell-interactive
            ./create_aad_script.ps1
      ```
7. **Datahub 웹 사이트** 이름 및 **가속기 웹 사이트** 이름을 입력 합니다. 스크립트의 출력을 기록 하 고 Azure FarmBeats을 설치 하는 사용자와 공유 합니다.

IT 관리자가 필요한 세부 정보를 제공 하면 **Aad 클라이언트 ID, Aad 클라이언트 암호, Datahub 웹 사이트 이름 & 가속기 웹 사이트 이름을**적어 둡니다.

   > [!NOTE]
   > 사례 2의 지침을 수행 하는 경우 aad 클라이언트 ID & AAD 클라이언트 암호를 [매개 변수 파일](#prepare-parameters-file) 에서 별도의 매개 변수로 추가 해야 합니다.

이제 다음 섹션으로 이동 하는 데 필요한 모든 정보가 있습니다.

### <a name="complete-azure-marketplace-sign-up"></a>완료 Azure Marketplace 등록

클라우드 셸 기반 설치 프로세스를 시작 하기 전에 Azure marketplace에서 Azure FarmBeats 제품에 등록을 완료 해야 합니다. 아래 단계에 따라 등록을 완료 합니다.

1.  Azure Portal에 로그인합니다. 오른쪽 위 모서리에서 계정을 선택 하 고 Azure FarmBeats를 설치 하려는 Azure AD 테 넌 트로 전환 합니다.

2.  포털 내의 Azure Marketplace로 이동 하 고 Marketplace에서 **Azure FarmBeats** 를 검색 합니다.

3.  Azure FarmBeats의 개요가 표시 된 새 창이 표시 됩니다. **만들기**를 선택합니다.

4.  새 창이 나타납니다. Azure FarmBeats를 설치 하려는 올바른 구독, 리소스 그룹 및 위치를 선택 하 여 등록 프로세스를 완료 합니다.

5.  입력 한 세부 정보의 유효성이 검사 되 면 **확인**을 선택 합니다. 사용 약관 페이지가 나타납니다. 용어를 검토 하 고 **만들기** 를 선택 하 여 등록 프로세스를 완료 합니다.

이 단계에서는 Azure Marketplace에서 등록 프로세스를 완료 합니다. 이제 매개 변수 파일 준비를 시작할 준비가 되었습니다.

### <a name="prepare-parameters-file"></a>매개 변수 파일 준비
필수 구성 요소 단계의 마지막 단계는 Cloud Shell 설치 하는 동안 입력으로 사용할 JSON 파일을 만드는 것입니다. JSON 파일의 매개 변수를 적절 한 값으로 바꾸어야 합니다.

샘플 JSON 파일은 아래에 제공 되어 있습니다. 샘플을 다운로드 하 고 필요한 세부 정보를 업데이트 합니다.

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":"dummy-test-acc1",
    "acceleratorWebsiteName":"dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

아래 매개 변수 테이블을 참조 하 여 각 매개 변수에 대해 자세히 알아볼 수 있습니다.

| 매개 변수 | 값|
|--- | ---|
|sku  | 사용자에 게 Datahub와 Accelerator 또는 Datahub를 모두 설치할 수 있는 옵션을 제공 합니다. Datahub만 설치 하려면 "datahub"를 사용 합니다. Datahub 및 Accelerator를 설치 하려면 "both"를 사용 합니다.|
|subscriptionId | Azure FarmBeats을 설치 하는 Azure 구독을 지정 합니다.|
|datahubResourceGroup| Datahub 리소스의 리소스 그룹 이름을 지정 합니다. 여기 Azure Marketplace에서 만든 리소스 그룹 이름을 입력 합니다.|
|위치 |Azure FarmBeats를 설치 하려는 위치/Azure 지역|
|datahubWebsiteName  | Datahub 웹 응용 프로그램에 대 한 고유 URL 접두사 |
|acceleratorResourceGroup  | 액셀러레이터 리소스의 리소스 그룹 이름을 지정 합니다.|
|acceleratorWebsiteName |액셀러레이터 웹 응용 프로그램의 고유한 URL 접두사입니다.|
|sentinelUsername | 센티널 위성 이미지를 가져올 사용자 이름입니다.|
|notificationEmailAddress  | Datahub 내에서 구성 하는 경고에 대 한 알림을 받을 전자 메일 주소|
|updateIfExists| 필드 기존 Azure FarmBeats 인스턴스를 업그레이드 하려는 경우에만 매개 변수 파일에 포함할 매개 변수입니다. 업그레이드의 경우 리소스 그룹 이름 및 위치와 같은 기타 세부 정보는 동일 해야 합니다.|
|aadAppClientId | 필드 미리 만든 AAD 앱을 사용 하는 경우에만 매개 변수 파일에 포함할 매개 변수입니다. 자세한 내용은 [사용 권한 확인](#verify-permissions) 섹션의 사례 2를 참조 하세요. |
|aadAppClientSecret  | 필드 필드 미리 만든 AAD 앱을 사용 하는 경우에만 매개 변수 파일에 포함할 매개 변수입니다. 자세한 내용은 [사용 권한 확인](#verify-permissions) 섹션의 사례 2를 참조 하세요.|

위의 표와 샘플 JSON 파일을 기반으로 하 여 매개 변수 JSON 파일을 만들고 로컬 컴퓨터에 저장 합니다.

## <a name="install"></a>설치

Azure FarmBeats 리소스의 실제 설치는 Bash 환경을 사용 하 여 Cloud Shell 브라우저 기반 명령줄 인터페이스에서 발생 합니다. Azure FarmBeats를 설치 하려면 아래 지침을 따르세요.

1. Azure Portal에 로그인합니다. Azure FarmBeats를 설치 하려는 Azure 구독 및 테 넌 트를 선택 합니다.
2. Azure Portal의 오른쪽 위 모퉁이에 있는 도구 모음에서 **Cloud Shell** 를 시작 합니다.
3. 기본 설정 셸 환경으로 Bash를 선택 합니다. **업로드** 단추 (아래 그림에 강조 표시 됨)를 선택 하 고 준비 된 매개 변수 JSON 파일을 업로드 합니다.

      ![프로젝트 FarmBeats](./media/install-azure-farmbeats/bash-2-1.png)

4. 다음 명령을 **복사** 하 고 명령이 업로드 된 파일의 올바른 경로를 가리키도록 **\<username >를** 올바른 값으로 바꿉니다.

    ```bash
          wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
5. 수정 된 명령을 실행 하 여 설치 프로세스를 시작 합니다. 다음을 묻는 메시지가 표시 됩니다.
 - **Azure FarmBeats 사용** 조건에 동의 합니다. "Y"를 입력 하 여 사용 약관에 동의 하는 경우 다음 단계를 진행 합니다. 사용 약관에 동의 하지 않는 경우 "N"을 입력 하 여 설치를 종료 합니다.

 - 다음으로 설치에 대 한 액세스 토큰을 입력 하 라는 메시지가 표시 됩니다. 생성 된 코드를 복사 하 고 **Azure 자격 증명**을 사용 하 여 [장치 로그인 페이지](https://microsoft.com/devicelogin) 에 로그인 합니다.

 - 로그인이 성공적으로 완료 되 면 설치 관리자가 센티널 계정 암호를 묻는 메시지를 표시 합니다. **센티널 계정 암호**를 입력 합니다.

6. 매개 변수 파일의 유효성을 검사 하 고 Azure 리소스의 설치를 시작 합니다. 설치를 완료 하는 데 약 **25 분** 이 소요 됩니다.    
> [!NOTE]
> 비활성 Cloud Shell 세션은 **20 분**후에 만료 됩니다. 설치 관리자가 Azure 리소스를 배포 하는 동안 Cloud Shell 세션을 활성 상태로 유지 하세요. 세션 시간이 초과 되 면 설치 프로세스를 다시 시작 해야 합니다.

설치가 완료 되 면 다음 출력 링크를 받게 됩니다.
* **DATAHUB URL**: Datahub api에 액세스 하기 위한 Swagger 링크입니다.
* **ACCELERATOR URL**: Azure FarmBeats Accelerator를 탐색 하기 위한 웹 응용 프로그램입니다.
* **설치 관리자 로그 파일**: 설치에 대 한 세부 정보를 포함 하는 로그 파일입니다. 필요한 경우이 로그 파일을 사용 하 여 설치 문제를 해결할 수 있습니다.

다음 검사를 수행 하 여 Azure FarmBeats 설치가 완료 되었는지 확인할 수 있습니다.

**Datahub**
1. Azure 자격 증명을 사용 하 여 제공 되는 액셀러레이터 URL (https://\<해당 하는 **datahub-웹 사이트 이름 >/swagger**)에 로그인 합니다.
2. 다른 FarmBeats API 개체를 보고 Api에 대 한 REST 작업을 수행할 수 있습니다.

**Accelerator**
1. Azure 자격 증명을 사용 하 여 제공 된 액셀러레이터 URL ( **https://\<(웹 사이트-이름 >. azurewebsites.**
2. 브라우저에서 팜을 만드는 옵션으로 액셀러레이터 키 사용자 인터페이스를 볼 수 있습니다.

위의 작업을 수행 하는 기능은 Azure FarmBeats가 성공적으로 설치 되었음을 나타냅니다.

## <a name="upgrade"></a>업그레이드
공개 미리 보기 버전에서 Azure FarmBeats의 기존 설치를 업그레이드 하려면 "**true**"로 설정 된 매개 변수 파일에 추가 "**Updateifexists**" 매개 변수를 사용 하 여 Cloud Shell에서 설치 명령을 다시 실행 해야 합니다. 업데이트 매개 변수에 대 한 아래 JSON 샘플의 마지막 줄을 참조 하세요.

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
이 명령은 기존 Azure FarmBeats 설치를 최신 버전으로 업데이트 하 고 출력 링크를 제공 합니다.

## <a name="uninstall"></a>제거

Azure FarmBeats Datahub 또는 Accelerator를 제거 하려면 다음 단계를 완료 합니다.

1.  Azure Portal에 로그인 하 고 이러한 구성 요소가 설치 된 **리소스 그룹을 삭제** 합니다.

2.  Azure Active Directory로 이동 하 여 Azure FarmBeats 설치에 연결 된 **AZURE AD 응용 프로그램 & 삭제** 합니다. 이렇게 하면 azure 구독에서 Azure FarmBeats 설치가 제거 됩니다.

## <a name="next-steps"></a>다음 단계
Azure 구독에 Azure FarmBeats을 설치 하는 방법을 배웠습니다. 이제 Azure FarmBeats 인스턴스에 [사용자를 추가](manage-users-in-azure-farmbeats.md#manage-users) 하는 방법에 대해 알아봅니다.
