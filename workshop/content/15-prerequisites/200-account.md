+++
title = "AWS Account and User"
weight = 200
+++

## AWS Account for Experimentation

우래의 앱을 배포하려면 AWS 어카운트에 접속해야 합니다. 여러분들이 이미 계정이 있고 어드민 권한이 있다면 [다음 단계](./300-nodejs.html)로 넘어가셔도 좋습니다.

만약 AWS 계정이 없다면 이곳에서 [새로운 계정을 생성](https://portal.aws.amazon.com/billing/signup)해주세요.

## Administrator User

2. AWS 계정으로 접속합니다.
3. IAM 콘솔로 이동해서 [새로운 사용자](https://console.aws.amazon.com/iam/home?#/users$new)를 생성합니다.
4. 사용자 이름을 입력하고 (e.g. `cdk-workshop`) "Programmatic access" 를 선택합니다.

    ![](./new-user-1.png)

5. **Next: Permissions** 를 클릭해서 다음 단계로 이동합니다.
6. **Attach existing policies directly** 를 클릭해서 **AdministratorAccess** 를 선택합니다.

    ![](./new-user-2.png)

7. **Next: Review** 를 클릭합니다.
8. **Create User** 를 클릭합니다.
9. 다음 화면에서, **Access key ID**가 표시되고 옵션이 표시됩니다. **Show**를 클릭하여 **Secret access key**를 표시합니다. 이 브라우저 창을 계속 열어 둡니다. 
   
 ![](./new-user-3.png)

## Configure your credentials

터미널 창을 열고 `aws configure`를 사용하여 환경을 설정합니다. __access key ID__ 및 __secret key__를 입력하고 기본 Region을 선택합니다.

```
aws configure
```

그리고 콘솔에서 각 정보를 입력하세요.

```
AWS Access Key ID [None]: <type key ID here>
AWS Secret Access Key [None]: <type access key>
Default region name [None]: <choose region (e.g. "us-east-1", "eu-west-1")>
Default output format [None]: <leave blank>
```
