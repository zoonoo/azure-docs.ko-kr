---
title: 파트너로부터 센서 데이터 받기
description: 이 문서에서는 파트너로부터 센서 데이터를 얻는 방법에 대해 설명합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 916c828365c8f9f50f408bd6c51182bb6e89605f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384197"
---
# <a name="get-sensor-data-from-sensor-partners"></a>센서 파트너로부터 센서 데이터 얻기

Azure FarmBeats를 사용하면 IoT 장치 및 센서의 스트리밍 데이터를 Datahub로 가져올 수 있습니다. 현재 다음 센서 장치 파트너가 지원됩니다.

  ![팜비츠 파트너](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

장치 데이터를 Azure FarmBeats와 통합하면 팜에 배포된 IoT 센서에서 데이터 허브로 지상 데이터를 얻을 수 있습니다. 사용 가능한 데이터는 FarmBeats 가속기를 통해 시각화할 수 있습니다. 이 데이터는 FarmBeats를 사용하여 데이터 융합 및 기계 학습/인공 지능(ML/AI) 모델 구축에 사용할 수 있습니다.

센서 데이터 스트리밍을 시작하려면 다음을 확인하십시오.

-  Azure 마켓플레이스에 FarmBeats를 설치했습니다.
-  팜에 설치하려는 센서와 장치를 결정했습니다.
-  토양 수분 센서를 사용하려는 경우 FarmBeats 토양 수분 센서 배치 맵을 사용하여 센서 수와 위치를 정확히 확인할 수 있습니다. 자세한 내용은 [맵 생성을](generate-maps-in-azure-farmbeats.md)참조하십시오.
- 팜에 있는 장치 파트너의 장치 또는 센서를 구입하고 배포합니다. 장치 파트너의 솔루션을 통해 센서 데이터에 액세스할 수 있는지 확인합니다.

## <a name="enable-device-integration-with-farmbeats"></a>FarmBeats와 장치 통합 활성화

센서 데이터 스트리밍을 시작한 후 FarmBeats 시스템에 데이터를 가져오는 프로세스를 시작할 수 있습니다. FarmBeats에 통합을 활성화하려면 장치 공급자에게 다음 정보를 제공합니다.

 - API 엔드포인트
 - 테넌트 ID
 - 클라이언트 ID
 - 클라이언트 암호
 - EventHub 연결 문자열

다음 단계에 따라 위의 정보를 생성할 수 있습니다.(FarmBeats가 배포된 Azure 구독에 액세스해야 하므로 이러한 단계는 Azure에서 수행해야 합니다.)

1. [https://manage.visualstudio.com](https://portal.azure.com/) 에 로그인합니다.

2. **FarmBeats 버전 1.2.7 이상에 있는 경우 단계 2a, 2b 및 2c를 건너뛰고 3단계로 이동하십시오.**. . FarmBeats UI의 오른쪽 상단에 있는 설정 아이콘을 클릭하여 FarmBeats 버전을 확인할 수 있습니다.

2a. Azure Active Directory -> 앱 등록으로 이동

2b. FarmBeats 배포의 일부로 만든 앱 등록을 클릭합니다. FarmBeats 데이터 허브와 이름이 동일합니다.

2c. "API 노출" -> 클릭 "클라이언트 응용 프로그램 추가" 클릭 하고 **04bb07795-8ddb-461a-bbee-02f9e1bf7b46를** 입력 하 고 "범위 승인"을 확인 합니다. 이렇게 하면 아래 단계를 수행하기 위해 Azure CLI(클라우드 셸)에 액세스할 수 있습니다.

3. Cloud Shell을 엽니다. 이 옵션은 Azure 포털의 오른쪽 위 모서리에 있는 도구 모음에서 사용할 수 있습니다.

    ![Azure 포털 도구 모음](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

5. 환경이 **PowerShell으로**설정되어 있는지 확인합니다. 기본적으로 Bash로 설정됩니다.

    ![파워쉘 툴바 설정](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

6. 홈 디렉토리로 이동합니다.

   ```azurepowershell-interactive 

    cd  

    ```

7. 다음 명령을 실행합니다. 이렇게 하면 홈 디렉터리로 스크립트가 다운로드됩니다.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1 

    ```

8. 다음 스크립트를 실행합니다. 스크립트는 Azure Active Directory -> 개요 페이지에서 가져올 수 있는 테넌트 ID를 요청합니다.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

9. 화면의 지침에 따라 API **끝점,** **테넌트 ID,** **클라이언트 ID,** **클라이언트 보안**및 EventHub 연결 문자열에 대한 값을 **캡처합니다.**

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>생성된 자격 증명을 사용하여 장치 데이터 통합

이제 이전 섹션에서 생성된 다음 정보가 있습니다.
 - API 엔드포인트
 - EventHub 연결 문자열
 - 클라이언트 ID
 - 클라이언트 암호
 - 테넌트 ID
 
FarmBeats를 연결하기 위해 장치 파트너에게 이 정보를 제공해야 합니다. 동일한 작업을 수행하려면 장치 파트너 포털로 이동합니다. 예를 들어, 데이비스 인스트루먼트, 기말교 또는 페슬 인스트루먼트(Metos.at)의 장치를 사용하는 경우 아래에 언급된 해당 페이지로 이동하십시오.

[데이비스 인스트루먼트](https://weatherlink.github.io/azure-farmbeats/setup)

[기형적 인](https://app.teralytic.com/)

[페슬 인스트루먼트](https://ng.fieldclimate.com/user-api-services)

 장치 공급자가 성공적인 통합을 확인합니다. 확인 후 Azure FarmBeats의 모든 장치 및 센서를 볼 수 있습니다.

## <a name="view-devices-and-sensors"></a>장치 및 센서 보기

다음 섹션을 사용하여 팜의 장치 및 센서를 봅니다.

### <a name="view-devices"></a>디바이스 보기

현재 FarmBeats는 다음 장치를 지원합니다.

- **노드**: 하나 이상의 센서가 연결된 장치입니다.
- **게이트웨이**: 하나 이상의 노드가 연결된 장치입니다.

다음 단계를 수행합니다.

1. 홈 페이지에서 메뉴에서 **장치를** 선택합니다.
  **장치** 페이지에는 장치 유형, 모델, 상태, 팜에 배치된 팜 및 메타데이터의 마지막 업데이트 날짜가 표시됩니다. 기본적으로 팜 열은 *NULL로*설정됩니다. 팜에 장치를 할당하도록 선택할 수 있습니다. 자세한 내용은 [장치 할당](#assign-devices)을 참조하십시오.
2. 장치에 연결된 장치 속성, 원격 분석 및 자식 장치를 보려면 장치를 선택합니다.

    ![디바이스 페이지](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>센서 보기

다음 단계를 수행합니다.

1. 홈 페이지에서 메뉴에서 **센서를** 선택합니다.
  **센서** 페이지에는 센서 유형, 연결된 팜, 상위 장치, 포트 이름, 포트 유형 및 마지막으로 업데이트된 상태에 대한 세부 정보가 표시됩니다.
2. 센서를 선택하여 센서 속성, 활성 경고 및 원격 분석을 센서에서 봅니다.

    ![센서 페이지](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>장치 할당  

센서 데이터가 유입되면 센서를 배포한 팜에 할당할 수 있습니다.

1. 홈 페이지에서 메뉴에서 **팜을** 선택합니다. **농장** 목록 페이지가 나타납니다.
2. 장치를 할당할 팜을 선택하고 **장치 추가를**선택합니다.
3. **장치 추가** 창이 나타납니다. 팜에 할당할 장치를 선택합니다.

    ![장치 추가 창](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. **장치 추가를**선택합니다. 또는 **장치** 메뉴로 이동하여 팜에 할당할 장치를 선택하고 **장치 연결**을 선택합니다.
5. 장치 **연결** 창에서 드롭다운 목록에서 팜을 선택하고 **모두에 적용을** 선택하여 팜을 선택한 모든 장치에 연결합니다.

    ![장치 연결 창](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. 각 장치를 다른 팜에 연결하려면 **Farm에 할당** 열에서 드롭다운 화살표를 선택하고 각 장치 행에 대한 팜을 선택합니다.
7. 장치 할당을 완료하려면 **할당을** 선택합니다.

### <a name="visualize-sensor-data"></a>센서 데이터 시각화

다음 단계를 수행합니다.

1. 홈 페이지에서 **메뉴에서 팜을** 선택하여 **팜** 페이지를 봅니다.
2. 센서 데이터를 보려는 **팜을** 선택합니다.
3. **팜** 대시보드에서 원격 분석 데이터를 볼 수 있습니다. 라이브 원격 분석을 보거나 **사용자 지정 범위를** 사용하여 특정 날짜 범위를 볼 수 있습니다.

    ![팜 대시보드](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>센서 삭제

다음 단계를 수행합니다.

1. 홈 페이지에서 메뉴에서 **센서를** 선택하여 **센서** 페이지를 봅니다.
2. 삭제할 장치를 선택하고 확인 창에서 **삭제를** 선택합니다.

    ![삭제 단추](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

확인 메시지는 센서가 성공적으로 삭제됨을 보여줍니다.

## <a name="delete-devices"></a>디바이스 삭제

다음 단계를 수행합니다.

1. 홈 페이지에서 메뉴에서 **장치를** 선택하여 **장치** 페이지를 봅니다.
2. 삭제할 장치를 선택하고 확인 창에서 **삭제를** 선택합니다.

    ![삭제 단추](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>다음 단계

이제 Azure FarmBeats 인스턴스로 센서 데이터가 유입됩니다. 이제 팜에 대한 [맵을 생성하는](generate-maps-in-azure-farmbeats.md#generate-maps) 방법을 알아봅니다.
