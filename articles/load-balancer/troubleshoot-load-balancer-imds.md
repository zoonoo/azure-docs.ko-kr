---
title: Azure Instance Metadata Service (IMDS)에 대 한 일반적인 오류 코드
titleSuffix: Azure Load Balancer
description: Azure Instance Metadata Service (IMDS)에 대 한 일반적인 오류 코드 및 해당 완화 방법의 개요
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: e648d71ed3c501c07a2491054b273a13d74cedaf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417628"
---
# <a name="error-codes-common-error-codes-when-using-imds-to-retrieve-load-balancer-information"></a>오류 코드: IMDS를 사용 하 여 부하 분산 장치 정보를 검색할 때 발생 하는 일반적인 오류 코드

이 문서에서는 Azure Instance Metadata Service (IMDS)를 사용 하는 동안 일반적인 배포 오류와 이러한 오류를 해결 하는 방법을 설명 합니다.

## <a name="error-codes"></a>오류 코드

| 오류 코드 | 오류 메시지 | 세부 정보 및 완화 방법 |
| --- | ---------- | ----------------- |
| 400 | 필수 매개 변수 ""이 (가) 없습니다 \<ParameterName> . 요청을 수정 하 고 다시 시도 하세요. | 오류 코드는 누락 된 매개 변수를 나타냅니다. </br> 누락 된 매개 변수를 추가 하는 방법에 대 한 자세한 내용은 [Azure Instance Metadata Service (IMDS)를 사용 하 여 부하 분산 장치 메타 데이터를 검색 하는 방법](howto-load-balancer-imds.md#sample-request-and-response)
| 400 | 매개 변수 값이 허용 되지 않거나 매개 변수 " \<ParameterValue> ParameterName"에 대해 매개 변수 값 ""이 허용 되지 않습니다. 요청을 수정 하 고 다시 시도 하세요. | 오류 코드는 요청 형식이 올바르게 구성 되지 않았음을 나타냅니다. </br> 자세한 내용은 [Azure Instance Metadata Service (IMDS)를 사용 하 여 부하 분산 장치 메타 데이터를 검색](howto-load-balancer-imds.md#sample-request-and-response) 하 여 요청 본문을 수정 하 고 다시 시도를 실행 하는 방법을 참조 하세요. |
| 400 | 예기치 않은 요청입니다. 쿼리 매개 변수를 확인 하 고 다시 시도 하세요. | 오류 코드는 요청 형식이 올바르게 구성 되지 않았음을 나타냅니다. </br> 자세한 내용은 [Azure Instance Metadata Service (IMDS)를 사용 하 여 부하 분산 장치 메타 데이터를 검색](howto-load-balancer-imds.md#sample-request-and-response) 하 여 요청 본문을 수정 하 고 다시 시도를 실행 하는 방법을 참조 하세요. |
| 404 | 부하 분산 장치 메타 데이터를 찾을 수 없습니다. VM에서 기본이 아닌 SKU 부하 분산 장치를 사용 하 고 있는지 확인 하 고 나중에 다시 시도 하세요. | 오류 코드는 가상 머신이 부하 분산 장치에 연결 되어 있지 않거나 부하 분산 장치가 표준 대신 기본 SKU 임을 나타냅니다. </br> 자세한 내용은 빠른 시작: 표준 부하 분산 장치를 배포 하기 위해 [Azure Portal를 사용 하 여 vm 부하를 분산 하는 공용 부하 분산 장치 만들기](quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard) 를 참조 하세요.|
| 404 | API를 찾을 수 없음: Path = " \<UrlPath> ", 메서드 = " \<Method> " | 오류 코드는 경로의 잘못 된 구성을 나타냅니다. </br> 자세한 내용은 [Azure Instance Metadata Service (IMDS)를 사용 하 여 부하 분산 장치 메타 데이터를 검색](howto-load-balancer-imds.md#sample-request-and-response) 하 여 요청 본문을 수정 하 고 다시 시도를 실행 하는 방법을 참조 하세요.|
| 405 | Http 메서드가 허용 되지 않음: 경로 = " \<UrlPath> ", 메서드 = " \<Method> " | 오류 코드는 지원 되지 않는 HTTP 동사를 나타냅니다. </br> 자세한 내용은 지원 되는 동사의 경우 [Azure Instance Metadata Service (IMDS)](/virtual-machines/windows/instance-metadata-service?tabs=windows.md#http-verbs) 를 참조 하세요. |
| 429 | 너무 많은 요청 | 오류 코드는 rate 제한을 나타냅니다. </br> 요금 제한에 대 한 자세한 내용은 [Azure Instance Metadata Service (IMDS)](/virtual-machines/windows/instance-metadata-service?tabs=windows#rate-limiting)를 참조 하세요.|
| 400 | 요청 본문이 MaxBodyLength 보다 큽니다. | 오류 코드는 MaxBodyLength 보다 큰 요청을 나타냅니다. </br> 본문 길이에 대 한 자세한 내용은 [Azure Instance Metadata Service (IMDS)를 사용 하 여 부하 분산 장치 메타 데이터를 검색 하는 방법](howto-load-balancer-imds.md#sample-request-and-response)을 참조 하세요.|
| 400 | 매개 변수 키 길이가 MaxParameterKeyLength 보다 큽니다. | 오류 코드는 MaxParameterKeyLength 보다 큰 매개 변수 키 길이를 나타냅니다. </br> 본문 길이에 대 한 자세한 내용은 [Azure Instance Metadata Service (IMDS)를 사용 하 여 부하 분산 장치 메타 데이터를 검색 하는 방법](howto-load-balancer-imds.md#sample-request-and-response)을 참조 하세요. |
| 400 | 매개 변수 값 길이가 MaxParameterValueLength 보다 큽니다. | 오류 코드는 MaxParameterValueLength 보다 큰 매개 변수 키 길이를 나타냅니다. </br> 값 길이에 대 한 자세한 내용은 [Azure Instance Metadata Service (IMDS)를 사용 하 여 부하 분산 장치 메타 데이터를 검색 하는 방법](howto-load-balancer-imds.md#sample-request-and-response)을 참조 하세요.|
| 400 | 매개 변수 헤더 값 길이가 MaxHeaderValueLength: ... 보다 큽니다. | 오류 코드는 MaxHeaderValueLength 보다 큰 매개 변수 헤더 값의 길이를 나타냅니다. </br> 값 길이에 대 한 자세한 내용은 [Azure Instance Metadata Service (IMDS)를 사용 하 여 부하 분산 장치 메타 데이터를 검색 하는 방법](howto-load-balancer-imds.md#sample-request-and-response)을 참조 하세요.|
| 404 | 지금은 Load Balancer metadata API를 사용할 수 없습니다. 나중에 다시 시도 하세요. | 오류 코드는 API를 프로 비전 할 수 있음을 나타냅니다. 나중에 요청을 시도 하세요. |
| 404 | /metadata/loadbalancer를 현재 사용할 수 없습니다. | 오류 코드는 API가 활성화 진행 중임을 나타냅니다. 나중에 요청을 시도 하세요. |
| 503 | 내부 서비스를 사용할 수 없습니다. 나중에 다시 시도 하세요.  | 오류 코드는 API를 일시적으로 사용할 수 없음을 나타냅니다. 나중에 요청을 시도 하세요. |
|  |  |

## <a name="next-steps"></a>다음 단계

[Azure Instance Metadata Service](/virtual-machines/windows/instance-metadata-service.md) 에 대 한 자세한 정보

