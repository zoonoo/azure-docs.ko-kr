---
title: 1000 Genomes
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 1000 Genomes 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 4418d7866f2476d528290f99d396dcf52c47c3ec
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039175"
---
# <a name="1000-genomes"></a>1000 Genomes

1000 Genomes Project는 2008~2015년에 운영된 것으로, 인간 변이와 유전자형 데이터의 최대 퍼블릭 카탈로그를 만들었습니다. 최종 데이터 세트에는 26개 모집단의 개인 2,504명과 8,400만 개의 식별된 변종에 대한 데이터가 포함되어 있습니다. 자세한 내용은 1000 Genome Project 웹 사이트 및 다음 발행물을 참조하세요.

파일럿 분석: 모집단 규모 시퀀싱의 인간 게놈 변이 맵 ‘Nature 467, 1061-1073(2010년 10월 28일)’

1단계 분석: 1,092개 인간 게놈의 유전적 변이 통합 맵 ‘Nature 491, 56-65(2012년 11월 1일)’

3단계 분석: 인간 유전적 변이의 글로벌 참조 ‘Nature 526, 68-74(2015년 10월 1일)’ 및 2,504개 인간 게놈의 구조적 변이 통합 맵 ‘Nature 526, 75-81(2015년 10월 1일)’

데이터 형식에 대한 자세한 내용은 http://www.internationalgenome.org/formats 를 참조하세요.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>데이터 원본

이 데이터 세트는 ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/의 미러입니다.

## <a name="data-volumes-and-update-frequency"></a>데이터 볼륨 및 업데이트 빈도

이 데이터 세트는 약 815TB의 데이터를 포함하며 매일 업데이트됩니다.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 서부 2 및 미국 중서부 Azure 지역에 저장됩니다. 선호도를 위해 미국 서부 2 또는 미국 중서부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="data-access"></a>데이터 액세스

미국 서부 2: https://dataset1000genomes.blob.core.windows.net/dataset

미국 중서부: https://dataset1000genomes-secondary.blob.core.windows.net/dataset

[SAS 토큰](../storage/common/storage-sas-overview.md): sv=2019-10-10&si=prod&sr=c&sig=9nzcxaQn0NprMPlSh4RhFQHcXedLQIcFgbERiooHEqM%3D

## <a name="use-terms"></a>사용 약관

최종 게시 후 1000 Genomes Project의 데이터는 데이터 세트 출처([http://www.internationalgenome.org/data](http://www.internationalgenome.org/data))에서 제공하는 사용 약관에 따라 모든 사람이 엠바고 없이 공개적으로 사용할 수 있습니다. 데이터 사용은 1000 Genome Project의 [FAQ]()에서 제공되는 세부 정보에 따라 인용되어야 합니다.

## <a name="contact"></a>연락처

https://www.internationalgenome.org/contact

## <a name="next-steps"></a>다음 단계

[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.