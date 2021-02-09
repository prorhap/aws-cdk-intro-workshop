+++
title = "npm run watch"
weight = 200
+++

## TypeScript 코드 컴파일하기

TypeScript 코드는 JavaScript로 컴파일해야 하기 때문에, 소스 파일들을 변경할 때 마다 `.js` 로 컴파일 해야합니다.

{{% notice info %}} 이 단계는 매우 중요한 단계로 마지막까지 "watch" 터미널 세션을 열어두어야 합니다.{{% /notice %}}

## 새로운 터미널 창 열기

**새로운** 터미널 세션을 엽니다. 이 워크샵이 진행되는 동안 백그라운드로 이 창을 열린 채로 유지할 것 입니다.

## 변경된 파일 watching

프로젝트 디렉터리에서:

```
cd cdk-workshop
```

그리고:

```
npm run watch
```

그러면, 다음을 확인할 수 있습니다.

```
Starting compilation in watch mode...
Found 0 errors. Watching for file changes.
...
```

이것은 TypeScript 컴파일러(`tsc`)를 "watch" 모드로 시작합니다. 그리고 이것은 프로젝트 디렉터리를 모니터링 하면서  `.ts` 파일이 변경될 때 마다 `.js` 로 자동으로 컴파일 합니다.

----

‼️ 워크샵 동안에는 `watch` 명령을 실행한 이 터미널 창을 열린채로 유지해주세요.

----
