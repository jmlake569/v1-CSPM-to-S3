# V1-CSPM-to-S3 Integration

This project provides a secure and automated way to collect cloud security and compliance events and store them in Amazon S3 for further analysis or integration with other services.

## Architecture

The solution consists of the following AWS resources:
- **S3 Bucket**: Stores the CSPM events
- **KMS Key**: Encrypts the data at rest (optional)
- **SNS Topic**: Receives events from Vision One CSPM
- **Lambda Function**: Processes and stores the events (Python 3.11)
- **Dead Letter Queue**: Handles failed message processing
- **IAM Roles/Policies**: Manages permissions

## Data Flow

1. Vision One sends security/compliance events to the SNS topic
2. The Lambda function receives these events
3. The function formats the events as JSON arrays
4. The formatted data is stored in S3 with timestamp-based filenames

## Deployment

The CloudFormation template is available in the repository. You can deploy it directly through the AWS Console or using AWS CLI.

Required Parameters:
- `Name`: Name for resource tagging (required)
- `Project`: Project name for resource tagging (required)
- `UseKMSEncryption`: Whether to use KMS encryption for the S3 bucket (optional, default: true)

Example AWS CLI deployment:
```bash
# With KMS encryption (default)
aws cloudformation deploy \
  --template-file cloudformation.yaml \
  --stack-name my-stack \
  --parameter-overrides \
    Name=MyResource \
    Project=MyProject

# Without KMS encryption
aws cloudformation deploy \
  --template-file cloudformation.yaml \
  --stack-name my-stack \
  --parameter-overrides \
    Name=MyResource \
    Project=MyProject \
    UseKMSEncryption=false
```

## Configuration

After deployment, you'll need to:
1. Note the `TopicARN` output from the CloudFormation stack
2. Configure this ARN in your Misconfiguration and Compliance dashboard in Vision One

## Outputs

- `EventsBucket`: The S3 bucket name where events are stored
- `TopicARN`: The SNS topic ARN to configure in Misconfigurations and Compliance dashboard in Vision One

## Security Features

- Optional KMS encryption for S3 (configurable via UseKMSEncryption parameter)
- IAM roles with least privilege
- Dead Letter Queue for error handling
- Retain policies on critical resources
- Resource tagging for better organization and cost allocation

## Resource Tagging

All resources that support tagging are tagged with:
- `Name`: Value provided through the `Name` parameter
- `Project`: Value provided through the `Project` parameter

This helps with:
- Resource organization
- Cost allocation
- Access control
- Resource filtering
- Compliance requirements

## Lambda Function

The Lambda function is written in Python 3.11 and handles:
- Receiving SNS messages from Vision One
- Parsing and formatting JSON messages
- Storing formatted data in S3 (with optional KMS encryption)

## Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License

MIT
