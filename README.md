# SpringBootAppEngineFlex
This repo contains a sample App Engine Flex SpringBoot application and a sample Apigee Edge proxy which leverages a Shared Flow that enables authenticating to Google Cloud Platform's Identity Aware Proxy (IAP) with a GCP Service Account.  

### Create the Apigee Edge proxy and shared flow
You will need access to an Apigee account to create the proxy and shared flow needed to follow this example. If you don't have an account you can sign up for a free trial using the link below. 

##### Sign-up for an Apigee Edge trial account
Visit the following page to create a free trial Apigee account: https://login.apigee.com/sign_up

### Import the proxy bundle
For the following steps, please log in to your Apigee Edge account.
2. Navigate to `Develop > API Proxies` in the left menu.
3. Click the `+Proxy` button in the top right hand corner of the screen.
4. Select `Proxy bundle` and click `Next`.

![image alt text](./media/create-proxy-import.png)

5. For `Zip Bundle:`, Select the `SpringBoot_rev1_2019_09_17.zip` zip archive.
6. Set `Proxy Name:` to `SpringBoot` and click `Next`.

![image alt text](./media/create-proxy-details.png)

7. Click `Build`.

### Import the shared flow bundle
1. Navigate to `Develop > Shared Flows` in the left menu.
2. Click the `+Shared Flow` button in the top right hand corner of the screen and select `Upload bundle...` from the drop down.
3. Select the `GCP-Auth_rev3_2019_09_17.zip` zip archive.
4. Set the `Name:` to `GCP-Auth` and select `Create`.

### Deploy the SpringBoot App Engine Flex application
The instructions for deploying the SpringBoot application can be found in the README file of the [`helloworld-springboot`](helloworld-springboot) directory. Please follow the steps in the README to deploy the App Engine Flex application and return to the steps below once complete.

### Enable and configure Google Cloud Identity Aware Proxy (IAP)


### Create a Key Value Map
The shared flow imported leverages a Key Value Map (KVM) to lookup values associated with your GCP project.  We will create a KVM in the following steps and set values for the service account and OAuth Client Credentials we will require for this example.

1. Navigate to `Admin > Environments > Key Value Maps` in the left menu.
2. Click the `+Key value map` in the top right corner of the screen.
3. Enter `GCP IAM` as the `Name` and select the `Encrypted` checkbox.
4. Once created, select the `GCP IAM` key value map by clicking on it.
5. In the `GCP IAM` key value map screen enter in the following key/values. You will need to click on the `+` icon in the top right of the screen to add these values individually.

    * jwt_issuer: set this value to the `client_email` found in the service account JSON file downloaded from your GCP project.  You can download the service account JSON file from the `APIs & Services > Credentials` section of your GCP project.
    ![image alt text](./media/apis-credentials.png)
    
    * private_key_PEM: set this value to the `private_key` value found in the service account JSON file downloaded from your GCP project.  Please also note that by default the `private_key` value will contain new line characters (\n) which need to be removed prior to entering the private key into this field.  You can do a find and replace of `\n` with ` `  to remove these characters in a text editor and then wrap the lines manually before copying and pasting the certificate's value into the input box.  The format should look similar to the image below before pasting into the input box.
    ![image alt text](./media/private-key-format.png)
    
    * iap.client_id: set this value to the Client ID of the the Identity Aware Proxy Client Application. This value will be found in the `APIs & Services > Credentials` of your GCP project under the `OAuth 2.0 client IDs` section in the `Client ID`  column.

Your key value map should look similar to the following image:
    ![image alt text](./media/key-value-map.png)

### Create the Cache for the Shared Flow
The GCP authentication shared flow leverages the cache to avoid authenticating with GCP each time a call is made to the IAP protected endpoint. We will need to create a cache that the shared flow leverages for this example.

1. Navigate to `Admin > Environments > Caches` in the left menu.
2. Click the `+Cache` button in the top right corner of the screen.
3. Enter the following parameters:
    * Name: `GCP IAM Cache`
    * Expires: Set the value to **30** minutes.
4. Click `Add Cache`.

# Test the integration
To test that the integration is working as expected, you will need to create a product which leverages the proxy we imported previously and register an application that consumes the product to get an API key.

### Create an API Product
1. Navigate to `Publich > API Products` in the left menu.
2. Click the `+API Product` button in the top right corner of the screen.
3. Enter the following parameters:
    * Name: `SpringBoot Product`
    * Display name: `SpringBoot Product`
    * Access: `Public`
4. Under the `API Resources` > `API Proxies` section click `add a proxy`.
5. Select the checkbox for the `SpringBoot` proxy we created earlier and click `Add`.

You should now have a form that looks like the following image:
    ![image alt text](./media/register-application.png)

6. Click on `Save` in the top right corner.

### Create an Application
1. Navigate to `Publish > Apps` in the left menu.
2. Click the `+App` button in the top right corner of the screen.
3. Enter the following parameters:
    * Name: `SpringBoot App`
    * Display name: `SpringBoot App`
    * Select the `Developer` radio button and choose a developer that is registered with Apigee.
4. Click `Add product` then select the `SpringBoot Product` checkbox and click `Add`.

You should now have a form that looks like the following image:
    ![image alt text](./media/add-spring-boot-product.png)
    
5. Click on `Create` in the top right corner.

Once created, your application is assigned an API key and secret. You can find these on the resulting screen under the `Credentials` section. 
6. Click `Show` next to the `Key` and copy the value to use at a later time.

### Apigee Community
If you have any questions/comments please visit https://community.apigee.com/index.html

This is not an official Google or Apigee product. This repository is used for educational purposes only.
