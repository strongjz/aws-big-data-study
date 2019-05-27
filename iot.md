# IOT 

* Integrates with elasticsearch, kinesis firehose, kinesis streams, dynamo, machine learning service. Can also trigger a cloudwatch function, write to s3, jsend to sqs or sns, or trigger a lambda function.
*   Authentication - x509 cert. Upload your own CSR or CA. Uses IAM or incognito
    *   Incognito allows other auth providers like FB/google, openid, saml or your own user pools.
    *   IoT policies and IAM policies
*   Device GW
    *   Maintains sessions and subscriptions
    *   Uses http or mttq
    *   Can support over a billion devices
*   Device Registry
    *   Stores attributes of a device or thing
*   Rules Engine
    *   Can transform messages and route them to AWS services via rule actions
    *   Lambda, dynamo, kineses streams and firehose, and ML
*   Device Shadow


### Blogs 

### Docs

https://docs.aws.amazon.com/iot/latest/developerguide/what-is-aws-iot.html 

https://docs.aws.amazon.com/iot/latest/developerguide/policy-actions.html 

https://docs.aws.amazon.com/iot/latest/developerguide/iam-policies.html 

https://docs.aws.amazon.com/iot/latest/developerguide/iot-provision.html 

https://docs.aws.amazon.com/iot/latest/developerguide/iot-device-shadows.html 

https://docs.aws.amazon.com/iot/latest/developerguide/iot-rule-actions.html

### Code


https://github.com/aws/aws-iot-device-sdk-python

https://www.youtube.com/watch?v=HEQkVHxu46A

