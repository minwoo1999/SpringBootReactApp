# SpringBootReactApp

## 빌드 시 Spring과 React.js가 동시에 빌드되어 같은 포트로 동작하도록 하는 것이 이 프로젝트의 목표입니다.

참고 사이트

* https://velog.io/@ohzzi/SpringBoot-React-%ED%95%9C-%EB%B2%88%EC%97%90-%EB%B9%8C%EB%93%9C%ED%95%98%EA%B8%B0

## 프론트엔드 작성

### React.js proxy 설정

<pre><code>


{
  "name": "test",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "@testing-library/jest-dom": "^5.16.5",
    "@testing-library/react": "^13.4.0",
    "@testing-library/user-event": "^13.5.0",
    "axios": "^1.2.0",
    "http-proxy-middleware": "^2.0.6",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.4.3",
    "react-scripts": "5.0.1",
    "sweetalert2": "^11.6.14",
    "web-vitals": "^2.1.4"
  },
  "proxy": "http://localhost:8080",
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "eslintConfig": {
    "extends": [
      "react-app",
      "react-app/jest"
    ]
  },
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  }
}






</code></pre>

## 백엔드 설정

### CrossOrigin 설정

* *Crossorigin:웹 페이지의 제한된 자원을 외부 도메인에서 접근을 허용해주는 메커니즘이다.

<code><pre>

@CrossOrigin(origins = "*")
@RestController
@RequiredArgsConstructor
public class DevController {

    @Autowired
    private SecurityService securityService;

    @RequestMapping(value = "/home", method = RequestMethod.GET)
    @CrossOrigin(origins = "http://localhost:3000")
    public String getAuthInfo(HttpServletRequest req) throws ExpiredJwtException {
        

            **자신의 소스코드 및 String 리턴값 설정
    }

}
</code></pre>

### 백엔드 빌드 시 FrontEnd도 같이 빌드


<code><pre>
def frontendDir = "$projectDir/src/main/test_dev"           //이 경로의 프론트의 경로를 적어주면됩니다.

sourceSets {
	main {
		resources { srcDirs = ["$projectDir/src/main/resources"]    //빌드된 파일이 위치할 장소
		}
	}
}

processResources { dependsOn "copyReactBuildFiles" }

task installReact(type: Exec) {
	workingDir "$frontendDir"
	inputs.dir "$frontendDir"
	group = BasePlugin.BUILD_GROUP
	if (System.getProperty('os.name').toLowerCase(Locale.ROOT).contains('windows')) {
		commandLine "npm.cmd", "audit", "fix"
		commandLine 'npm.cmd', 'install' }
	else {
		commandLine "npm", "audit", "fix" commandLine 'npm', 'install'
	}
}

task buildReact(type: Exec) {
	dependsOn "installReact"
	workingDir "$frontendDir"
	inputs.dir "$frontendDir"
	group = BasePlugin.BUILD_GROUP
	if (System.getProperty('os.name').toLowerCase(Locale.ROOT).contains('windows')) {
		commandLine "npm.cmd", "run-script", "build"
	} else {
		commandLine "npm", "run-script", "build"
	}
}

task copyReactBuildFiles(type: Copy) {
	dependsOn "buildReact"
	from "$frontendDir/build"
	into "$projectDir/src/main/resources/static"
}


</code></pre>

### 백엔드에서는 생성된 index.html 연결이 되야한다.(에러처리시 index로 이동시키는 코드)

<code><pre>

package com.devtest.devtest.controller;

import org.springframework.boot.web.servlet.error.ErrorController;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class WebController implements ErrorController {
    // 백엔드에서 React.js 라우터에 있는 주소로 주소를 임의 변경하면,
    // 매핑되는 주소가 없으므로 404 오류가 생긴다.
    // 이를 방지하기 위해 에러가 발생하면 프론트엔드에서 작성한 frontend/src/index.html을 전송한다.
    @GetMapping({ "/", "/error" })
    public String index() {
        return "index";
    }

    /*400에러 발생 시 getErrorPath() 호출*/
    public String getErrorPath() {
        return "/error";
    }
}

</code></pre>

### 백엔드: application properties에서 정적파일 view 매핑

spring.thymeleaf.prefix=classpath:/static/
spring.mvc.view.suffix=.html



### 여기까지 완료하게되면 8080포트만 키면 react정보를 불러올수있게된다.




