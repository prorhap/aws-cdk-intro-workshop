+++
title = "Project structure"
weight = 300
+++

## IDE 열기

이제 여러분이 선호하는 IDE를 이용해서 프로젝트를 열어 살펴볼 시간 입니다.

> VSCode 를 사용한다면, 프로젝트 디렉터리에서 `code .` 를 타이핑해서 실행할 수 있습니다.

## 프로젝트 디렉터리 살펴보기

여러분은 이런 형태의 구조를 볼 수 있을 것 입니다.:

![](./structure.png)

* __`lib/cdk-workshop-stack.ts`__ CDK 애플리케이션의 메인 스택으로 여러분들이 주요하게 작업하게 될 파일 입니다.
* `bin/cdk-workshop.ts` CDK 애플리케이션의 시작점 입니다.여기서는 `lib/cdk-workshop-stack.ts`에 정의된 스택을 로딩합니다.
* `package.json` npm 모듈의 manifest 파일 입니다. 여기에는 앱 이름, 버전, 의존성과 같은 정보와 "watch". "build" 와 같은 빌드 스크립트 정보를 포함합니다.
* `cdk.json` 은 앱을 어떻게 실행해야하는지 toolkit 에게 말해줍니다. 우리 예제에서는
  `"npx ts-node bin/cdk-workshop.ts"` 형태로 기술되어 있습니다.
* `tsconfig.json` 프로젝트의 [typescript configuration](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)
* `.gitignore` and `.npmignore` 에는 소스에서 그리고 모듈을 퍼블리싱 할 때 포함시키고 제외해야할 파일을 기술합니다.  
* `node_modules` 는 npm을 통해 관리하는, 프로젝트의 의존성 모듈을 포함합니다.

## Your app's entry point

`bin/cdk-workshop.ts` 파일을 살펴볼까요.

```js
#!/usr/bin/env node
import * as cdk from '@aws-cdk/core';
import { CdkWorkshopStack } from '../lib/cdk-workshop-stack';

const app = new cdk.App();
new CdkWorkshopStack(app, 'CdkWorkshopStack');
```

이 코드는 `lib/cdk-workshop-stack.ts` 에 있는 `CdkWorkshopStack` 클래스 파일을 로드하고 인스턴스화 합니다. 앞으로 이 파일은 거의 볼 필요 없어요.

## The main stack

`lib/cdk-workshop-stack.ts`를 열어볼까요. 이것은 우리 애플리케이션에서 핵심이 되는 부분 입니다.

```ts
import * as sns from '@aws-cdk/aws-sns';
import * as subs from '@aws-cdk/aws-sns-subscriptions';
import * as sqs from '@aws-cdk/aws-sqs';
import * as cdk from '@aws-cdk/core';

export class CdkWorkshopStack extends cdk.Stack {
  constructor(scope: cdk.App, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    const queue = new sqs.Queue(this, 'CdkWorkshopQueue', {
      visibilityTimeout: cdk.Duration.seconds(300)
    });

    const topic = new sns.Topic(this, 'CdkWorkshopTopic');

    topic.addSubscription(new subs.SqsSubscription(queue));
  }
}
```

여러분들이 보는 것 처럼, 우리의 앱은 샘플 CDK 스택을 갖고 생성되었습니다. (`CdkWorkshopStack`).

이 스택은 다음을 포함합니다:

- SQS Queue (`new sqs.Queue`)
- SNS Topic (`new sns.Topic`)
- Topic에 퍼블리싱 된 모든 메시지를 수신하도록 Queue를 구독하기 (`topic.addSubscription`)
