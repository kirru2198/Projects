### Overview of IAM Role Creation for EC2 to Access SNS

1. **Introduction to IAM Roles**:
   - By default, AWS services are independent and do not have access to each other.
   - To allow an EC2 instance to send messages to the SNS service, we need to create an IAM role with the necessary permissions.

2. **Creating an IAM Role**:
   - Navigate to the IAM dashboard and select "Roles."
   - Click on "Create Role" and choose the EC2 service.
   - Assign permissions, such as "SNS Full Access."
   - Review the policy generated, which specifies the allowed actions and resources.

3. **Understanding IAM Policies**:
   - The policy includes statements that define the principal (EC2), actions (e.g., `sns:Publish`), and resources (SNS topics).
   - The role will be assumed by the EC2 instance, allowing it to use the permissions defined in the policy.

4. **Instance Profiles**:
   - An instance profile is created from the IAM role and is assigned to the EC2 instance.
   - Remember that EC2 instances use instance profiles, not IAM roles directly.

5. **Creating an SNS Topic**:
   - After setting up the IAM role and instance profile, create an SNS topic.
   - Specify properties such as the display name and topic name.

6. **Setting Up VPC and Endpoints**:
   - If the EC2 instance needs to communicate with SNS privately, create a VPC endpoint for SNS.
   - This allows the EC2 instance to send messages to SNS without going through the public internet.

7. **Publishing Messages to SNS**:
   - Use the AWS CLI command to publish messages to the SNS topic:
     $$
     \text{aws sns publish --region us-east-1 --topic-arn <topic-arn> --message "This is from SNS using EC2"}
     $$
   - Verify that the message was received by checking the logs of the subscribed Lambda functions.

8. **Troubleshooting and Support**:
   - If you encounter issues, you can reach out for support or refer to the shared template for guidance.

### Conclusion
The session covered the creation of IAM roles, instance profiles, SNS topics, and the setup of VPC endpoints to facilitate secure communication between EC2 instances and SNS. For further assistance, participants were encouraged to reach out via email for one-on-one support.

---
