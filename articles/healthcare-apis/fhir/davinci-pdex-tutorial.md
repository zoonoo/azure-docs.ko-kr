---
title: 자습서-Davinci.txt PDex-FHIR 용 Azure API
description: 이 자습서에서는 Da 다빈치 지불자 데이터 교환 구현 가이드에 대 한 테스트를 통과 하도록 Azure API를 설정 하는 과정을 안내 합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 06/02/2021
ms.openlocfilehash: 0e6ee8ba2288ae61eb1fe0d5e6b76ebc63a64897
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111562949"
---
# <a name="davinci-pdex"></a>Davinci.txt PDex

이 자습서에서는 [Da 다빈치 지불자 PDEX IG (데이터 교환 구현 가이드](http://hl7.org/fhir/us/davinci-pdex/toc.html) )에 대 한 [Touchstone](https://touchstone.aegis.net/touchstone/) 테스트를 전달 하도록 Fhir 용 Azure API를 설정 하는 과정을 안내 합니다.

> [!NOTE]
> 이러한 모든 테스트에 대해 JSON 테스트에 대해 실행 합니다. FHIR 용 Azure API는 JSON 및 XML을 모두 지원 하지만 JSON 또는 XML에 액세스 하기 위한 별도의 끝점은 없습니다. 이로 인해 모든 XML 테스트가 실패 합니다. XML에서 기능 문을 보려는 경우 format 매개 변수를 전달 하면 됩니다 \_ . \` GET {fhirurl}/metadata? \_ format = xml\`

## <a name="touchstone-capability-statement"></a>Touchstone 기능 문

초점을 맞춘 테스트의 첫 번째 집합은 PDex IG 기능 문에 대해 Azure API for FHIR을 테스트 하는 것입니다. 이러한 테스트에는 세 가지 유효성 검사 프로세스가 있습니다.

* 첫 번째 테스트는 성능 요구 사항에 대 한 기본 기능 문의 유효성을 검사 하 고 업데이트 없이 전달 합니다.

* 두 번째 테스트는 US Core에 대해 추가 된 모든 프로필의 유효성을 검사 합니다. 이 테스트는 업데이트 없이 통과 하지만 많은 경고를 포함 합니다. 이러한 경고를 제거 하려면 [미국 코어 프로필을 로드](validation-against-profiles.md)해야 합니다. 모든 프로필을 만드는 과정을 안내 하는 [샘플 HTTP 파일](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/USCore.http) 을 만들었습니다. 최신 버전을 포함 하는 HL7 사이트에서 직접 [프로필](http://hl7.org/fhir/us/core/STU3.1.1/profiles.html#profiles) 을 가져올 수도 있습니다.

* 세 번째 테스트는 환자 모든 작업이 지원 되는지 확인 합니다. 지금은이 테스트가 실패 합니다. 작업은 FHIR 용 Azure API에서 2021 년 6 월에 제공 되며, 이제 Cosmos DB의 오픈 소스 FHIR 서버에서 사용할 수 있습니다. 그러나이 기능은 기능 문에 없으므로 버그 [1989](https://github.com/microsoft/fhir-server/issues/1989)에 대 한 수정을 릴리스할 때까지이 테스트가 실패 합니다. 

 
:::image type="content" source="media/cms-tutorials/davinci-pdex-test-script-failed.png" alt-text="Davinci.txt PDex를 실행 하지 못했습니다.":::

## <a name="touchstone-member-match-test"></a>Touchstone $member-일치 테스트

지불자 데이터 교환 섹션의 [두 번째 테스트](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/01-Member-Match&activeOnly=false&contentEntry=TEST_SCRIPTS) 는 [$member 일치 작업이](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html)있는지 테스트 합니다. $Member-일치 작업에 대 한 자세한 내용은 [$member 일치 작업 개요](tutorial-member-match.md)를 참조 하세요.

이 테스트에서는 테스트를 통과 하기 위해 일부 샘플 데이터를 로드 해야 합니다. [여기](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/membermatch.http) 에는 테스트에 필요한 환자와 검사를 연결 하는 rest 파일이 있습니다. 이 데이터가 로드 되 면이 테스트를 성공적으로 전달할 수 있습니다. 데이터가 로드 되지 않은 경우 정확히 일치 하는 항목을 찾지 못해 422 응답을 받게 됩니다.

:::image type="content" source="media/cms-tutorials/davinci-pdex-test-script-passed.png" alt-text="Davinci.txt PDex 테스트 스크립트를 통과 했습니다.":::

## <a name="touchstone-patient-by-reference"></a>참조로 Touchstone 환자

검토할 다음 테스트는 [참조 테스트로](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/02-PatientByReference&activeOnly=false&contentEntry=TEST_SCRIPTS) 인 한 환자입니다. 이 테스트 집합은 다양 한 검색 조건에 따라 환자를 찾을 수 있는지 유효성을 검사 합니다. 환자를 참조로 테스트 하는 가장 좋은 방법은 자신의 데이터에 대해 테스트 하는 것 이지만, 사용 하기 위해 로드할 수 있는 [샘플 리소스 파일도](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/PDex_Sample_Data.http) 업로드 했습니다.

:::image type="content" source="media/cms-tutorials/davinci-pdex-test-execution-passed.png" alt-text="Davinci.txt PDex 실행이 성공 했습니다.":::

## <a name="touchstone-patienteverything-test"></a>Touchstone 환자/$everything 테스트

살펴볼 최종 테스트는 환자를 테스트 하는 것입니다. 이 테스트의 경우 환자를 로드 한 다음 해당 환자 ID를 사용 하 여 $everything 작업을 사용 하 여 환자와 관련 된 모든 데이터를 끌어올 수 있는지 테스트 합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Touchstone에서 지불자 Exchange 테스트를 전달 하는 방법을 살펴보았습니다. 다음으로, 모든 FHIR 기능 용 Azure API에 대해 알아볼 수 있습니다.

>[!div class="nextstepaction"]
>[지원되는 기능](fhir-features-supported.md)  