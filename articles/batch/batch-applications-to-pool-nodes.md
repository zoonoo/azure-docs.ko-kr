---
title: 응용 프로그램 및 데이터를 풀 노드에 복사
description: 응용 프로그램 및 데이터를 풀 노드에 복사하는 방법을 알아봅니다.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.openlocfilehash: 226a0d69ac387142ecf580537e35f8754ac848a6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385585"
---
# <a name="copying-applications-and-data-to-pool-nodes"></a>응용 프로그램 및 데이터를 풀 노드에 복사

Azure Batch는 데이터 및 응용 프로그램을 작업에서 사용할 수 있도록 계산 노드에 데이터 및 응용 프로그램을 가져오는 여러 가지 방법을 지원합니다. 전체 작업을 실행하려면 데이터와 응용 프로그램이 필요할 수 있으므로 모든 노드에 설치해야 합니다. 일부는 특정 작업에만 필요하거나 작업에 설치해야 하지만 모든 노드에 있을 필요는 없습니다. 일괄 처리에는 이러한 각 시나리오에 대한 도구가 있습니다.

- **풀 시작 작업 리소스 파일**: 풀의 모든 노드에 설치해야 하는 응용 프로그램 또는 데이터의 경우. 설치 명령을 수행하려면 응용 프로그램 패키지 또는 시작 작업의 리소스 파일 모음과 함께 이 메서드를 사용합니다.  

예제: 
- 시작 작업 명령줄을 사용하여 응용 프로그램 이동 또는 설치

- Azure 저장소 계정의 특정 파일 또는 컨테이너 목록을 지정합니다. 자세한 내용은 [REST 설명서의 #resourcefile 추가를](https://docs.microsoft.com/rest/api/batchservice/pool/add#resourcefile) 참조하십시오.

- 풀에서 실행되는 모든 작업은 MyApplication.msi에 먼저 설치해야 하는 MyApplication.exe를 실행합니다. 이 메커니즘을 사용하는 경우 시작 작업의 성공 속성 **대기를** **true로**설정해야 합니다. 자세한 내용은 REST [설명서의 add#starttask를](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)참조하십시오.

- 풀에서 **응용 프로그램 패키지 참조:** 풀의 모든 노드에 설치해야 하는 응용 프로그램 또는 데이터의 경우 응용 프로그램 패키지와 연결된 설치 명령은 없지만 시작 작업을 사용하여 설치 명령을 실행할 수 있습니다. 응용 프로그램에 설치가 필요하지 않거나 많은 수의 파일로 구성된 경우 이 방법을 사용할 수 있습니다. 응용 프로그램 패키지는 많은 수의 파일 참조를 작은 페이로드에 결합하기 때문에 많은 수의 파일에 적합합니다. 100개 이상의 개별 리소스 파일을 하나의 작업에 포함시키려는 경우 Batch 서비스는 단일 작업에 대한 내부 시스템 제한에 대해 발생할 수 있습니다. 또한 동일한 응용 프로그램의 여러 버전이 있고 응용 프로그램 중에서 선택해야 하는 엄격한 버전 지정 요구 사항이 있는 경우 응용 프로그램 패키지를 사용합니다. 자세한 내용은 응용 프로그램 배포를 읽고 [Batch 응용 프로그램 패키지를 사용하여 노드를 계산합니다.](https://docs.microsoft.com/azure/batch/batch-application-packages)

- **작업 준비 작업 리소스 파일**: 작업을 실행하기 위해 설치해야 하지만 전체 풀에 설치할 필요가 없는 응용 프로그램 또는 데이터의 경우 예를 들어, 풀에 다양한 유형의 작업이 있고 하나의 작업 유형만 MyApplication.msi를 실행해야 하는 경우 설치 단계를 작업 준비 작업에 넣는 것이 좋습니다. 작업 준비 작업에 대한 자세한 내용은 [Batch 계산 노드에서 작업 준비 및 작업 릴리스 작업 실행을](https://azure.microsoft.com/documentation/articles/batch-job-prep-release/)참조하십시오.

- **작업 리소스 파일**: 응용 프로그램 이나 데이터가 개별 작업에만 관련 된 경우. 예를 들어 다섯 개의 작업이 있고 각 태스크가 서로 다른 파일을 처리한 다음 출력을 Blob 저장소에 씁니다.  이 경우 각 작업에고유한 입력 파일이 있으므로 **태스크 리소스 파일** 컬렉션에 입력 파일을 지정해야 합니다.

## <a name="determine-the-scope-required-of-a-file"></a>파일에 필요한 범위 결정

풀, 작업 또는 작업에 필요한 파일인 파일의 범위를 결정해야 합니다. 풀로 범위가 조정된 파일은 풀 응용 프로그램 패키지 또는 시작 작업을 사용해야 합니다. 작업 범위가 조정된 파일은 작업 준비 작업을 사용해야 합니다. 풀 또는 작업 수준에서 범위가 조정된 파일의 좋은 예는 응용 프로그램입니다. 작업 범위가 조정된 파일은 작업 리소스 파일을 사용해야 합니다.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Batch 계산 노드에 데이터를 가져와야 하는 다른 방법

Batch REST API에 공식적으로 통합되지 않은 Batch 계산 노드에 데이터를 가져와도 다른 방법이 있습니다. Azure Batch 노드를 제어할 수 있고 사용자 지정 실행 을 실행할 수 있으므로 Batch 노드가 대상에 연결되어 있고 해당 원본에 대한 자격 증명이 Azure Batch 노드에 있는 한 사용자 지정 원본의 수에 관계없이 데이터를 가져올 수 있습니다. 몇 가지 일반적인 예는 다음과 같습니다.

- SQL에서 데이터 다운로드
- 다른 웹 서비스/사용자 지정 위치에서 데이터 다운로드
- 네트워크 공유 매핑

### <a name="azure-storage"></a>Azure Storage

Blob 저장소에는 다운로드 확장성 목표가 있습니다. Azure 저장소 파일 공유 확장성 대상은 단일 Blob과 동일합니다. 크기는 필요한 노드 및 풀 수에 영향을 미칩니다.

