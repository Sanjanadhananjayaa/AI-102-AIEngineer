# Exercise 1: Analyze Images with Computer Vision

## Lab scenario

Azure AI Vision is an artificial intelligence capability that enables software systems to interpret visual input by analyzing images. In Microsoft Azure, the **Vision** Azure AI service provides pre-built models for common computer vision tasks, including analysis of images to suggest captions and tags, detection of common objects, landmarks, celebrities, brands, and the presence of adult content. You can also use the Azure AI Vision service to analyze image color and formats, and to generate "smart-cropped" thumbnail images.

## Objectives

In this lab, you will complete the following tasks:

 + Task 1: Open the cloned folder in Visual Studio Code
 + Task 2: Provision a Cognitive Services resource
 + Task 3: Prepare to use the Azure AI Vision SDK
 + Task 4: View the images you will analyze
 + Task 5: Analyze an image to suggest a caption
 + Task 6: Get suggested tags for an image
 + Task 7: Get image categories
 + Task 8: Get brands in an image
 + Task 9: Detect and locate objects in an image
 + Task 10: Get moderation ratings for an image
 + Task 11: Generate a thumbnail image

## Task 1: Open the cloned folder in Visual Studio Code

1.  Start Visual Studio Code (the program icon is pinned to the Desktop).

     ![Visual Studio Code Icon](./images/vscode.png) 

2.  Open a file, From the top-left options, Click on **File->Open Folder** and navigate to **C:\LabFiles** choose **AI-102-AIEngineer-master** folder and select **select folder**

      **Note:** You may be prompted to complete a 2-minute survey. Go ahead and select **No, thanks**. You may need to do this more than once.
  
      **Note:** When **Do you trust the authors of the filesin tjis folder?** prompted select **Yes, I trust the authors**.

4.  Wait while additional files are installed to support the C# code projects in the repo.

## Task 2: Provision a Cognitive Services resource

If you don't already have one in your subscription, you'll need to provision a **Azure AI Services** resource.

1. Open the Azure portal at `https://portal.azure.com`, and sign in using the Microsoft account associated with your Azure subscription. Here, enter your credentials:

   - **Email/Username:** <inject key="AzureAdUserEmail"></inject>

   - **Password:** <inject key="AzureAdUserPassword"></inject>

2. Select the **&#65291;Create a resource** button, search for **Azure AI services**, select **Azure AI Services**, and select create to create an Azure AI services multi-service account resource with the following setting

    - **Subscription**: *Your Azure subscription*
    - **Resource group**: *Ai-102-<inject key="DeploymentID" enableCopy="false" /></inject>*.
    - **Region**: **<inject key="Region" enableCopy="false"/>**.
    - **Name**: **AIservice-<inject key="DeploymentID" enableCopy="false" /></inject>**.
    - **Pricing tier**: Standard S0
    - **By checking this box I acknowledge that I have read and understood all the terms below**: select the checkbox.

3. Select **Review + create** and **Create**.
4. Wait for the deployment to complete. Once the deployment is successful, click on **Go to resources** to view the deployment details.
5. In the left navigation pane, under the **Resource Management** section, choose **Keys and Endpoint**. Make sure to note down the Keys and Endpoint values in notepad. You'll require the endpoint and one of the keys from this page for the subsequent procedure.

     > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
     > - Navigate to the Lab Validation Page, from the upper right corner in the lab guide section.
     > - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
     > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
     > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out. 


## Task 3: Prepare to use the Azure AI Vision SDK

In this exercise, you'll complete a partially implemented client application that uses the Azure AI Vision SDK to analyze images.

> **Note**: You can choose to use the SDK for **C#**. In the steps below, perform the actions appropriate for C# Language.

1. In Visual Studio Code, in the **Explorer** pane, browse to the **15-computer-vision** folder and expand the **C-Sharp**  folder.
2. Right-click the **image-analysis** folder and open an integrated terminal. Then install the Azure AI Vision SDK package by running the appropriate command for your language preference:

     **C#**
     
     ```
     dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
     ```

3. View the contents of the **image-analysis** folder, and note that it contains a file for configuration settings:
    - **C#**: appsettings.json

    Open the configuration file and update the configuration values it contains to reflect the **endpoint** and an authentication **key** for your Azure AI services resource. Save your changes.
4. Note that the **image-analysis** folder contains a code file for the client application:

    - **C#**: Program.cs

    Open the code file and at the top, under the existing namespace references, find the comment **Import namespaces**. Then, under this comment, add the following language-specific code to import the namespaces you will need to use the Azure AI Vision SDK:

     **C#**
     
     ```C#
     // import namespaces
     using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
     using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
     ```

    
## Task 4: View the images you will analyze

In this exercise, you will use the Azure AI Vision service to analyze multiple images.

1. In Visual Studio Code, expand the **image-analysis** folder and the **images** folder it contains.
2. Select each of the image files in turn to view then in Visual Studio Code.

## Task 5: Analyze an image to suggest a caption

Now you're ready to use the SDK to call the Vision service and analyze an image.

1. In the code file for your client application (**Program.cs**), in the **Main** function, note that the code to load the configuration settings has been provided. Then find the comment **Authenticate Azure AI Vision client**. Then, under this comment, add the following language-specific code to create and authenticate a Azure AI Vision client object:

     **C#**
     
     ```C#
     // Authenticate Azure AI Vision client
     ApiKeyServiceClientCredentials credentials = new ApiKeyServiceClientCredentials(cogSvcKey);
     cvClient = new ComputerVisionClient(credentials)
     {
         Endpoint = cogSvcEndpoint
     };
     ```

2. In the **Main** function, under the code you just added, note that the code specifies the path to an image file and then passes the image path to two other functions (**AnalyzeImage** and **GetThumbnail**). These functions are not yet fully implemented.

3. In the **AnalyzeImage** function, under the comment **Specify features to be retrieved**, add the following code:

     **C#**
     
     ```C#
     // Specify features to be retrieved
     List<VisualFeatureTypes?> features = new List<VisualFeatureTypes?>()
     {
         VisualFeatureTypes.Description,
         VisualFeatureTypes.Tags,
         VisualFeatureTypes.Categories,
         VisualFeatureTypes.Brands,
         VisualFeatureTypes.Objects,
         VisualFeatureTypes.Adult
     };
     ```

4. In the **AnalyzeImage** function, under the comment **Get image analysis**, add the following code (including the comments indicating where you will add more code later.):

     **C#**
     
     ```C
     // Get image analysis
     using (var imageData = File.OpenRead(imageFile))
     {    
         var analysis = await cvClient.AnalyzeImageInStreamAsync(imageData, features);
     
         // get image captions
         foreach (var caption in analysis.Description.Captions)
         {
             Console.WriteLine($"Description: {caption.Text} (confidence: {caption.Confidence.ToString("P")})");
         }
     
         // Get image tags
     
     
         // Get image categories
     
     
         // Get brands in the image
     
     
         // Get objects in the image
     
     
         // Get moderation ratings
         
     
     }            
     ```

    
5. Save your changes and return to the integrated terminal for the **image-analysis** folder, and enter the following command to run the program with the argument **images/street.jpg**:

     **C#**
     
     ```
     dotnet run images/street.jpg
     ```
    
6. Observe the output, which should include a suggested caption for the **street.jpg** image.
7. Run the program again, this time with the argument **images/building.jpg** to see the caption that gets generated for the **building.jpg** image.
8. Repeat the previous step to generate a caption for the **images/person.jpg** file.

## Task 6: Get suggested tags for an image

It can sometimes be useful to identify relevant *tags* that provide clues about the contents of an image.

1. In the **AnalyzeImage** function, under the comment **Get image tags**, add the following code:

     **C#**
     
     ```C
     // Get image tags
     if (analysis.Tags.Count > 0)
     {
         Console.WriteLine("Tags:");
         foreach (var tag in analysis.Tags)
         {
             Console.WriteLine($" -{tag.Name} (confidence: {tag.Confidence.ToString("P")})");
         }
     }
     ```

2. Save your changes and run the program once for each of the image files in the **images** folder, observing that in addition to the image caption, a list of suggested tags is displayed.

## Task 7: Get image categories

The Vision service can suggest *categories* for images, and within each category it can identify well-known landmarks.

1. In the **AnalyzeImage** function, under the comment **Get image categories**, add the following code:

     **C#**
     
     ```C
     // Get image categories
     List<LandmarksModel> landmarks = new List<LandmarksModel> {};
     Console.WriteLine("Categories:");
     foreach (var category in analysis.Categories)
     {
         // Print the category
         Console.WriteLine($" -{category.Name} (confidence: {category.Score.ToString("P")})");
     
         // Get landmarks in this category
         if (category.Detail?.Landmarks != null)
         {
             foreach (LandmarksModel landmark in category.Detail.Landmarks)
             {
                 if (!landmarks.Any(item => item.Name == landmark.Name))
                 {
                     landmarks.Add(landmark);
                 }
             }
         }
     }
     
     // If there were landmarks, list them
     if (landmarks.Count > 0)
     {
         Console.WriteLine("Landmarks:");
         foreach(LandmarksModel landmark in landmarks)
         {
             Console.WriteLine($" -{landmark.Name} (confidence: {landmark.Confidence.ToString("P")})");
         }
     }
     
     ```
    
2. Save your changes and run the program once for each of the image files in the **images** folder, observing that in addition to the image caption and tags, a list of suggested categories is displayed along with any recognized landmarks (in particular in the **building.jpg** image).

## Task 8: Get brands in an image

Some brands are visually recognizable from logo's, even when the name of the brand is not displayed. The Vision service is trained to identify thousands of well-known brands.

1. In the **AnalyzeImage** function, under the comment **Get brands in the image**, add the following code:

     **C#**
     
     ```C
     // Get brands in the image
     if (analysis.Brands.Count > 0)
     {
         Console.WriteLine("Brands:");
         foreach (var brand in analysis.Brands)
         {
             Console.WriteLine($" -{brand.Name} (confidence: {brand.Confidence.ToString("P")})");
         }
     }
     ```
    
2. Save your changes and run the program once for each of the image files in the **images** folder, observing any brands that are identified (specifically, in the **person.jpg** image).

## Task 9: Detect and locate objects in an image

*Object detection* is a specific form of computer vision in which individual objects within an image are identified and their location indicated by a bounding box..

1. In the **AnalyzeImage** function, under the comment **Get objects in the image**, add the following code:

     **C#**
     
     ```C
     // Get objects in the image
     if (analysis.Objects.Count > 0)
     {
         Console.WriteLine("Objects in image:");
     
         // Prepare image for drawing
         Image image = Image.FromFile(imageFile);
         Graphics graphics = Graphics.FromImage(image);
         Pen pen = new Pen(Color.Cyan, 3);
         Font font = new Font("Arial", 16);
         SolidBrush brush = new SolidBrush(Color.Black);
     
         foreach (var detectedObject in analysis.Objects)
         {
             // Print object name
             Console.WriteLine($" -{detectedObject.ObjectProperty} (confidence: {detectedObject.Confidence.ToString("P")})");
     
             // Draw object bounding box
             var r = detectedObject.Rectangle;
             Rectangle rect = new Rectangle(r.X, r.Y, r.W, r.H);
             graphics.DrawRectangle(pen, rect);
             graphics.DrawString(detectedObject.ObjectProperty,font,brush,r.X, r.Y);
     
         }
         // Save annotated image
         String output_file = "objects.jpg";
         image.Save(output_file);
         Console.WriteLine("  Results saved in " + output_file);   
     }
     ```
    
2. Save your changes and run the program once for each of the image files in the **images** folder, observing any objects that are detected. After each run, view the **objects.jpg** file that is generated in the same folder as your code file to see the annotated objects.

## Task 10: Get moderation ratings for an image

Some images may not be suitable for all audiences, and you may need to apply some moderation to identify images that are adult or violent in nature.

1. In the **AnalyzeImage** function, under the comment **Get moderation ratings**, add the following code:

     **C#**
     
     ```C
     // Get moderation ratings
     string ratings = $"Ratings:\n -Adult: {analysis.Adult.IsAdultContent}\n -Racy: {analysis.Adult.IsRacyContent}\n -Gore: {analysis.Adult.IsGoryContent}";
     Console.WriteLine(ratings);
     ```

2. Save your changes and run the program once for each of the image files in the **images** folder, observing the ratings for each image.

> **Note**: In the preceding tasks, you used a single method to analyze the image, and then incrementally added code to parse and display the results. The SDK also provides individual methods for suggesting captions, identifying tags, detecting objects, and so on - meaning that you can use the most appropriate method to return only the information you need, reducing the size of the data payload that needs to be returned. See the [.NET SDK documentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) for more details.

## Task 11: Generate a thumbnail image

In some cases, you may need to create a smaller version of an image named a *thumbnail*, cropping it to include the main visual subject within new image dimensions.

1. In your code file, find the **GetThumbnail** function; and under the comment **Generate a thumbnail**, add the following code:

     **C#**

     ```C
     // Generate a thumbnail
     using (var imageData = File.OpenRead(imageFile))
     {
         // Get thumbnail data
         var thumbnailStream = await cvClient.GenerateThumbnailInStreamAsync(100, 100,imageData, true);
     
         // Save thumbnail image
         string thumbnailFileName = "thumbnail.png";
         using (Stream thumbnailFile = File.Create(thumbnailFileName))
         {
             thumbnailStream.CopyTo(thumbnailFile);
         }
     
         Console.WriteLine($"Thumbnail saved in {thumbnailFileName}");
     }
     ```

    
2. Save your changes and run the program once for each of the image files in the **images** folder, (**images/building.jpg**, **images/person.jpg**, **images/street.jpg**).
  
     **C#**

     ```
     dotnet run images/street.jpg
     ```

  
3. opening the **thumbnail.jpg** file that is generated in the same folder as your code file.

     **C#**

     ```
     dotnet run thumbnail.jpg
     ```

> **Note**: If you face any issues after running the command like process cannot access the file because it is being used by another process please ignore and proceed with next lab.

## More information

In this exercise, you explored some of the image analysis and manipulation capabilities of the Azure AI Vision service. The service also includes capabilities for reading text, detecting faces, and other computer vision tasks.

For more information about using the **Azure AI Vision** service, see the [Azure AI Vision documentation](https://docs.microsoft.com/azure/cognitive-services/computer-vision/).

### Review
In this lab, you have completed:

 + Open the cloned folder in Visual Studio Code
 + Provision a Cognitive Services resource
 + Prepare to use the Azure AI Vision SDK
 + View the images you will analyze
 + Analyze an image to suggest a caption
 + Get suggested tags for an image
 + Get image categories
 + Get brands in an image
 + Detect and locate objects in an image
 + Get moderation ratings for an image
 + Generate a thumbnail image

## You have successfully completed the lab
