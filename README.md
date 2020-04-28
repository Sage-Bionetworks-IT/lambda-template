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

```shell script
$ sam build --use-container
```

### Run locally

```shell script
$ sam local invoke HelloWorldFunction --event events/event.json
```

### Run unit tests
Tests are defined in the `tests` folder in this project. Use PIP to install the
[pytest](https://docs.pytest.org/en/latest/) and run unit tests.

```shell script
$ python -m pytest tests/ -v
```

## Deployment

### Build

```shell script
sam build
```

## Deploy Lambda to S3
This requires the correct permissions to upload to bucket
`bootstrap-awss3cloudformationbucket-19qromfd235z9` and
`essentials-awss3lambdaartifactsbucket-x29ftznj6pqw`

```shell script
sam package --template-file .aws-sam/build/template.yaml \
  --s3-bucket essentials-awss3lambdaartifactsbucket-x29ftznj6pqw \
  --output-template-file .aws-sam/build/lambda-template.yaml

aws s3 cp .aws-sam/build/lambda-template.yaml s3://bootstrap-awss3cloudformationbucket-19qromfd235z9/lambda-template/master/
```

## Install Lambda into AWS
Create the following [sceptre](https://github.com/Sceptre/sceptre) file

config/prod/lambda-template.yaml
```yaml
template_path: "remote/lambda-template.yaml"
stack_name: "lambda-template"
stack_tags:
  Department: "Platform"
  Project: "Infrastructure"
  OwnerEmail: "it@sagebase.org"
hooks:
  before_launch:
    - !cmd "curl https://s3.amazonaws.com/bootstrap-awss3cloudformationbucket-19qromfd235z9/lambda-template/master/lambda-template.yaml --create-dirs -o templates/remote/lambda-template.yaml"
```

Install the lambda using sceptre:
```shell script
sceptre --var "profile=my-profile" --var "region=us-east-1" launch prod/lambda-template.yaml
```

## Author

Your Name Here.
