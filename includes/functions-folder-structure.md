
지정된 함수 앱의 모든 함수에 대한 코드는 호스트 구성 파일과 하나 이상의 하위 폴더를 포함하는 루트 폴더에 포함되어 있습니다. 각 하위 폴더에는 다음 예제와 같이 별도의 함수에 대한 코드가 포함되어 있습니다.

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

*host.json* 파일은 일부 런타임별 구성을 포함하며 함수 앱의 루트 폴더에 있습니다. 사용 가능한 설정에 대한 자세한 내용은 WebJobs.Script 리포지토리 wiki에서 [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)을 참조하세요.

각 함수에는 하나 이상의 코드 파일, function.json 구성 및 기타 종속성을 포함하는 폴더가 있습니다.

<!---HONumber=AcomDC_0824_2016-->