
# Modernization Workshop

## 소개

AWS 클라우드에서 컨테이너를 실행하면 강력하고 확장 가능한 애플리케이션 및 서비스를 구축할 수 있습니다.AWS에 가입하여 Amazon EKS, Amazon Elastic Container 서비스 (ECS) 및 AWS Fargate를 사용하여 컨테이너를 실행하는 방법에 대해 자세히 알아보십시오. 이 워크숍에서는 컨테이너 전문가들이 클라우드에서 컨테이너를 사용하여 최신 애플리케이션을 시작하고 구축하기 위해 알아야 할 내용을 다루는 라이브 데모와 실습을 진행합니다.

## 준비

- [Workshop Setup](./00.Setup.md)

## Mythical Mysfits

이제 설정 단계에서 모든 것이 제대로 작동했다면 CloudFormation에서 생성한 S3 버킷의 정적 사이트 엔드포인트에서 Mythical Mysfits 웹 사이트를 사용할 수 있을 것입니다.

`http://BUCKET_NAME.s3-website.REGION.amazonaws.com/`

사이트에서 사이트를 방문할 수 있습니다.

편의를 위해 CloudFormation 콘솔의 containersid 스택의 출력 탭에서 링크를 확인할 수 있습니다. 또는 workshop-1/cfn-outputs.json 파일에 저장된 클라우드포메이션 출력에서 BUCKET_NAME을 찾을 수도 있습니다.

REGION은 CloudFormation 스택을 배포한 지역의 코드여야 합니다. 사이트를 볼 수 있는지 확인해 보세요. 하지만 Mythical Mysfits 모노리스 서비스를 시작하기 전까지는 아직 콘텐츠가 많이 보이지 않을 것입니다.

![Mythical Mysfits](https://static.us-east-1.prod.workshops.aws/5aca20e9-0109-4e7f-a06d-55c2074a2de7/static/images/00-website.png?Key-Pair-Id=K36Q2WVO3JP7QD&Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9zdGF0aWMudXMtZWFzdC0xLnByb2Qud29ya3Nob3BzLmF3cy81YWNhMjBlOS0wMTA5LTRlN2YtYTA2ZC01NWMyMDc0YTJkZTcvKiIsIkNvbmRpdGlvbiI6eyJEYXRlTGVzc1RoYW4iOnsiQVdTOkVwb2NoVGltZSI6MTY5ODAyMTIzN319fV19&Signature=FvdcsvTaXJXquk4SgRSZqsb5y1OrsXQBcAelPROiC7HAXATl3n1NHF-ecxf-i0gj9GzgczE3OI7v6bPaTStMWV4ydYCRCk1pj8XA2p9GPCNHPEqEjr0c4M49pyoLGqsKG0sHTjxbT3UtxjgSAf9jHF4XVe%7E7RMJ5FmQIqJm1e9K5h6R6W1Zei7ZH9IWMf3B-k%7ESFPdU44ntykruCh1dF5awe68-pidB6ExmK8PLiw%7EK3xfRqrAfBAeebx8NcfcGGczySH0G2QFLGDBQMZeKeiyOMoPA9S1txth5Xz9eAh5MCTq9tAChKDeryDpG0U5U8zomzKm2IA7Gc%7EXgJfakTDw__)

## Labs

- [Lab 1: Mythical Mysfits 모노리스를 컨테이너화하기](./01.Lab-1.md)
- [Lab 2: AWS Fargate를 이용하여 컨테이너 배포하기](./02.Lab-2.md)
- [Lab 3: ALB와 ECS 서비스를 이용하여 모노리스를 스케일링하기](./03.Lab-3.md)
