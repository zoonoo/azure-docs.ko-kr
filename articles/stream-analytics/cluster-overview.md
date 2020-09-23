---
title: Azure Stream Analytics 클러스터의 개요(미리 보기)
description: Stream Analytics 클러스터의 단일 테넌트 전용 제품에 대해 알아봅니다.
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 613cf7d9b68fe42c26f2c01cb1fb5dd1da1e1fb5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945448"
---
# <a name="overview-of-azure-stream-analytics-cluster-preview"></a>Azure Stream Analytics 클러스터의 개요(미리 보기)

Azure Stream Analytics 클러스터는 복잡하고 까다로운 스트리밍 사용 사례에 사용할 수 있는 단일 테넌트 배포입니다. 전체 규모에서 Stream Analytics 클러스터는 실시간으로 200MB/초 이상을 처리할 수 있습니다. 전용 클러스터에서 실행되는 Stream Analytics 작업은 표준 제품의 모든 기능을 활용할 수 있으며 입력 및 출력에 대한 프라이빗 링크 연결 지원을 포함합니다.

Stream Analytics 클러스터는 클러스터에 할당된 CPU 및 메모리 리소스의 양을 나타내는 SU(스트리밍 단위)로 청구됩니다. 스트리밍 단위는 표준 및 전용 제품에서 동일합니다. 각 클러스터에 36, 72, 108, 144, 180 또는 216개 SU를 구매할 수 있습니다. Stream Analytics 클러스터는 조직에서 스트리밍 플랫폼으로 사용할 수 있으며 다양한 사용 사례에서 작업하는 여러 팀이 공유할 수 있습니다.

## <a name="what-are-stream-analytics-clusters"></a>Stream Analytics 클러스터란?

Stream Analytics 클러스터는 다중 테넌트 환경에서 실행 중인 Stream Analytics 작업을 지원하는 동일한 엔진에 의해 구동됩니다. 단일 테넌트 전용 클러스터에는 다음과 같은 기능이 있습니다.

* 다른 테넌트의 노이즈가 없는 단일 테넌트 호스팅 리소스는 실제로 “격리”되며, 트래픽이 버스트되면 성능이 향상됩니다.

* 시간이 지남에 따라 스트리밍 사용량이 증가할 때 36에서 216 SU로 클러스터를 확장합니다.

* VNet 지원을 통해 Stream Analytics 작업을 프라이빗 엔드포인트를 사용하여 다른 리소스에 안전하게 연결할 수 있습니다.

* 모든 지역에서 C# 사용자 정의 함수 및 사용자 지정 역직렬 변환기를 작성할 수 있습니다.

* 유지 관리 비용이 없어 실시간 분석 솔루션을 빌드하는 데 집중할 수 있습니다.

## <a name="how-to-get-started"></a>시작하는 방법

[Azure Portal](https://aka.ms/asaclustercreateportal)을 통해 [Stream Analytics 클러스터를 만들](create-cluster.md) 수 있습니다. 온보딩에 대한 질문이 있거나 도움이 필요한 경우 [Stream Analytics 팀](mailto:askasa@microsoft.com)에 문의할 수 있습니다.

## <a name="frequently-asked-questions"></a>자주 묻는 질문

### <a name="how-do-i-choose-between-a-stream-analytics-cluster-and-a-stream-analytics-job"></a>Stream Analytics 클러스터와 Stream Analytics 작업 중에서 어떻게 선택하나요?

가장 쉽게 시작하는 방법은 서비스에 익숙해질 수 있도록 Stream Analytics 작업을 만들고 개발하는 것이며 분석 요구 사항을 충족할 수 있는 방법을 확인하는 것입니다.

Stream Analytics 작업만으로는 VNet이 지원되지 않습니다. 사용자의 입력 또는 출력이 방화벽 또는 Azure Virtual Network에서 보호되는 경우 다음과 같은 두 가지 옵션이 있습니다.

* 로컬 머신에서 VNet을 통해 보호되는 입력 및 출력 리소스(예: Azure Event Hubs 또는 Azure SQL Database)에 대한 액세스 권한이 있는 경우 로컬 머신에서 [Visual Studio에 대한 Azure Stream Analytics 도구를 설치](stream-analytics-tools-for-visual-studio-install.md)할 수 있습니다. 비용을 들이지 않고 디바이스에서 [로컬로 Stream Analytics 작업을 개발하고 테스트](stream-analytics-live-data-local-testing.md)할 수 있습니다. 아키텍처에서 Stream Analytics를 사용할 준비가 되면 Stream Analytics 클러스터를 만들고, 프라이빗 엔드포인트를 구성하고, 대규모로 작업을 실행할 수 있습니다.

* Stream Analytics 클러스터를 만들고, 파이프라인에 필요한 프라이빗 엔드포인트를 사용하여 클러스터를 구성하고, 클러스터에서 Stream Analytics 작업을 실행할 수 있습니다.

### <a name="what-performance-can-i-expect"></a>어떤 성능을 예측할 수 있나요?

SU는 표준 및 전용 제품에서 동일합니다. 전체 36 SU 클러스터를 활용하는 단일 작업은 밀리초 대기 시간으로 약 36MB/초 처리량을 달성할 수 있습니다. 정확한 수는 이벤트 형식 및 분석 유형에 따라 달라집니다. Stream Analytics 클러스터는 전용이므로 더 안정적인 성능 보장을 제공합니다. 클러스터에서 실행되는 모든 작업은 사용자에게만 속합니다.

### <a name="can-i-scale-my-cluster"></a>내 클러스터 크기를 조정할 수 있나요?

예. 변경 요청을 충족하기 위해 필요에 따라 [확장하거나 축소](scale-cluster.md)할 수 있도록 클러스터의 용량을 손쉽게 구성할 수 있습니다.

### <a name="can-i-run-my-existing-jobs-on-these-new-clusters-ive-created"></a>내가 만든 새 클러스터에서 기존 작업을 실행할 수 있나요?

예. 기존 작업을 새로 만든 Stream Analytics 클러스터에 연결하고 평소와 같이 실행할 수 있습니다. 기존 Stream Analytics 작업을 처음부터 다시 만들 필요가 없습니다.

### <a name="how-much-will-these-clusters-cost-me"></a>이러한 클러스터의 비용은 얼마인가요?

Stream Analytics 클러스터는 선택한 SU 용량을 기준으로 요금이 청구됩니다. 클러스터는 시간 단위로 청구되며 해당 클러스터에서 실행되는 작업당 추가 요금이 발생하지 않습니다. 프라이빗 엔드포인트 청구 업데이트는 [Private Link 서비스 가격 책정 페이지](https://azure.microsoft.com/pricing/details/private-link/)를 참조하세요.

### <a name="which-inputs-and-outputs-can-i-privately-connect-to-from-my-stream-analytics-cluster"></a>Stream Analytics 클러스터에서 개인적으로 연결할 수 있는 입력 및 출력은 무엇인가요?

Stream Analytics는 다양한 입력 및 출력 형식을 지원합니다. Azure Private Link를 지원하는 이러한 서비스는 개인적으로 작업에 연결할 수 있습니다. 작업에서 입력 및 출력 리소스에 액세스할 수 있도록 클러스터에 [프라이빗 엔드포인트를 만들](private-endpoints.md) 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Stream Analytics 클러스터의 개요를 살펴봤습니다. 다음으로 클러스터를 만들고 Stream Analytics 작업을 실행할 수 있습니다. 

* [Stream Analytics 클러스터 만들기](create-cluster.md)
* [Azure Stream Analytics 클러스터에서 프라이빗 엔드포인트 관리](private-endpoints.md)
* [Stream Analytics 클러스터에서 Stream Analytics 작업 관리](manage-jobs-cluster.md)
* [Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
