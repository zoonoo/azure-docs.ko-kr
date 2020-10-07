---
title: Azure FarmBeats란?
description: Azure FarmBeats에 대한 개요를 제공합니다.
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 12bb0b0098b5108bf780b88fc42b86861ea6fcdc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "87439564"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Azure FarmBeats 개요(미리 보기)

Azure FarmBeats는 Azure Marketplace에서 사용할 수 있는 B2B(Business-to-Business) 제품입니다. 여러 공급자의 농업 데이터 세트를 집계할 수 있습니다. Azure FarmBeats를 통해 융합된 데이터 세트를 기반으로 AI(인공 지능) 또는 ML(기계 학습) 모델을 구축할 수 있습니다. Azure FarmBeats를 사용하면 농업 비즈니스가 미분화된 데이터 엔지니어링 대신 핵심 부가 가치에 중점을 둘 수 있습니다.

> [!NOTE]
> Azure FarmBeats는 현재 퍼블릭 미리 보기로 제공됩니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. Azure FarmBeats는 서비스 수준 계약 없이 제공됩니다. 지원을 받으려면 [Azure FarmBeats 지원 포럼](https://aka.ms/farmbeatssupport)을 사용하세요.

![Farm Beats 프로젝트](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Azure FarmBeats 미리 보기를 통해 다음을 수행할 수 있습니다.

- 위성 이미지를 기준으로 하는 식생 지수 및 급수 지수를 사용하여 농장 상태를 평가합니다.
- 사용할 토양 수분 센서의 개수와 배치할 위치에 대한 권장 사항을 확인합니다.
- 다양한 공급업체의 센서를 통해 수집된 토양 데이터를 시각화하여 농장 상태를 추적합니다.
- 위성 및 센서의 융합 데이터를 기준으로 토양 수분 지도를 가져옵니다.
- 집계된 데이터 세트를 토대로 AI/ML 모델을 빌드하여 실행 가능한 인사이트를 얻습니다.
- 농장 상태 권장 지침을 제공하여 디지털 농업 솔루션을 빌드하거나 보강합니다.

## <a name="datahub"></a>Datahub

Azure FarmBeats Datahub는 여러 공급자의 다양한 농업 데이터 세트를 집계, 정규화 및 컨텍스트화할 수 있는 API 계층입니다. Azure FarmBeats를 사용하여 다음을 얻을 수 있습니다.
- [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), [Pessl Instruments](https://metos.at/)라는 센서 공급자 두 곳의 **센서 데이터**
- 유럽 우주국 [Sentinel-2](https://sentinel.esa.int/web/sentinel/home) 위성 임무의 **위성 이미지**
- [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/) , [DJI](https://dji.com/)라는 이미지 공급자 세 곳의 **드론 이미지**

Datahub는 확장 가능한 API 플랫폼으로 설계되었습니다. Azure FarmBeats와 통합하기 위해 더 많은 공급자와 협력하고 있으므로 솔루션을 빌드하는 동안 더 많은 선택권을 가질 수 있습니다.

## <a name="accelerator"></a>액셀러레이터

Azure FarmBeats 액셀러레이터는 Datahub를 기반으로 구축된 샘플 웹 애플리케이션입니다. 액셀러레이터를 통해 사용자 인터페이스와 모델 개발을 신속하게 시작할 수 있습니다. Azure FarmBeats 액셀러레이터는 Azure FarmBeats의 API를 사용합니다. 수집된 센서 데이터를 차트로 시각화하고 모델 출력을 맵으로 시각화합니다. 예를 들어 액셀러레이터를 사용하여 농장을 신속하게 만들고 이 농장에 대한 식생 지수 맵 또는 센서 배치 맵을 쉽게 얻을 수 있습니다.

## <a name="role-based-access-control-rbac"></a>RBAC(역할 기반 Access Control)

관리자는 미리 정의된 역할 중 하나를 사용하여 Azure FarmBeats에 대한 액세스 규칙을 정의할 수 있습니다. 역할은 사용자가 액세스할 수 있는 애플리케이션의 영역과 수행할 수 있는 작업을 결정합니다. Azure FarmBeats에는 사용자와 파트너를 위한 두 가지 종류의 역할이 있습니다.

### <a name="user-roles"></a>사용자 역할

[관리자는 사용자를 추가 및 관리](manage-users-in-azure-farmbeats.md)하고 두 개의 사용자 역할을 기반으로 액세스 수준을 정의할 수 있습니다. 관리 및 읽기 전용입니다.

### <a name="partner-roles"></a>파트너 역할

관리자는 Azure FarmBeats에 데이터 공급자로 여러 파트너를 추가할 수 있습니다. 다음은 FarmBeats에서 사용 가능한 파트너 역할 및 해당 권한을 요약한 것입니다.

| 파트너 유형    |   동작  | 범위 |
| ---- | -------- | -------- |
| 센서 파트너  |   만들기, 읽기, 업데이트 <br/> <br/> 읽기, 업데이트 | DeviceModel, 디바이스, SensorModel, 센서 <br/> <br/> ExtendedType |
| 이미지 파트너  |   만들기, 읽기, 업데이트 <br/> <br/> 읽기, 업데이트 <br/> <br/> 읽기 | 장면, SceneFile <br/> <br/> ExtendedType <br/> <br/> 팜 |
| 날씨 파트너* <br/> <br/>  (* 서비스 예정) |   만들기, 읽기, 업데이트 <br/> <br/> 읽기, 업데이트 <br/> <br/> 읽기 | WeatherDataModel, WeatherDataLocation, JobType <br/> <br/> ExtendedType <br/> <br/> 팜 |

## <a name="resources"></a>리소스

Azure FarmBeats는 추가 요금 없이 제공되며, 사용하는 Azure 리소스에 대해서만 요금을 지불합니다. 다음 리소스를 사용하여 제품에 대한 자세한 정보를 알아볼 수 있습니다.

- [Azure FarmBeats 블로그](https://aka.ms/farmbeatsblog)를 방문하면 최신 Azure FarmBeats 뉴스에 대한 정보를 얻을 수 있습니다.
- [Azure FarmBeats 지원 포럼](https://aka.ms/farmbeatssupport)에 질문을 게시하면 도움을 받을 수 있습니다.
- [Azure FarmBeats 피드백 포럼](https://aka.ms/farmbeatsfeedback)에 기능 아이디어를 게시하거나 투표하여 피드백을 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure FarmBeats 설치](install-azure-farmbeats.md)
