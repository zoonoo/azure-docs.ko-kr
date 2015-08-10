Azure 리소스 관리자와 함께 템플릿을 배포할 때 지정하고자 하는 값으로 매개 변수를 정의합니다. 템플릿은 모든 매개 변수 값이 포함 된 매개 변수라는 섹션을 포함합니다. 배포하는 프로젝트에 따라 또는 환경에 따라 달라지는 이러한 값에 대한 매개 변수를 정의해야 합니다. 항상 동일하게 유지되는 값으로 매개 변수를 정의하지 마십시오. 각 매개 변수 값은 배포되는 리소스를 정의하는 템플릿에 사용됩니다.

템플릿에서 각 매개 변수에 대해 설명할 것입니다.

### gatewayName

게이트웨이의 이름입니다. 이 게이트웨이에 API 앱을 등록합니다.

    "gatewayName": {
      "type": "string"
    }

### apiAppName

만들 API 앱의 이름입니다. 이름은 8\~50자를 포함해야 합니다.
    
    "apiAppName": {
      "type": "string"
    }

### apiAppSecret

API 앱에 대한 암호입니다. 이 값은 base64로 인코딩된 문자열이어야 합니다.

    "apiAppSecret": {
      "type": "securestring"
    }

### location

새 API 앱에 대한 위치입니다.

    "location": {
      "type": "string"
    }

<!---HONumber=July15_HO5-->