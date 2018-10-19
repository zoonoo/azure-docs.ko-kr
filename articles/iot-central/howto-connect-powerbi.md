---
title: Power BI 대시보드에서 Azure IoT Central 데이터 시각화 | Microsoft Docs
description: Azure IoT Central Analytics Power BI 솔루션 템플릿을 사용하여 IoT Central 데이터를 시각화하고 분석합니다.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 6f8bed8aba43d77cdfad49f4fe62a4c7aa2c5ce3
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368072"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Power BI 대시보드에서 Azure IoT Central 데이터 시각화 및 분석

*이 항목의 내용은 관리자에게 적용됩니다.*

![Power BI 솔루션 템플릿 파이프라인](media/howto-connect-powerbi/iot-continuous-data-export.png)

Azure IoT Central Analytics Power BI 솔루션 템플릿을 사용하여 IoT 장치의 성능을 모니터링하기 위한 강력한 Power BI 대시보드를 만듭니다. Power BI 대시보드에서 다음을 수행할 수 있습니다.
- 시간이 지남에 따라 장치가 보내는 데이터의 양 추적
- 원격 분석, 상태 및 이벤트 간의 데이터 볼륨 비교
- 많은 측정값을 보고하는 장치 식별
- 장치 측정의 기록 추세 관찰
- 많은 중요한 이벤트를 전송하는 데 문제가 있는 장치 식별

이 솔루션 템플릿은 [연속 데이터 내보내기](howto-export-data.md)에서 Azure Blob 저장소 계정으로 데이터를 가져오는 파이프라인을 설정합니다. 이 데이터는 Azure Functions, Azure Data Factory 및 Azure SQL Database를 거치며 처리 및 변환됩니다. PBIX 파일로 다운로드할 수 있는 Power BI 보고서에서 출력을 시각화하고 분석할 수 있습니다. 이러한 모든 리소스는 필요에 따라 각 구성 요소를 사용자 지정할 수 있도록 Azure 구독에서 생성됩니다. 이 솔루션 템플릿은 완전한 오픈 소스이므로 [Github 리포지토리](https://aka.ms/iotcentralgithubpowerbisolutiontemplate)를 방문하여 아키텍처에 대해 자세히 알아보고 솔루션을 확장할 수 있습니다.

## <a name="get-the-azure-iot-central-analytics-solution-templatehttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Microsoft AppSource에서 [Azure IoT Central Analytics 솔루션 템플릿](https://aka.ms/iotcentralpowerbisolutiontemplate) 받기

## <a name="prerequisites"></a>필수 조건
템플릿을 설정하려면 다음이 필요합니다.
- Azure 구독에 액세스
- IoT Central 앱에서 [연속 데이터 내보내기](howto-export-data.md)를 사용하여 데이터를 내보내야 합니다. Power BI 대시보드를 최대한 활용하려면 측정, 장치 및 장치 템플릿 스트림을 켜는 것이 좋습니다.
- Power BI Desktop(최신 버전)
- Power BI Pro(다른 사용자와 대시보드를 공유하려는 경우)

## <a name="reports"></a>보고서

두 보고서는 자동으로 생성됩니다. 

첫 번째 보고서는 장치에서 보고하는 측정값의 기록 보기를 보여주고, 측정값 및 장치를 다양한 유형으로 세분화하여 가장 높은 측정값을 전송합니다.

![Power BI 보고서 페이지 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

두 번째 보고서는 이벤트를 자세히 설명하고 보고된 오류 및 경고의 기록 보기를 보여줍니다. 또한 특히 오류 이벤트 및 경고 이벤트를 비롯해 가장 많은 이벤트를 모두 보고하는 장치를 보여줍니다.

![Power BI 보고서 페이지 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="resources"></a>리소스

[Azure IoT Central Analytics 솔루션 템플릿](https://aka.ms/iotcentralpowerbisolutiontemplate)을 가져오려면 AppSource를 방문합니다.

아키텍처에 대한 자세히 알아보고 솔루션을 확장하려면 [Github 리포지토리](https://aka.ms/iotcentralgithubpowerbisolutiontemplate)를 방문합니다.

## <a name="next-steps"></a>다음 단계

이제 Power BI에서 데이터를 시각화하는 방법을 파악했으므로 다음과 같이 다음 단계를 제안합니다.

> [!div class="nextstepaction"]
> [장치를 관리하는 방법](howto-manage-devices.md)
