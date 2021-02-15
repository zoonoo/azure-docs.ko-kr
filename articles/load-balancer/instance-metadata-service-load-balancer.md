---
title: Azure Instance Metadata Service을 사용 하 여 부하 분산 장치 정보 검색
titleSuffix: Azure Load Balancer
description: Azure Instance Metadata Service를 사용 하 여 부하 분산 장치 정보를 검색 하는 방법을 알아봅니다.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 0d7d08eb5e77e744fb6ce0abefc550bc79de4c8c
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519085"
---
# <a name="retrieve-load-balancer-information-by-using-the-azure-instance-metadata-service"></a>Azure Instance Metadata Service을 사용 하 여 부하 분산 장치 정보 검색

IMDS (Azure Instance Metadata Service)는 현재 실행 중인 가상 머신 인스턴스에 대 한 정보를 제공 합니다. 서비스는 잘 알려진 169.254.169.254 (라우팅할 수 없는 IP)에서 사용할 수 있는 REST API입니다. 

Azure 표준 Load Balancer 뒤에 가상 컴퓨터 또는 가상 컴퓨터 집합 인스턴스를 추가 하는 경우 IMDS를 사용 하 여 부하 분산 장치 및 인스턴스와 관련 된 메타 데이터를 검색 합니다.

메타 데이터에는 가상 머신 또는 가상 머신 확장 집합에 대 한 다음 정보가 포함 됩니다.

* 표준 SKU 공용 IP입니다.
* 네트워크 인터페이스의 각 개인 IP에 대 한 부하 분산 장치의 인바운드 규칙 구성입니다.
* 네트워크 인터페이스의 각 개인 IP에 대 한 부하 분산 장치의 아웃 바운드 규칙 구성입니다.

## <a name="access-the-load-balancer-metadata-using-the-imds"></a>IMDS를 사용 하 여 부하 분산 장치 메타 데이터에 액세스

부하 분산 장치 메타 데이터에 액세스 하는 방법에 대 한 자세한 내용은 [Azure Instance Metadata Service를 사용 하 여 부하 분산 장치 정보에 액세스](howto-load-balancer-imds.md)를 참조 하세요.

## <a name="troubleshoot-common-error-codes"></a>일반적인 오류 코드 문제 해결

일반적인 오류 코드 및 해당 완화 방법에 대 한 자세한 내용은 [IMDS를 사용할 때 일반적인 오류 코드 문제 해결](troubleshoot-load-balancer-imds.md)을 참조 하세요. 

## <a name="support"></a>지원

여러 번 시도한 후 메타 데이터 응답을 검색할 수 없는 경우 Azure Portal에서 지원 문제를 만듭니다.

## <a name="next-steps"></a>다음 단계
[Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md) 에 대 한 자세한 정보

[표준 부하 분산 장치 배포](quickstart-load-balancer-standard-public-portal.md)

