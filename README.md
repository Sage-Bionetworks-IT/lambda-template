# lambda-template
Template for creating lambda repositories
A template for quickly starting a new AWS lambda project.

## Naming
Naming conventions:
* for a vanilla Lambda: `lambda-<context>`
* for a Cloudformation Transform macro: `cfn-transform-<context>`
* for a Cloudformation Custom Resource: `cfn-cr-<context>`

## Development

### Contributions
Contributions are welcome.

### Requirements
Run `pipenv install --dev` to install both production and development
requirements, and `pipenv shell` to activate the virtual environment. For more
information see the [pipenv docs](https://pipenv.pypa.io/en/latest/).

After activating the virtual environment, run `pre-commit install` to install
the [pre-commit](https://pre-commit.com/) git hook.

### Create a local build

```bash
$ sam build --use-container
```

### Run locally

```bash
$ sam local invoke HelloWorldFunction --event events/event.json
```

### Run unit tests
Tests are defined in the `tests` folder in this project. Use PIP to install the
[pytest](https://docs.pytest.org/en/latest/) and run unit tests.

```bash
$ python -m pytest tests/ -v
```

## Deployment

### Build

```shell script
sam build
```

## Deploy Lambda to S3
This requires the correct permissions to upload to bucket
`bootstrap-awss3cloudformationbucket-19qromfd235z9`.

```shell script
sam package --template-file .aws-sam/build/template.yaml \
  --s3-bucket essentials-awss3lambdaartifactsbucket-x29ftznj6pqw \
  --output-template-file .aws-sam/build/my-lambda.yaml

aws s3 cp .aws-sam/build/template.yaml s3://bootstrap-awss3cloudformationbucket-19qromfd235z9/my-lambda-repo/master
```

## Install Lambda into AWS
Create the following [sceptre](https://github.com/Sceptre/sceptre) file

config/prod/my-lambda.yaml
```yaml
template_path: "remote/my-lambda.yaml"
stack_name: "my-lambda"
stack_tags:
  Department: "Platform"
  Project: "Infrastructure"
  OwnerEmail: "it@sagebase.org"
hooks:
  before_launch:
    - !cmd "curl https://s3.amazonaws.com/essentials-awss3lambdaartifactsbucket-x29ftznj6pqw/my-lambda-repo/master/my-lambda.yaml --create-dirs -o templates/remote/my-lambda.yaml"
```

Install the lambda using sceptre:
```bash script
sceptre --var "profile=my-profile" --var "region=us-east-1" launch prod/my-lambda
```

## Author

Your Name Here.
