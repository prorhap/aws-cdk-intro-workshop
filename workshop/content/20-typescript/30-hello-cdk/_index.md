+++
title = "Hello, CDK!"
chapter = true
weight = 30
+++

# Hello, CDK!

이번 장에서는 CDK 코드를 작성합니다. 현재 우리 앱에 있는 SNS/SQS 코드 대신에 Lambda 함수를 추가하고 그 앞에 API Gateway를 놓을 것 입니다.

사용자는 엔드포인트에 있는 URL을 클릭할 수 있고, 그러면 우리가 정의한 함수로부터 따뜻한 인사말을 받게됩니다.

![](/images/hello-arch.png)

첫번째로, 샘플 코드를 지워볼까요.