---
title: ENCODE
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 ENCODE 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 921f04b4f874fd83403a613823df0f2bd600feb8
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039196"
---
# <a name="encode-encyclopedia-of-dna-elements"></a>ENCODE: Encyclopedia of DNA Elements

[ENCODE(Encyclopedia of DNA Elements) 컨소시엄](https://www.encodeproject.org/help/project-overview/)은 NHGRI(National Human Genome Research Institute)에서 자금을 제공한 연구 그룹의 진행 중인 국제 협업입니다. ENCODE의 목표는 단백질 및 RNA 수준에서 작용하는 요소를 비롯한 인간 게놈의 기능 요소와 유전자가 활성화된 환경과 세포를 제어하는 통제 요소의 포괄적인 부분 목록을 작성하는 것입니다.

ENCODE 조사관들은 다양한 분석 및 방법을 사용하여 기능 요소를 확인합니다. 유전자 요소의 발견 및 주석 달기는 주로 다양한 범위의 RNA 원본 유전자 배열 순서 밝히기, 비교 유전체학, 통합적 생물정보 기법, 사람의 큐레이션으로 이루어집니다. 통제 요소는 일반적으로 DNA 과민증 분석, DNA 메틸화 분석, DNA 및 RNA와 상호 작용하는 단백질의 IP(면역 침강)(즉, 수정된 히스톤, 전사 인자, 염색질 조정 장치, RNA 결합 단백질)를 통해 조사되며 그 이후에 유전자 배열 순서 밝히기가 진행됩니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>데이터 원본

이 데이터 세트는 https://www.encodeproject.org/ 에 있는 데이터 저장소의 미러입니다.

## <a name="data-volumes-and-update-frequency"></a>데이터 볼륨 및 업데이트 빈도

이 데이터 세트는 약 756TB의 데이터를 포함하며 한 달에 한 번 매월 첫째 주에 업데이트됩니다.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 서부 2 및 미국 중서부 Azure 지역에 저장됩니다. 선호도를 위해 미국 서부 2 또는 미국 중서부에서 컴퓨팅 리소스를 찾는 것이 좋습니다.

## <a name="data-access"></a>데이터 액세스

미국 서부 2: https://datasetencode.blob.core.windows.net/dataset

미국 중서부: https://datasetencode-secondary.blob.core.windows.net/dataset

[SAS 토큰](/azure/storage/common/storage-sas-overview): ?sv=2019-10-10&si=prod&sr=c&sig=9qSQZo4ggrCNpybBExU8SypuUZV33igI11xw0P7rB3c%3D

## <a name="use-terms"></a>사용 약관

외부 데이터 사용자는 형식 또는 크기에 관계없이 모든 ENCODE 데이터를 기반으로 한 결과를 제한 없이 자유롭게 다운로드, 분석 및 게시할 수 있으며, 개별 회원으로서 또는 컨소시엄의 일부로서 ENCODE 데이터 생산자에 대한 유예 기간을 포함하지 않습니다. 게시되지 않은 ENCODE 데이터를 사용하는 연구원은 데이터 생산자에게 연락하여 가능한 게시물에 대해 논의하는 것이 좋습니다. 이 컨소시엄은 자체적인 분석 작업의 결과를 독립적인 게시물로 계속 게시합니다.

ENCODE에서는 게시물 및 프레젠테이션에 ENCODE 데이터 세트(게시된 경우 또는 게시되지 않은 경우 모두 포함)를 사용하는 연구원은 [https://www.encodeproject.org/help/citing-encode/](https://www.encodeproject.org/help/citing-encode/)에 보고된 다음과 같은 모든 방식으로 ENCODE 컨소시엄을 인용하도록 요청합니다.

## <a name="contact"></a>연락처

궁금하거나 우려되는 점 또는 의견이 있는 경우 지원 센터(encode-help@lists.stanford.edu)로 메일을 보내세요.

## <a name="next-steps"></a>다음 단계

[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.