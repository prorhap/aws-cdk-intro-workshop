+++
title = "CloudWatch Logs"
weight = 500
+++

## Viewing CloudWatch logs for our Lambda function

가장 먼저 할 일은 hit counter AWS Lambda 함수의 로그를 살펴 보는 것입니다.

이를 위해서는 [SAM CLI](https://github.com/awslabs/aws-sam-cli) and [awslogs](https://github.com/jorgebastida/awslogs) 와 같은 여러 도구가 있습니다. 이 워크샵에서는 AWS 콘솔을 통해 로그를 찾는 방법을 사용할 것 입니다.

1. [AWS Lambda 콘솔](https://console.aws.amazon.com/lambda/home)을 엽니다. (리전이 올바르게 설정되었는지 확인)
   
2. __HitCounter__ Lambda 함수를 클릭합니다.
   (함수 이름에는 `CdkWorkshopStack-HelloHitCounter` 이 포함되어 있습니다):
    ![](./logs1.png)

3. __Monitoring__ 을 클릭합니다.
    ![](./logs2.png)

4. __View Logs in CloudWatch__ 를 클릭해서 AWS CloudWatch 콘솔을 엽니다.
    ![](./logs3.png)

5. 가장 최근의 로그 그룹을 선택합니다.

6. "errorMessage" 문자열을 포함하는 가장 최근 로그를 찾아보면, 다음과 같은 메시지를 볼 수 있습니다.


   ```json
   {
       "errorMessage": "User: arn:aws:sts::585695036304:assumed-role/CdkWorkshopStack-HelloHitCounterHitCounterHandlerS-TU5M09L1UBID/CdkWorkshopStack-HelloHitCounterHitCounterHandlerD-144HVUNEWRWEO is not authorized to perform: dynamodb:UpdateItem on resource: arn:aws:dynamodb:us-east-1:585695036304:table/CdkWorkshopStack-HelloHitCounterHits7AAEBF80-1DZVT3W84LJKB",
       "errorType": "AccessDeniedException",
       "stackTrace": [
           "Request.extractError (/var/runtime/node_modules/aws-sdk/lib/protocol/json.js:48:27)",
           "Request.callListeners (/var/runtime/node_modules/aws-sdk/lib/sequential_executor.js:105:20)",
           "Request.emit (/var/runtime/node_modules/aws-sdk/lib/sequential_executor.js:77:10)",
           "Request.emit (/var/runtime/node_modules/aws-sdk/lib/request.js:683:14)",
           "Request.transition (/var/runtime/node_modules/aws-sdk/lib/request.js:22:10)",
           "AcceptorStateMachine.runTo (/var/runtime/node_modules/aws-sdk/lib/state_machine.js:14:12)",
           "/var/runtime/node_modules/aws-sdk/lib/state_machine.js:26:10",
           "Request.<anonymous> (/var/runtime/node_modules/aws-sdk/lib/request.js:38:9)",
           "Request.<anonymous> (/var/runtime/node_modules/aws-sdk/lib/request.js:685:12)",
           "Request.callListeners (/var/runtime/node_modules/aws-sdk/lib/sequential_executor.js:115:18)"
       ]
   }
   ```

---

Lambda 함수가 DynamoDB 테이블에 쓸 수 없는 것 같네요. 우리는 DyanmoDB에 쓸 수 있는 권한을 부여하지 않았습니다. 이제 권한을 추가해 볼까요.

