---
title: "나의 첫 npm 패키지, ww-timer 회고"
date: 2024-04-29 00:03:00 +0900
author:
  name: "박 찬영"
  link: https://github.com/univdev
categories: [Tech, Talk]
tags: [npm, package, ww-timer]
image:
  path: /assets/post_images/2024-04-29-my-first-npm-library/2024-04-29-00-13-45.png
  alt: "나의 첫 패키지, ww-timer 회고"
---
![나의 첫 패키지, ww-timer 회고](/assets/post_images/2024-04-29-my-first-npm-library/2024-04-29-00-13-45.png)

# 나의 첫 패키지
저는 한 달 쯤 전에 npm에 [ww-timer라는 라이브러리](https://www.npmjs.com/package/ww-timer)를 배포했습니다.<br>
현재 개발하고 있는 서비스는 글로벌 시장을 목표로 하고 있는 생산성 앱인데, 이 앱의 기능 중 하나로 타이머가 들어갈 예정입니다.<br>
웹에서 Timer를 개발하려면 필히 `setTimeout`이나 `setInterval`을 사용해야 하는데 이러한 비동기 함수는 브라우저의 작업량에 따라 실행 순위가 후순위로 밀리고 밀려 결국에는 실제 시간과 엄청난 차이가 벌어지게 되는 문제가 발생했습니다.<br>
이러한 문제는 결국 Web Worker를 사용하여 해결하였으나 Web Worker를 사용하는 방법 자체가 파일의 파편화와 선언적이지 못한 코드를 만들었기 때문에 이 문제를 해결하고자 별도의 패키지로 제작하게 되었습니다.

# 문제점
기본적으로 브라우저는 매우 바쁩니다. 특히나 오늘날의 웹페이지는 Javascript로 작성되고 동작하는 경우가 매우 많아 브라우저는 Javascript를 처리하기 위해 앞단에서 매우 바쁜 일을 처리합니다.<br>
Javascript는 단일 스레드이기에 한번에 하나의 업무만을 처리할 수 있는데 심지어 그 업무 처리 방식도 '동기적으로 처리 될 수 있는 업무'를 우선하여 처리하고, 손이 남으면 비동기 업무를 수행합니다.<br>
그렇기에 `setTimeout`이나 `setInterval`과 같은 비동기 함수는 브라우저가 얼마나 바쁘냐에 따라 실행 시간의 정확도가 매우 떨어지는 결과를 야기할 수 있습니다.<br>
이는 일반적인 경우에는 별 문제가 없으나 타이머와 같이 매우 정밀한 시간 제어가 필요한 어플리케이션의 경우 치명적인 결과를 발생 시킬 수 있습니다.

# Web Worker를 이용한 해결 방법
현실 세계에서 일이 바쁘다면 직원을 더 채용합니다.<br>
자바스크립트의 세계에서는 매우 바쁜 기본 스레드의 업무를 덜어주기 위해 [Web Worker](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API)라는 것을 사용합니다.<br>
간단하게 설명하자면 DOM 처리와 같은 브라우저의 화면 처리 업무를 제외한 대부분의 업무를 수행할 수 있는 노동자를 고용한다고 볼 수 있어요.

## Web Worker 코드 살펴보기
ww-timer는 지정한 시간마다 기본 스레드에게 메시지를 보내는 아주 단순한 코드로 구성 되어 있습니다.
{% raw %}
```javascript
(() => {
  self.addEventListener('message', (event) => {
    if (typeof event.data !== 'object') {
      throw new Error('Invalid data');
    }

    if (typeof event.data.interval !== 'number') {
      throw new Error('Invalid interval');
    }

    self.setInterval(() => {
      self.postMessage('called');
    }, event.data.interval);
  });
})();
```
{% endraw %}

Web Worker에서의 `self`는 Web Worker가 동작하고 있는 Background Thread를 의미합니다.<br>
이 곳에서는 `window`객체를 참조해야 사용할 수 있는 API를 제외한 대부분의 API를 사용할 수 있습니다.

Web Worker는 `postMessage` 함수를 이용하여 Web Worker가 동작하는 기본 스레드에게 메시지를 전송할 수 있습니다.<br>
또한 `onMessage` 이벤트를 사용하여 기본 스레드가 Web Worker에게 보내오는 메시지 역시 수신이 가능합니다.

이를 바탕으로 위 코드의 동작 원리를 해석하자면 다음과 같습니다.
1. 기본 스레드에서 Web Worker를 생성한다.
2. Web Worker에게 `{ interval: 1000 }`이라는 메시지를 보낸다고 가정한다.
3. Web Worker는 해당 메시지를 토대로 `interval`을 생성한다.
4. 기본 스레드에서 보내온 `interval`만큼의 시간이 지날 때 마다 기본 스레드에게 `called` 라는 메시지를 전송한다.

## 클라이언트 코드 살펴보기
Web Worker의 실행을 돕는 코드는 다음과 같이 작성되어 있습니다.

{% raw %}
```javascript
class WWTimer {
  lastTime = Date.now();

  isActive = false;

  wwInstance = null;

  constructor(callback, interval = 1000) {
    if (typeof callback !== 'function') {
      throw new Error('Callback function is required.');
    }
    if (interval <= 1) {
      throw new Error('Interval must be greater than 1.');
    }

    this.lastTime = Date.now();
    this.isActive = false;
    this.workerURI = getWorkerURI(worker);

    this.wwInstance = new Worker(this.workerURI);

    this.wwInstance.postMessage({ interval });

    this.wwInstance.addEventListener('message', (event) => {
      if (event.data === 'called' && this.isActive) {
        const elapsedTime = Date.now() - this.lastTime;
        this.lastTime = Date.now();
        callback({ currentTime: new Date(), timeElapsed: elapsedTime });
      }
    });
  }

  start() {
    this.isActive = true;
  }

  pause() {
    this.isActive = false;
  }

  destroy() {
    this.wwInstance.terminate();
  }
}

export default WWTimer;
```
{% endraw %}

조금 코드가 길어지긴 했는데 원리 자체는 매우 단순합니다.
1. `new WWTimer(callback, interval)`를 사용하여 인스턴스를 생성합니다.
2. 생성자에 의해 Web Worker 인스턴스가 생성 됩니다.
3. 사용자가 두번째 매개변수로 전달한 `interval`을 Web Worker가 이해할 수 있도록 객체로 만들어 전송합니다.
4. Web Worker가 메시지를 전송할 때마다 현재 시간을 기록합니다.
5. 첫번째 실행 이후에는 이전 호출 시간과 비교하여 정확히 얼마의 시간이 지난 다음에 `interval`이 실행 되었는지를 저장하는 `timeElapsed` 프로퍼티와 현재 시간을 담은 `currentTime` 프로퍼티를 생성합니다.
6. 이 데이터를 `callback`의 매개변수로 전달하여 보다 유연한 처리를 가능하게 합니다.

# 트러블 슈팅
## 번들링 이후 Web Worker의 URL을 못찾는 이슈
Web Worker를 브라우저에서 생성할 때는 다음의 API를 사용합니다.
```javascript
const worker = new Worker('worker 파일의 경로');
```
매개변수로 포함 되어야 하는 Worker 파일의 경로는 반드시 **브라우저가 접근할 수 있는 경로**여야 합니다.<br>
하지만 npm 패키지는 번들러에 의해 번들링 되고 사용자의 컴퓨터에 다운받게 되는 순간 `node_modules`라는 폴더에 포함 되는 코드 조각이 되기 때문에 브라우저가 접근할 수 있는 경로 자체가 존재하지 않습니다.

이 이슈는 Web Worker에서 동작할 소스코드를 Javascript 소스코드가 아닌 일반 평문으로 취급하면서 해결할 수 있었습니다.<br>
Javascript에는 자체적으로 파일을 생성하고 이 파일에 대한 경로를 생성할 수 있는 강력한 API가 존재합니다.<br>
바로 [Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob)이라는 API입니다.

Blob API는 자바스크립트로 바이너리 파일을 생성할 수 있는 API입니다. (심지어 브라우저에서도 사용 가능한!)<br>
```javascript
const blob = new Blob(['File Content'], { type: 'file mimetype' });
```
이런 식으로 사용할 수 있는데, 첫번째 매개변수에는 파일을 구성할 데이터를 문자열 형태로 기입합니다.<br>
두번째 매개변수에는 해당 파일을 취급하는 방식(?) 에 대해 기입합니다.<br>
대표적으로는 `text/plain` 혹은 아래 예시에 등장하는 `text/javascript`가 있겠네요.

Web Worker 파일을 메모리에 저장하고, 메모리에 저장 된 Web Worker 파일에 접근할 수 있도록 URL을 생성해주는 함수를 아래와 같이 만들었습니다.
```javascript
export const getWorkerURI = (workerScript) => {
  if (typeof workerScript !== 'string') {
    throw new Error('Worker script must be a string');
  }

  const blob = new Blob([workerScript], { type: 'text/javascript' });
  const url = URL.createObjectURL(blob);

  window.setTimeout(() => URL.revokeObjectURL(url));

  return url;
};
```

1. 매개변수로 Web Worker에서 동작할 소스코드를 매개변수로 전달한다.
2. 이를 Blob API를 이용하여 Javascript File로 만들고 메모리에 저장한다.
3. 메모리에 저장 된 파일을 URL API를 이용하여 실제 접근 가능한 URL로 만든다.
4. URL을 생성했으니 더이상 필요 없어진 URL 객체를 메모리에서 제거한다. (Worker API에 전달 될 시간은 필요하니까 비동기로 처리)
5. URL을 반환한다.

이로써 `node_modules`에 포함 되는 패키지에서도 Web Worker를 사용할 수 있게 되었습니다.
## 배포파일 최적화 이슈
![엄청나게 큰 용량](/assets/post_images/2024-04-29-my-first-npm-library/2024-04-29-01-03-38.png)

생각보다 별거 없는 코드와 기능인데도 불구하고 용량이 매우 큽니다.<br>
결론부터 말씀드리자면 이는 제 어처구니 없는 실수에 인한 것으로 불필요한 파일이 같이 업로드 되었기 때문입니다.

[ww-timer 패키지 레포지토리](https://github.com/univdev/ww-timer)에는 `examples`라는 폴더가 있습니다.<br>
다른 오픈소스들과 마찬가지로 이를 실제 프로젝트에서 어떻게 사용할 수 있는지를 나타낸 사용 예시 폴더입니다.

이 예시 파일에는 React를 사용한 프로젝트에서 어떻게 사용할 수 있는지에 대한 예시가 존재했는데, 이 파일이 npm 패키지에 그대로 올라가버린 것입니다.<br>
![파일 목록에 떡하니 존재하는 examples 폴더](/assets/post_images/2024-04-29-my-first-npm-library/2024-04-29-01-14-26.png)
_존재감이 독보적인 examples 폴더_

이 이슈는 `.npmignore` 파일을 수정하면서 해결하였습니다.

`.npmignore`는 npm 저장소에 업로드 할 파일과 안 할 파일을 구분하기 위한 용도로 사용 됩니다.<br>
여담이지만 만약 레포지토리에 `.npmignore`가 없다면 `.gitignore`로 대체한다고 합니다.

이 파일에
```npmignore
examples/
```
위와 같이 추가함으로써 용량 문제를 해결하였습니다.

![examples 폴더가 빠진 파일 목록](/assets/post_images/2024-04-29-my-first-npm-library/2024-04-29-01-13-31.png)
_examples 폴더가 빠진 파일 목록 (그 외에도 몇 개 더 빠진건 비밀)_
# 회고
![주간 다운로드 237회!](/assets/post_images/2024-04-29-my-first-npm-library/2024-04-29-01-24-33.png){: width="340" }
_주간 다운로드 237회!_

돌이켜 생각해보면 정말 보잘 것 없는 기능이고 API도 부족한 바가 많다고 생각하는 라이브러리이지만 여태까지 npm 라이브러리를 써보기만 하다가 직접 만들어보니 많은 것을 배울 수 있었다고 생각합니다.<br>
특히나 패키지를 배포한 1주일 간 제가 만든 패키지의 다운로드 건 수가 **200건 이상**이 찍힌 것을 목격했을 때는 기분이 정말 좋았습니다.<br>
마치 어렸을 때 플래시 게임 커뮤니티에 투고했었던 게임이 월간 베스트에 올라갔었던 기분과 비슷했달까요.<br>
비록 지금은 주간 다운로드 횟수가 10회도 못넘기고 있지만 그대로 매우 뜻깊은 경험이었다고 생각합니다.
