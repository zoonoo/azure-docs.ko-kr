---
title: 자체 호스팅 통합 런타임 자동 업데이트 및 만료 알림
description: 자체 호스팅 통합 런타임 자동 업데이트 및 만료 알림에 대해 알아봅니다.
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 972015f0f42a8a869de0edcc8f0e921ae7278cd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376261"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>자체 호스팅 통합 런타임 자동 업데이트 및 만료 알림

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 자체 호스팅 통합 런타임을 최신 버전으로 자동 업데이트하는 방법과 ADF에서 자체 호스팅 통합 런타임의 버전을 관리하는 방법을 설명합니다.

## <a name="self-hosted-integration-runtime-auto-update"></a>자체 호스팅 통합 런타임 자동 업데이트
일반적으로 자체 호스팅 통합 런타임을 로컬 컴퓨터 또는 Azure VM에 설치하는 경우 자체 호스팅 통합 런타임의 버전을 관리하는 두 가지 옵션인 자동 업데이트 또는 수동 관리가 있습니다. 일반적으로 ADF는 새로운 기능 릴리스, 버그 수정 또는 향상된 기능을 포함하여 매월 자체 호스팅 통합 런타임의 두 가지 새 버전을 릴리스합니다. 따라서 최신 기능 및 향상된 기능을 사용하려면 최신 버전으로 업데이트하는 것이 좋습니다.

가장 편리한 방법은 자체 호스팅 통합 런타임을 만들거나 편집할 때 자동 업데이트를 사용하도록 설정하는 것입니다. 그러면 자동으로 최신 버전으로 업데이트됩니다. 원하는 시간 슬롯에 업데이트를 예약할 수도 있습니다.

![자동 업데이트 사용](media/create-self-hosted-integration-runtime/shir-auto-update.png)

자체 호스팅 통합 런타임 클라이언트에서 마지막 업데이트 날짜/시간을 확인할 수 있습니다.

![업데이트 시간을 확인하는 스크린샷](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> 자체 호스팅 통합 런타임의 안정성을 보장하기 위해 매달 새 버전이 두 번 릴리스되더라도 한 번만 자동으로 업데이트됩니다. 따라서 경우에 따라 자동 업데이트 버전이 실제 최신 버전보다 이전 버전인 것을 확인할 수 있습니다. 최신 버전을 가져오려면 [다운로드 센터](https://www.microsoft.com/download/details.aspx?id=39717)로 이동하면 됩니다.

## <a name="self-hosted-integration-runtime-expire-notification"></a>자체 호스팅 통합 런타임 만료 알림
자체 호스팅 통합 런타임의 버전을 수동으로 제어하려는 경우 자동 업데이트 설정을 해제하고 수동으로 설치할 수 있습니다. 자체 호스팅 통합 런타임의 각 버전은 1년 후에 만료됩니다. 만료 메시지는 ADF 포털 및 자체 호스팅 통합 런타임 클라이언트에 만료 **90일** 전부터 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory의 통합 런타임 개념](./concepts-integration-runtime.md)을 검토합니다.

- [Azure Portal에서 자체 호스팅 통합 런타임을 만드는](./create-self-hosted-integration-runtime.md) 방법을 알아봅니다.
