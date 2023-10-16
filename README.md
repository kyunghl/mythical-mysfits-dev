
# Modernization Workshop

## Workshop Setup

오늘 워크샵에서는 Workshop Studio를 활용하려고 합니다.

### Workshop Studio 접속

- [One-click join link](https://catalog.us-east-1.prod.workshops.aws/join?access-code=4c75-0c5eba-a4)
- (CHECK) Workshop Studio의 One-click join link로 했을 때 기술적인 문제는 없었는가?

### 소개

AWS 클라우드에서 컨테이너를 실행하면 강력하고 확장 가능한 애플리케이션 및 서비스를 구축할 수 있습니다.AWS에 가입하여 Amazon EKS, Amazon Elastic Container 서비스 (ECS) 및 AWS Fargate를 사용하여 컨테이너를 실행하는 방법에 대해 자세히 알아보십시오. 이 워크숍에서는 컨테이너 전문가들이 클라우드에서 컨테이너를 사용하여 최신 애플리케이션을 시작하고 구축하기 위해 알아야 할 내용을 다루는 라이브 데모와 실습을 진행합니다.

### Cloud9 접속

- [Cloud9 콘솔](https://us-east-1.console.aws.amazon.com/cloud9control/home?region=us-east-1#/)

### 코드 받기

#### 1. Mythical Mysfits Workshop Repository 복제

새 Cloud9 IDE의 하단 패널에는 터미널 명령줄 터미널이 열리고 바로 사용할 수 있는 것을 볼 수 있습니다. 터미널에서 다음 git 명령어를 이 워크샵을 수행하기 위해 필요한 코드를 받고 하위 디렉터리로 변경합니다.

```bash
git clone https://github.com/aws-samples/amazon-ecs-mythicalmysfits-workshop.git

cd amazon-ecs-mythicalmysfits-workshop/workshop-1
```

#### 2. 설치 스크립트를 사용하여 몇 가지 추가 자동 설치 단계를 실행합니다

```bash
script/setup
```

이 스크립트는 불필요한 Docker 이미지를 삭제하여 디스크 공간을 확보하고, DynamoDB 테이블을 일부 시드 데이터로 채우고, 사이트 자산을 S3에 업로드하고, 나중에 설명하겠지만 일부 Docker 관련 인증 메커니즘을 설치합니다.“성공!” 메시지가 표시되는지 확인하십시오.스크립트가 완료되면 메시지가 표시됩니다.

#### 3. 마지막으로, 현재 지역을 기본값으로 하여 aws cli를 구성해야 합니다

```bash
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')

echo "export ACCOUNT_ID=${ACCOUNT_ID}" >> ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" >> ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure get default.region
```

### SSH key 생성

Cloud9에서 SSH 키를 생성하려면 이 명령을 실행하십시오.이 키는 작업자 노드 인스턴스에서 필요한 경우 ssh 액세스를 허용하는 데 사용됩니다.

```bash
ssh-keygen
```

기본 값으로 생성합니다.

아래 명령으로 EC2 키를 업로드합니다.

```bash
aws ec2 import-key-pair --key-name "mythicaleks" --public-key-material file://~/.ssh/id_rsa.pub
```

ImportKeyPair 작업을 호출할 때 키가 유효한 OpenSSH 공개 키 형식 오류가 발생하면, 다음 명령을 대신 시도해보시기 바랍니다.

```bash
aws ec2 import-key-pair --key-name "mythicaleks" --public-key-material fileb://~/.ssh/id_rsa.pub
```

## Mythical Mysfits 소개

이제 설정 단계에서 모든 것이 제대로 작동했다면 CloudFormation에서 생성한 S3 버킷의 정적 사이트 엔드포인트에서 Mythical Mysfits 웹 사이트를 사용할 수 있을 것입니다.

`http://BUCKET_NAME.s3-website.REGION.amazonaws.com/`

사이트에서 사이트를 방문할 수 있습니다.

편의를 위해 CloudFormation 콘솔의 containersid 스택의 출력 탭에서 링크를 확인할 수 있습니다. 또는 workshop-1/cfn-outputs.json 파일에 저장된 클라우드포메이션 출력에서 BUCKET_NAME을 찾을 수도 있습니다.

REGION은 CloudFormation 스택을 배포한 지역의 코드여야 합니다. 사이트를 볼 수 있는지 확인해 보세요. 하지만 Mythical Mysfits 모노리스 서비스를 시작하기 전까지는 아직 콘텐츠가 많이 보이지 않을 것입니다.

### Lab 1. Containerize the Mythical Mysfits monolith

Mythical Mysfits 인프라는 EC2 VM에서 직접 실행되고 있습니다. 첫 번째 단계는 현재 Mythical Mysfits 플랫폼(모놀리스 애플리케이션이라고도 함)을 컨테이너화하여 코드 패키징 방식을 현대화하는 것입니다.이를 위해 컨테이너 이미지를 빌드하기 위해서 Dockerfile을 생성해야 합니다.

AWS Cloud9를 사용하여 Dockerfile을 작성하고 컨테이너 이미지를 빌드한 다음 이를 실행하여 adoption을 처리할 수 있는지 확인하는 과제입니다.

![Lab1: Build and test monolith container image](https://static.us-east-1.prod.workshops.aws/5aca20e9-0109-4e7f-a06d-55c2074a2de7/static/images/01-arch.png?Key-Pair-Id=K36Q2WVO3JP7QD&Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9zdGF0aWMudXMtZWFzdC0xLnByb2Qud29ya3Nob3BzLmF3cy81YWNhMjBlOS0wMTA5LTRlN2YtYTA2ZC01NWMyMDc0YTJkZTcvKiIsIkNvbmRpdGlvbiI6eyJEYXRlTGVzc1RoYW4iOnsiQVdTOkVwb2NoVGltZSI6MTY5ODAyMTIzN319fV19&Signature=FvdcsvTaXJXquk4SgRSZqsb5y1OrsXQBcAelPROiC7HAXATl3n1NHF-ecxf-i0gj9GzgczE3OI7v6bPaTStMWV4ydYCRCk1pj8XA2p9GPCNHPEqEjr0c4M49pyoLGqsKG0sHTjxbT3UtxjgSAf9jHF4XVe%7E7RMJ5FmQIqJm1e9K5h6R6W1Zei7ZH9IWMf3B-k%7ESFPdU44ntykruCh1dF5awe68-pidB6ExmK8PLiw%7EK3xfRqrAfBAeebx8NcfcGGczySH0G2QFLGDBQMZeKeiyOMoPA9S1txth5Xz9eAh5MCTq9tAChKDeryDpG0U5U8zomzKm2IA7Gc%7EXgJfakTDw__)

#### 1. Dockerfile 초안을 검토하고 주석으로 표시된 누락된 지침을 파일에 추가합니다

Mythical Mysfits의 개발자 중 한 명이 Dockerfile 작업을 시작했지만 끝내기도 전에 우선순위가 높은 프로젝트로 갔습니다.

Cloud9 파일 트리에서 `amazon-ecs-mythicalmysfits-workshop/workshop-1/app/monolith-service`로 이동한 다음 Dockerfile.draft를 두 번 클릭하여 편집할 파일을 엽니다.

Docker는 Dockerfile에 나열된 지침을 단계별로 실행하여 컨테이너 이미지를 빌드합니다. Docker는 레이어가 베이스에서 시작하여 변경 사항을 도입하는 각 명령을 새 레이어로 실행한다는 아이디어를 기반으로 구축되었습니다. Docker는 각 레이어를 캐시하므로 이미지를 개발하고 다시 빌드할 때 수정이 없는 경우 Docker는 캐시의 레이어 (중간 레이어라고도 함) 를 재사용합니다.편집이 적용된 레이어에 도달하면 새 중간 레이어를 빌드하고 이 레이어를 특정 빌드에 연결합니다.따라서 이미지 재구축과 같은 작업을 매우 효율적으로 수행할 수 있으며 여러 빌드 버전을 쉽게 유지 관리할 수 있습니다.

![Docker Image](https://static.us-east-1.prod.workshops.aws/5aca20e9-0109-4e7f-a06d-55c2074a2de7/static/images/01-container-image.png?Key-Pair-Id=K36Q2WVO3JP7QD&Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9zdGF0aWMudXMtZWFzdC0xLnByb2Qud29ya3Nob3BzLmF3cy81YWNhMjBlOS0wMTA5LTRlN2YtYTA2ZC01NWMyMDc0YTJkZTcvKiIsIkNvbmRpdGlvbiI6eyJEYXRlTGVzc1RoYW4iOnsiQVdTOkVwb2NoVGltZSI6MTY5ODAyMTIzN319fV19&Signature=FvdcsvTaXJXquk4SgRSZqsb5y1OrsXQBcAelPROiC7HAXATl3n1NHF-ecxf-i0gj9GzgczE3OI7v6bPaTStMWV4ydYCRCk1pj8XA2p9GPCNHPEqEjr0c4M49pyoLGqsKG0sHTjxbT3UtxjgSAf9jHF4XVe%7E7RMJ5FmQIqJm1e9K5h6R6W1Zei7ZH9IWMf3B-k%7ESFPdU44ntykruCh1dF5awe68-pidB6ExmK8PLiw%7EK3xfRqrAfBAeebx8NcfcGGczySH0G2QFLGDBQMZeKeiyOMoPA9S1txth5Xz9eAh5MCTq9tAChKDeryDpG0U5U8zomzKm2IA7Gc%7EXgJfakTDw__)

예를 들어, 초안 파일에서 첫 번째 줄 (FROM ubuntu:20.04) 은 기본 이미지를 시작점으로 지정합니다. 다음 명령어인 RUN apt-get -y update 는 Docker가 Ubuntu 리포지토리에서 패키지 목록을 업데이트하는 새 레이어를 생성합니다. 이런 과정은 보통 ENTRYPOINT 또는 실행 파일을 실행하는 마지막 명령에 도달할 때까지 계속됩니다.

<details close>

  <summary>HINT: DockeFile.draft 작성을 위한 유용한 링크</summary>
  
Here are links to external documentation to give you some ideas:

`#[TODO]: Copy the "service" directory into container image`

- Consider the [COPY](https://docs.docker.com/engine/reference/builder/#copy) command
- You're copying both the python source files and requirements.txt from the "monolith-service/service" directory on your EC2 instance into a working directory within the container, which can be something like "/MythicalMysfitsService"
- Consider the [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) command as a way to navigate within the context of the container's directory structure

`#[TODO]: Install dependencies listed in the requirements.txt file using pip3`

- Consider the [RUN](https://docs.docker.com/engine/reference/builder/#run) command
- More on [pip and requirements files](https://pip.pypa.io/en/stable/user_guide/#requirements-files)
- Btw, the team upgraded to python3 recently, so you'll want to use 'pip3' not 'pip'

`#[TODO]: Specify a listening port for the container`

- Consider the [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) command
- App listening portNum can be found in the app source - mythicalMysfitsService.py

`#[TODO]: Run "mythicalMysfitsService.py" as the final step. We want this container to run as an executable. Looking at ENTRYPOINT and CMD for this?`

- Consider the [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint) and [CMD](https://docs.docker.com/engine/reference/builder/#cmd) - commands
- [ENTRYPOINT and CMD](https://docs.docker.com/engine/reference/builder/#understand-how-cmd-and-entrypoint-interact) can be used together
- Our ops team typically runs 'python3 mythicalMysfitsService.py' to launch the application on our servers.

</details>
