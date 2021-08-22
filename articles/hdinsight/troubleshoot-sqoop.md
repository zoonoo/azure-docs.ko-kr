---
title: ESP 클러스터의 일부 사용자에 대해 Sqoop import/export(가져오기/내보내기) 명령이 실패함 - Azure HDInsight
description: 'HDInsight ESP 클러스터의 일부 사용자에 대한 Apache Sqoop import/export(가져오기/내보내기) 명령이 "가져오기 실패: java.io.IOException: 준비 디렉터리 /user/yourusername/.staging의 소유권이 예상과 다름" 오류로 인해 실패합니다.'
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/01/2021
ms.openlocfilehash: 2eba44d735b1b8ea9a88164ede0ca4860efcce8a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112283936"
---
# <a name="scenario-sqoop-importexport-command-fails-for-usernames-greater-than-20-characters-in-azure-hdinsight-esp-clusters"></a>시나리오: Azure HDInsight ESP 클러스터에서 20자를 초과하는 사용자 이름에 대해 Sqoop import/export(가져오기/내보내기) 명령이 실패함

이 문서에서는 ADLS Gen2(ABFS) 스토리지 계정을 사용하여 Azure HDInsight ESP(Enterprise Security Pack) 지원 클러스터를 사용할 때 알려진 문제 및 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Sqoop import/export(가져오기/내보내기) 명령을 실행할 때 일부 사용자의 경우 아래 오류로 인해 실패합니다.

```
ERROR tool.ImportTool: Import failed: java.io.IOException:
The ownership on the staging directory /user/yourlongdomainuserna/.staging is not as expected. 
It is owned by yourlongdomainusername.
The directory must be owned by the submitter yourlongdomainuserna or yourlongdomainuserna@AADDS.CONTOSO.COM
```

위의 예제에서 `/user/yourlongdomainuserna/.staging`은 사용자 이름 `yourlongdomainusername`에 대해 잘린 20자 사용자 이름을 표시합니다.

## <a name="cause"></a>원인

사용자 이름의 길이가 20자를 초과합니다. 

자세한 내용은 [Azure Active Directory Domain Services 관리형 도메인에서 개체 및 자격 증명을 동기화하는 방법](../active-directory-domain-services/synchronization.md)을 참조하세요.

## <a name="workaround"></a>해결 방법

20자 이하의 사용자 이름을 사용합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](includes/hdinsight-troubleshooting-next-steps.md)]
