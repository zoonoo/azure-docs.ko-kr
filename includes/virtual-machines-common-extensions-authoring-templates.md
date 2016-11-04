## Azure 리소스 관리자 템플릿 개요
Azure Resource Manager 템플릿을 사용하면 리소스 간의 종속성을 정의하여 JSON 언어에서 Azure IaaS 인프라를 선언적으로 지정할 수 있습니다. Azure Resource Manager 템플릿에 대한 자세한 개요는 아래 문서를 참조하세요.

[리소스 그룹 개요](../articles/resource-group-overview.md)

## VM 확장에 대한 샘플 템플릿 코드 조각
Azure Resource Manager 템플릿의 일부로 VM 확장을 배포하려면 템플릿에 확장 구성을 선언적으로 지정해야 합니다. 다음은 확장 구성을 지정하는 형식입니다.

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

위에서 볼 수 있는 것처럼 확장 템플릿은 다음 두 주요 부분으로 구성됩니다.

1. 확장 이름, 게시자 및 버전
2. 확장 구성

## 모든 확장에 대한 게시자, 유형 및 typeHandlerVersion 식별
Azure VM 확장은 Microsoft 및 신뢰할 수 있는 타사 게시자가 게시하며 각 확장은 게시자, 유형 및 typeHandlerVersion으로 고유하게 식별됩니다. 다음과 같이 결정될 수 있습니다.

<!---HONumber=AcomDC_0330_2016-->