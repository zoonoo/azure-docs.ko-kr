---
title: 인증서 콘텐츠를 Base-64로 변환 - Azure HDInsight
description: 서비스 주체 인증서 콘텐츠를 Azure HDInsight의 Base-64로 인코딩된 문자열 형식으로 변환
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 1ccd47b0a2f10c67dc808fb13aafca55fa5a5e67
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110088753"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>HDInsight에서 서비스 주체 인증서 콘텐츠를 Base-64로 인코딩된 문자열 형식으로 변환

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

입력이 Base-64가 아닌 문자, 세 개 이상의 안쪽 여백 문자 또는 안쪽 여백 문자 사이에 공백이 아닌 문자를 포함하기 때문에 유효한 Base-64 문자열이 아니라는 오류 메시지가 표시됩니다.

## <a name="cause"></a>원인

Data Lake를 기본 또는 추가 스토리지로 사용하여 클러스터를 만들기 위해 PowerShell 또는 Azure 템플릿 배포를 사용하는 경우 Data Lake Storage 계정에 액세스하기 위해 제공되는 서비스 주체 인증서 콘텐츠는 Base-64 형식입니다. pfx 인증서 콘텐츠를 Base-64로 인코딩된 문자열로 잘못 변환하면 이 오류가 발생할 수 있습니다.

## <a name="resolution"></a>해결 방법

pfx 형식의 서비스 주체 인증서가 있으면(샘플 서비스 주체 생성 단계는 [여기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.hdinsight/hdinsight-datalake-store-azure-storage) 참조) 다음 PowerShell 명령 또는 C# 코드 조각을 사용하여 인증서 콘텐츠를 Base-64 형식으로 변환합니다.

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