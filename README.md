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



