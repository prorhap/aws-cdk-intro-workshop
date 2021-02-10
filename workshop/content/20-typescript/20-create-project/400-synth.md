+++
title = "cdk synth"
weight = 400
+++

## Synthesize a template from your app

AWS CDK 앱은 코드를 사용해서 작성하는 인프라의 **정의** 일 뿐입니다. CDK 앱을 실행하면, 여러분의 애플리케이션에 정의된 각 스택에 대한 AWS CloudFormation 템플릿을 생성합니다. (CDK 용어를 사용해서 말하면, **synthesize** 한다고 합니다.)

CDK 앱을 synthesize 하기 위해서는 `cdk synth` 명령을 사용합니다. 샘플 앱을 synthesize 해볼까요.

{{% notice info %}} CDK CLI는 cdk.json 파일과 같은 디렉터리에서 실행해야 합니다. 만약 터미널에서 디렉터리를 변경한다면 다시 원래 위치로 돌아와서 명령을 실행해주세요.{{% /notice %}}

```
cdk synth
```

다음과 같은 CloudFormation 템플릿을 출력하게 됩니다.

```yaml
Resources:
  CdkWorkshopQueue50D9D426:
    Type: AWS::SQS::Queue
    Properties:
      VisibilityTimeout: 300
    Metadata:
      aws:cdk:path: CdkWorkshopStack/CdkWorkshopQueue/Resource
  CdkWorkshopQueuePolicyAF2494A5:
    Type: AWS::SQS::QueuePolicy
    Properties:
      PolicyDocument:
        Statement:
          - Action: sqs:SendMessage
            Condition:
              ArnEquals:
                aws:SourceArn:
                  Ref: CdkWorkshopTopicD368A42F
            Effect: Allow
            Principal:
              Service: sns.amazonaws.com
            Resource:
              Fn::GetAtt:
                - CdkWorkshopQueue50D9D426
                - Arn
        Version: "2012-10-17"
      Queues:
        - Ref: CdkWorkshopQueue50D9D426
    Metadata:
      aws:cdk:path: CdkWorkshopStack/CdkWorkshopQueue/Policy/Resource
  CdkWorkshopQueueCdkWorkshopStackCdkWorkshopTopicD7BE96438B5AD106:
    Type: AWS::SNS::Subscription
    Properties:
      Protocol: sqs
      TopicArn:
        Ref: CdkWorkshopTopicD368A42F
      Endpoint:
        Fn::GetAtt:
          - CdkWorkshopQueue50D9D426
          - Arn
    Metadata:
      aws:cdk:path: CdkWorkshopStack/CdkWorkshopQueue/CdkWorkshopStackCdkWorkshopTopicD7BE9643/Resource
  CdkWorkshopTopicD368A42F:
    Type: AWS::SNS::Topic
    Metadata:
      aws:cdk:path: CdkWorkshopStack/CdkWorkshopTopic/Resource
  CDKMetadata:
    Type: AWS::CDK::Metadata
    Properties:
      Modules: aws-cdk=1.21.1,@aws-cdk/aws-cloudwatch=1.21.1,@aws-cdk/aws-iam=1.21.1,@aws-cdk/aws-kms=1.21.1,@aws-cdk/aws-sns=1.21.1,@aws-cdk/aws-sns-subscriptions=1.21.1,@aws-cdk/aws-sqs=1.21.1,@aws-cdk/core=1.21.1,@aws-cdk/cx-api=1.21.1,@aws-cdk/region-info=1.21.1,jsii-runtime=node.js/v13.6.0
    Condition: CDKMetadataAvailable
Conditions:
  CDKMetadataAvailable:
    Fn::Or:
      - Fn::Or:
          - Fn::Equals:
              - Ref: AWS::Region
              - ap-east-1
          - Fn::Equals:
              - Ref: AWS::Region
              - ap-northeast-1
          - Fn::Equals:
              - Ref: AWS::Region
              - ap-northeast-2
          - Fn::Equals:
              - Ref: AWS::Region
              - ap-south-1
          - Fn::Equals:
              - Ref: AWS::Region
              - ap-southeast-1
          - Fn::Equals:
              - Ref: AWS::Region
              - ap-southeast-2
          - Fn::Equals:
              - Ref: AWS::Region
              - ca-central-1
          - Fn::Equals:
              - Ref: AWS::Region
              - cn-north-1
          - Fn::Equals:
              - Ref: AWS::Region
              - cn-northwest-1
          - Fn::Equals:
              - Ref: AWS::Region
              - eu-central-1
      - Fn::Or:
          - Fn::Equals:
              - Ref: AWS::Region
              - eu-north-1
          - Fn::Equals:
              - Ref: AWS::Region
              - eu-west-1
          - Fn::Equals:
              - Ref: AWS::Region
              - eu-west-2
          - Fn::Equals:
              - Ref: AWS::Region
              - eu-west-3
          - Fn::Equals:
              - Ref: AWS::Region
              - me-south-1
          - Fn::Equals:
              - Ref: AWS::Region
              - sa-east-1
          - Fn::Equals:
              - Ref: AWS::Region
              - us-east-1
          - Fn::Equals:
              - Ref: AWS::Region
              - us-east-2
          - Fn::Equals:
              - Ref: AWS::Region
              - us-west-1
          - Fn::Equals:
              - Ref: AWS::Region
              - us-west-2
```

이 템플릿은 네개의 리소스를 정의하고 있습니다.

- **AWS::SQS::Queue** - 우리가 사용할 Queue
- **AWS::SNS::Topic** - SNS Topic
- **AWS::SNS::Subscription** - Queue와 Topic 사이의 구독 설정
- **AWS::SQS::QueuePolicy** - 위 Topic이 Queue에 메시지를 보낼수 있게 하기 위한 IAM 정책

{{% notice info %}}Toolkit 은 자동으로  **AWS::CDK::Metadata** 를 각 스택으로 추가합니다. 이것은 AWS CDK 팀에서 분석 및 보안 문제가있는 버전을 식별하는데 사용됩니다. 자세한 내용은 AWS CDK User Guide의 [Version Reporting](https://docs.aws.amazon.com/cdk/latest/guide/tools.html) 를 참고해주세요. 우리는 나머지 이 워크샵을 진행하면서 diff 화면에서는 메타데이터 리소스를 제외할 것 입니다.{{% /notice %}}

