# python alexa hello world

Inspired from https://alexa-skills-kit-python-sdk.readthedocs.io/en/latest/GETTING_STARTED.html

## Dev Loop

See: https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-quick-start.html

Firstly, we need a `S3 bucket` where we can upload our Lambda functions packaged as ZIP before we deploy anything - If you don't have a S3 bucket to store code artifacts then this is a good time to create one:

```bash
aws s3 mb s3://BUCKET_NAME
```

### Build 
(anytime dependencies or code or template changes)
By default, this command writes built artifacts to `.aws-sam/build` folder.
```
pip freeze > hello_world/requirements.txt
sam build

# (if native modules) sam build --use-container
```

### Package

AWS Lambda Python runtime requires a flat folder with all dependencies including the application. 
SAM will use `CodeUri` property to know where to look up for both application and dependencies:

```yaml
...
    HelloWorldFunction:
        Type: AWS::Serverless::Function
        Properties:
            CodeUri: hello_world/
            ...
```

```
AWS_PROFILE=***** sam package \
     --s3-bucket sk-alexa-hello-world \
     --region us-west-2 \
     --output-template-file packaged.yaml
```

### Deploy

```
AWS_PROFILE=******* sam deploy \
     --template-file packaged.yaml \
     --stack-name alexa-hello-world \
     --capabilities CAPABILITY_IAM \
     --region us-west-2
```
After deployment is complete you can run the following command to retrieve the API Gateway Endpoint URL:

```bash
aws cloudformation describe-stacks \
    --stack-name sam-app \
    --query 'Stacks[].Outputs'
```



## Local development

**Invoking function locally through local API Gateway**

```bash
sam local start-api
```

 

## Testing

We use **Pytest** and **pytest-mock** for testing our code and you can install it using pip: ``pip install pytest pytest-mock`` 

Next, we run `pytest` against our `tests` folder to run our initial unit tests:

```bash
python -m pytest tests/ -v
```
