---
title: Azure Stream Analytics의 데이터 보호
description: 이 문서에서는 Azure Stream Analytics 작업에서 사용하는 개인 데이터를 암호화하는 방법을 설명합니다.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 102b03ad4fe247ae0abc4e2312d7027c6170333f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019468"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Azure Stream Analytics의 데이터 보호 

Azure Stream Analytics은 실시간 분석 파이프라인을 빌드할 수 있게 해 주는 완전히 관리되는 플랫폼 서비스입니다. 클러스터 프로비저닝, 사용을 수용할 수 있는 노드 확장, 내부 체크포인트 관리 등 모든 힘든 작업이 백그라운드에서 관리됩니다.

## <a name="private-data-assets-that-are-stored"></a>저장된 프라이빗 데이터 자산

Azure Stream Analytics는 실행하기 위해 다음 메타데이터와 데이터를 유지합니다. 

* 쿼리 정의 및 관련 구성  

* 사용자 정의 함수 또는 집계  

* Stream Analytics 런타임에 필요한 검사점

* 참조 데이터의 스냅샷 

* Stream Analytics 작업에서 사용하는 리소스의 연결 정보

산업 또는 환경에서 규정 준수 의무를 충족하는 데 도움이 되도록 [Microsoft의 규정 준수 제품](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)에 대해 자세히 알아볼 수 있습니다. 

## <a name="in-region-data-residency"></a>지역 내 데이터 보존
Azure Stream Analytics은 위에 설명된 고객 데이터 및 기타 메타데이터를 저장합니다. Azure Stream Analytics는 고객 데이터를 기본적으로 단일 영역에 저장하므로 이 서비스는 [Trust Center](https://azuredatacentermap.azurewebsites.net/)에 지정된 데이터를 포함하여 지역 데이터 보존 요구 사항을 자동으로 충족합니다.
또한, 사용자가 선택한 스토리지 계정으로 암호화하여 단일 지역에 stream analytics 작업과 관련된 모든 데이터 자산 (고객 데이터 및 기타 메타데이터)을 저장하도록 선택할 수 있습니다.

## <a name="encrypt-your-data"></a>데이터 암호화

Stream Analytics는 인프라 전반에 걸쳐 동급 최고의 암호화 표준을 자동으로 채택하여 데이터를 암호화하고 보호합니다. 인프라 관리에 대해 걱정할 필요가 없도록 모든 데이터를 안전하게 저장하는 Stream Analytics를 신뢰하기만 하면 됩니다.

고객 관리 키를 사용하여 데이터를 암호화하려는 경우 고유한 스토리지 계정 (범용 V1 또는 V2)을 사용하여 Stream Analytics 런타임에 필요한 개인 데이터 자산을 저장할 수 있습니다. 필요에 따라 스토리지 계정을 암호화할 수 있습니다. Stream Analytics 인프라에서 영구적으로 저장되는 개인 데이터 자산은 없습니다. 

이 설정은 Stream Analytics 작업을 만들 때 구성해야 하며, 작업의 수명 주기 전체에서 수정할 수 없습니다. Stream Analytics에서 사용되는 스토리지를 수정하거나 삭제하지 않는 것이 좋습니다. 스토리지 계정을 삭제하면 모든 프라이빗 데이터 자산이 영구적으로 삭제되므로 작업이 실패하게 됩니다. 

Stream Analytics 포털을 사용하여 키를 스토리지 계정으로 업데이트하거나 순환시킬 수 없습니다. REST API를 사용하여 키를 업데이트할 수 있습니다.


### <a name="configure-storage-account-for-private-data"></a>프라이빗 데이터에 대한 스토리지 계정 구성 

스토리지 계정을 암호화하여 모든 데이터를 보호하고, 프라이빗 데이터의 위치를 명시적으로 선택합니다. 

산업 또는 환경에서 규정 준수 의무를 충족하는 데 도움이 되도록 [Microsoft의 규정 준수 제품](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)에 대해 자세히 알아볼 수 있습니다. 

다음 단계를 사용하여 프라이빗 데이터 자산에 대한 스토리지 계정을 구성합니다. 이 구성은 스토리지 계정이 아닌 Stream Analytics 작업에서 이루어집니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다. 

1. 결과 목록에서  **Analytics** > **Stream Analytics 작업** 을 선택합니다. 

1. 이름, 지역, 크기 등의 필요한 세부 정보를 사용하여 Stream Analytics 작업 페이지를 채웁니다. 

1. *내 스토리지 계정에서 이 작업에 필요한 모든 프라이빗 데이터 자산의 보안을 유지* 라는 확인란을 선택합니다.

1. 구독에서 스토리지 계정을 선택합니다. 이 설정은 작업의 수명 주기 전체에서 수정할 수 없습니다. 또한 작업을 만든 후에는 이 옵션을 추가할 수 없습니다.

1. 연결 문자열을 사용하여 인증하려면 인증 모드 드롭다운에서 **연결 문자열** 을 선택합니다. 스토리지 계정 키는 구독에서 자동으로 채워집니다.

   ![프라이빗 데이터 스토리지 계정 설정](./media/data-protection/storage-account-create.png)

1. 관리 ID (프리뷰)를 사용하여 인증하려면 인증 모드 드롭다운에서 **관리 ID** 를 선택합니다. 관리 ID를 선택하는 경우 *스토리지 Blob 데이터 참가자* 역할을 사용하여 스토리지 계정의 액세스 제어 목록에 Stream Analytics 작업을 추가해야 합니다. 작업에 대한 액세스 권한을 부여하지 않으면 작업에서 실행할 수 없습니다. 액세스 권한을 부여하는 방법에 대한 자세한 내용은 [Azure RBAC를 사용하여 다른 리소스에 관리 ID 액세스 할당](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md#use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource)을 참조하세요.

   :::image type="content" source="media/data-protection/storage-account-create-msi.png" alt-text="관리 ID 인증을 사용하는 프라이빗 데이터 스토리지 계정 설정":::

## <a name="private-data-assets-that-are-stored-by-stream-analytics"></a>Stream Analytics에 의해 저장되는 프라이빗 데이터 자산

Stream Analytics에서 유지해야 하는 모든 프라이빗 데이터는 스토리지 계정에 저장됩니다. 프라이빗 데이터 자산의 예는 다음과 같습니다. 

* 작성한 쿼리와 관련된 구성  

* 사용자 정의 함수 

* Stream Analytics 런타임에 필요한 검사점

* 참조 데이터의 스냅샷 

Stream Analytics 작업에서 사용되는 리소스의 연결 세부 정보도 저장됩니다. 모든 데이터를 보호하기 위해 스토리지 계정을 암호화합니다. 

산업 또는 환경에서 규정 준수 의무를 충족하는 데 도움이 되도록 [Microsoft의 규정 준수 제품](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)에 대해 자세히 알아볼 수 있습니다. 

## <a name="enables-data-residency"></a>데이터 보존 사용 
이 기능을 사용하여 스토리지 계정을 적절하게 제공함으로써 발생할 수 있는 데이터 보존 요구 사항을 적용할 수 있습니다.

## <a name="known-issues"></a>알려진 문제
관리 ID를 사용하여 입력 또는 출력을 인증할 때 고객 관리 키를 사용하는 작업이 실패하게 되는 알려진 문제가 있습니다. 

## <a name="next-steps"></a>다음 단계

* [Azure Storage 계정 만들기](../storage/common/storage-account-create.md)
* [Azure Stream Analytics의 입력 이해](stream-analytics-add-inputs.md)
* [Azure Stream Analytics 작업의 검사점 및 재생 개념](stream-analytics-concepts-checkpoint-replay.md)
* [Stream Analytics에서 조회에 대한 참조 데이터 사용](stream-analytics-use-reference-data.md)
