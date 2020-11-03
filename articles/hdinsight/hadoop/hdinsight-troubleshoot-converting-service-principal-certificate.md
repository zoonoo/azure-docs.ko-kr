---
title: 인증서 콘텐츠를 base-64-Azure HDInsight로 변환
description: Azure HDInsight에서 서비스 주체 인증서 콘텐츠를 base-64로 인코딩된 문자열 형식으로 변환
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 88faf878d9528b314e59c81dad7a97d4700b608f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289137"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>HDInsight에서 서비스 사용자 인증서 콘텐츠를 base-64로 인코딩된 문자열 형식으로 변환

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

입력이 기본이 아닌 64 문자, 두 개 이상의 패딩 문자 또는 패딩 문자 사이에 공백이 아닌 문자를 포함 하 고 있기 때문에 올바른 Base-64 문자열이 아닙니다. 라는 오류 메시지가 표시 됩니다.

## <a name="cause"></a>원인

PowerShell 또는 Azure 템플릿 배포를 사용 하 여 Data Lake를 기본 또는 추가 저장소로 사용 하 여 클러스터를 만드는 경우 Data Lake 저장소 계정에 액세스 하기 위해 제공 된 서비스 주체 인증서 콘텐츠가 64 형식으로 제공 됩니다. Pfx 인증서 콘텐츠를 base-64로 인코딩된 문자열로 잘못 변환 하면이 오류가 발생할 수 있습니다.

## <a name="resolution"></a>해결 방법

Pfx 형식으로 서비스 사용자 인증서를 사용 하는 경우 (샘플 서비스 주체 만들기 단계는 [여기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) 참조) 다음 PowerShell 명령 또는 c # 코드 조각을 사용 하 여 인증서 콘텐츠를 base-64 형식으로 변환 합니다.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]