---
title: 인증서 내용을 기본 64로 변환 - Azure HDInsight
description: Azure HDInsight에서 서비스 주 인증서 내용을 기본 64 인코딩된 문자열 형식으로 변환
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: d6119e4f8c651ba482a24f46b44ff15f870858ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894180"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>서비스 주 인증서 내용을 HDInsight의 기본 64 인코딩 된 문자열 형식으로 변환

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

입력이 기본이 아닌 64문자, 두 개 이상의 패딩 문자 또는 패딩 문자 중 공백이 아닌 문자를 포함하므로 입력이 유효한 Base-64 문자열이 아님을 알리는 오류 메시지가 나타납니다.

## <a name="cause"></a>원인

PowerShell 또는 Azure 템플릿 배포를 사용하여 Data Lake를 기본 저장소 또는 추가 저장소로 클러스터를 만드는 경우 Data Lake 저장소 계정에 액세스하기 위해 제공된 서비스 주인증서 콘텐츠는 기본 64 형식입니다. pfx 인증서 내용을 기본 64 인코딩된 문자열로 잘못 변환하면 이 오류가 발생할 수 있습니다.

## <a name="resolution"></a>해결 방법

서비스 주 인증서를 pfx 형식으로 지정하면(샘플 서비스 주체 만들기 단계는 [여기를](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) 참조) 다음 PowerShell 명령 또는 C# 스니펫을 사용하여 인증서 내용을 기본 64 형식으로 변환합니다.

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

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
