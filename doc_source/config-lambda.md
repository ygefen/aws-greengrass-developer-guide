# Configure the Lambda Function for AWS Greengrass<a name="config-lambda"></a>

You are now ready to configure your Lambda function for AWS Greengrass\.

1. In the AWS IoT console, under **Greengrass**, choose **Groups**, and then choose the group that you created in [Module 2](module2.md)\.

1. On the group configuration page, choose **Lambdas**\.  
![\[Screenshot of the Group Overview page with Lambdas highlighted.\]](http://docs.aws.amazon.com/greengrass/latest/developerguide/images/gg-get-started-030.png)

1. Choose **Add your first Lambda**\.  
![\[Screenshot of the Bring Lambda functions to the Edge widget with the Add your first Lambda button highlighted.\]](http://docs.aws.amazon.com/greengrass/latest/developerguide/images/gg-get-started-031.png)

1. Choose **Use existing Lambda**\.  
![\[Screenshot of Add a Lambda to your Greengrass Group with the Use existing Lambda button highlighted.\]](http://docs.aws.amazon.com/greengrass/latest/developerguide/images/gg-get-started-032.png)

1. Search for the name of the Lambda you created in the previous step \(**Greengrass\_HelloWorld**, not the alias name\), select it, and then choose **Next**:  
![\[Screenshot of Use existing Lambda with Greengrass_HelloWorld and the Next button highlighted.\]](http://docs.aws.amazon.com/greengrass/latest/developerguide/images/gg-get-started-033.png)

1. For the version, choose **Alias: GG\_HelloWorld**, and then choose **Finish**\. You should see the **Greengrass\_HelloWorld** Lambda function in your group, using the **GG\_HelloWorld** alias\.

1. Choose the ellipsis \(…\) for the Lambda function, then choose **Edit Configuration**:  
![\[Screenshot of MyFirstGroup with the ellipsis and Edit Configuration highlighted.\]](http://docs.aws.amazon.com/greengrass/latest/developerguide/images/gg-get-started-034.png)

1. On the **Group\-specific Lambda configuration** page, make the following changes:
   + For **Timeout** \- set to 25 seconds\. This Lambda function sleeps for 20 seconds before each invocation\.
   + For **Lambda lifecycle** \- select **Make this function long\-lived and keep it running indefinitely**\.

      
![\[Screenshot of the configuration page with 25 (seconds) and the Make this function long-lived and keep it running indefinitely radio button selected.\]](http://docs.aws.amazon.com/greengrass/latest/developerguide/images/gg-get-started-035.png)

   A *long\-lived* Lambda function starts automatically after AWS Greengrass starts and keeps running in its own container \(or sandbox\)\. This is in contrast to an *on\-demand* Lambda function which starts when invoked and stops when there are no tasks left to execute\. For more information, see [Lifecycle Configuration for Greengrass Lambda Functions](lambda-functions.md#lambda-lifecycle)\.
**Note**  
The Lambda function in this tutorial accepts **JSON** input payloads, but Greengrass Lambda functions also support **Binary** input payloads\. Binary support is especially useful when interacting with device data, because the restricted hardware capabilities of devices often make it difficult or impossible to construct a JSON data type\.

1. Choose **Update** to save your changes to the Lambda function configuration\.

1. An AWS Greengrass Lambda function can subscribe or publish messages \(using the [MQTT protocol](http://mqtt.org/)\):
   + To and from other devices \(or device shadows\) within the AWS Greengrass core\. Information about device shadows is provided in [Module 5](module5.md)\.
   + To other Lambda functions\.
   + To the AWS IoT cloud\.

   The AWS Greengrass group controls the way in which these components interact by using subscriptions that enable greater security and to provide predictable interactions\. 

   A *subscription* consists of a source, target, and topic\. The source is the originator of the message\. The target is the destination of the message\. The topic allows you to filter the data that is sent from the source to the target\. The source or target can be an AWS Greengrass device, a Lambda function, a device shadow, or the AWS IoT cloud\. A subscription is directed in the sense that messages flow in a specific direction\. For an AWS Greengrass device to send messages to and receive messages from a Lambda function, you must set up two subscriptions: one from the device to the Lambda and another from the Lambda function to the device\. The `Greengrass_HelloWorld` Lambda function sends messages only to the `hello/world` topic in the AWS IoT cloud, as shown in the following `greengrassHelloWorld.py` code snippet:

   ```
   def greengrass_hello_world_run():
       if not my_platform:
           client.publish(topic='hello/world', payload='Hello world! Sent from Greengrass Core.')
       else:
           client.publish(topic='hello/world', payload='Hello world! Sent from Greengrass Core running on platform: {}'.format(my_platform))
   
       # Asynchronously schedule this function to be run again in 5 seconds
       Timer(5, greengrass_hello_world_run).start()
   
   # Execute the function above:
   greengrass_hello_world_run()
   ```

   Because the `Greengrass_HelloWorld` Lambda function sends messages only to the `hello/world` topic in the AWS IoT cloud, you only need to create one subscription from the Lambda function to the AWS IoT cloud, as shown next\.

1. On the group configuration page, choose **Subscriptions**, and then choose **Add your first Subscription**\.  
![\[Screenshot of the Group configuration page, with Subscription and Add your first Subscription highlighted.\]](http://docs.aws.amazon.com/greengrass/latest/developerguide/images/gg-get-started-036.png)

1. In the **Select a source** field, choose **Select**\. Then, on the **Lambdas** tab, choose **Greengrass\_HelloWorld** as the source\.   
![\[Screenshot of the Select a source page with Lambdas and Greengrass_HelloWorld highlighted.\]](http://docs.aws.amazon.com/greengrass/latest/developerguide/images/gg-get-started-037.png)

1. For the **Select a target** field, choose **Select**\. Then, on the **Service** tab, choose **IoT Cloud**, and then choose **Next**\.  
![\[The Select a target with the Services tab, IoT Cloud and the Next button highlighted.\]](http://docs.aws.amazon.com/greengrass/latest/developerguide/images/gg-get-started-038.png)

1. In the **Optional topic filter** field, type **hello/world**, then choose **Next**\.  
![\[Screenshot with hello/world highlighted under Optional topic filter.\]](http://docs.aws.amazon.com/greengrass/latest/developerguide/images/gg-get-started-039.png)

1. Finally, choose **Finish**\.