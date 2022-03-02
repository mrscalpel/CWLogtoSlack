#Listening for Amazon ECS CloudWatch Events

Step 1: Create the Lambda function

In this procedure, you create a simple Lambda function to serve as a target for Amazon ECS event stream messages.

    Open the AWS Lambda console at https://console.aws.amazon.com/lambda/

.

*Choose Create function.

    *On the Author from scratch screen, do the following:

    *For Name, enter a value.

    *For Runtime, choose Python 3.6.

    *For Role, choose Create a new role with basic Lambda permissions.

*Choose Create function.

In the Function code section, edit the sample code to match the following example

```
import json

def lambda_handler(event, context):
    if event["source"] != "aws.ecs":
       raise ValueError("Function only supports input from events with a source type of: aws.ecs")
       
    print('Here is the event:')
    print(json.dumps(event))
```



*Choose Save.

#Register an event rule

To route events to your Lambda function

    Open the CloudWatch console at https://console.aws.amazon.com/cloudwatch/

.

*On the navigation pane, choose Events, Rules, Create rule.

 *For Event Source, choose ECS as the event source. By default, the rule applies to all Amazon ECS events for all of your Amazon ECS groups. Alternatively, you can select specific events or a specific Amazon ECS group.

 *For Targets, choose Add target, for Target type, choose Lambda function, and then select your Lambda function.

 *Choose Configure details.

 *For Rule definition, type a name and description for your rule and choose Create rule.


Now use this log group as input to the Lambda Function we are going to create below.


```
import json
import base64
import gzip
from io import BytesIO
import urllib.request
import json
from urllib.error import HTTPError, URLError
from urllib.request import Request

def lambda_handler(event,context):
    print(json.dumps(event))
    cw_data = str(event['detail']['stoppedReason'])
    print(cw_data)

    body = {"text": cw_data}
    myurl = "https://hooks.slack.com/services/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"

    req = urllib.request.Request(myurl)
    req.add_header('Content-Type', 'application/json; charset=utf-8')

    jsondata = json.dumps(body)
    jsondataasbytes = jsondata.encode('utf-8')   # needs to be bytes

    req.add_header('Content-Length', len(jsondataasbytes))
    response = urllib.request.urlopen(req, jsondataasbytes)
```
