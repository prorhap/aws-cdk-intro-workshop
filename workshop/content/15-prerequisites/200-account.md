+++
title = "AWS Account and User"
weight = 200
+++

## AWS Account

우래의 앱을 배포하려면 AWS 어카운트에 접속해야 합니다. 

실습을 위한 해시(hash)를 받았다면 아래 **Event Engine** 단원으로 가서 설정을 진행해주시고, 

여러분들이 이미 계정이 있고 어드민 권한이 있다면 [다음 단계](./300-nodejs.html)로 넘어가셔도 좋습니다. 만약 AWS 계정이 없다면 이곳에서 [새로운 계정을 생성](https://portal.aws.amazon.com/billing/signup)해주세요.



## Event Engine

미리 제공받은 해시를 사용해서 실습을 진행할 수 있습니다. 

1. [https://dashboard.eventengine.run/](https://dashboard.eventengine.run/)로 이동하여 포탈에 접속한 뒤 제공받은 해시 값을 입력합니다. 
2. 오른쪽 아래에 있는 버튼이 **Accept Terms & Login** 으로 변경되면 이를 클릭합니다.
3. 로그인 방식으로 **Email One-Time Password (OTP)** 을 선택합니다.
4. passcode를 받을 email 주소를 입력 후 **Send passcode** 버튼을 클릭합니다.
5. 받은 passcode 를 입력 후 **Sign in** 버튼을 클릭합니다.
6. Team Dashboard 화면에서 **AWS Console** 버튼을 클릭합니다. 그러면 대화상자가 열릴 것 입니다.
7. **Credentials / CLI Snippets** 에서 운영체제를 선택하고 자격증명을 복사합니다. 다음처럼 보일 것 입니다.

```bash
export AWS_DEFAULT_REGION=ap-northeast-2
export AWS_ACCESS_KEY_ID=ASIAUVHCXU6L6242TNBA
export AWS_SECRET_ACCESS_KEY=K12UEffSG5GYAveRXh46M18dAVQpXp2aQeG/TdcX
export AWS_SESSION_TOKEN=AowGZXIvYXdzEPf//////////wEaDFkE0EPYXOPgmFxU
```

{{% notice tip %}}
위 예시에 있는 자격증명이 아닌, 여러분 화면에 있는 자격증명을 복사하여야합니다 ;)
{{% /notice %}}

5. 이 자격증명을 텍스트 파일로 저장하거나 에디터에 복사합니다 - 이 자격증명은 여러분이 터미널을 사용할 때 적용합니다.
6. 자격증명을 기록했다면 **Open AWS Console**을 클릭하여, AWS Console을 엽니다.

이제 [다음 단계](./300-nodejs.html)로 넘어가주세요.





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

터미널 창을 열고 `aws configure`를 사용하여 환경을 설정합니다. __access key ID__ 및 __secret key__ 를 입력하고 기본 Region을 선택합니다.

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
