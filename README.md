# sagemaker-automation
1. create a lmabda function with below setting
    ![](image/fet1.tiff)
2. create a lambda function with code below, you would need to replace the 'bucket', 'prefix' and 'transform_channel' to your own s3 object path and filename

``` Python
import boto3
import os
import time
REGION = boto3.session.Session().region_name
sagemaker = boto3.client('sagemaker')
bucket = 'neochen-fet' # Replace with your own bucket name if needed
prefix = 'blazingtext' #Replace with the prefix under which you want to store the data if needed
transform_channel = 'dev_nolabel.json'

def lambda_handler(event, context):
    try:
        response = sagemaker.create_transform_job(
            TransformJobName='fetworkshop'+time.strftime("%Y%m%d-%H%M%S"),
            ModelName='blazingtext-2019-08-13-06-09-24-102',
            BatchStrategy='MultiRecord',
            TransformInput={
                'DataSource': {
                    'S3DataSource': {
                        'S3DataType': 'S3Prefix',
                        'S3Uri': 's3://{}/{}'.format(bucket, transform_channel)
                    }
                },
                'ContentType': 'application/jsonlines',
                'CompressionType': 'None',
                'SplitType': 'Line'
            },
            TransformOutput={
                'S3OutputPath': 's3://{}/{}/transform/'.format(bucket, prefix),
                'AssembleWith': 'Line'
            },
            TransformResources={
                'InstanceType': 'ml.m4.xlarge',
                'InstanceCount': 1
            }
        )
    except Exception as e:
        print(e)
        print('Unable to create transform job.')
        raise(e)
```
