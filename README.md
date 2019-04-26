# app-servers


## Simple Task:

- Launch EC2 (t3.medium , ubuntu)
- Install Java8, Jenkins
- Create Simple CI
- Checkout repo
- Make package
- Upload on S3
- Create Version
- Deploy it on ElasticBeanstalk.


## Solution -- Simple CI script 

`$ sudo apt install openjdk-8-jdk`

First create the Ubuntu ec2 instance and install the jenkins using this [link](https://www.digitalocean.com/community/tutorials/how-to-install-jenkins-on-ubuntu-16-04) and make it running over the hosting url. Then Configure your jenkins pipeline and add this script code in your jenkins console prompt.

```shell
BuildName="version-$BUILD_NUMBER"
BucketName="devopssessionbuild"
BucketKey="python-builds"
ApplicationName="PythonApps"
EnvironmentName="Pythonapps-env"

cd python-app

zip -r $BuildName.zip *

aws s3 cp $BuildName.zip s3://$BucketName/$BucketKey/ --region ap-south-1

rm $BuildName.zip

aws elasticbeanstalk create-application-version --application-name "$ApplicationName" --version-label "$BuildName" --description "Build created from JENKINS. Job:$JOB_NAME, BuildId:$BUILD_DISPLAY_NAME, GitCommit:$GIT_COMMIT, GitBranch:$GIT_BRANCH" --source-bundle S3Bucket=$BucketName,S3Key=$BucketKey/$BuildName.zip --region ap-south-1

aws elasticbeanstalk update-environment --environment-name "$EnvironmentName" --version-label "$BuildName" --region ap-south-1

```
