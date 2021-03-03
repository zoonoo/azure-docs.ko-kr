---
title: 새 업데이트를 가져오는 방법 | Microsoft Docs
description: IoT Hub에 대 한 IoT Hub 장치 업데이트로 새 업데이트를 가져오기 위한 How-To 가이드입니다.
author: andbrown
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: d8757f3076f784576f95bbdfc30abf578446c776
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663291"
---
# <a name="import-new-update"></a>새 업데이트 가져오기
IoT Hub에 대 한 장치 업데이트로 새 업데이트를 가져오는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* [IoT Hub 사용 하도록 설정 된 장치 업데이트를 사용 하는 IoT Hub에 대 한 액세스](create-device-update-account.md). IoT Hub에 대해 S1 (Standard) 계층 이상을 사용 하는 것이 좋습니다. 
* IoT Hub 내에서 장치 업데이트를 위해 프로 비전 된 IoT 장치 (또는 시뮬레이터)입니다.
   * 실제 장치를 사용 하는 경우 이미지 업데이트를 위한 업데이트 이미지 파일 또는 패키지 업데이트를 위한 [APT 매니페스트 파일이](device-update-apt-manifest.md) 필요 합니다.
* [PowerShell 5](https://docs.microsoft.com/powershell/scripting/install/installing-powershell) 이상
* 지원되는 브라우저:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> 이 서비스에 전송 되는 일부 데이터는이 인스턴스가 생성 된 지역 외부의 지역에서 처리 될 수 있습니다.

## <a name="create-device-update-import-manifest"></a>장치 업데이트 가져오기 매니페스트 만들기

1. 업데이트 이미지 파일 또는 APT 매니페스트 파일이 PowerShell에서 액세스할 수 있는 디렉터리에 있는지 확인 합니다.

2. [IoT Hub 리포지토리의 장치 업데이트](https://github.com/azure/iot-hub-device-update)를 복제 하거나, powershell에서 액세스할 수 있는 위치에 .zip 파일로 다운로드 합니다. zip 파일이 다운로드 되 면 탭을 마우스 오른쪽 단추로 클릭 `Properties`  >  `General` `Unblock` `Security` 하 여 powershell 보안 경고 프롬프트를 표시 하지 않도록 섹션을 선택 >.

3. PowerShell에서 디렉터리로 이동 하 여 `tools/AduCmdlets` 다음을 실행 합니다.

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. 샘플 매개 변수 값을 바꿔서 업데이트를 설명 하는 JSON 파일인 가져오기 매니페스트를 생성 하 여 다음 명령을 실행 합니다.
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    빠른 참조를 위해 위의 매개 변수에 대 한 몇 가지 예제 값은 다음과 같습니다. 전체 설명서는 아래의 전체 가져오기 매니페스트 스키마를 참조 하세요.

    | 매개 변수 | 설명 |
    | --------- | ----------- |
    | deviceManufacturer | 업데이트와 호환 되는 장치의 제조업체 (예: Contoso)
    | deviceModel | 업데이트가 호환 되는 장치 (예: Toaster)의 모델
    | updateProvider | 업데이트 id의 공급자 부분 (예: Fabrikam)
    | updateName | 업데이트 id의 이름 부분 (예: ImageUpdate)
    | updateVersion | 업데이트 버전 (예: 2.0)
    | updateType | <ul><li>`microsoft/swupdate:1`이미지 업데이트에 대 한 지정</li><li>`microsoft/apt:1`패키지 업데이트 지정</li></ul>
    | installedCriteria | <ul><li>업데이트 유형에 대 한 SWVersion 값을 지정 합니다. `microsoft/swupdate:1`</li><li>업데이트 형식에 권장 값을 지정 `microsoft/apt:1` 합니다.
    | updateFilePath | 컴퓨터의 업데이트 파일 경로

    전체 가져오기 매니페스트 스키마

    | 속성 | Type | 설명 | 제한 |
    | --------- | --------- | --------- | --------- |
    | UpdateId | `UpdateId` 개체의  멤버의 부모에 대해 SQL Server 인스턴스 이름을 표시합니다. | Id를 업데이트 합니다. |
    | UpdateType | 문자열 | 업데이트 형식: <ul><li>`microsoft/apt:1`참조 에이전트를 사용 하 여 패키지 기반 업데이트를 수행 하는 경우를 지정 합니다.</li><li>`microsoft/swupdate:1`참조 에이전트를 사용 하 여 이미지 기반 업데이트를 수행 하는 경우를 지정 합니다.</li><li>`microsoft/simulator:1`샘플 에이전트 시뮬레이터를 사용 하는 경우를 지정 합니다.</li><li>사용자 지정 에이전트를 개발 하는 경우 사용자 지정 유형을 지정 합니다.</li></ul> | <ul><li>형식: `{provider}/{type}:{typeVersion}`</li><li>최대 32 문자 합계</li></ul> |
    | InstalledCriteria | 문자열 | 업데이트가 성공적으로 적용 되었는지 여부를 확인 하기 위해 에이전트에서 해석 되는 문자열:  <ul><li>업데이트 형식에 대해 SWVersion **값** 을 지정 `microsoft/swupdate:1` 합니다.</li><li>`{name}-{version}` `microsoft/apt:1` APT 파일에서 가져온 이름 및 버전에 대 한 업데이트 유형을 지정 합니다.</li><li>업데이트 유형에 대 한 업데이트 파일의 해시를 지정 `microsoft/simulator:1` 합니다.</li><li>사용자 지정 에이전트를 개발 하는 경우 사용자 지정 문자열을 지정 합니다.</li></ul> | 최대 64 문자 |
    | 호환성 | 개체의 배열 `CompatibilityInfo` | 이 업데이트와 호환 되는 장치의 호환성 정보입니다. | 최대 10 개 항목 |
    | CreatedDateTime | 날짜/시간 | 업데이트를 만든 날짜와 시간입니다. | UTC로 구분 된 ISO 8601 날짜 및 시간 형식 |
    | ManifestVersion | 문자열 | 매니페스트 스키마 버전을 가져옵니다. `2.0`인터페이스 및 인터페이스와 호환 되는을 지정 `urn:azureiot:AzureDeviceUpdateCore:1` `urn:azureiot:AzureDeviceUpdateCore:4` 합니다.</li></ul> | `2.0`이어야 합니다. |
    | 파일 | 개체의 배열 `File` | 페이로드 파일 업데이트 | 최대 5 개 파일 |

참고: 모든 필드는 필수입니다.

## <a name="review-generated-import-manifest"></a>생성 된 가져오기 매니페스트 검토

예제:
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-update"></a>업데이트 가져오기

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 장치 업데이트를 사용 하 여 IoT Hub으로 이동 합니다.

2. 페이지의 왼쪽에 있는 "자동 장치 관리"에서 "장치 업데이트"를 선택 합니다.

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="업데이트 가져오기" lightbox="media/import-update/import-updates-3.png":::

3. 화면 위쪽에 여러 탭이 표시 됩니다. 업데이트 탭을 선택 합니다.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="업데이트" lightbox="media/import-update/updates-tab.png":::

4. "배포 준비 완료" 헤더 아래에 있는 "+ 새 업데이트 가져오기"를 선택 합니다.

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="새 업데이트 가져오기" lightbox="media/import-update/import-new-update-2.png":::

5. "매니페스트 파일 가져오기"에서 폴더 아이콘 또는 텍스트 상자를 선택 합니다. 파일 선택 대화 상자가 표시 됩니다. PowerShell cmdlet을 사용 하 여 이전에 만든 가져오기 매니페스트를 선택 합니다. 다음으로 "하나 이상의 업데이트 파일 선택" 아래에서 폴더 아이콘 또는 텍스트 상자를 선택 합니다. 파일 선택 대화 상자가 표시 됩니다. 업데이트 파일을 선택 합니다.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="업데이트 파일 선택" lightbox="media/import-update/select-update-files.png":::

6. "저장소 컨테이너 선택" 아래에서 폴더 아이콘 또는 텍스트 상자를 선택 합니다. 그런 다음 적절 한 저장소 계정을 선택 합니다. 저장소 컨테이너는 업데이트 파일을 임시로 준비 하는 데 사용 됩니다.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Storage 계정" lightbox="media/import-update/storage-account.png":::

7. 컨테이너를 이미 만든 경우 다시 사용할 수 있습니다. 그렇지 않으면 "+ 컨테이너"를 선택 하 여 업데이트에 대 한 새 저장소 컨테이너를 만듭니다.  사용할 컨테이너를 선택 하 고 "선택"을 클릭 합니다.

   :::image type="content" source="media/import-update/container.png" alt-text="컨테이너 선택" lightbox="media/import-update/container.png":::

8. "제출"을 선택 하 여 가져오기 프로세스를 시작 합니다.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="업데이트 게시" lightbox="media/import-update/publish-update.png":::

9. 가져오기 프로세스가 시작 되 고 화면이 "가져오기 기록" 섹션으로 전환 됩니다. 가져오기 프로세스가 완료 될 때까지 진행률을 보려면 "새로 고침"을 선택 합니다. 업데이트 크기에 따라 몇 분만에 완료 될 수 있지만 시간이 오래 걸릴 수 있습니다.

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="가져오기 시퀀싱 업데이트" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. 상태 열에 가져오기가 성공 했음을 나타내는 경우 "배포 준비" 헤더를 선택 합니다. 이제 목록에 가져온 업데이트가 표시 됩니다.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="작업 상태" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>다음 단계

[그룹 만들기](create-update-group.md)

[가져오기 개념에 대 한 자세한 정보](import-concepts.md)
