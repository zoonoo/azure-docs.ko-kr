---
title: 양식 인식기 샘플 라벨링 도구를 배포하는 방법
titleSuffix: Azure Cognitive Services
description: 지도 학습에 도움이 되는 양식 인식기 샘플 라벨링 도구를 배포할 수 있는 다양한 방법을 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/20/2020
ms.author: pafarley
ms.openlocfilehash: 795d21e05ade652b52c06d597ca4c5fef85e7245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152845"
---
# <a name="deploy-the-sample-labeling-tool"></a>샘플 라벨링 도구 배포

양식 인식기 샘플 레이블 지정 도구는 간단한 사용자 인터페이스(UI)를 제공하는 응용 프로그램으로, 감독 학습을 위해 양식(문서)에 수동으로 레이블을 지정하는 데 사용할 수 있습니다. 이 문서에서는 다음 방법을 알려주는 링크와 지침을 제공합니다.

* [샘플 라벨링 도구를 로컬로 실행](#run-the-sample-labeling-tool-locally)
* [샘플 레이블 지정 도구를 Azure 컨테이너 인스턴스(ACI)에 배포합니다.](#deploy-with-azure-container-instances-aci)
* [오픈 소스 OCR 양식 라벨링 도구 사용 및 기여](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>샘플 라벨링 도구를 로컬로 실행

데이터 레이블 지정을 시작하는 가장 빠른 방법은 샘플 레이블 지정 도구를 로컬로 실행하는 것입니다. 다음 빠른 시작은 양식 인식기 REST API 및 샘플 레이블 지정 도구를 사용하여 수동으로 레이블이 지정된 데이터로 사용자 지정 모델을 학습합니다. 

* [빠른 시작: 양식에 레이블을 지정하고, 모델을 학습하고, 샘플 레이블 지정 도구를 사용하여 양식을 분석합니다.](./quickstarts/label-tool.md)

## <a name="deploy-with-azure-container-instances-aci"></a>Azure 컨테이너 인스턴스(ACI)를 통해 배포

시작하기 전에 샘플 레이블 지정 도구를 ACI(Azure 컨테이너 인스턴스)에 배포하는 두 가지 방법이 있습니다. 두 옵션 모두 ACI를 사용하여 샘플 레이블 지정 도구를 실행하는 데 사용됩니다. 

* [Azure 포털 사용](#azure-portal)
* [Azure CLI 사용](#azure-cli)

### <a name="azure-portal"></a>Azure portal

다음 단계에 따라 Azure 포털을 사용하여 새 리소스를 만듭니다. 

1. [Azure 포털에](https://portal.azure.com/signin/index/)로그인합니다.
2. **리소스 만들기를 선택합니다.** 
3. 다음으로 **웹 앱을**선택합니다. 

   > [!div class="mx-imgBorder"]
   > ![웹앱 선택](./media/quickstarts/formre-create-web-app.png)
   
4. 먼저 기본 탭이 선택되어 있는지 **확인합니다.** 이제 몇 가지 정보를 제공해야 합니다. 

   > [!div class="mx-imgBorder"]
   > ![기본 사항 선택](./media/quickstarts/formre-select-basics.png)
   * 구독 - 기존 Azure 구독 선택
   * 리소스 그룹 - 기존 리소스 그룹을 다시 사용하거나 이 프로젝트에 대해 새 리소스 그룹을 만들 수 있습니다. 새 리소스 그룹을 만드는 것이 좋습니다.
   * 이름 - 웹 앱에 이름을 지정합니다. 
   * 게시 - **도커 컨테이너** 선택
   * 운영 체제 - **리눅스** 선택
   * 지역 - 자신에게 적합한 지역을 선택합니다.
   * Linux 요금제 - 앱 서비스에 대한 가격 책정 계층/요금제(요금제)를 선택합니다. 

   > [!div class="mx-imgBorder"]
   > ![웹 앱 구성](./media/quickstarts/formre-select-docker-linux.png)

5. 다음으로 **Docker** 탭을 선택합니다. 

   > [!div class="mx-imgBorder"]
   > ![도커 선택](./media/quickstarts/formre-select-docker.png)

6. 이제 Docker 컨테이너를 구성해 보겠습니다. 달리 명시되지 않는 한 모든 필드는 필수입니다.

   * 옵션 - **단일 컨테이너** 선택
   * 이미지 소스 - **개인 레지스트리** 선택 
   * 서버 URL - 이 것을`https://mcr.microsoft.com`
   * 사용자 이름(선택 사항) - 사용자 이름을 만듭니다. 
   * 암호(선택 사항) - 기억할 보안 암호를 만듭니다.
   * 이미지 및 태그 - 이 것을 다음으로 설정합니다.`mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * 시작 명령 - 이 것을`./run.sh eula=accept`

   > [!div class="mx-imgBorder"]
   > ![Docker 구성](./media/quickstarts/formre-configure-docker.png)

7. 이것으로 끝입니다. 그런 다음 **검토 + 만들기를**선택한 다음 **만들기를** 선택하여 웹 앱을 배포합니다. 완료되면 리소스개요에 제공된 URL에서 웹 **앱에** 액세스할 수 있습니다.

> [!NOTE]
> 웹 앱을 만들 때 권한 부여/인증을 구성할 수도 있습니다. 이 작업을 시작할 필요는 없습니다. 

### <a name="azure-cli"></a>Azure CLI

Azure 포털을 사용하는 대신 Azure CLI를 사용하여 리소스를 만들 수 있습니다. 계속하기 전에 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 설치해야 합니다. Azure CLI로 이미 작업 중인 경우 이 단계를 건너뛸 수 있습니다. 

이 명령에 대해 알아야 할 몇 가지 사항이 있습니다.

* `DNS_NAME_LABEL=aci-demo-$RANDOM`DNS 이름을 생성합니다. 
* 이 샘플에서는 리소스를 만드는 데 사용할 수 있는 리소스 그룹이 있다고 가정합니다. 구독과 연결된 유효한 리소스 그룹으로 바꿉습니다. `<resource_group_name>` 
* 리소스를 만들 위치를 지정해야 합니다. 웹 `<region name>` 앱의 원하는 지역으로 바꿉습니다. 
* 이 명령은 자동으로 EULA를 수락합니다.

Azure CLI에서 이 명령을 실행하여 샘플 레이블 지정 도구에 대한 웹 앱 리소스를 만듭니다. 

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resorunce_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8
  --command-line "./run.sh eula=accept"
```

### <a name="connect-to-azure-ad-for-authorization"></a>권한 부여를 위해 Azure AD에 연결

웹 앱을 Azure Active Directory에 연결하는 것이 좋습니다. 이렇게 하면 유효한 자격 증명을 가진 사용자만 로그인하여 웹 앱을 사용할 수 있습니다. Azure Active Directory에 연결하려면 [앱 서비스 앱 구성의](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) 지침에 따릅니다.

## <a name="open-source-on-github"></a>GitHub의 오픈 소스

OCR 양식 라벨링 도구는 GitHub의 오픈 소스 프로젝트로도 사용할 수 있습니다. 이 도구는 React + Redux를 사용하여 빌드된 웹 응용 프로그램으로 TypeScript로 작성되었습니다. 자세한 내용을 보거나 기여하려면 [OCR 양식 레이블 지정 도구를](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

빠른 시작 [레이블이 있는 Train을](./quickstarts/label-tool.md) 사용하여 도구를 사용하여 교육 데이터에 수동으로 레이블을 지정하고 감독 학습을 수행하는 방법을 알아봅니다.
