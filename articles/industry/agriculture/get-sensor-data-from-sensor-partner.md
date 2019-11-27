---
title: 파트너에서 센서 데이터 가져오기
description: 파트너에서 센서 데이터를 가져오는 방법을 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b985dfc1f16372c3fad1b0a5c0894931b4c15dcc
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406498"
---
# <a name="get-sensor-data-from-sensor-partners"></a>센서 파트너에서 센서 데이터 가져오기

Azure FarmBeats를 사용 하면 IoT 장치 및 센서의 스트리밍 데이터를 데이터 허브로 가져올 수 있습니다. 현재 지원 되는 센서 장치 파트너는 다음과 같습니다.

  ![프로젝트 팜 비트](./media/get-sensor-data-from-sensor-partner/partner-information-1.png)

장치 데이터를 Azure FarmBeats와 통합 하면 팜에 배포 된 IoT 센서에서 데이터 허브로의 그라운드 데이터를 가져올 수 있습니다. FarmBeats Accelerator를 통해 사용 가능한 데이터를 시각화할 수 있으며 FarmBeats를 사용 하 여 데이터 fusion 및 AI/ML 모델 빌드에 사용할 수 있습니다.

센서 데이터 스트리밍을 시작 하려면 다음을 확인 합니다.

-  Azure Marketplace에서 FarmBeats을 설치 했습니다.
-  팜에 설치 하려는 센서 및 장치에 대해 결정 했습니다.
-  토양 습기 센서를 사용 하려는 경우 FarmBeats 토양 습기 센서 배치 맵을 사용 하 여 센서 수에 대 한 권장 사항을 얻고 센서를 정확 하 게 배치 해야 할 수 있습니다. 자세한 내용은 [맵 생성](generate-maps.md)을 참조 하세요.

- 팜의 장치 파트너에서 장치/센서를 구매 하 고 배포 합니다. 장치 파트너의 솔루션을 통해 센서 데이터에 액세스할 수 있는지 확인 합니다.

### <a name="enable-device-integration-with-farmbeats"></a>FarmBeats와 장치 통합 사용   

센서 데이터의 스트리밍을 시작한 후에는 FarmBeats 시스템으로 데이터를 가져오는 프로세스를 시작할 수 있습니다. FarmBeats에 대 한 통합을 사용 하도록 설정 하려면 장치 공급자에 게 다음 정보를 제공 해야 합니다.  

 - API 엔드포인트  
 - 테넌트 ID  
 - 클라이언트 ID  
 - 클라이언트 암호  
 - EventHub 연결 문자열

위의 정보는 시스템 통합자에 의해 제공 됩니다. 장치 통합을 사용 하도록 설정 하는 동안 문제가 발생 하면 시스템 통합자에 게 문의 하세요.

또는 Azure Cloud Shell에서이 스크립트를 실행 하 여 자격 증명을 생성할 수 있습니다. 아래 단계를 수행하세요.

1. [ZIP 파일](https://aka.ms/farmbeatspartnerscript) 을 다운로드 하 고 로컬 드라이브에 압축을 풉니다. ZIP 파일 내에서 두 개의 파일을 찾을 수 있습니다.
2. https://portal.azure.com/에 로그인 하 여 Cloud Shell를 엽니다 (이 옵션은 포털의 오른쪽 상단 모음에서 사용할 수 있음).  

    ![프로젝트 팜 비트](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 환경이 **PowerShell** 로 설정 되었는지 확인 합니다. 기본적으로 Bash로 설정 됩니다.

    ![프로젝트 팜 비트](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

4. Cloud Shell에서 위의 1 단계에서 설명한 두 파일을 업로드 합니다.

    ![프로젝트 팜 비트](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

5. 파일이 업로드 된 디렉터리로 이동 합니다. 기본적으로 홈 디렉터리 > 사용자 이름으로 업로드 됩니다.
6. 다음 스크립트를 실행합니다.

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```
7. 화면의 지시에 따라 값을 캡처합니다. (API 끝점, 테 넌 트 ID, 클라이언트 ID, 클라이언트 암호 및 EventHub 연결 문자열). EventHub 연결 문자열은 swagger에서 API 응답의 일부로 사용할 수 있습니다.

**생성 된 자격 증명을 사용 하 여 장치 데이터 통합**

이전 섹션에서 생성 한 자격 증명 집합을 사용 하 여 FarmBeats을 연결 하려면 장치 파트너 포털을 방문 하세요.

 - API 엔드포인트  
 - EventHub 연결 문자열  
 - 클라이언트 ID  
 - 클라이언트 암호  
 - 테넌트 ID  

 장치 공급자가 성공적으로 통합 되었는지 확인 합니다. 확인 되 면 Azure FarmBeats에서 모든 장치 및 센서를 볼 수 있습니다.

## <a name="view-devices-and-sensors"></a>장치 및 센서 보기

다음 섹션을 사용 하 여 팜의 장치 및 센서를 확인 합니다.

### <a name="view-devices"></a>디바이스 보기

현재 FarmBeats는 다음 장치를 지원 합니다.

- **노드**: 하나 이상의 센서가 연결 되는 장치입니다.
- **게이트웨이**: 하나 이상의 노드가 연결 된 장치입니다.

다음 단계를 사용합니다.

1. 홈 페이지의 메뉴에서 **장치** 를 선택 합니다.
  장치 페이지에는 장치 유형, 모델, 상태, 배치한 팜 및 메타 데이터에 대 한 마지막 업데이트 날짜가 표시 됩니다. 기본적으로 팜 열은 NULL로 설정 됩니다. 팜에 장치를 할당 하도록 선택할 수 있습니다. 자세한 내용은 [장치 할당](#assign-devices)을 참조 하세요.
2. 장치를 선택 하 여 장치 속성, 원격 분석 및 장치에 연결 된 자식 장치를 확인 합니다.  

    ![프로젝트 팜 비트](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>센서 보기

다음 단계를 사용합니다.

1. 홈 페이지의 메뉴에서 **센서** 를 선택 합니다.
  센서 페이지에는 센서 유형, 연결 된 팜, 부모 장치, 포트 이름, 포트 유형 및 마지막 업데이트 상태에 대 한 세부 정보가 표시 됩니다.
2. 센서 속성, 활성 경고 및 센서의 원격 분석을 보려면 센서를 선택 합니다.

    ![프로젝트 팜 비트](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>장치 할당  

센서 데이터 흐름이 완료 되 면 센서를 배포한 팜에 할당할 수 있습니다.

1. 홈 페이지의 메뉴에서 **팜** 을 선택 합니다. **팜** 목록 페이지가 표시 됩니다.  
2. 장치를 할당 하려는 팜을 선택 하 고 **장치 추가**를 선택 합니다.  
3. **장치 추가** 창이 표시 됩니다. 팜에 할당 하려는 장치를 선택 합니다.

    ![프로젝트 팜 비트](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. **장치 추가**를 선택 합니다. 또는 **장치** 메뉴로 이동 하 여 팜에 할당 하려는 장치를 선택 하 고 **장치 연결**을 선택 합니다.  
5. **장치 연결** 창에서 팜 드롭다운을 선택 하 고 **모두에 적용** 을 선택 하 여 선택한 모든 장치에 팜을 연결 합니다.

    ![프로젝트 팜 비트](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. 각 장치를 다른 팜에 연결 하려면 **팜에 할당** 열에서 드롭다운을 선택 하 고 각 장치 행에 대해 팜을 선택 합니다.  
7. **할당** 을 선택 하 여 장치 할당을 완료 합니다.

### <a name="visualize-sensor-data"></a>센서 데이터 시각화

다음 단계를 사용합니다.

1. 홈 페이지의 메뉴에서 **팜** 을 선택 하 여 **팜** 페이지를 표시 합니다.  
2. 센서 데이터를 보려는 **팜을** 선택 합니다.  
3. **팜** 대시보드에서 원격 분석 데이터를 볼 수 있습니다. 라이브 원격 분석을 보도록 선택 하거나 **사용자 지정 범위** 를 사용 하 여 특정 날짜 범위에서 볼 수 있습니다.

    ![프로젝트 팜 비트](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-sensor"></a>센서 삭제

다음 단계를 수행하세요.

1. 홈 페이지의 메뉴에서 **센서** 를 선택 하 여 **센서** 페이지를 표시 합니다.  
2. 삭제 하려는 장치를 선택 하 고 확인 창에서 **삭제** 를 선택 합니다.

    ![프로젝트 팜 비트](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

센서가 성공적으로 삭제 되었다는 확인 메시지가 표시 됩니다.  

## <a name="delete-devices"></a>디바이스 삭제

다음 단계를 수행하세요.

1. 홈 페이지의 메뉴에서 **장치** 를 선택 하 여 장치 페이지를 표시 합니다.  
2. 삭제 하려는 장치를 선택 하 고 확인 창에서 **삭제** 를 선택 합니다.

    ![프로젝트 팜 비트](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>다음 단계

이제 Azure FarmBeats 인스턴스로 흐르는 센서 데이터가 있습니다. 이제 팜에 대 한 [맵을 생성](generate-maps.md#generate-maps) 하는 방법에 대해 알아봅니다.
