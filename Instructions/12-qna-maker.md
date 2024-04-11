# Exercise 01: Create a Question Answering Solution

## Lab scenario

One of the most common conversational scenarios is providing support through a knowledge base of frequently asked questions (FAQs). Many organizations publish FAQs as documents or web pages, which works well for a small set of question and answer pairs, but large documents can be difficult and time-consuming to search.

The **Language** service includes a *question answering* capability that enables you to create a knowledge base of question and answer pairs that can be queried using natural language input, and is most commonly used as a resource that a bot can use to look up answers to questions submitted by users.

> **Note**: The question answering capability in the Language service is a newer version of the QnA Maker service - which is still available as a separate service.

## Objectives

In this lab, you will complete the following tasks:

+ Task 1: Open the cloned folder in Visual Studio Code
+ Task 2: Create a Language resource
+ Task 3: Create a question answering project
+ Task 4: Add a sources to the knowledge base
+ Task 5: Edit the knowledge base
+ Task 6: Train and test the knowledge base
+ Task 7: Deploy and test the knowledge base
+ Task 8: Create a bot for the knowledge base

## Estimated timing: 60 minutes

## Architecture diagram

![](./images/a(11).JPG)

## Task 1: Open the cloned folder in Visual Studio Code

1.  Start Visual Studio Code (the program icon is pinned to the bottom taskbar).

     ![Visual Studio Code Icon](./images/vscode.png)

2.  Open a file, From the top-left options, Click on **file->Open Folder** and navigate to **C:\AllFiles\AI-102-AIEngineer-prod**.

    **Note:** You may be prompted to complete a 2-minute survey. Go ahead and select **No, thanks**. You may need to do this more than once.

## Task 2: Create a Language resource

To create and host a knowledge base for question answering, you need a **Language service** resource in your Azure subscription.

1. Open the Azure portal.

1. Select the **&#65291;Create a resource** button, search for *Language*, and create a **Language service** resource.

1. Click **Select** on the **Custom question answering** block. Then click **Continue to create your resource**. You will need to enter the following settings:
    
    - **Subscription**: *Your Azure subscription*
    - **Resource group**: **Ai-102-<inject key="DeploymentID" enableCopy="false" /></inject>**
    - **Region**: **<inject key="Region" enableCopy="false"/>**
    - **Name**: **AI-Language-<inject key="DeploymentID" enableCopy="false"/>**
    - **Pricing tier**: Standard S
    - **Azure Search location**: **<inject key="Region" enableCopy="false"/>**.
    - **Azure Search pricing tier**: Free (F) (*If this tier is not available, select Basic (B)*)
    - **By checking this box I acknowledge that I have read and understood all the terms below**: select the checkbox.
    
1. Select **Review + Create** to navigate to the **Review + Create** tab, and then select **Create**.

1. Wait for deployment to complete, and then view the deployment details.

## Task 3: Create a question answering project

To create a knowledge base for question answering in your Language resource, you can use the Language Studio portal to create a question answering project. In this case, you'll create a knowledge base containing questions and answers about [Microsoft Learn](https://docs.microsoft.com/learn).

1. In a new browser tab, go to the *Language Studio* portal at `https://language.azure.com` and sign in using the Microsoft account associated with your Azure subscription.

2. If prompted to choose a Language resource else click on **Sign in** and  select the following settings:
    - **Azure Directory**: The Azure directory containing your subscription.
    - **Azure subscription**: Your Azure subscription.
    - **Resource type** : Language.
    - **Language resource**: **AI-Language-<inject key="DeploymentID" enableCopy="false"/>**.
    -  Select **Done**.
3. If you are <u>not</u> prompted to choose a language resource, it may be because you have multiple Language resources in your subscription; in which case:
    1. On the bar at the top if the page, click the **Settings (&#9881;)** button.
    2. On the **Settings** page, view the **Resources** tab.
    3. At the top of the page, click **Language Studio** to return to the Language Studio home page.

4. At the top of the portal, in the **Create new** menu, select **Custom question answering**.

5. In the ***Create a project*** wizard, on the **Choose language setting** page, select the option to set the language for all projects in this resource, and select **English** as the language. Then click **Next**.

6. On the **Enter basic information** page, enter the following details and then click **Next**:
    - **Name** LearnFAQ
    - **Description**: FAQ for Microsoft Learn
    - **Default answer when no answer is returned**: Sorry, I don't understand the question

7. On the **Review and finish** page, click **Create project**.

## Task 4: Add a sources to the knowledge base

You can create a knowledge base from scratch, but it's common to start by importing questions and answers from an existing FAQ page or document. In this case, you'll import data from an existing FAQ web page for Microsoft learn, and you'll also import some pre-defined "chit chat" questions and answers to support common conversational exchanges.

1. On the **Manage sources** page for your question answering project, in the **&#9547; Add source** list, select **URLs**. Then in the **Add URLs** dialog box, select **&#9547; Add url** and set the following name and URL  before you select **Add all** to add it to the knowledge base:
    - **Name**: `Learn FAQ Page`
    - **URL**: `https://docs.microsoft.com/en-us/learn/support/faq`
1. On the **Manage sources** page for your question answering project, in the **&#9547; Add source** list, select **Chitchat**. The in the **Add chit chat** dialog box, select **Friendly** and select **Add chit chat**.

## Task 5: Edit the knowledge base

Your knowledge base has been populated with question and answer pairs from the Microsoft Learn FAQ, supplemented with a set of conversational *chit-chat* question  and answer pairs. You can extend the knowledge base by adding additional question and answer pairs.

1. In your **LearnFAQ** project in Language Studio, select the **Edit knowledge base** page to see the existing question and answer pairs (if some tips are displayed, read them and choose **Got it** to dismiss them, or select **Skip all**)
1. In the knowledge base, on the **Question answer pairs** tab, select **&#65291;**, and create a new question answer pair with the following settings:
    - **Source**: `https://docs.microsoft.com/en-us/learn/support/faq`
    - **Question**: `What is Microsoft certification?`
    - **Answer**: `The Microsoft Certified Professional program enables you to validate and prove your skills with Microsoft technologies.`
1. Select **Done**.
1. In the page for the **What is Microsoft certification?** question that is created, expand **Alternate questions**. Then add the alternate question `How can I demonstrate my Microsoft technology skills?`.

    >**Note:** In some cases, it makes sense to enable the user to follow up on an answer by creating a *multi-turn* conversation that enables the user to iteratively refine the question to get to the answer they need.

1. Under the answer you entered for the certification question, expand **Follow-up prompts** and add  the following follow-up prompt:
    - **Text displayed in the prompt to the user**: `Learn more about certification`.
    - Select the **Create link to new pair** tab, and enter this text: `You can learn more about certification on the [Microsoft certification page](https://docs.microsoft.com/learn/certifications/).`
    - Select **Show in contextual flow only**. This option ensures that the answer is only ever returned in the context of a follow-up question from the original certification question.
1. Select **Add prompt**.

## Task 6: Train and test the knowledge base

Now that you have a knowledge base, you can test it in Language Studio.

1. Save the changes to your knowledge base by selecting the **Save** button under the **Question answer pairs** tab on the left.
1. After the changes have been saved, select the **Test** button to open the test pane.
1. In the test pane, at the top, deselect **Include short answer response** (if not already unselected). Then at the bottom enter the message `Hello`. A suitable response should be returned.
1. In the test pane, at the bottom enter the message `What is Microsoft Learn?`. An appropriate response from the FAQ should be returned.
1. Enter the message `Thanks!` An appropriate chit-chat response should be returned.
1. Enter the message `Tell me about Microsoft certification`. The answer you created should be returned along with a follow-up prompt link.
1. Select the **Learn more about certification** follow-up link. The follow-up answer with a link to the certification page should be returned.
1. When you're done testing the knowledge base, close the test pane.

## Task 7: Deploy and test the knowledge base

The knowledge base provides a back-end service that client applications can use to answer questions. Now you are ready to publish your knowledge base and access its REST interface from a client.

1. In the **LearnFAQ** project in Language Studio, select the **Deploy knowledge base** page.

2. At the top of the page, click **Deploy**. Then click **Deploy** to confirm you want to deploy the knowledge base.

3. When deployment is complete, click **Get prediction URL** to view the REST endpoint for your knowledge base, and copy it to the clipboard (but don't close the dialog box yet).

4. In Visual Studio Code, in the **12-qna** folder, open **ask-question.cmd**. This script uses *Curl* to call the REST interface of a question answering endpoint.

5. In the script, replace *YOUR_PREDICTION_ENDPOINT* with the prediction endpoint you copied (ensuring it is enclosed in the quotation marks).

6. Return to the browser and in the **Get prediction URL** dialog box, note that the sample request includes a value for the **Ocp-Apim-Subscription-Key** parameter, which looks similar to *ab12c345de678fg9hijk01lmno2pqrs34*. This is the authorization key for your resource. Copy it to the clipboard, and then click **Close** to close the dialog box.

7. Return to Visual Studio Code, and in the **ask-question.cmd** script, replace *YOUR_KEY* with the key you copied (ensuring it is enclosed in the quotation marks).

8. Note that the Curl command in the script submits a **question** parameter with the value **What is a Learning Path?**.

9. Verify that the entire script looks similar to the following code, then save the file.

    ```
    @echo off
    SETLOCAL ENABLEDELAYEDEXPANSION

    rem Set variables
    set prediction_url="https://some-name.cognitiveservices.azure.com/language/......."
    set key="ab12c345de678fg9hijk01lmno2pqrs34"

    curl -X POST !prediction_url! -H "Ocp-Apim-Subscription-Key: !key!" -H "Content-Type: application/json" -d "{'question': 'What is a learning Path?' }"
    ```

10. In Visual Studio Code in the Explorer pane, right-click the **ask-question.cmd** script and select **Open in Integrated Terminal**.

11. In the terminal pane, enter the command `.\ask-question.cmd` to run the script and view the JSON response that is returned by the service, which should contain an appropriate answer to the question *What is a learning path?*.

## Task 8: Create a bot for the knowledge base

Most commonly, the client applications used to retrieve answers from a knowledge base are bots.

1. Return to Language Studio in the browser, and in the **Deploy knowledge base** page, select **Create Bot**. This opens the Azure portal in a new browser tab so you can create a bot in your Azure subscription (if prompted, sign in).
1. In the Azure portal, create a bot with the following settings (most of these will be pre-populated for you):

    *If some values are missing, refresh your browser.*  

    - **Bot handle**: *leave default*
    - **Subscription**: *Your Azure subscription*
    - **Resource group**: **Resource group**: **Ai-102-<inject key="DeploymentID" enableCopy="false" /></inject>**
    - **Pricing tier**: Standard.
    - **Creation type**: Create new User-assigned managed identity.

1. Select **Next**, then set the following if not already populated automatically:

    - **App name**: *Same as the **Bot handle** with a unique ID and *.azurewebsites.net* appended automatically.*
    - **SDK language**: *Choose either C# or Node.js*
    - **Creation type**: *This may be set automatically to a suitable plan if one exists. If not, select **Create a new app service plan***.
    - **Language Resource Key**: *They key you copied earlier*.
1. Select **Review + create**. Then select **Create**.

1. Wait for your bot to be created. Then select **Go to resource group** (or alternatively, on the home page, select **Resource groups**).
1. To open the  bot, select the Azure Bot resource in the resources list.
1. In the overview pane for your bot, select the **Test in Web Chat** page, and wait until the bot displays the message **Hello and welcome!** (it may take a few seconds to initialize).
1. Use the test chat interface to ensure your bot answers questions from your knowledge base as expected. For example, try submitting `What is Microsoft certification?`.

### Review
In this lab, you have completed:

+ Opened the cloned folder in Visual Studio Code
+ Created a Language resource
+ Created a question answering project
+ Added a sources to the knowledge base
+ Edited the knowledge base
+ Trained and test the knowledge base
+ Deployed and test the knowledge base
+ Created a bot for the knowledge base

## You have successfully completed the lab.


