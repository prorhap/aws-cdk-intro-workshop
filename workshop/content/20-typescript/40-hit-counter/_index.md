+++
title = "Writing constructs"
chapter = true
weight = 40
+++

# Writing constructs

이번 장에서는 `HitCounter` 라고 하는 새로운 construct를 정의합니다. 이 construct는 API Gateway 백엔드로 사용되는 모든 Lambda 함수에 연결할 수 있으며 각 URL 경로에 대한 요청 수를 계산합니다. 이 값을 DynamoDB 테이블에 저장할 것 입니다.

![](/images/hit-counter.png)

## See Also

- [Writing Constructs in the AWS CDK User Guide](https://docs.aws.amazon.com/CDK/latest/userguide/writing_constructs.html)