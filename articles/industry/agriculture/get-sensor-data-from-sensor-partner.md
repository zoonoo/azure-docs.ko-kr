---
title: 파트너로부터 센서 데이터 얻기
description: 이 문서에서는 파트너로부터 센서 데이터를 얻는 방법을 설명합니다.
author: RiyazPishori
ms.topic: article
ms.date: 11/04/2019
ms.author: riyazp
ms.openlocfilehash: 3f0077a1868aaf03bf5f6ed1febd4ba5734c6806
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108751286"
---
# <a name="get-sensor-data-from-sensor-partners"></a>센서 파트너로부터 센서 데이터 얻기

Azure FarmBeats를 사용하면 스트리밍 데이터를 IoT 디바이스 및 센서에서 데이터 허브로 가져올 수 있습니다. 현재 지원되는 센서 디바이스 파트너는 다음과 같습니다.

  ![FarmBeats 파트너](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

디바이스 데이터를 Azure FarmBeats와 통합하면 토양 데이터를 팜에 배포된 IoT 센서에서 데이터 허브로 가져올 수 있습니다. 사용 가능한 데이터는 FarmBeats 가속기를 통해 시각화할 수 있습니다. 데이터는 FarmBeats를 사용하여 데이터 융합 및 ML/AI(기계 학습/인공 지능) 모델을 빌드하는 데 사용할 수 있습니다.

센서 데이터 스트리밍을 시작하기 전에 다음을 확인합니다.

-  FarmBeats를 Azure Marketplace에 설치했습니다.
-  팜에 설치하려는 센서와 디바이스를 결정했습니다.
-  토양 수분 센서를 사용하려는 경우 FarmBeats 토양 수분 센서 배치 맵을 사용하여 센서 수와 센서를 정확히 배치해야 하는 위치에 대한 권장 사항을 얻습니다. 자세한 내용은 [맵 생성](generate-maps-in-azure-farmbeats.md)을 참조하세요.
- 팜의 디바이스 파트너로부터 디바이스 또는 센서를 구매하고 배포합니다. 디바이스 파트너의 솔루션을 통해 센서 데이터에 액세스할 수 있는지 확인합니다.

## <a name="enable-device-integration-with-farmbeats"></a>FarmBeats와 디바이스 통합 사용

센서 데이터의 스트리밍이 시작되면 데이터를 FarmBeats 시스템으로 가져오는 프로세스를 시작할 수 있습니다. FarmBeats와의 통합을 사용하도록 설정하려면 디바이스 공급자에게 다음 정보를 제공합니다.

 - API 엔드포인트
 - 테넌트 ID
 - 클라이언트 ID
 - 클라이언트 암호
 - EventHub 연결 문자열

아래 단계에 따라 위의 정보를 생성합니다.

> [!NOTE]
> FarmBeats가 배포된 Azure 구독에 액세스하려면 Azure에서 이러한 단계를 완료해야 합니다.

1. [https://manage.visualstudio.com](https://portal.azure.com/ ) 에 로그인합니다.

2. **FarmBeats 버전 1.2.7 이상인 경우 a, b 및 c 단계를 건너뛰고 3단계로 이동합니다.** FarmBeats UI의 오른쪽 위 모서리에서 **설정** 아이콘을 선택하여 FarmBeats 버전을 확인할 수 있습니다.

      a.  **Azure Active Directory** > **앱 등록** 으로 차례로 이동합니다.

      b. FarmBeats 배포의 일환으로 만든 **앱 등록** 을 선택합니다. FarmBeats 데이터 허브와 이름이 같습니다.

      c. **API 표시** > **클라이언트 애플리케이션 추가** 를 차례로 선택하고, **04b07795-8ddb-461a-bbee-02f9e1bf7b46** 을 입력하고, **권한 부여 범위** 를 선택합니다. 그러면 Azure CLI(Cloud Shell)에 액세스하여 아래 단계를 수행할 수 있습니다.

3. Cloud Shell을 엽니다. 이 옵션은 Azure Portal의 오른쪽 위 모서리에 있는 도구 모음에서 사용할 수 있습니다.

    ![Azure Portal 도구 모음](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. 환경이 **PowerShell** 로 설정되어 있는지 확인합니다. 기본적으로 Bash로 설정되어 있습니다.

    ![PowerShell 도구 모음 설정](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. 홈 디렉터리로 이동합니다.

    ```azurepowershell-interactive
    cd
    ```

6. 다음 명령을 실행합니다. 그러면 Azure AD 요청에 사용하도록 인증된 계정이 연결됩니다.

    ```azurepowershell-interactive
    Connect-AzureAD
    ```

7. 다음 명령을 실행합니다. 그러면 스크립트가 홈 디렉터리에 다운로드됩니다.

    ```azurepowershell-interactive

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

8. 다음 스크립트를 실행합니다. 이 스크립트는 **Azure Active Directory** > **개요** 페이지에서 가져올 수 있는 테넌트 ID를 요청합니다.

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1

    ```

> [!NOTE]
> 1. 데이터 허브 API 엔드포인트 이름은 소문자여야 합니다.
> 2. 데이터 허브 API 엔드포인트에 대한 FarmBeats 웹 사이트 이름 URL을 복사하는 경우 후행 슬래시(/)가 없는지 확인합니다.

9. 화면의 지침에 따라 **API 엔드포인트**, **테넌트 ID**, **클라이언트 ID**, **클라이언트 암호** 및 **EventHub 연결 문자열** 에 대한 값을 캡처합니다.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>생성된 자격 증명을 사용하여 디바이스 데이터 통합

이제 이전 섹션에서 생성된 다음 정보가 있습니다.
 - API 엔드포인트
 - EventHub 연결 문자열
 - 클라이언트 ID
 - 클라이언트 암호
 - 테넌트 ID

FarmBeats를 연결하려면 디바이스 파트너에게 이러한 정보를 제공해야 합니다. 동일한 작업을 수행하려면 디바이스 파트너 포털로 이동합니다. 예를 들어 Davis Instruments, Teralytic 또는 Pessl Instruments(Metos.at)의 디바이스를 사용하는 경우 아래에서 설명한 대로 해당 페이지로 이동합니다.

1. [Davis Instruments](https://weatherlink.github.io/azure-farmbeats/setup)

2. [Teralytic](https://app.teralytic.com/)

3. [Pessl Instruments](https://ng.fieldclimate.com/user-api-services)

디바이스 공급자가 성공적인 통합을 확인합니다. 확인되면 Azure FarmBeats에 모든 디바이스 및 센서가 표시됩니다.

## <a name="view-devices-and-sensors"></a>디바이스 및 센서 보기

다음 섹션을 사용하여 팜의 디바이스 및 센서를 확인합니다.

### <a name="view-devices"></a>디바이스 보기

현재 FarmBeats에서 지원하는 디바이스는 다음과 같습니다.

- **노드:** 하나 이상의 센서가 연결된 디바이스입니다.
- **게이트웨이:** 하나 이상의 노드가 연결된 디바이스입니다.

다음 단계를 수행합니다.

1. 홈 페이지의 메뉴에서 **디바이스** 를 선택합니다.
  **디바이스** 페이지에 디바이스 유형, 모델, 상태, 디바이스가 배치된 팜 및 메타데이터의 마지막 업데이트 날짜가 표시됩니다. 기본적으로 팜 열은 *NULL* 로 설정됩니다. 디바이스를 팜에 할당하도록 선택할 수 있습니다. 자세한 내용은 [디바이스 할당](#assign-devices)을 참조하세요.
2. 디바이스를 선택하여 디바이스 속성, 원격 분석 및 디바이스에 연결된 자식 디바이스를 확인합니다.

    ![디바이스 페이지](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>센서 보기

다음 단계를 수행합니다.

1. 홈 페이지의 메뉴에서 **센서** 를 선택합니다.
  **센서** 페이지에 센서 유형, 센서가 연결된 팜, 부모 디바이스, 포트 이름, 포트 유형 및 마지막으로 업데이트한 상태에 대한 세부 정보가 표시됩니다.
2. 센서를 선택하여 센서의 센서 속성, 활성 경고 및 원격 분석을 확인합니다.

    ![센서 페이지](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>디바이스 할당

센서 데이터가 들어오면 센서를 배포한 팜에 할당할 수 있습니다.

1. 홈 페이지의 메뉴에서 **팜** 을 선택합니다. **팜** 목록 페이지가 표시됩니다.
2. 디바이스를 할당하려는 팜을 선택하고, **디바이스 추가** 를 선택합니다.
3. **디바이스 추가** 창이 표시됩니다. 팜에 할당하려는 디바이스를 선택합니다.

    ![디바이스 추가 창](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. **디바이스 추가** 를 선택합니다. 또는 **디바이스** 메뉴로 이동하여 팜에 할당하려는 디바이스를 선택하고, **디바이스 연결** 을 선택합니다.
5. **디바이스 연결** 창의 드롭다운 목록에서 팜을 선택하고, **모든 항목에 적용** 을 선택하여 팜을 선택한 모든 디바이스에 연결합니다.

    ![디바이스 연결 창](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. 각 디바이스를 다른 팜에 연결하려면 **팜에 할당** 열에서 드롭다운 화살표를 선택하고, 각 디바이스 행에 대한 팜을 선택합니다.

7. **할당** 을 선택하여 디바이스 할당을 완료합니다.

### <a name="visualize-sensor-data"></a>센서 데이터 시각화

다음 단계를 수행합니다.

1. 홈 페이지의 메뉴에서 **팜** 을 선택하여 **팜** 페이지를 표시합니다.
2. 센서 데이터를 보려는 **팜** 을 선택합니다.
3. **팜** 대시보드에 원격 분석 데이터가 표시됩니다. 라이브 원격 분석을 보거나 **사용자 지정 범위** 를 사용하여 특정 날짜 범위를 확인할 수 있습니다.

    ![팜 대시보드](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>센서 삭제

다음 단계를 수행합니다.

1. 홈 페이지의 메뉴에서 **센서** 를 선택하여 **센서** 페이지를 표시합니다.
2. 삭제하려는 디바이스를 선택하고, 확인 창에서 **삭제** 를 선택합니다.

    ![센서 삭제 페이지 및 삭제 단추가 강조 표시된 스크린샷](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

확인 메시지에서 센서가 성공적으로 삭제되었음을 보여 줍니다.

## <a name="delete-devices"></a>디바이스 삭제

다음 단계를 수행합니다.

1. 홈 페이지의 메뉴에서 **디바이스** 를 선택하여 **디바이스** 페이지를 표시합니다.
2. 삭제하려는 디바이스를 선택하고, 확인 창에서 **삭제** 를 선택합니다.

    ![삭제 단추](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>다음 단계

이제 Azure FarmBeats 인스턴스로 흐르는 센서 데이터가 있습니다. 이제 팜에 대한 [맵을 생성](generate-maps-in-azure-farmbeats.md#generate-maps)하는 방법을 알아봅니다.
