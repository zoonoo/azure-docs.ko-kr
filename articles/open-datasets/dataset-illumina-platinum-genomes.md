---
title: Illumina Platinum Genomes
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 Illumina Platinum Genomes 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 0a1b9d5a41a0dcc45bea0456c2c3714b787a5af8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284248"
---
# <a name="illumina-platinum-genomes"></a>Illumina Platinum Genomes

전 세계 연구자는 전체 게놈 시퀀싱을 통해 인간 게놈을 더 완전하고 정확하게 특성화할 수 있습니다. 이렇게 하려면 게놈 세트에 높은 신뢰도의 변이가 포함된 포괄적인 게놈 전체 카탈로그가 벤치마크로 필요합니다. Illumina에서는 3세대 가계도의 개인 17명에 대한 깊이 있는 전체 게놈 시퀀스 데이터를 생성했습니다. Illumina에서는 현재 사용할 수 있는 다양한 알고리즘을 사용하여 각 게놈의 변이를 추출했습니다.

데이터에 대한 자세한 내용은 공식 [Illumina 사이트](https://www.illumina.com/platinumgenomes.html)를 참조하세요.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>데이터 원본

이 데이터 세트는 ftp://ussd-ftp.illumina.com/의 미러입니다.

## <a name="data-volumes-and-update-frequency"></a>데이터 볼륨 및 업데이트 빈도

이 데이터 세트는 약 2GB의 데이터를 포함하며 매일 업데이트됩니다.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 서부 2 및 미국 중서부 Azure 지역에 저장됩니다. 선호도를 위해 미국 서부 2 또는 미국 중서부에서 컴퓨팅 리소스를 찾는 것이 좋습니다.

## <a name="data-access"></a>데이터 액세스

미국 서부 2: 'https://datasetplatinumgenomes.blob.core.windows.net/dataset '

미국 중서부: 'https://datasetplatinumgenomes-secondary.blob.core.windows.net/dataset '

[SAS 토큰](../storage/common/storage-sas-overview.md): sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=FFfZ0QaDcnEPQmWsshtpoYOjbzd4jtwIWeK%2Fc4i9MqM%3D

## <a name="use-terms"></a>사용 약관

데이터는 제한 없이 사용할 수 있습니다. 자세한 내용과 인용 세부 정보는 [공식 Illumina 사이트](https://www.illumina.com/platinumgenomes.html)를 참조하세요.

## <a name="contact"></a>연락처

이 데이터 세트에 대한 질문이나 피드백은 platinumgenomes@illumina.com에 문의하세요.

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-platinum-genomes -->

> [!TIP]
> **[대신 데이터 세트를 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-platinum-genomes)** .

## <a name="getting-the-illumina-platinum-genomes-from-azure-open-datasets-and-doing-initial-analysis"></a>Azure Open Datasets에서 Illumina Platinum Genomes 가져오기 및 초기 분석 수행 

Jupyter Notebook, GATK 및 Picard를 사용하여 다음을 수행합니다.

1. VariantFiltration를 사용하여 유전자형에 주석 달기
2. 특정 변이 선택
3. 관련 변이 필터링 - 추출 없음 또는 특정 지역
4. 일치 분석 수행
5. 최종 VCF 파일을 테이블로 변환 

**종속성:**

이 Notebook에는 다음 라이브러리가 필요합니다.

- Azure Storage `pip install azure-storage-blob`

- numpy `pip install numpy`

- GATK(Genome Analysis Toolkit)(*사용자는 이 Notebook을 사용하여 Broad Institute 웹 페이지의 GATK를 동일한 컴퓨팅 환경으로 다운로드해야 함: https://github.com/broadinstitute/gatk/releases* )

**중요 정보: 이 Notebook은 Python 3.6 커널을 사용하고 있습니다.**

## <a name="getting-the-genomics-data-from-azure-open-datasets"></a>Azure Open Datasets에서 유전체학 데이터 가져오기

여러 가지 퍼블릭 유전체학 데이터가 Azure Open Datasets로 [여기](https://azure.microsoft.com/services/open-datasets/catalog/)에 업로드되었습니다. 이 개방형 데이터 세트에 연결된 Blob 서비스를 만듭니다. 아래에서 `Illumina Platinum Genomes` 데이터 세트에 대한 Azure Open Dataset의 데이터 호출 프로시저 예제를 찾을 수 있습니다.

### <a name="downloading-the-specific-illumina-platinum-genomes"></a>특정 'Illumina Platinum Genomes' 다운로드

```python
import os
import uuid
import sys
from azure.storage.blob import BlockBlobService, PublicAccess

blob_service_client = BlockBlobService(account_name='datasetplatinumgenomes', sas_token='sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=FFfZ0QaDcnEPQmWsshtpoYOjbzd4jtwIWeK%2Fc4i9MqM%3D')     
blob_service_client.get_blob_to_path('dataset/2017-1.0/hg38/small_variants/NA12877', 'NA12877.vcf.gz', './NA12877.vcf.gz')
```

### <a name="1-annotate-genotypes-using-variantfiltration"></a>1. VariantFiltration를 사용하여 genotypes에 주석 달기

**중요 정보: GATK가 시스템에서 실행되고 있는지 확인합니다.**

이형접합 유전자형을 필터링하려는 경우 VariantFiltration의 `--genotype-filter-expression isHet == 1` 옵션을 사용합니다. `--genotype-filter-name` 옵션을 사용하여 도구에 대한 주석 값을 지정하는 방식으로 이형접합 유전자형에 레이블을 지정할 수 있습니다. 여기서 이 매개 변수의 값은 `isHetFilter`로 설정됩니다. 첫 번째 예제에서는 Illimina Platinum Genomes의 `NA12877.vcf.gz`를 사용했지만 사용자는 다른 데이터 세트 `Platinum Genomes`의 vcf 파일을 사용할 수 있습니다.

```python
run gatk VariantFiltration -V NA12877.vcf.gz -O outputannot.vcf --genotype-filter-expression "isHet == 1" --genotype-filter-name "isHetFilter"
```

### <a name="2-select-specific-variants"></a>2. 특정 변이 선택

VCF 파일에서 변이 하위 집합을 선택합니다. 이 도구를 사용하면 특정 분석을 용이하게 하기 위해 다양한 조건에 따라 변이 하위 집합을 선택할 수 있습니다. 이러한 분석의 예로는 환자 및 대조군의 비교와 대조, 특정 요구 사항을 충족하는 변이 또는 비변이 추출 또는 일부 예기치 않은 결과에 대한 문제 해결 등이 있습니다.

더 큰 추출 집합에서 변이 하위 집합을 선택하기 위한 다양한 옵션이 있습니다.

전체 샘플 이름 또는 패턴 일치를 기준으로 추출 집합에서 하나 이상의 샘플을 추출합니다.
주석 값에 대한 임계값을 설정하는 조건을 포함하도록 지정합니다. **"DP > 1000"(시퀀싱 정도가 1000x보다 큼), "AF < 0.25"(대립유전자 빈도가 0.25보다 작은 부위)를 예로 들 수 있습니다.** 이러한 조건은 "JEXL 식"으로 작성되며 관련 정보는 JEXL 식 사용에 대한 문서에 설명되어 있습니다.
지정된 다른 추출 집합에도 있는 변이를 포함하거나 제외하기 위해 일치 또는 불일치 트랙을 제공합니다.
해당 유형(예: INDEL만 해당), 멘델 법칙 위반 증거, 필터링 상태, 대립유전자 등의 조건에 따라 변이를 선택합니다. 또한 특정 주석의 원래 값을 기록하는 여러 옵션도 사용할 수 있습니다. 이러한 값은 새 추출 집합을 부분으로 나누거나, 대립유전자를 자르는 경우 다시 계산됩니다.

입력: 하위 집합을 선택할 수 있는 VCF 형식의 변이 추출 집합입니다.

출력: 선택한 변이 하위 집합을 포함하는 새 VCF 파일입니다.

```python
run gatk SelectVariants -R Homo_sapiens_assembly38.fasta -V outputannot.vcf --select-type-to-include SNP --select-type-to-include INDEL -O selective.vcf
```

### <a name="3-transform-filtered-genotypes-to-no-call"></a>3. 필터링된 유전자형을 추출 없음으로 변환 

--set-filtered-gt-to-nocall을 사용하여 SelectVariants를 실행하면 null 유전자형 추출이 플래그로 지정된 유전자형이 추가로 변환됩니다. 

이 변환은 다운스트림 도구에서 FORMAT-level 필터 필드를 구문 분석하지 않기 때문에 필요합니다.

**'No call'** 이 있는 변이를 필터링하는 방법

```python
run gatk SelectVariants -V outputannot.vcf --set-filtered-gt-to-nocall -O outputnocall.vcf
```

### <a name="4-check-the-concordance-of-vcf-file-with-ground-truth"></a>4. 실측 자료로 VCF 파일의 일치 확인

실제 VCF를 기준으로 입력 VCF의 사이트 수준 일치를 평가합니다.
이 도구는 두 변이 추출 집합을 서로 상대적으로 평가하고 6열 요약 메트릭 테이블을 생성합니다. 

**이 함수는 다음을 수행합니다.**

1. SNP 및 INDEL 추출 계층화
2. 참-긍정, 거짓-긍정 및 거짓-부정 추출 보고
3. 민감도 및 정밀도 계산

이 도구는 실제 VCF의 모든 레코드가 실제 변이를 전달한다고 가정합니다. 평가 VCF의 경우 이 도구는 필터링되지 않은 전달 추출만 사용합니다.

필요에 따라 다음과 같은 변이 레코드의 VCF를 생성하고 각 변이의 일치 상태를 주석으로 표시하도록 도구를 설정할 수 있습니다.

참 긍정 및 거짓 부정(즉, 실제 VCF의 모든 변이): 민감도를 계산하는 데 유용합니다.

참 긍정 및 거짓 긍정(즉, 평가 VCF의 모든 변이): 아티팩트의 기계 학습 분류자를 위한 학습 데이터 집합을 가져오는 데 유용합니다.

**R 또는 Python의 통계 분석용 TSV 파일을 생성하기 위해 이러한 출력 VCF를 VariantsToTable에 전달할 수 있습니다.**

```python
 run gatk Concordance -R Homo_sapiens_assembly38.fasta -eval outputannot.vcf --truth outputnocall.vcf  --summary summary.tsv 
```

### <a name="5-variantstotable"></a>5. VariantsToTable

VCF 파일의 필드를 탭으로 구분된 테이블로 추출합니다. 이 도구는 VCF 파일의 각 변이에 대한 지정된 필드를 탭으로 구분된 테이블로 추출합니다. 이러한 테이블이 VCF보다 작업하기 더 쉬울 수 있습니다. 기본적으로 이 도구는 VCF 파일의 PASS 또는 (필터링되지 않은) 변이만 추출합니다. 필터링된 변이는 --show-filtered 플래그를 추가하여 출력에 포함할 수 있습니다. 이 도구는 INFO(즉, 사이트 수준) 필드와 FORMAT(즉, 샘플 수준) 필드를 모두 추출할 수 있습니다.

INFO/사이트 수준 필드:

`-F` 인수를 사용하여 INFO 필드를 추출합니다. 각 필드는 출력 파일에서 단일 열을 차지합니다. 이 필드는 표준 VCF 열(예: CHROM, ID, MOAL) 또는 INFO 필드의 주석 이름(예: AC, AF)일 수 있습니다. 또한 이 도구는 다음 필드를 지원합니다.

EVENTLENGTH(이벤트 길이) TRANSITION(이중대립유전자 전이(SNP)의 경우 1, 이중대립유전자 변이(SNP)의 경우 0, INDEL 및 다중대립유전자의 경우 -1) HET(이형접합 유전자형 수) HOM-REF(동형접합 참조 유전자형 수) HOM-VAR(동형접합 변이 유전자형 수) NO-CALL(추출 없음 유전자형 수) TYPE(변이 유형, 가능한 값: NO_VARIATION, SNP, MNP, INDEL, SYMBOLIC, MIXED) VAR(비참조 유전자형 수) NSAMPLES(샘플 수) NCALLED(추출된 샘플 수) MULTI-ALLELIC(이 변형이 다중대립유전자형인지 여부? true/false)

FORMAT/샘플 수준 필드:

`-GF` 인수를 사용하여 FORMAT/샘플 수준 필드를 추출합니다. 이 도구는 이름이 샘플별로 "SAMPLE_NAME.FORMAT_FIELD_NAME"인 새 열을 만듭니다(예: NA12877.GQ, NA12878.GQ).

입력:

테이블로 변환할 VCF 파일

출력:

VCF 파일의 요청된 필드 값을 포함하는 탭으로 구분된 파일입니다.

```python
run gatk VariantsToTable -V NA12877.vcf.gz -F CHROM -F POS -F TYPE -F AC -F AD -F AF -GF DP -GF AD -O outputtable.table
```

## <a name="references"></a>참조 항목

1. VariantFiltration: https://gatk.broadinstitute.org/hc/en-us/articles/360036827111-VariantFiltration 
2. 변이 선택: https://gatk.broadinstitute.org/hc/en-us/articles/360037052272-SelectVariants
3. 일치: https://gatk.broadinstitute.org/hc/en-us/articles/360041851651-Concordance
4. 변이 테이블: https://gatk.broadinstitute.org/hc/en-us/articles/360036882811-VariantsToTable 
5. Illumina Platinum Genomes: https://www.illumina.com/platinumgenomes.html 

<!-- nbend -->

---

## <a name="next-steps"></a>다음 단계

[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.