---
title: Azure Cognitive Services를 Azure Stack에 배포 | Microsoft Docs
description: Azure Stack에 Azure Cognitive Services를 배포 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: mabrigg
ms.reviewer: guanghu
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 1ccbe8b268725cf3d0747486a20e0597d023662e
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55890606"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack"></a>Azure Stack에 Azure Cognitive Services 배포

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

> [!Note]  
> Azure Stack에서 azure Cognitive Services 미리 보기입니다.

Azure Stack에서 컨테이너 지원과 Azure Cognitive Services를 사용할 수 있습니다. Azure Cognitive Services에서 컨테이너 지원 같은 Azure에서 사용할 수 있는 풍부한 Api를 사용할 수 있습니다. 컨테이너 사용 유연성 배포 배달 서비스를 호스트 하는 위치에서 사용 하도록 설정 [Docker 컨테이너](https://www.docker.com/what-container)합니다. 컨테이너 지원은 현재 부분을 포함 하 여 Azure Cognitive Services의 하위 집합에 대 한 미리 보기로 제공 [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)를 [얼굴](https://docs.microsoft.com/azure/cognitive-services/face/overview), 및 [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), 및 [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS).

컨테이너 화는 소프트웨어 배포는 컨테이너 이미지로 패키지 되는 응용 프로그램 또는 서비스를 포함 하 여 해당 종속성 및 구성 하는 방법입니다. 거의 또는 전혀 수정 하면 컨테이너 호스트 이미지를 배포할 수 있습니다. 각 컨테이너는 기본 운영 체제 및 기타 컨테이너에서 격리 됩니다. 자체 시스템에는 이미지를 실행 하는 데 필요한 구성 요소에 있습니다. 컨테이너 호스트에 가상 컴퓨터 보다 적은 공간을 차지 합니다. 또한 단기 작업에 대 한 이미지에서 컨테이너를 만들 수 없습니다 하 고 더 이상 필요를 제거 합니다.

## <a name="use-containers-with-cognitive-services-on-azure-stack"></a>Azure Stack에서 Cognitive Services를 사용 하 여 컨테이너를 사용 합니다.

- **데이터 제어**  
  앱 사용자가 Cognitive Services를 사용 하는 동안 해당 데이터를 제어할 수 있습니다. Cognitive Services는 글로벌 Azure에 데이터 또는 공용 클라우드를 보낼 수 없습니다는 앱 사용자에 게 제공할 수 있습니다.

- **모델 업데이트 제어**  
  사용자 버전 및 해당 솔루션에 배포 된 모델의 업데이트를 제공 합니다.

- **이식 가능한 아키텍처**  
  사설, 공용 클라우드로 솔루션을 배포할 수 있도록 하는 이식 가능한 앱 아키텍처를 생성할 수 있도록 클라우드 온-프레미스 또는 지 합니다. Azure stack에서 Kubernetes 클러스터 또는 Azure Kubernetes Service, Azure Container Instances에 컨테이너를 배포할 수 있습니다. 자세한 내용은 [Azure Stack에 Kubernetes 배포](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)를 참조하세요.

- **높은 처리량과 짧은 대기 시간**  
   앱 사용자에 게 높은 처리량과 짧은 대기 시간에 대 한 트래픽 급증을 사용 하 여 크기를 조정 하는 기능을 제공 합니다. 해당 응용 프로그램 논리 및 데이터에 가까운 실제로 Azure Kubernetes Service에서 실행 하는 Cognitive Services를 사용 하도록 설정 합니다.

Azure Stack을 사용 하 여 고가용성 및 탄력적인 크기 조정에 대 한 응용 프로그램 컨테이너와 함께 Kubernetes 클러스터에서 Cognitive Services 컨테이너를 배포 합니다. App Services, 함수, Blob storage, SQL 또는 mySQL 데이터베이스를 작성 하는 구성 요소를 사용 하 여 Cognitive services를 결합 하 여 응용 프로그램을 개발할 수 있습니다. 

Cognitive Services 컨테이너에 대 한 자세한 내용은로 이동 [Azure Cognitive Services에서 컨테이너 지원](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support)합니다.

## <a name="deploy-the-azure-face-api"></a>Azure의 Face API를 배포 합니다.

이 문서에서는 Azure Stack의 Kubernetes 클러스터에서 Azure Face API를 배포 하는 방법을 설명 합니다. Azure Stack Kubernetes 클러스터에 다른 cognitive services 컨테이너를 배포 하려면 동일한 접근 방식을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

시작 하기 전에를 해야 합니다.

1.  Azure Cognitive Services Container Registry에서 얼굴 컨테이너 이미지를 컨테이너 레지스트리에 대 한 액세스를 요청 합니다. 세부 정보 섹션으로 이동에 대 한 [개인 컨테이너 레지스트리에 대 한 액세스를 요청](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry)합니다.

2.  Azure Stack에서 Kubernetes 클러스터를 준비 합니다. 문서를 따르면 [Azure Stack에 Kubernetes 배포](azure-stack-solution-template-kubernetes-deploy.md)합니다.

## <a name="create-azure-resources"></a>Azure 리소스 만들기

Face, LUIS, 또는 텍스트 인식 컨테이너를 각각 미리 보기에 Azure에서 Cognitive Service 리소스를 만듭니다. Cognitive 서비스 컨테이너를 인스턴스화하는 리소스의 구독 키 및 끝점 URL을 사용 하도록 해야 합니다.

1.  Azure Portal에서 Azure 리소스를 만듭니다. 얼굴 컨테이너를 미리 보려면 하려는 경우 해당 글꼴 리소스를 Azure portal에서 만들어야 합니다. 자세한 내용은 [빠른 시작: Azure Portal에서 Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)를 참조하세요.

    >  [!Note]  
    >  얼굴 또는 Computer Vision 리소스 F0 가격 책정 계층을 사용 해야 합니다.

2.  Azure 리소스에 대한 엔드포인트 URL 및 구독 키를 가져옵니다. Azure 리소스를 만든 후 미리 보기에 대 한 해당 얼굴, LUIS, 또는 텍스트 인식 컨테이너를 인스턴스화하는 리소스의 구독 키 및 끝점 URL을 사용 해야 합니다.

## <a name="create-a-kubernetes-secret"></a>Kubernetes 비밀 만들기 

Kubectl take 사용에는 개인 컨테이너 레지스트리에 액세스 하도록 비밀 명령을 만듭니다. 바꿉니다 **&lt;사용자 이름&gt;** 사용자 이름 및 **&lt;암호&gt;** 자격 증명에 제공 된 암호를 사용 하 여 Azure에서 받은 Cognitive 서비스 팀입니다.

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>준비 된 YAML 파일 구성

YAML을 사용 하려는 cognitive 서비스 Kubernetes 클러스터에서의 배포를 단순화 하는 파일을 구성 합니다.

YAML 샘플 구성 파일을 Azure Stack에 얼굴 서비스 배포는 다음과 같습니다.

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

이 YAML 파일을 구성, Azure Container Registry에서 cognitive service 컨테이너 이미지를 가져오는 데 사용 하는 암호를 사용 합니다. 하 고 암호 파일을 사용 하 여 컨테이너의 특정 복제본을 배포 합니다. 또한 사용자는이 서비스를 외부에서 액세스할 수 있는지 확인 하는 부하 분산 장치를 만듭니다.

키 필드에 대 한 세부 정보:

| 필드 | 메모 |
| --- | --- |
| replicaNumber | 만들 인스턴스의 초기 복제본을 정의 합니다. 물론 배포 후 나중에 확장할 수 있습니다. |
| ImageLocation | ACR의 특정 cognitive 서비스 컨테이너 이미지의 위치를 나타냅니다. 예를 들어, 얼굴 서비스: `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |끝점 URL의 단계에서 기록해 둔 [Azure 리소스 만들기](#create-azure-resources) |
| ApiKey | 단계에서 기록해 둔 구독 키 [Azure 리소스 만들기](#create-azure-resources) |
| SecretName | 개인 컨테이너 레지스트리에 액세스 하도록 비밀 만들기의 단계에서는 앞서 언급 한 비밀 이름 |

## <a name="deploy-the-cognitive-service"></a>Cognitive 서비스 배포

Cognitive 서비스 컨테이너에 배포 하려면 다음 명령을 사용 하 여

```bash  
    Kubectl apply -f <yamlFineName>
```
배포 하는 방법을 모니터링 하려면 다음 명령 사용 합니다. 
```bash  
    Kubectl get pod – watch
```

## <a name="test-the-cognitive-service"></a>Cognitive 서비스 테스트

액세스는 [OpenAPI 사양](https://swagger.io/docs/specification/about/) (이전의 Swagger 사양)에서 인스턴스화된는 컨테이너에서 지 원하는 작업을 설명 하는 합니다 **swagger/** 해당 컨테이너에 대 한 상대 URI입니다. 예를 들어 다음 URI는 앞의 예제에서 인스턴스화된 감정 분석 컨테이너에 대한 OpenAPI 사양에 대한 액세스를 제공합니다.

```HTTP  
http:<External IP>:5000/swagger
```

다음 명령에서 외부 IP 주소를 가져올 수 있습니다. 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>Python 사용 하 여 services 사용해 보기

Cognitive services에 Azure Stack에서 몇 가지 간단한 Python 스크립트를 실행 하 여 유효성 검사를 시도할 수 있습니다. 에 대 한 공식 Python 빠른 시작 샘플 [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)를 [얼굴](https://docs.microsoft.com/azure/cognitive-services/face/overview), 및 [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), 및 [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) ( LUIS) 참조용입니다.

Python 앱 컨테이너에서 실행 되는 서비스를 실행할 수 있도록 염두에 두 가지 필요성 가지가 있습니다. 
1. 컨테이너에서 cognitive services 인증에 대 한 하위 키 필요가 없지만 여전히 SDK를 충족 하는 자리 표시자 문자열을 입력 해야 합니다. 
2. 실제 서비스 끝점 IP 주소는 base_URL 대체 

샘플 Python 스크립트 사용 얼굴 서비스를 검색 하 여 이미지에서 얼굴 프레임 Python SDK는 다음과 같습니다. 

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>다음 단계

[설치 및 Computer Vision API 컨테이너를 실행 하는 방법입니다.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[설치 하 고 Face API 컨테이너를 실행 하는 방법](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers)

[설치 및 Text Analytics API 컨테이너를 실행 하는 방법](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[설치 및 언어 이해 (LIUS) 컨테이너를 실행 하는 방법](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)
