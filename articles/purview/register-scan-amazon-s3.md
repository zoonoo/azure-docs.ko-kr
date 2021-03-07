---
title: Amazon S3 버킷을 검색 하는 방법
description: 이 방법 가이드에서는 Amazon S3 버킷을 검색 하는 방법에 대해 자세히 설명 합니다.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/07/2021
ms.custom: references_regions
ms.openlocfilehash: ddd5c5de85da5ae8cec9d24d33dfd2bf035b5b34
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438731"
---
# <a name="azure-purview-connector-for-amazon-s3"></a>Amazon S3 용 Azure 부서의 범위 커넥터

이 방법 가이드에서는 Azure 부서의 범위를 사용 하 여 Amazon S3 standard 버킷에 현재 저장 된 구조화 되지 않은 데이터를 검색 하 고 데이터에 존재 하는 중요 한 정보의 유형을 검색 하는 방법에 대해 설명 합니다. 이 방법 가이드에서는 데이터를 쉽게 보호할 수 있도록 데이터를 저장 하는 Amazon S3 버킷을 식별 하는 방법에 대해서도 설명 합니다.

이 서비스의 경우 부서의 범위를 사용 하 여 부서의 범위 스캐너가 실행 되는 AWS에 대 한 보안 액세스 권한이 있는 Microsoft 계정 제공 합니다. 부서의 범위 스캐너는 Amazon S3 버킷에 대 한이 액세스를 사용 하 여 데이터를 읽은 다음 메타 데이터 및 분류만 포함 하 여 검색 결과를 Azure로 다시 보고 합니다. 부서의 범위 분류 및 레이블 지정 보고서를 사용 하 여 데이터 검색 결과를 분석 하 고 검토할 수 있습니다.

이 방법 가이드에서는 Amazon S3 버킷을 부서의 범위 리소스로 추가 하 고 Amazon S3 데이터에 대 한 검색을 만드는 방법에 대해 알아봅니다.

## <a name="purview-scope-for-amazon-s3"></a>Amazon S3의 부서의 범위 범위

다음 범위는 Amazon S3 버킷을 부서의 범위 데이터 원본으로 등록 하 고 검색 하는 데만 적용 됩니다.

|범위  |설명  |
|---------|---------|
|**데이터 제한**     |    부서의 범위 스캐너 서비스는 현재 테 넌 트 당 최대 100 GB의 데이터에 대해 Amazon S3 버킷 검색을 지원 합니다.     |
|**파일 형식**     | 부서의 범위 스캐너 서비스는 현재 다음과 같은 파일 형식을 지원 합니다. <br><br>avro, .csv, .doc, .docm, .docx,. 점,. json, odp, .xlt, odt,. orc,. parquet, .pdf, .pot, .pppsx, .ppt,. pptm, .pptx, .pv, .pssv, tsv, .txt, .pc, .xls, .xlsb, .xlsm, .xlsx, .xlt, .xml        |
|**지역**     | Amazon S3 서비스의 부서의 범위 커넥터는 현재 **AWS 미국 동부 (Ohio)** 및 **유럽 (프랑크푸르트)** 지역에만 배포 됩니다. <br><br>자세한 내용은 [저장소 및 지역 검색](#storage-and-scanning-regions)을 참조 하세요.   |
|     |         |

자세한 내용은 다음의 문서화 된 부서의 범위 제한을 참조 하세요.

- [Azure 부서의 범위를 사용 하 여 리소스에 대 한 할당량 관리 및 늘리기](how-to-manage-quotas.md)
- [Azure 부서의 범위에서 지원 되는 데이터 원본 및 파일 형식](sources-and-scans.md)
### <a name="storage-and-scanning-regions"></a>저장소 및 스캔 지역

다음 표에서는 데이터가 저장 되는 영역을 Azure 부서의 범위에서 검색할 지역에 매핑합니다.

> [!IMPORTANT]
> 고객은 버킷의 지역에 따라 모든 관련 데이터 전송 요금에 대 한 요금이 청구 됩니다.
>

| 저장소 지역 | 지역 검사 |
| ------------------------------- | ------------------------------------- |
| 미국 동부 (Ohio)                  | 미국 동부 (Ohio)                        |
| 미국 동부 (N. 버지니아           | 미국 동부 (Ohio)                        |
| 미국 서 부 (N. 캘리포니아         | 미국 동부 (Ohio)                        |
| 미국 서부(오리건)                | 미국 동부 (Ohio)                        |
| 아프리카 (카보베르데)              | 유럽 (프랑크푸르트)                    |
| 아시아 태평양 (홍콩)        | 유럽 (프랑크푸르트)                    |
| 아시아 태평양 (뭄바이)           | 유럽 (프랑크푸르트)                    |
| 아시아 태평양 (오사카-로컬)      | 유럽 (프랑크푸르트)                    |
| 아시아 태평양 (서울)            | 유럽 (프랑크푸르트)                    |
| 아시아 태평양(싱가포르)        | 유럽 (프랑크푸르트)                    |
| 아시아 태평양(시드니)           | 유럽 (프랑크푸르트)                    |
| 아시아 태평양(도쿄)            | 유럽 (프랑크푸르트)                    |
| 캐나다 (중부)                | 미국 동부 (Ohio)                        |
| 중국 (베이징)                 | 지원되지 않음                    |
| 중국 (Ningxia)                 | 지원되지 않음                   |
| 유럽 (프랑크푸르트)              | 유럽 (프랑크푸르트)                    |
| 유럽 (아일랜드)                | 유럽 (프랑크푸르트)                    |
| 유럽 (런던)                 | 유럽 (프랑크푸르트)                    |
| 유럽 (Milan)                  | 유럽 (프랑크푸르트)                    |
| 유럽 (파리)                  | 유럽 (프랑크푸르트)                    |
| 유럽 (스톡홀름)              | 유럽 (프랑크푸르트)                    |
| 중동 (바레인)           | 유럽 (프랑크푸르트)                    |
| 남부 아메리카 (상투메 파울로)       | 미국 동부 (Ohio)                        |
| | |
## <a name="prerequisites"></a>필수 구성 요소

Amazon S3 버킷을 부서의 범위 데이터 원본으로 추가 하 고 S3 데이터를 검색 하기 전에 다음 필수 구성 요소를 수행 했는지 확인 합니다.

- Azure 부서의 범위 데이터 원본 관리자 여야 합니다.

- 버킷을 부서의 범위 리소스로 추가 하는 경우 [AWS ARN](#retrieve-your-new-role-arn), [버킷 이름](#retrieve-your-amazon-s3-bucket-name)및 경우에 따라 [AWS 계정 ID](#locate-your-aws-account-id)의 값이 필요 합니다.

### <a name="create-a-purview-account"></a>부서의 범위 계정 만들기

- **부서의 범위 계정이 이미 있는 경우** AWS S3 지원에 필요한 구성을 계속 진행할 수 있습니다. [AWS 버킷에 대 한 부서의 범위 자격 증명 만들기](#create-a-purview-credential-for-your-aws-bucket-scan)를 시작 합니다.

- **부서의 범위 계정을 만들어야** 하는 경우 [Azure 부서의 범위 계정 인스턴스 만들기](create-catalog-portal.md)의 지침을 따르세요. 계정을 만든 후 여기로 돌아와서 구성을 완료 하 고 Amazon s 3 용 부서의 범위 connector 사용을 시작 합니다.

### <a name="create-a-purview-credential-for-your-aws-bucket-scan"></a>AWS 버킷 검색을 위한 부서의 범위 자격 증명 만들기

이 절차에서는 AWS 버킷을 검색할 때 사용할 새 부서의 범위 자격 증명을 만드는 방법에 대해 설명 합니다.

> [!TIP]
> [검색을 구성](#create-a-scan-for-your-amazon-s3-bucket)하는 동안 프로세스 중간에 새 자격 증명을 만들 수도 있습니다. 이 경우 **자격 증명** 필드에서 **새로 만들기** 를 선택 합니다.
>

1. 부서의 범위에서 **관리 센터로** 이동 하 고 **보안 및 액세스** 아래에서 **자격 증명** 을 선택 합니다.

1. **새로** 만들기를 선택 하 고 오른쪽에 표시 되는 **새 자격 증명** 창에서 다음 필드를 사용 하 여 부서의 범위 자격 증명을 만듭니다.

    |필드 |설명  |
    |---------|---------|
    |**이름**     |이 자격 증명에 대 한 의미 있는 이름을 입력 하거나 기본값을 사용 합니다.        |
    |**설명**     |이 자격 증명에 대 한 설명 (선택 사항)을 입력 합니다 (예:). `Used to scan the tutorial S3 buckets`         |
    |**인증 방법**     |역할 ARN을 사용 하 여 버킷에 액세스 하므로 **역할 arn** 을 선택 합니다.         |
    |**Microsoft 계정 ID**     |이 값을 클립보드에 복사 하려면 클릭 합니다. [AWS에서 역할 ARN을 만들](#create-a-new-aws-role-for-purview)때이 값을 **Microsoft 계정 ID** 로 사용 합니다.           |
    |**외부 ID**     |이 값을 클립보드에 복사 하려면 클릭 합니다. [AWS에서 역할 ARN을 만들](#create-a-new-aws-role-for-purview) 때이 값을 **외부 ID** 로 사용 합니다.        |
    |**역할 ARN**     | [AMAZON iam 역할을 만들었으면](#create-a-new-aws-role-for-purview)iam 영역의 역할로 이동 하 여 **역할 arn** 값을 복사 하 고 여기에 입력 합니다. 예: `arn:aws:iam::284759281674:role/S3Role` <br><br>자세한 내용은 [새 역할 ARN 검색](#retrieve-your-new-role-arn)을 참조 하세요. |
    | | |

    자격 증명 만들기를 완료 하면 **만들기** 를 선택 합니다.

부서의 범위 자격 증명에 대 한 자세한 내용은 [Azure 부서의 범위 공개 미리 보기 설명서](manage-credentials.md)를 참조 하세요.

### <a name="create-a-new-aws-role-for-purview"></a>부서의 범위에 대 한 새 AWS 역할 만들기

1.  **Amazon Web Services** 콘솔을 열고 **보안, Id 및 규정 준수** 에서 **IAM** 을 선택 합니다.

1. **역할** 을 선택한 다음 **역할을 만듭니다**.

1. **다른 AWS 계정을** 선택 하 고 다음 값을 입력 합니다.

    |필드  |설명  |
    |---------|---------|
    |**계정 ID**     |    Microsoft 계정 ID를 입력 합니다. 예: `615019938638`     |
    |**외부 ID**     |   옵션에서 **외부 Id 필요**...를 선택한 다음 지정 된 필드에 외부 id를 입력 합니다. <br>예: `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`    <br><br>사용자가이 외부 ID를 찾을 수 있습니다.  |
    | | |

    > [!NOTE]
    > 부서의 범위 자격 증명을 만든 부서의 범위 **관리 센터** 자격 증명 영역에서 **Microsoft 계정 ID** 및 **외부 ID** 에 대 한 값을 찾을 수 있습니다  >   . [](#create-a-purview-credential-for-your-aws-bucket-scan)
    >

    예를 들면 다음과 같습니다.

    ![AWS 계정에 Microsoft 계정 ID를 추가 합니다.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. **역할 만들기 > 권한 정책 연결** 영역에서 **S3** 에 표시 되는 사용 권한을 필터링 합니다. **AmazonS3ReadOnlyAccess** 을 선택 하 고 **다음: 태그** 를 선택 합니다.

    ![새 Amazon S3 검색 역할에 대 한 ReadOnlyAccess 정책을 선택 합니다.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

1. **태그 추가 (선택 사항)** 영역에서 필요에 따라이 새 역할에 대 한 의미 있는 태그를 만들도록 선택할 수 있습니다. 유용한 태그를 사용 하면 사용자가 만드는 각 역할에 대 한 액세스를 구성, 추적 및 제어할 수 있습니다.

    필요에 따라 태그의 새 키 및 값을 입력 합니다. 작업을 완료 하거나이 단계를 건너뛰려면 **다음: 검토** 를 선택 하 여 역할 세부 정보를 검토 하 고 역할 만들기를 완료 합니다.

    ![새 역할에 대 한 액세스를 구성, 추적 또는 제어 하는 의미 있는 태그를 추가 합니다.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. **검토** 영역에서 다음을 수행 합니다.

    - **역할 이름** 필드에 역할에 대 한 의미 있는 이름을 입력 합니다.
    - 역할 **설명** 상자에 역할의 용도를 식별 하는 설명 (선택 사항)을 입력 합니다.
    - **정책** 섹션에서 올바른 정책 (**AmazonS3ReadOnlyAccess**)이 역할에 연결 되어 있는지 확인 합니다.

    그런 다음 **역할 만들기** 를 선택 하 여 프로세스를 완료 합니다.

    예를 들면 다음과 같습니다.

    ![역할을 만들기 전에 세부 정보를 검토 하십시오.](./media/register-scan-amazon-s3/review-role.png)


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>암호화 된 Amazon S3 버킷의 검색 구성

AWS 버킷은 여러 암호화 유형을 지원 합니다. **AWS-KMS** 암호화를 사용 하는 버킷의 경우 검색을 사용 하려면 특별 한 구성이 필요 합니다.

> [!NOTE]
> 암호화 안 함, AES-256 또는 AWS S3 암호화를 사용 하는 버킷의 경우이 섹션을 건너뛰고 [Amazon s3 버킷 이름을 계속 검색](#retrieve-your-amazon-s3-bucket-name)합니다.
>

**Amazon S3 버킷에 사용 되는 암호화 유형을 확인 하려면 다음을 수행 합니다.**

1. AWS에서 **저장소**  >  **S3** >로 이동 하 고 왼쪽 메뉴에서 **버킷** 을 선택 합니다.

    ![Amazon S3 버킷 탭을 선택 합니다.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. 확인 하려는 버킷을 선택 합니다. 버킷에 대 한 세부 정보 페이지에서 **속성** 탭을 선택 하 고 **기본 암호화** 영역까지 아래로 스크롤합니다.

    - 선택한 버킷이 모든 항목에 대해 구성 되 고, 버킷에 대 한 기본 암호화를 사용 하지 않도록 설정 된 경우를 포함 하 여 **AWS** 암호화를 **사용 하지 않도록 설정** 된 경우이 절차의 나머지 부분을 건너뛰고 [Amazon S3 버킷 이름 검색](#retrieve-your-amazon-s3-bucket-name)을 계속 합니다.

    - 선택한 버킷이 **AWS** 암호화를 사용 하도록 구성 된 경우 아래에 설명 된 대로 계속 해 서 사용자 지정 **AWS** 암호화를 사용 하 여 버킷 검색을 허용 하는 새 정책을 추가 합니다.

    예를 들면 다음과 같습니다.

    ![AWS로 구성 된 Amazon S3 버킷 보기-KMS 암호화](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**사용자 지정 AWS-KMS 암호화를 사용 하 여 버킷 검색을 허용 하는 새 정책을 추가 하려면 다음을 수행 합니다.**

1. AWS에서 **서비스**  >   **IAM**  >   **정책** 으로 이동 하 고 **정책 만들기** 를 선택 합니다.

1. **정책 만들기**  >  **비주얼 편집기** 탭에서 다음 값을 사용 하 여 정책을 정의 합니다.

    |필드  |설명  |
    |---------|---------|
    |**서비스**     |  을 입력 하 고 **KMS** 를 선택 합니다.       |
    |**actions**     | **액세스 수준** 에서 **쓰기** 를 선택 하 여 **쓰기** 섹션을 확장 합니다.<br>확장 된 후에는 **암호 해독** 옵션만 선택 합니다.        |
    |**리소스**     |특정 리소스 또는 **모든 리소스** 를 선택 합니다.         |
    | | |

    완료 되 면 **정책 검토** 를 선택 하 여 계속 합니다.

    ![AWS-KMS 암호화를 사용 하 여 버킷을 검색 하는 정책을 만듭니다.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. **정책 검토** 페이지에서 정책에 대 한 의미 있는 이름과 설명 (선택 사항)을 입력 하 고 **정책 만들기** 를 선택 합니다.

    새로 만든 정책이 정책 목록에 추가 됩니다.

1. 검색을 위해 추가한 역할에 새 정책을 연결 합니다.

    1. **IAM**  >  **역할** 페이지로 다시 이동 하 여 [이전](#create-a-new-aws-role-for-purview)에 추가한 역할을 선택 합니다.

    1. **사용 권한** 탭에서 **정책 연결** 을 선택 합니다.

        ![역할의 사용 권한 탭에서 정책 연결을 선택 합니다.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. **권한 연결** 페이지에서 위에서 만든 새 정책을 검색 하 여 선택 합니다. 정책 **연결** 을 선택 하 여 정책을 역할에 연결 합니다.

        **요약** 페이지가 업데이트 되 고 새 정책이 역할에 연결 됩니다.

        ![역할에 연결 된 새 정책이 있는 업데이트 된 요약 페이지를 봅니다.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>새 역할 ARN 검색

[Amazon S3 버킷에 대 한 검색을 만들](#create-a-scan-for-your-amazon-s3-bucket)때 AWS 역할 arn을 기록 하 여 부서의 범위에 복사 해야 합니다.

**사용자 역할을 검색 하려면 다음을 수행 합니다.**

1. AWS **id 및 액세스 관리 (IAM)**  >  **역할** 영역에서 [부서의 범위에 대해 만든](#create-a-purview-credential-for-your-aws-bucket-scan)새 역할을 검색 하 고 선택 합니다.

1. 역할의 **요약** 페이지에서 **역할 arn** 값의 오른쪽에 있는 **클립보드로 복사** 단추를 선택 합니다.

    ![역할 ARN 값을 클립보드에 복사 합니다.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

1. [Amazon S3 버킷에 대 한 검색을 만들](#create-a-scan-for-your-amazon-s3-bucket)때 사용할 준비가 된 안전한 위치에이 값을 붙여넣습니다.

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Amazon S3 버킷 이름 검색

[Amazon s3 버킷에 대 한 검색을 만들](#create-a-scan-for-your-amazon-s3-bucket) 때 부서의 범위에 복사할 amazon s3 버킷의 이름이 필요 합니다.

**버킷 이름을 검색 하려면:**

1. AWS에서 **저장소**  >  **S3** >로 이동 하 고 왼쪽 메뉴에서 **버킷** 을 선택 합니다.

    ![Amazon S3 버킷 탭을 봅니다.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. 버킷을 검색 하 고 선택 하 여 버킷 세부 정보 페이지를 표시 한 다음 버킷 이름을 클립보드에 복사 합니다.

    예를 들면 다음과 같습니다.

    ![S3 버킷 URL을 검색 하 여 복사 합니다.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    버킷 이름을 보안 파일에 붙여넣고 `s3://` 여기에 접두사를 추가 하 여 버킷을 부서의 범위 리소스로 구성할 때 입력 해야 하는 값을 만듭니다.

    예: `s3://purview-tutorial-bucket`

> [!NOTE]
> 버킷의 루트 수준만 부서의 범위 데이터 원본으로 지원 됩니다. 예를 들어 하위 폴더가 포함 된 다음 URL은 지원 *되지 않습니다* . `s3://purview-tutorial-bucket/view-data`
>

### <a name="locate-your-aws-account-id"></a>AWS 계정 ID 찾기

AWS 계정을 부서의 범위 데이터 원본으로 모든 버킷에 등록 하려면 AWS 계정 ID가 필요 합니다.

AWS 계정 ID는 AWS 콘솔에 로그인 하는 데 사용 하는 ID입니다. 또한 IAM 대시보드에 로그인 한 후에 탐색 옵션 왼쪽의 왼쪽에서 로그인 URL의 숫자 부분으로이를 찾을 수 있습니다.

예를 들면 다음과 같습니다.

![AWS 계정 ID를 검색 합니다.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>단일 Amazon S3 버킷을 부서의 범위 리소스로 추가

부서의 범위에 데이터 원본으로 등록 하려는 단일 S3 버킷이 있거나 AWS 계정에 여러 버킷이 있지만이를 부서의 범위에 등록 하지 않으려는 경우이 절차를 사용 합니다.

1. Amazon S3 URL의 전용 부서의 범위 커넥터를 사용 하 여 부서의 범위 포털을 시작 합니다. 이 URL은 Amazon S3 부서의 범위 connector 제품 관리 팀에서 제공 합니다.

    ![부서의 범위 포털을 시작 합니다.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Azure 부서의 범위 **원본** 페이지로 이동 하 고 레지스터 등록 아이콘 **을 선택** ![ 합니다.](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3**  >  **계속** 합니다.

    ![Amazon AWS 버킷을 부서의 범위 데이터 원본으로 추가 합니다.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > [컬렉션이](manage-data-sources.md#manage-collections) 여러 개인 경우 Amazon s 3을 특정 컬렉션에 추가 하려면 오른쪽 위에 있는 **지도 보기** 를 선택한 다음 등록 **등록** 아이콘을 선택 합니다 ![ .](./media/register-scan-amazon-s3/register-button.png) 단추를 클릭 합니다.
    >

1. 열리는 **소스 등록 (Amazon S3)** 창에서 다음 세부 정보를 입력 합니다.

    |필드  |설명  |
    |---------|---------|
    |**이름**     |의미 있는 이름을 입력 하거나 제공 된 기본값을 사용 합니다.         |
    |**버킷 URL**     | 다음 구문을 사용 하 여 AWS 버킷 URL을 입력 합니다.   `s3://<bucketName>`     <br><br>**참고**: 하위 폴더 없이 버킷의 루트 수준만 사용 해야 합니다. 자세한 내용은 [Amazon S3 버킷 이름 검색](#retrieve-your-amazon-s3-bucket-name)을 참조 하세요. |
    |**컬렉션 선택** |컬렉션 내에서 데이터 원본을 등록 하도록 선택한 경우 해당 컬렉션은 이미 나열 되어 있습니다. <br><br>필요에 따라 다른 컬렉션을 선택 **하거나, 컬렉션** 을 할당 하지 않거나, **새로** 만들기를 선택 하 여 새 컬렉션을 만듭니다. <br><br>부서의 범위 컬렉션에 대 한 자세한 내용은 [Azure 부서의 범위에서 데이터 원본 관리](manage-data-sources.md#manage-collections)를 참조 하세요.|
    | | |

    완료 되 면 **마침** 을 선택 하 여 등록을 완료 합니다.

[Amazon S3 버킷에 대 한 검색 만들기](#create-a-scan-for-your-amazon-s3-bucket)를 계속 진행 합니다.

## <a name="add-all-of-your-amazon-s3-buckets-as-purview-resources"></a>모든 Amazon S3 버킷을 부서의 범위 리소스로 추가

Amazon 계정에 여러 S3 버킷이 있고 모든을 부서의 범위 데이터 원본으로 등록 하려는 경우이 절차를 사용 합니다.

1. Amazon S3 URL의 전용 부서의 범위 커넥터를 사용 하 여 부서의 범위 포털을 시작 합니다. 이 URL은 Amazon S3 부서의 범위 connector 제품 관리 팀에서 제공 합니다.

    ![Amazon S3 전용 부서의 범위 포털에 대 한 시작 커넥터](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Azure 부서의 범위 **원본** 페이지로 이동 하 고 레지스터 등록 아이콘 **을 선택** ![ 합니다.](./media/register-scan-amazon-s3/register-button.png) > **Amazon 계정**  >  **계속** 합니다.

    ![Amazon 계정을 부서의 범위 데이터 원본으로 추가 합니다.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > [컬렉션이](manage-data-sources.md#manage-collections) 여러 개인 경우 Amazon s 3을 특정 컬렉션에 추가 하려면 오른쪽 위에 있는 **지도 보기** 를 선택한 다음 등록 **등록** 아이콘을 선택 합니다 ![ .](./media/register-scan-amazon-s3/register-button.png) 단추를 클릭 합니다.
    >

1. 열리는 **소스 등록 (Amazon S3)** 창에서 다음 세부 정보를 입력 합니다.

    |필드  |설명  |
    |---------|---------|
    |**이름**     |의미 있는 이름을 입력 하거나 제공 된 기본값을 사용 합니다.         |
    |**AWS 계정 ID**     | AWS 계정 ID를 입력 합니다. 자세한 내용은 [AWS 계정 ID 찾기](#locate-your-aws-account-id) 를 참조 하세요.|
    |**컬렉션 선택** |컬렉션 내에서 데이터 원본을 등록 하도록 선택한 경우 해당 컬렉션은 이미 나열 되어 있습니다. <br><br>필요에 따라 다른 컬렉션을 선택 **하거나, 컬렉션** 을 할당 하지 않거나, **새로** 만들기를 선택 하 여 새 컬렉션을 만듭니다. <br><br>부서의 범위 컬렉션에 대 한 자세한 내용은 [Azure 부서의 범위에서 데이터 원본 관리](manage-data-sources.md#manage-collections)를 참조 하세요.|
    | | |

    완료 되 면 **마침** 을 선택 하 여 등록을 완료 합니다.

[Amazon S3 버킷에 대 한 검색 만들기](#create-a-scan-for-your-amazon-s3-bucket)를 계속 합니다.

## <a name="create-a-scan-for-your-amazon-s3-bucket"></a>Amazon S3 버킷에 대 한 검색 만들기

부서의 범위 데이터 원본으로 버킷을 추가 하면 예약 된 간격으로 또는 즉시 실행 되도록 검색을 구성할 수 있습니다.

1. Azure 부서의 범위 **원본** 영역으로 이동한 후 다음 중 하나를 수행 합니다.

    - **지도 보기** 에서 **새로 만들기 검색** ![ 새 검사 아이콘을 선택 합니다.](./media/register-scan-amazon-s3/new-scan-button.png) 데이터 원본 상자에 있습니다.
    - **목록 뷰에서** 데이터 원본에 대 한 행을 마우스로 가리키고 **새** 검색 ![ 새 스캔 아이콘을 선택 ](./media/register-scan-amazon-s3/new-scan-button.png) 합니다.

1. 오른쪽에 열리는 **검색 ...** 창에서 다음 필드를 정의한 후 **계속** 을 선택 합니다.

    |필드  |설명  |
    |---------|---------|
    |**이름**     |  검색에 대 한 의미 있는 이름을 입력 하거나 기본값을 사용 합니다.       |
    |**유형** |모든 버킷을 포함 하 여 AWS 계정을 추가한 경우에만 표시 됩니다. <br><br>현재 옵션은 **모든** Amazon s 3만 포함  >  합니다. 부서의 범위의 지원 매트릭스가 확장 될 때 선택할 수 있는 추가 옵션을 계속 조정 합니다. |
    |**자격 증명**     |  역할 ARN을 사용 하 여 부서의 범위 자격 증명을 선택 합니다. <br><br>**팁**: 지금 새 자격 증명을 만들려는 경우 **새로** 만들기를 선택 합니다. 자세한 내용은 [AWS 버킷 검색을 위한 부서의 범위 자격 증명 만들기](#create-a-purview-credential-for-your-aws-bucket-scan)를 참조 하세요.     |
    |     |         |

    부서의 범위는 역할 ARN이 유효한 지, 버킷 내의 버킷 및 개체가 액세스할 수 있는지를 자동으로 확인 한 다음 연결에 성공 하면 계속 됩니다.

    > [!TIP]
    > 계속 하기 전에 다른 값을 입력 하 고 연결을 직접 테스트 하려면 오른쪽 아래에서 **연결 테스트** 를 선택 하 고 **계속** 을 선택 합니다.
    >

1. **검색 규칙 집합 선택** 창에서 **AmazonS3** 기본 규칙 집합을 선택 하거나 **새 스캔 규칙 집합** 을 선택 하 여 새 사용자 지정 규칙 집합을 만듭니다. 규칙 집합을 선택한 후 **계속** 을 선택 합니다.

    새 사용자 지정 검색 규칙 집합을 만들도록 선택 하는 경우 마법사를 사용 하 여 다음 설정을 정의 합니다.

    |창  |Description  |
    |---------|---------|
    |**새 스캔 규칙 집합** /<br>**검색 규칙 설명**    |   규칙 집합에 대 한 의미 있는 이름과 설명 (선택 사항)을 입력 합니다.      |
    |**파일 형식 선택**     | 검색에 포함 하려는 모든 파일 형식을 선택 하 고 **계속** 을 선택 합니다.<br><br>새 파일 형식을 추가 하려면 **새 파일 형식** 을 선택 하 고 다음을 정의 합니다. <br>-추가 하려는 파일 확장명 <br>-선택적 설명  <br>-파일 내용이 사용자 지정 구분 기호를 포함 하거나 시스템 파일 형식 인지 여부입니다. 그런 다음 사용자 지정 구분 기호를 입력 하거나 시스템 파일 형식을 선택 합니다. <br><br>**만들기** 를 선택 하 여 사용자 지정 파일 형식을 만듭니다.     |
    |**분류 규칙 선택**     |   데이터 집합에서 실행 하려는 분류 규칙으로 이동 하 여 선택 합니다.      |
    |     |         |

    규칙 집합 만들기를 완료 하면 **만들기** 를 선택 합니다.

1. **검색 트리거 설정** 창에서 다음 중 하나를 선택 하 고 **계속** 을 선택 합니다.

    - 되풀이 검색 일정을 구성 하기 위해 **되풀이**
    - 즉시 시작 하는 **검색을 구성 하는** 경우

1. **검색 검색** 창에서 검색 세부 정보가 올바른지 확인 한 다음, 이전 창에서 **한 번** 선택한 경우 **저장** 또는 **저장 및 실행** 을 선택 합니다.

    > [!NOTE]
    > 시작 되 면 검색을 완료 하는 데 최대 24 시간이 걸릴 수 있습니다. 각 검색을 시작한 후에 정보 **보고서** 를 검토 하 고 24 시간 내에 카탈로그를 검색할 수 있습니다.
    >

자세한 내용은 [부서의 범위 검색 결과 탐색](#explore-purview-scanning-results)을 참조 하세요.

## <a name="explore-purview-scanning-results"></a>부서의 범위 검색 결과 살펴보기

Amazon S3 버킷에 대 한 부서의 범위 검사가 완료 되 면 부서의 범위 **원본** 영역에서 드릴 다운 하 여 검사 기록을 확인 합니다.

데이터 원본을 선택 하 여 세부 정보를 확인 한 다음 **검색** 탭을 선택 하 여 현재 실행 중이거나 완료 된 검색을 확인 합니다.
여러 버킷을 포함 하는 AWS 계정을 추가한 경우 각 버킷에 대 한 검색 기록이 계정 아래에 표시 됩니다.

예를 들면 다음과 같습니다.

![AWS 계정 원본에 AWS S3 버킷 검색을 표시 합니다.](./media/register-scan-amazon-s3/account-scan-history.png)

부서의 범위의 다른 영역을 사용 하 여 Amazon S3 버킷을 비롯 한 데이터 공간에 대 한 세부 정보를 확인할 수 있습니다.

- **부서의 범위 data catalog를 검색** 하 고 특정 버킷을 필터링 합니다. 예를 들면 다음과 같습니다.

    ![AWS S3 자산에 대 한 카탈로그를 검색 합니다.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- 정보 **보고서를 보고** 분류, 민감도 레이블, 파일 형식 및 콘텐츠에 대 한 자세한 정보에 대 한 통계를 확인 합니다.

    모든 부서의 범위 통찰력 보고서에는 Azure 데이터 원본의 나머지 결과와 함께 Amazon S3 검사 결과가 포함 됩니다. 관련 된 경우 추가 **Amazon S3** 자산 유형이 보고서 필터링 옵션에 추가 되었습니다.

    자세한 내용은 [Azure 부서의 범위의 통찰력 이해](concept-insights.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure 부서의 범위 통찰력 보고서에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Purview의 인사이트 이해](concept-insights.md)
