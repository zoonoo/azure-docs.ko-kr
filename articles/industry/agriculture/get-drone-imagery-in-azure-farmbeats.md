---
title: 드론 이미지 가져오기
description: 이 문서에서는 파트너로부터 드론 이미지를 가져오는 방법을 설명합니다.
author: RiyazPishori
ms.topic: article
ms.date: 11/04/2019
ms.author: riyazp
ms.openlocfilehash: 7ef68267fdd6f1072271f0a0d3f2ab1427ecd4c8
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108751268"
---
# <a name="get-drone-imagery-from-drone-partners"></a>드론 파트너로부터 드론 이미지 가져오기

이 문서에서는 드론 이미지 파트너의 orthomosaic 데이터를 Azure FarmBeats Datahub로 가져오는 방법을 설명합니다. orthomosaic은 드론이 수집한 데이터를 기하학적으로 수정하고 붙인 항공 그림 또는 이미지입니다.

현재 지원되는 이미지 파트너는 다음과 같습니다.

  ![FarmBeats 드론 이미지 파트너](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

드론 이미지 데이터를 Azure FarmBeats와 통합하면 팜에서 수행하는 드론 비행의 orthomosaic 데이터를 datahub로 가져올 수 있습니다. 데이터를 사용할 수 있게 되면 FarmBeats Accelerator에서 볼 수 있습니다. 데이터 퓨전, AI 및 기계 학습 모델 빌드에 데이터를 사용할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

  - Azure FarmBeats를 설치했는지 확인합니다. FarmBeats를 설치하는 방법에 대한 자세한 내용은 [Azure FarmBeats 설치](install-azure-farmbeats.md)를 참조하세요.
  - FarmBeats 시스템에 정의된 드론 이미지가 필요한 팜이 있는지 확인합니다.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>FarmBeats와 드론 이미지 통합 사용

FarmBeats와 통합을 사용하려면 디바이스 공급자에게 다음 정보를 제공합니다.
 - API 엔드포인트
 - 테넌트 ID
 - 클라이언트 ID
 - 클라이언트 암호

다음 단계를 수행합니다.

1. 이 [스크립트](https://aka.ms/farmbeatspartnerscript)를 다운로드하고 로컬 드라이브에 추출합니다. zip 파일 안에 2개의 파일이 있습니다.
2. [Azure Portal](https://portal.azure.com/)에 로그인하고 Azure Cloud Shell을 엽니다. 이 옵션은 포털의 오른쪽 위 모서리에 있는 도구 모음에서 사용할 수 있습니다.

    ![포털의 오른쪽 위 도구 모음에서 Azure Cloud Shell 열기](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 환경이 **PowerShell** 로 설정되어 있는지 확인합니다.

    ![PowerShell 설정](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. 1단계에서 다운로드한 파일 2개를 Cloud Shell 인스턴스에 업로드합니다.

    ![파일 업로드](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. 파일을 업로드한 디렉터리로 이동합니다. 기본적으로 홈 디렉터리 아래의 사용자 이름 디렉터리에 업로드됩니다.
6. 다음 스크립트를 실행합니다.

    ```azurepowershell-interactive

    ./generateCredentials.ps1

    ```

7. 화면 지시에 따라 API 엔드포인트, 테넌트 ID, 클라이언트 ID, 클라이언트 암호 및 EventHub 연결 문자열의 값을 캡처합니다.

    파트너의 드론 소프트웨어 시스템에 필요한 자격 증명을 입력한 후에는 FarmBeats 시스템에서 모든 팜을 가져올 수 있습니다. 그런 다음, 팜 세부 정보를 사용하여 플라이트 경로 계획과 드론 이미지 수집을 수행할 수 있습니다.

    원시 이미지가 드론 공급자의 소프트웨어에서 처리된 후 드론 소프트웨어 시스템이 붙인 orthomosaic과 기타 처리된 이미지를 datahub에 업로드합니다.

## <a name="view-drone-imagery"></a>드론 이미지 보기

데이터가 FarmBeats datahub로 보내진 후 FarmBeats Datahub API를 사용하여 Scene Store를 쿼리할 수 있습니다.

또는 **팜 세부 정보** 페이지에서 최신 드론 이미지를 볼 수 있습니다. 이미지를 보려면 다음 단계를 수행합니다.

1. 이미지를 업로드한 팜을 선택합니다. **팜** 세부 정보 페이지가 나타납니다.
2. 최신 **Precision Maps(정밀 지도)** 섹션으로 스크롤합니다.
3. **Drone Imagery(드론 이미지)** 섹션에서 이미지를 봅니다.

    ![Drone Imagery(드론 이미지) 섹션](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>드론 이미지 다운로드

Drone Imagery(드론 이미지) 섹션을 선택하면 드론 orthomosaic의 고해상도 이미지가 표시된 팝업이 열립니다.

![고해상도 orthomosaic](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>모든 드론 지도 보기

드론 공급자가 업로드한 파일과 이미지가 **지도** 섹션에 표시됩니다. **지도** 섹션을 선택하고 **팜** 을 기준으로 필터링한 다음, 보고 다운로드할 적절한 파일을 선택합니다.

  ![지도 섹션](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>다음 단계

FarmBeats Datahub [API](rest-api-in-azure-farmbeats.md)를 사용하여 드론 이미지를 가져오는 방법을 알아봅니다.
