![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdXc7aY%2FbtrvL5jxqMf%2F6uOTqd4XlkhO4Yhk6RqLt0%2Fimg.png)

### Chrome API

extension과 현재 브라우저의 탭에서 방문 중인 웹 사이트 간 인터렉션을 위해 필요합니다.

### Content scripts

웹 페이지의 컨텍스트에서 실행되고 DOM 엘리먼트, 객체 및 메서드에 전체 엑세스 권한이 있는 js 파일입니다. 

Messaging passing API를 이용해서 react로 만든 extension과 컨텐츠 스크립트가 인터렉션할 수 있습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbmme33%2FbtrvLJnr9oT%2Fv8n4eLfeOAMGCnKj2BQpdk%2Fimg.png)

메세징 API와 인터렉션하기 위해서 3가지 사항이 필요합니다.

1\. Access to the Chrome API

2\. Permissions

3\. Content scripts

#### Access to the Chrome API

타입스크립트에서는 @types/chrome 를 설치합니다.

```sh
npm install @types/chrome --save-dev
```

#### Permissions

manifest.json 파일에 현재 탭에 대한 권한을 추가합니다.

```json
"permissions": [
   "activeTab"
],
```

#### Content scripts 

타입스크립트를 이용해 content scripts를 작성하기 위해서 craco 를 설치합니다. 

craco 를 사용하면 CRA 설정을 eject 없이 쉽게 커스터마이징할 수 있습니다.

```sh
npm install @craco/craco --save
```

```js
// craco.config.js

module.exports = {
   webpack: {
       configure: (webpackConfig, {env, paths}) => {
           return {
               ...webpackConfig,
               entry: {
                   main: [env === 'development' && require.resolve('react-dev-utils/webpackHotDevClient'),paths.appIndexJs].filter(Boolean),
                   content: './src/chromeServices/DOMEvaluator.ts',
               },
               output: {
                   ...webpackConfig.output,
                   filename: 'static/js/[name].js',
               },
               optimization: {
                   ...webpackConfig.optimization,
                   runtimeChunk: false,
               }
           }
       },
   }
}
```

빌드 명령어를 craco를 사용하도록 변경해줍니다.

```json
"build": "INLINE_RUNTIME_CHUNK=false craco build",
```

content scripts를 찾을 위치를 chrome에게 알려주기 위해 manifest.json 파일에 코드를 추가합니다.

```json
"content_scripts": [
   {
       "matches": ["http://*/*", "https://*/*"],
       "js": ["./static/js/content.js"]
   }
],
```

위와 같은 설정을 하면 크롬에서 content.js 파일을 모든 웹 사이트에 주입시켜 줍니다.

### 참고

[https://blog.logrocket.com/creating-chrome-extension-react-typescript/](https://blog.logrocket.com/creating-chrome-extension-react-typescript/)
