# Serverless Permission Separation
### Policy files and instructions for separating serverless framework permissions.

By default the serverless framework wants to deploy with admin permissions. This setup separates deployment permissions
(that you might use in CI) from Cloudformation permissions needed to execute the deployment stack and minimizes the scope of 
both permission sets.

Last updated: Feb 1, 2023

In the 3 json policy files in this directory:
 * Replace `<ACCOUNT_ID>` with your account id
 * Replace `<REGION>` with your region OR `*` for all regions
 * Replace `<STAGE>` with the name of the stage you are working with or `*` for all stages
 * Replace `<PROJECT>` with your project name
 * Replace `<APPLICATON>` with your application name

In AWS
 * Create a `<PROJECT>-lambda` role with whatever policies your lambda needs, don't forget logging.
 * Create a `<PROJECT>-cloudformation` role and create and attach a `<PROJECT>-cloudformation` policy.
   * This policy reference the <PROJECT>-lambda role. If you change that role name, change the `iam:PassRole` directive.
 * Create a deployment user (or role and user) and create and attach a `<PROJECT>-deployment` policy and attach the <PROJECT>-deploy policy
   * This policy references the <PROJECT>-cloudformation role and the <APPLICATION>-lambda role. If you change either role name, change the `iam:PassRole` directive.

In your `serverless.yml` file in the `provider` section set the cloudformation role to use.
`cfnRole: arn:aws:iam::<ACCOUNT_ID>:role/<PROJECT>-cloudformation`


The PROJECT-cloudformation.json policy has permission to create Lambdas, API Gateway, and **SQS**. 
You can remove SQS if you don't need it. Permissions for other resources in your stack will need to be added.

### Deployment
Add credentials to your `~/.aws/credentials` file under a profile for the deployment user and set 
the `AWS_PROFILE` environment variable to the name of the profile you created or invoke it with the `--aws-profile` option.

`sls deploy --stage dev --aws-profile <DEPLOYMENT_PROFILE>`

Thanks to https://dav009.medium.com/serverless-framework-minimal-iam-role-permissions-ba34bec0154e#:~:text=You%20need%20at%20least%20three,Lambda%20role
for the original writeup that these files are based off of.
