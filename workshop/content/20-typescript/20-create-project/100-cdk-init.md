+++
title = "cdk init"
weight = 100
+++

## Create project directory

여러분의 시스템에 빈 디렉터리를 생성합니다.

```
mkdir cdk-workshop && cd cdk-workshop
```

## cdk init

`cdk init` 명령을 사용해서 새로운 TypeScript CDK 프로젝트를 생성합니다.

```
cdk init sample-app --language typescript
```

화면에서 다음과 같은 내용을 확인할 수 있습니다.

```
Applying project template app for typescript
Initializing a new git repository...
Executing npm install...
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN tst@0.1.0 No repository field.
npm WARN tst@0.1.0 No license field.

# Welcome to your CDK TypeScript project!

You should explore the contents of this project. It demonstrates a CDK app with an instance of a stack (`CdkWorkshopStack`)
which contains an Amazon SQS queue that is subscribed to an Amazon SNS topic.

The `cdk.json` file tells the CDK Toolkit how to execute your app.

## Useful commands

 * `npm run build`   compile typescript to js
 * `npm run watch`   watch for changes and compile
 * `npm run test`    perform the jest unit tests
 * `cdk deploy`      deploy this stack to your default AWS account/region
 * `cdk diff`        compare deployed stack with current state
 * `cdk synth`       emits the synthesized CloudFormation template
```

화면에서 볼 수 있듯이, 시작하는 데 유용한 여러 명령을 확인할 수 있습니다.

## See Also

- [AWS CDK Command Line Toolkit (cdk) in the AWS CDK User Guide](https://docs.aws.amazon.com/CDK/latest/userguide/tools.html)
