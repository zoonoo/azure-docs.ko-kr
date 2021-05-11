---
title: Azure Instance Metadata Service(IMDS)에 대한 일반적인 오류 코드
titleSuffix: Azure Load Balancer
description: Azure Instance Metadata Service(IMDS)에 대한 일반적인 오류 코드 및 해당 완화 방법의 개요
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: e932e211996a05b2740613381735a7de3492e5bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100519187"
---
# <a name="error-codes-common-error-codes-when-using-imds-to-retrieve-load-balancer-information"></a>오류 코드: IMDS를 사용하여 부하 분산 장치 정보를 검색할 때 발생하는 일반적인 오류 코드

이 문서에서는 Azure Instance Metadata Service(IMDS)를 사용하는 동안 일반적으로 발생하는 배포 오류 및 해당 오류를 해결하는 방법을 설명합니다.

## <a name="error-codes"></a>오류 코드

| 오류 코드 | 오류 메시지 | 세부 정보 및 완화 방법 |
| --- | ---------- | ----------------- |
| 400 | 필수 매개 변수 '\<ParameterName>' 누락. 요청을 수정한 다음 다시 시도하세요. | 이 오류 코드는 누락된 매개 변수를 나타냅니다. </br> 누락된 매개 변수를 추가하는 방법에 대한 자세한 내용은 [Azure Instance Metadata Service(IMDS)를 사용하여 부하 분산 장치 메타데이터를 검색하는 방법](howto-load-balancer-imds.md#sample-request-and-response)을 참조하세요.
| 400 | 매개 변수 값이 허용되지 않거나 매개 변수 'ParameterName'에 대해 매개 변수 값 '\<ParameterValue>'이 허용되지 않습니다. 요청을 수정한 다음 다시 시도하세요. | 이 오류 코드는 요청 형식이 올바르게 구성되지 않았음을 나타냅니다. </br> [Azure Instance Metadata Service(IMDS)를 사용하여 부하 분산 장치 메타데이터를 검색하는 방법](howto-load-balancer-imds.md#sample-request-and-response)에서 자세한 내용을 참조하여 요청 본문을 수정하고 다시 시도를 실행하세요. |
| 400 | 예기치 않은 요청. 쿼리 매개 변수를 확인하고 다시 시도하세요. | 이 오류 코드는 요청 형식이 올바르게 구성되지 않았음을 나타냅니다. </br> [Azure Instance Metadata Service(IMDS)를 사용하여 부하 분산 장치 메타데이터를 검색하는 방법](howto-load-balancer-imds.md#sample-request-and-response)에서 자세한 내용을 참조하여 요청 본문을 수정하고 다시 시도를 실행하세요. |
| 404 | 부하 분산 장치 메타데이터를 찾을 수 없습니다. VM에서 기본이 아닌 SKU 부하 분산 장치를 사용하고 있는지 확인하고 나중에 다시 시도하세요. | 이 오류 코드는 가상 머신이 부하 분산 장치에 연결되어 있지 않거나 부하 분산 장치가 표준이 아닌 기본 SKU임을 나타냅니다. </br> [빠른 시작: Azure Portal을 사용하여 VM 부하를 분산하는 공용 부하 분산 장치 만들기](quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard)에서 자세한 내용을 참조하여 표준 부하 분산 장치를 배포하세요.|
| 404 | API를 찾을 수 없음: 경로 = '\<UrlPath>', 메서드 = '\<Method>' | 이 오류 코드는 잘못된 경로 구성을 나타냅니다. </br> [Azure Instance Metadata Service(IMDS)를 사용하여 부하 분산 장치 메타데이터를 검색하는 방법](howto-load-balancer-imds.md#sample-request-and-response)에서 자세한 내용을 참조하여 요청 본문을 수정하고 다시 시도를 실행하세요.|
| 405 | Http 메서드가 허용되지 않음: 경로 = '\<UrlPath>', 메서드 = '\<Method>' | 이 오류 코드는 지원되지 않는 HTTP 동사를 나타냅니다. </br> 지원되는 동사에 대한 자세한 내용은 [Azure Instance Metadata Service(IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#http-verbs)를 참조하세요. |
| 429 | 너무 많은 요청 | 이 오류 코드는 속도 제한을 나타냅니다. </br> 속도 제한에 대한 자세한 내용은 [Azure Instance Metadata Service(IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#rate-limiting)를 참조하세요.|
| 400 | 요청 본문이 MaxBodyLength보다 큼 | 이 오류 코드는 MaxBodyLength보다 큰 요청을 나타냅니다. </br> 본문 길이에 대한 자세한 내용은 [Azure Instance Metadata Service(IMDS)를 사용하여 부하 분산 장치 메타데이터를 검색하는 방법](howto-load-balancer-imds.md#sample-request-and-response)을 참조하세요.|
| 400 | 매개 변수 키 길이가 MaxParameterKeyLength보다 큼 | 이 오류 코드는 MaxParameterKeyLength보다 큰 매개 변수 키 길이를 나타냅니다. </br> 본문 길이에 대한 자세한 내용은 [Azure Instance Metadata Service(IMDS)를 사용하여 부하 분산 장치 메타데이터를 검색하는 방법](howto-load-balancer-imds.md#sample-request-and-response)을 참조하세요. |
| 400 | 매개 변수 값 길이가 MaxParameterValueLength보다 큼 | 이 오류 코드는 MaxParameterValueLength보다 큰 매개 변수 값 길이를 나타냅니다. </br> 값 길이에 대한 자세한 내용은 [Azure Instance Metadata Service(IMDS)를 사용하여 부하 분산 장치 메타데이터를 검색하는 방법](howto-load-balancer-imds.md#sample-request-and-response)을 참조하세요.|
| 400 | 매개 변수 헤더 값 길이가 MaxHeaderValueLength보다 큼 | 이 오류 코드는 MaxHeaderValueLength보다 큰 매개 변수 헤더 값 길이를 나타냅니다. </br> 값 길이에 대한 자세한 내용은 [Azure Instance Metadata Service(IMDS)를 사용하여 부하 분산 장치 메타데이터를 검색하는 방법](howto-load-balancer-imds.md#sample-request-and-response)을 참조하세요.|
| 404 | 지금은 부하 분산 장치 메타데이터 API를 사용할 수 없습니다. 나중에 다시 시도하세요. | 이 오류 코드는 API를 프로비전할 수 있음을 나타냅니다. 나중에 요청을 시도하세요. |
| 404 | /metadata/loadbalancer는 현재 사용할 수 없음 | 이 오류 코드는 API가 활성화되는 중임을 나타냅니다. 나중에 요청을 시도하세요. |
| 503 | 내부 서비스를 사용할 수 없습니다. 나중에 다시 시도하세요.  | 이 오류 코드는 API를 일시적으로 사용할 수 없음을 나타냅니다. 나중에 요청을 시도하세요. |
|  |  |

## <a name="next-steps"></a>다음 단계

[Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md)에 대해 자세히 알아봅니다.

