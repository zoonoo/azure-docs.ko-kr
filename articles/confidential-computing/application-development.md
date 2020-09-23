---
title: Azure 기밀 컴퓨팅 개발 도구
description: 도구 및 라이브러리를 사용 하 여 기밀 컴퓨팅을 위한 응용 프로그램 개발
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 6bb3b8dbc7887419f7901a52b56c25f60c869abb
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997851"
---
# <a name="application-development-on-intel-sgx"></a>Intel SGX에서 응용 프로그램 개발 


기밀 컴퓨팅 인프라에는 특정 도구 및 소프트웨어가 필요 합니다. 이 페이지에서는 Intel SGX에서 실행 되는 Azure 기밀 컴퓨팅 가상 머신에 대 한 응용 프로그램 개발과 관련 된 개념을 구체적으로 설명 합니다. 이 페이지를 읽기 전에 [INTEL SGX virtual machines 및 enclaves의 소개를 참조](confidential-computing-enclaves.md)하세요. 

enclave 및 격리된 환경의 성능을 활용하려면 기밀 컴퓨팅을 지원하는 도구를 사용해야 합니다. enclave 애플리케이션 개발을 지원하는 다양한 도구가 있습니다. 예를 들어 다음과 같은 오픈 소스 프레임워크를 사용할 수 있습니다. 

- [Open Enclave Software Development Kit (OE SDK)](#oe-sdk)
- [CCF(기밀 컨소시엄 프레임워크)](#ccf)

## <a name="overview"></a>개요

enclave를 사용하여 빌드된 애플리케이션은 다음 두 가지 방법으로 분할됩니다.

1. "신뢰할 수 없는" 구성 요소(호스트)
1. "신뢰할 수 있는" 구성 요소(enclave)


![앱 개발](media/application-development/oe-sdk.png)


**호스트**는 신뢰할 수 없는 환경에서 실행되는 enclave 애플리케이션입니다. 호스트에 배포된 enclave 코드는 호스트에서 액세스할 수 없습니다. 

**enclave**는 애플리케이션 코드와 캐시된 데이터/메모리가 실행되는 위치입니다. 비밀 및 중요한 데이터가 보호된 상태로 유지되도록 보안 계산이 enclave에서 수행되어야 합니다. 


애플리케이션을 디자인하는 동안 enclaves에서 실행해야 하는 애플리케이션 부분을 식별하고 결정하는 것이 중요합니다. 신뢰할 수 있는 구성 요소에 배치하도록 선택한 코드는 애플리케이션의 나머지 부분과 격리됩니다. enclave가 초기화되고 코드가 메모리에 로드되면 신뢰할 수 없는 구성 요소에서 해당 코드를 읽거나 변경할 수 없습니다. 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>OE SDK(Open Enclave 소프트웨어 개발 키트) <a id="oe-sdk"></a>

enclave에서 실행되는 코드를 작성하려면 공급자를 통해 지원되는 라이브러리 또는 프레임워크를 사용합니다. [OE SDK(Open Enclave SDK)](https://github.com/openenclave/openenclave)는 다양한 다른 기밀 컴퓨팅 사용 하드웨어를 추상화할 수 있는 오픈 소스 SDK입니다. 

OE SDK는 모든 CSP의 모든 하드웨어에서 단일 추상화 계층으로 빌드됩니다. OE SDK는 Azure 기밀 컴퓨팅 가상 머신에서 사용되어 enclave를 기반으로 하는 애플리케이션을 만들고 실행할 수 있습니다.

## <a name="confidential-consortium-framework-ccf"></a>CCF (기밀 컨소시엄 Framework) <a id="ccf"></a>

[Ccf](https://github.com/Microsoft/CCF) 는 노드의 분산 네트워크 이며 각각 자체 enclaves를 실행 합니다. 신뢰할 수 있는 노드 네트워크를 사용 하면 분산 원장을 실행할 수 있습니다. 원장은 프로토콜에서 사용할 수 있는 안전 하 고 신뢰할 수 있는 구성 요소를 제공 합니다. 

![CCF 노드](media/application-development/ccf.png)

이 오픈 소스 프레임 워크는 블록 체인에 대 한 높은 전체, 세분화 기밀성 및 컨소시엄 거 버 넌 스를 가능 하 게 합니다. TEEs를 사용 하는 각 노드에서 보안 합의 및 트랜잭션 처리를 보장할 수 있습니다.


## <a name="next-steps"></a>다음 단계 
- [기밀 컴퓨팅 DCsv2 시리즈 가상 머신 배포](quick-create-portal.md)
- [OE SDK를 다운로드 하 여 설치 하 고 응용 프로그램 개발 시작](https://github.com/openenclave/openenclave)