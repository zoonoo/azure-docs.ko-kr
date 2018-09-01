---
title: Azure Stack에 대 한 유효성 검사 보고서 | Microsoft Docs
description: Azure Stack 준비 검사 보고서를 사용 하 여 유효성 검사 결과를 검토 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 06b5660a9428e98d2e99b5d447a05700968ec884
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2018
ms.locfileid: "43381916"
---
# <a name="azure-stack-validation-report"></a>Azure Stack 유효성 검사 보고서
Azure Stack 준비 검사 도구를 사용 하 여 배포 및 Azure Stack 환경 서비스를 지 원하는 유효성 검사를 실행 합니다. .Json 보고서 파일에 결과 기록 하는 도구입니다. 보고서에는 Azure Stack의 배포를 위한 필수 구성 요소의 상태에 대 한 자세 하 고 요약 된 데이터가 표시 됩니다. 또한 보고서에는 기존 Azure Stack 배포에 대 한 비밀 회전 하는 방법에 대 한 정보가 표시 됩니다.  

 ## <a name="where-to-find-the-report"></a>보고서를 찾을 수 있는 위치
결과를 기록 도구를 실행 하는 경우 **AzsReadinessCheckerReport.json**합니다. 이 도구는 또한 라는 로그 만듭니다 **AzsReadinessChecker.log**합니다. PowerShell에서 유효성 검사 결과 사용 하 여 이러한 파일의 위치를 표시합니다.

![유효성 검사 실행](./media/azure-stack-validation-report/validation.png)

파일을 모두 동일한 컴퓨터에서 실행 하는 경우 후속 유효성 검사의 결과 유지 합니다.  예를 들어, 인증서의 유효성을 검사 하 고, Azure id의 유효성을 검사 하려면 다시 실행 하 고, 등록 유효성을 검사 하는 제 3 시간 후에 도구를 실행할 수 있습니다. 세 개의 모든 유효성 검사의 결과가 결과.json 보고서에서 사용할 수 있습니다.  

기본적으로 두 파일에 기록 됩니다 *C:\Users\<사용자 이름 > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*합니다.  
- 사용 된 **-OutputPath** ***&lt;경로&gt;*** 끝에 다른 보고서 위치를 지정 하려면 실행된 명령줄 매개 변수입니다.   
- 사용 된 **-CleanReport** 매개 변수에서 정보를 지우려면 실행 명령의 끝 *AzsReadinessCheckerReport.json*합니다. 이전에 대 한 도구를 실행합니다.

## <a name="view-the-report"></a>보고서 보기
PowerShell에서 보고서를 보려는 보고서에 대 한 경로 대 한 값으로 제공 **-ReportPath**합니다. 이 명령은 보고서의 내용을 표시 하 고 결과 아직 있지 않은 유효성 검사를 식별 합니다.

예를 들어 보고서가 있는 위치에 열려 있는 PowerShell 프롬프트에서 보고서를 보려면 다음을 실행 합니다. 
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

출력에는 다음 이미지와 유사합니다.

![보고서 보기](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>요약 보고서를 보려면
보고서의 요약을 확인 하려면 추가 합니다 **-요약** PowerShell 명령줄의 끝으로 전환 합니다. 예:  
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

요약 결과 되지 않은 유효성 검사 나타나며 전달 하거나 완료 되는 유효성 검사 실패를 나타냅니다. 출력에는 다음 이미지와 유사합니다.

![보고서-요약](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>필터링 된 보고서를 보려면
단일 유형의 유효성 검사에서 필터링 되는 보고서를 보기를 사용 합니다 **-ReportSections** 다음 값 중 하나를 사용 하 여 매개 변수:
- 인증서
- AzureRegistration
- AzureIdentity
- 교육   
- 모두  

예를 들어 보고서를 보려면 인증서에 대 한 요약만 사용 하 여 다음 PowerShell 명령줄: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>참고 항목
[시작-AzsReadinessChecker cmdlet 참조](azure-stack-azsreadiness-cmdlet.md)
