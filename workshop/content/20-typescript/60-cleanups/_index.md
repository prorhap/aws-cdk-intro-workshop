+++
title = "Clean up"
weight = 60
chapter = true
+++

# Clean up your stack

계정에 예상치 못한 비용이 청구되지 않도록하려면 CDK 스택을 정리해야합니다.

AWS CloudFormation 콘솔을 통해 스택을 삭제하거나`cdk destroy`를 사용할 수 있습니다.

```
cdk destroy
```

아래와 같은 질문에서

```
Are you sure you want to delete: CdkWorkshopStack (y/n)?
```

"y"를 입력하면 스택이 삭제되는 것을 볼 수 있습니다.

