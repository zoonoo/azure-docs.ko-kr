---
title: 드론 이미지 가져오기
description: 이 문서에서는 파트너로부터 드론 이미지를 얻는 방법에 대해 설명합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3e452cd548738e5f211899d3a6a676f883d800ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132041"
---
# <a name="get-drone-imagery-from-drone-partners"></a>드론 파트너로부터 드론 이미지 받기

이 문서에서는 드론 이미지 파트너의 정형고모종 데이터를 Azure FarmBeats Datahub로 가져오는 방법에 대해 설명합니다. 정형병은 기하학적으로 수정되고 무인 항공기에 의해 수집 된 데이터에서 스티치 공중 그림 또는 이미지입니다.

현재 다음 이미지 파트너가 지원됩니다.

  ![FarmBeats 드론 이미지 파트너](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

Azure FarmBeats와 드론 이미지 데이터를 통합하면 팜에서 수행하는 드론 비행에서 데이터 허브로 정형고모사 데이터를 얻을 수 있습니다. 데이터를 사용할 수 있게 되면 FarmBeats 가속기에서 데이터를 볼 수 있습니다. 데이터는 데이터 융합 및 인공 지능 및 기계 학습 모델 구축에 사용할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

  - Azure FarmBeats를 설치했는지 확인합니다. FarmBeats를 설치하는 방법에 대한 자세한 내용은 [Azure FarmBeats 설치를](install-azure-farmbeats.md)참조하십시오.
  - FarmBeats 시스템에 정의된 드론 이미지를 원하는 팜이 있는지 확인합니다.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>FarmBeats와 드론 이미지 통합 지원

FarmBeats와의 통합을 활성화하려면 장치 공급자에게 다음 정보를 제공합니다.
 - API 엔드포인트
 - 테넌트 ID
 - 클라이언트 ID
 - 클라이언트 암호

다음 단계를 수행합니다.

1. 이 [스크립트를](https://aka.ms/farmbeatspartnerscript)다운로드하여 로컬 드라이브로 추출합니다. zip 파일 내부에 두 개의 파일이 있습니다.
2. [Azure Portal](https://portal.azure.com/)에 로그인하고 Azure Cloud Shell을 엽니다. 이 옵션은 포털의 오른쪽 위 모서리에 있는 도구 모음에서 사용할 수 있습니다.

    ![포털의 오른쪽 위 표시줄에서 Azure 클라우드 셸 열기](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 환경이 **PowerShell으로**설정되어 있는지 확인합니다.

    ![파워쉘 설정](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Cloud Shell 인스턴스에서 1단계에서 다운로드한 두 파일을 업로드합니다.

    ![파일 업로드](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. 파일이 업로드된 디렉터리로 이동합니다. 기본적으로 사용자 이름으로 홈 디렉토리에 업로드됩니다.
6. 다음 스크립트를 실행합니다.

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. 화면의 지침에 따라 API 엔드포인트, 테넌트 ID, 클라이언트 ID, 클라이언트 보안 및 EventHub 연결 문자열의 값을 캡처합니다.

    파트너의 무인 항공기 소프트웨어 시스템에 필요한 자격 증명을 입력한 후 FarmBeats 시스템에서 모든 팜을 가져올 수 있습니다. 그런 다음 팜 세부 정보를 사용하여 비행 경로 계획 및 드론 이미지 수집을 수행할 수 있습니다.

    원시 이미지가 드론 공급자의 소프트웨어에 의해 처리된 후 드론 소프트웨어 시스템은 스티치된 정형고모사ic 및 기타 처리된 이미지를 데이터 허브에 업로드합니다.

## <a name="view-drone-imagery"></a>드론 이미지 보기

데이터가 FarmBeats 데이터 허브로 전송된 후 FarmBeats Datahub API를 사용하여 씬 저장소를 쿼리할 수 있습니다.

또는 **팜 세부 정보** 페이지에서 최신 드론 이미지를 볼 수 있습니다. 이미지를 보려면 단계를 따릅니다.

1. 이미지가 업로드된 팜을 선택합니다. **팜** 세부 정보 페이지가 나타납니다.
2. 아래로 스크롤하여 최신 정밀도 섹션으로 **이동합니다.**
3. **드론 이미지** 섹션에서 이미지를 봅니다.

    ![드론 이미지 섹션](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>드론 이미지 다운로드

드론 이미지 섹션을 선택하면 드론 정형고모사증의 고해상도 이미지를 표시하는 팝업이 열립니다.

![고해상도 정형고모사믹](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>모든 드론 지도 보기

드론 공급자가 업로드한 파일과 이미지가 **지도** 섹션에 표시됩니다. **맵** 섹션을 선택하고 **팜별로**필터링하고 적절한 파일을 선택하여 보고 다운로드합니다.

  ![지도 섹션](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>다음 단계

FarmBeats Datahub [API를](rest-api-in-azure-farmbeats.md) 사용하여 드론 이미지를 얻는 방법에 대해 알아봅니다.
