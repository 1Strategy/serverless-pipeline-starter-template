# AWS Serverless CI/CD Pipeline Starter Template
---

This template is meant as a starting guide for building a Serverless CI/CD Pipeline for AWS SAM Applications.

_Note: This is easily adaptable in many ways to other applications. This project assumes you are using AWS SAM for application development, and Github as the source repository._

![Pipeline Sample Image](./assets/serverless-pipeline.png)

## Prerequisites
---
- [Installing AWS CLI ](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)
- [Configuring AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)
- [Generage GitHub OAuth Token](https://docs.aws.amazon.com/codepipeline/latest/userguide/GitHub-create-personal-token-CLI.html)


## Configuring Parameters
---
Replace the `--value "XXXX"` with your information, and run each of the following commands in your terminal to generate parameters in AWS Parameter Store, which will be used to populate Stack runtime values.

```bash
aws ssm put-parameter \
    --name "/sns/serverless-pipeline/notifications/primary-email" \
    --description "Email address for primary recipient of Pipeline notifications" \
    --type "String" \
    --value "PRIMARY_EMAIL_ADDRESS"

aws ssm put-parameter \
    --name "/codepipeline/serverless-pipeline/github/repo" \
    --description "Github Repository name for CloudFormation Stack serverless-pipeline" \
    --type "String" \
    --value "GITHUB_REPO_NAME"

aws ssm put-parameter \
    --name "/codepipeline/serverless-pipeline/github/token" \
    --description "Github Token for CloudFormation Stack serverless-pipeline" \
    --type "String" \
    --value "TOKEN"

aws ssm put-parameter \
    --name "/codepipeline/serverless-pipeline/github/user" \
    --description "Github Username for CloudFormation Stack serverless-pipeline" \
    --type "String" \
    --value "GITHUB_USER"
```

## Provisioning
---
Update the `tags.json` file and populate with any necessary tags for the stack.

```json
[
    {
        "Key": "created_by",
        "Value": "scott schmidt"
    }
]
```

Run the following AWS CLI command to create a pipeline for your SAM Serverless App:

```bash
aws cloudFormation create-stack \
    --stack-name serverless-pipeline \
    --template-body file://pipeline.yaml \
    --capabilities CAPABILITY_NAMED_IAM \
    --tags file://tags.json
```

This may take a couple of minutes to complete, therefore give it a minute or two and then run the following command to retrieve the Git repository:

```bash
aws cloudFormation describe-stacks \
    --stack-name serverless-pipeline \
    --query 'Stacks[].Outputs'
```

## Software Release Process
---
Follow standard Git workflow to deploy code to a Master branch on GitHub. This can be accomplished by pushing directly to Master, _if you like living dangerously_, or through a Pull Request approval/merge on GitHub.

```bash
☠ sam-starter-template ➤ 309674a|master💥
➜ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   template.yaml

no changes added to commit (use "git add" and/or "git commit -a")
☠ sam-starter-template ➤ 309674a|master💥
➜ git add .
☠ sam-starter-template ➤ 309674a|master💥
➜ git commit -m 'adds new feature...'
[master 105ba46] adds new feature...
 1 file changed, 1 insertion(+), 1 deletion(-)
☠ sam-starter-template ➤ 105ba46|master💣
➜ git push origin master
Enumerating objects: 11, done.
Counting objects: 100% (11/11), done.
Delta compression using up to 4 threads
Compressing objects: 100% (6/6), done.
Writing objects: 100% (6/6), 564 bytes | 564.00 KiB/s, done.
Total 6 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To https://github.com/someuser/sam-starter-template.git
   309674a..105ba46  master -> master
☠ sam-starter-template ➤ 105ba46|master💣
➜
```
The pipeline will now use a webhook with GitHub to pull your source code release and promote it through the pipeline.

## Authors
---
* Scott Schmidt [1Strategy](https://www.1strategy.com)

## License
---
Copyright 2019 1Strategy

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.

## References
---
* [AWS CloudFormation Resource References](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)
* [AWS S3 Developer's Guide](https://docs.aws.amazon.com/AmazonS3/latest/dev/Welcome.html)
* [AWS SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html)
* [AWS CodePipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/welcome.html)
* [CodePipeline State Changes](https://docs.aws.amazon.com/codepipeline/latest/userguide/detect-state-changes-cloudwatch-events.html)
