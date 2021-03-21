---
title: 새 업데이트를 가져오는 방법 | Microsoft Docs
description: IoT Hub에 대 한 IoT Hub 장치 업데이트로 새 업데이트를 가져오기 위한 How-To 가이드입니다.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: b9d40848abdd85beeca592001b697e3c50b7cd59
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008565"
---
# <a name="import-new-update"></a>새 업데이트 가져오기
IoT Hub에 대 한 장치 업데이트로 새 업데이트를 가져오는 방법에 대해 알아봅니다. 아직 수행 하지 않은 경우 기본 [가져오기 개념](import-concepts.md)을 숙지 해야 합니다.

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

2. 업데이트 이미지 파일 또는 APT 매니페스트 파일이 있는 디렉터리에 **Aduupdate** 라는 텍스트 파일을 만듭니다. 그런 다음, [.Psm1](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) PowerShell cmdlet을 열고 내용을 텍스트 파일에 복사한 다음 텍스트 파일을 저장 합니다.

3. PowerShell에서 2 단계에서 PowerShell cmdlet을 만든 디렉터리로 이동 합니다. 다음을 실행합니다.

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

    빠른 참조를 위해 위의 매개 변수에 대 한 몇 가지 예제 값은 다음과 같습니다. 자세한 내용은 전체 [가져오기 매니페스트 스키마](import-schema.md) 를 볼 수도 있습니다.

    | 매개 변수 | 설명 |
    | --------- | ----------- |
    | deviceManufacturer | 업데이트가 호환 되는 (예: Contoso) 장치의 제조업체입니다. _제조업체_ [장치 속성과](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-plug-and-play#device-properties)일치 해야 합니다.
    | deviceModel | 업데이트가 호환 되는 장치 (예: Toaster)의 모델입니다. _모델_ [장치 속성과](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-plug-and-play#device-properties)일치 해야 합니다.
    | updateProvider | 업데이트를 만들거나 직접 담당 하는 엔터티입니다. 회사 이름이 될 수도 있습니다.
    | updateName | 업데이트 클래스의 식별자입니다. 클래스는 사용자가 선택 하는 것이 될 수 있습니다. 일반적으로 장치 또는 모델 이름이 됩니다.
    | updateVersion | 이 업데이트를 동일한 공급자와 이름을 가진 다른 사용자와 구별 하는 버전 번호입니다. 는 장치에 있는 개별 소프트웨어 구성 요소의 버전과 일치 하지 않습니다 (선택 하는 경우에는 가능 함).
    | updateType | <ul><li>`microsoft/swupdate:1`이미지 업데이트에 대 한 지정</li><li>`microsoft/apt:1`패키지 업데이트 지정</li></ul>
    | installedCriteria | <ul><li>업데이트 유형에 대 한 SWVersion 값을 지정 합니다. `microsoft/swupdate:1`</li><li>업데이트 형식에 권장 값을 지정 `microsoft/apt:1` 합니다.
    | updateFilePath | 컴퓨터의 업데이트 파일 경로


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

[!NOTE]
아래 지침에서는 Azure Portal UI를 통해 업데이트를 가져오는 방법을 보여 줍니다. [IoT Hub api에 대 한 장치 업데이트](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) 를 사용 하 여 업데이트를 가져올 수도 있습니다. 

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 장치 업데이트를 사용 하 여 IoT Hub으로 이동 합니다.

2. 페이지의 왼쪽에 있는 "자동 장치 관리"에서 "장치 업데이트"를 선택 합니다.

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="업데이트 가져오기" lightbox="media/import-update/import-updates-3.png":::

3. 화면 위쪽에 여러 탭이 표시 됩니다. 업데이트 탭을 선택합니다.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="업데이트" lightbox="media/import-update/updates-tab.png":::

4. "배포 준비 완료" 헤더 아래에 있는 "+ 새 업데이트 가져오기"를 선택 합니다.

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="새 업데이트 가져오기" lightbox="media/import-update/import-new-update-2.png":::

5. "매니페스트 파일 가져오기 선택"에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 파일 선택기 대화 상자가 표시됩니다. PowerShell cmdlet을 사용 하 여 이전에 만든 가져오기 매니페스트를 선택 합니다. 다음으로 "하나 이상의 업데이트 파일 선택" 아래에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 파일 선택기 대화 상자가 표시됩니다. 업데이트 파일을 선택 합니다.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="업데이트 파일 선택" lightbox="media/import-update/select-update-files.png":::

6. "스토리지 컨테이너 선택" 아래에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 그런 다음 적절한 스토리지 계정을 선택합니다. 저장소 컨테이너는 업데이트 파일을 임시로 준비 하는 데 사용 됩니다.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Storage 계정" lightbox="media/import-update/storage-account.png":::

7. 이미 컨테이너를 만든 경우 다시 사용할 수 있습니다. (그렇지 않으면 "+ 컨테이너"를 선택하여 업데이트를 위한 새 스토리지 컨테이너를 만듭니다.)  사용할 컨테이너를 선택하고 "선택"을 클릭합니다.

   :::image type="content" source="media/import-update/container.png" alt-text="컨테이너 선택" lightbox="media/import-update/container.png":::

8. "제출"을 선택하여 가져오기 프로세스를 시작합니다.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="업데이트 게시" lightbox="media/import-update/publish-update.png":::

9. 가져오기 프로세스가 시작 되 고 화면이 "가져오기 기록" 섹션으로 전환 됩니다. 가져오기 프로세스가 완료 될 때까지 진행률을 보려면 "새로 고침"을 선택 합니다. 업데이트 크기에 따라 몇 분만에 완료 될 수 있지만 시간이 오래 걸릴 수 있습니다.

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="가져오기 시퀀싱 업데이트" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. 상태 열에 가져오기가 성공했음을 나타내는 경우 "배포할 준비가 됨" 헤더를 선택합니다. 이제 목록에 가져온 업데이트가 표시됩니다.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="작업 상태" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>다음 단계

[그룹 만들기](create-update-group.md)

[가져오기 개념에 대 한 자세한 정보](import-concepts.md)
