---
title: "Microsoft Azure StorSimple 데이터 관리자 UI | Microsoft Docs"
description: "StorSimple 데이터 관리자 서비스 UI를 사용하는 방법 설명(비공개 미리 보기)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: 53a8599df2c647613122cd791b680e2e658586b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-using-the-storsimple-data-manager-service-ui-private-preview"></a>StorSimple 데이터 관리자 서비스 UI를 사용하여 관리(비공개 미리 보기)

이 문서에서는 StorSimple 데이터 관리자 UI를 사용하여 StorSimple 8000 시리즈 장치에 있는 데이터에 대한 데이터 변환을 수행하는 방법을 설명합니다. 그러면 Azure Media Services, Azure HDInsight, Azure Machine Learning 및 Azure Search와 같은 다른 Azure 서비스에서 변환된 데이터를 사용할 수 있습니다. 


## <a name="use-storsimple-data-transformation"></a>StorSimple 데이터 변환 사용

StorSimple 데이터 관리자는 데이터 변환을 인스턴스화할 수 있는 리소스입니다. 데이터 변환 서비스를 사용하면 데이터를 StorSimple 온-프레미스 장치에서 Azure Storage의 Blob로 이동할 수 있습니다. 따라서 워크플로에서 저장소 계정으로 이동하려는 StorSimple 장치 및 대상 데이터에 대한 세부 정보를 지정해야 합니다.

### <a name="create-a-storsimple-data-manager-service"></a>StorSimple 데이터 관리자 서비스 만들기

StorSimple 데이터 관리자 서비스를 만들려면 다음 단계를 수행합니다.

1. StorSimple 데이터 관리자 서비스를 만들려면 [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager)로 이동합니다.

2. **+** 아이콘을 클릭하고 StorSimple 데이터 관리자를 검색합니다. StorSimple 데이터 관리자 서비스를 클릭한 다음 **만들기**를 클릭합니다.

3. 이 서비스를 만들기 위해 구독을 사용하도록 설정하면 다음 블레이드가 표시됩니다.

    ![StorSimple 데이터 관리자 리소스 만들기](./media/storsimple-data-manager-ui/create-new-data-manager-service.png)

4. 입력 항목을 입력하고 **만들기**를 클릭합니다. 지정된 위치는 저장소 계정 및 StorSimple Manager 서비스를 저장한 위치여야 합니다. 현재 미국 서부 및 유럽 서부에서만 지원됩니다. 따라서 StorSimple Manager 서비스, 데이터 관리자 서비스 및 연결된 저장소 계정은 모두 앞서 지원되는 지역에 있습니다. 서비스를 만들려면 약 1분이 걸립니다.

### <a name="create-a-data-transformation-job-definition"></a>데이터 변환 작업 정의 만들기

StorSimple 데이터 관리자 서비스 내에서 데이터 변환 작업 정의를 만들어야 합니다. 작업 정의는 저장소 계정에 이동하는 데 관심이 있는 데이터의 세부 정보를 네이티브 형식으로 지정합니다. 

새 데이터 변환 작업 정의를 만들려면 다음 단계를 수행합니다.

1.  만든 서비스로 이동합니다. **+작업 정의**를 클릭합니다.

    ![+작업 정의 클릭](./media/storsimple-data-manager-ui/click-add-job-definition.png)

2. 새 작업 정의 블레이드가 열립니다. 작업 정의에 이름을 지정하고 **원본**을 클릭합니다. **구성 데이터 원본** 블레이드에서 StorSimple 장치 및 대상 데이터에 대한 세부 정보를 지정합니다.

    ![작업 정의 만들기](./media/storsimple-data-manager-ui//create-new-job-deifnition.png)

3. 새 데이터 관리자 서비스이기 때문에 데이터 리포지토리가 구성되지 않습니다. StorSimple Manager를 데이터 리포지토리로 추가하려면 데이터 리포지토리 드롭다운에서 **새로 추가**를 클릭한 다음에 **데이터 리포지토리 추가**를 클릭합니다.

4. **StorSimple 8000 시리즈 관리자**를 리포지토리 유형으로 선택하고 **StorSimple Manager**의 속성을 입력합니다. **리소스 ID** 필드에 StorSimple Manager의 등록 키에 있는 **:** 앞의 숫자를 입력해야 합니다.

    ![데이터 원본 만들기](./media/storsimple-data-manager-ui/create-new-data-source.png)

5.  완료되면 **확인**을 클릭합니다. 이렇게 하면 데이터 리포지토리를 절약하고 이러한 매개 변수를 다시 입력하지 않고 다른 작업 정의에서 이 StorSimple Manager를 다시 사용할 수 있습니다. **확인**을 클릭한 후에 StorSimple Manager가 드롭다운 목록에 표시되는 데 몇 초 정도가 걸립니다.

6.  **구성 데이터 원본** 블레이드에서 관련 데이터를 가진 장치 이름 및 볼륨 이름을 입력합니다.

7.  **필터** 하위 섹션에서 관련 데이터를 포함하는 루트 디렉터리를 입력합니다(이 필드는 `\`으로 시작해야 함). 여기에서 파일 필터를 추가할 수 있습니다.

8.  데이터 변환 서비스는 스냅숏을 통해 Azure까지 푸시되는 데이터에서 작동합니다. 이 작업을 실행할 때 최신 데이터에서 작동하도록 작업을 실행할 때마다 백업을 사용하거나 보관된 데이터를 사용하는 경우 클라우드에서 최신 기존 백업을 사용할 수 있습니다.

    ![새 데이터 원본 세부 정보](./media/storsimple-data-manager-ui/new-data-source-details.png)

9. 다음으로 대상 설정을 구성해야 합니다. 지원되는 대상에는 Azure Storage 계정 및 Azure Media Services 계정이라는 두 가지 유형이 있습니다. 저장소 계정을 선택하여 해당 계정에서 Blob에 파일을 배치합니다. Media Services 계정을 선택하여 해당 계정에서 자산에 파일을 배치합니다. 다시 리포지토리를 추가해야 합니다. 드롭다운 목록에서 **새로 추가** 및 **설정 구성** 차례로 선택합니다.

    ![데이터 싱크 만들기](./media/storsimple-data-manager-ui/create-new-data-sink.png)

10. 여기에서 추가하려는 리포지토리의 유형 및 리포지토리와 관련된 다른 매개 변수 유형을 선택할 수 있습니다. 두 경우 모두 저장소 큐는 작업이 실행될 때 만들어집니다. 이 큐는 준비가 완료된 변환 Blob에 대한 메시지로 채워집니다. 이 큐의 이름은 작업 정의의 이름과 같습니다. **Media Services**를 리포지토리 유형으로 선택하는 경우 큐가 만들어질 저장소 계정 자격 증명을 입력할 수 있습니다.

    ![새 데이터 싱크 세부 정보](./media/storsimple-data-manager-ui/new-data-sink-details.png)

11. 몇 초 정도 걸리는 작업인 데이터 리포지토리를 추가한 후에 **대상 계정 이름**의 드롭다운에서 리포지토리를 찾을 수 있습니다.  필요한 대상을 선택합니다.

12. **확인**을 클릭하여 작업 정의를 만듭니다. 이제 작업 정의가 설정되었습니다. UI를 통해 이 작업 정의를 여러 번 사용할 수 있습니다.

    ![새 작업 정의 추가](./media/storsimple-data-manager-ui/add-new-job-definition.png)

### <a name="run-the-job-definition"></a>작업 정의 실행

StorSimple에서 작업 정의에 지정된 저장소 계정으로 데이터를 이동할 때마다 해당 데이터를 호출해야 합니다. 작업을 호출할 때마다 매개 변수를 변경하는 유연성이 개선되었습니다. 단계는 다음과 같습니다.

1. StorSimple 데이터 관리자 서비스를 선택한 다음 **모니터링**으로 이동합니다. **지금 실행**을 클릭합니다.

    ![작업 정의 트리거](./media/storsimple-data-manager-ui/run-now.png)

2. 실행하려는 작업 정의를 선택합니다. **실행 설정**을 클릭하여 이 작업 실행에 대해 변경하려는 모든 설정을 수정할 수 있습니다.

    ![작업 설정 실행](./media/storsimple-data-manager-ui/run-settings.png)

3. **확인**을 클릭한 다음 **실행**을 클릭하여 작업을 시작합니다. 이 작업을 모니터링하려면 StorSimple 데이터 관리자에서 **작업** 페이지로 이동합니다.

    ![작업 목록 및 상태](./media/storsimple-data-manager-ui/jobs-list-and-status.png)

4. 또한 **작업** 블레이드의 모니터링 외에도 StorSimple에서 저장소 계정으로 파일을 이동할 때마다 메시지가 추가되는 저장소 큐에 대기할 수 있습니다.


## <a name="next-steps"></a>다음 단계

[.NET SDK를 사용하여 StorSimple 데이터 관리자 작업을 시작합니다](storsimple-data-manager-dotnet-jobs.md).