# 60499_Project

## Project Goals
The main goal of the project is to develop our programming skills and learn new technologies. Using our newly acquired skills we developed software that processes images and “tags” them. The images and tags are then stored in a database where the information is displayed to the end user for interpretation. This paper will explain the technologies chosen, how they work and why they were chosen. By the end you should understand the overall architecture of the project and how to make additions to it yourself.

## Overall Architecture
The high-level architecture is very simple for this project, see figure 1.1. 

### Figure 1.1
![alt text](Figure-Images/Figure1-1.jpg "The top portion represents the front-end portion of the service. The user uses a web browser to make requests to a file server to the images that belong to his account. The file server then communicates with a Microsoft SQL Cloud Database, set up in Azure to retrieve user and image information.")

The bottom portion of figure 1.1 shows the user setting up a device, this can be any device that’s connected to a camera and has access to the internet. The device takes a picture and sends that picture using a REST API request to our Web Service hosted in Microsoft’s Azure Cloud Service. After processing the image, it should be added to the database previously mentioned along with it’s tags.

## Images & Tags
After an image has been processed two objects will be created. The first being the same image created however, the image will have boxes around identified objects within an image, see figure 2.1.  Each object will have a corresponding colour. The second object after processing is the tags. To accompany the image the Web App will also create a JSON file which will provide more information of the “boxes” from the new image, see figure 2.2. For each object identified a JSON object called “tags” will be created with the following properties. “bbox” represents the four points of the “box” that surround an identified object. “label” provides the user with what object our image processing thinks it has identified. “Score” gives a value from 0 to 1 which represents the likelihood that the label is correct. 1 being the most likely and 0 being unlikely. Generally, the algorithm won’t identify objects that are far below 0.5.

### Figure 2.1

### Figure 2.2

## Web Application Service
To start, we will discuss the Web App hosted on Microsoft’s Azure. If you’re not familiar with Microsoft’s cloud based platform a good starting point is the following link https://docs.microsoft.com/en-us/azure/architecture/cloud-adoption/getting-started/what-isazure.
One of the reasons for using Azure over Amazon’s AWS is that Microsoft has a verbose library of deep learning algorithms which allows us to easily train our programs to recognize objects then process images quickly. The library is called Microsoft Cognitive Toolkit or CNTK (https://www.microsoft.com/en-us/cognitive-toolkit/). There’re several different algorithms provided by the library however, we will be using Faster-RCNN. If you would like to run FasterRCNN on your PC you can do so by following this guide (https://docs.microsoft.com/enus/cognitive-toolkit/Object-Detection-using-Faster-R-CNN). You will need Python 3.5, PIP and Anaconda as your Python Environment. This is a good way to see how the library itself works before we get into implementing it in a Web Application. By using Faster-RCNN we greatly improve the speed at which we can identify objects which is going to allow us to make our software more scalable. If you would like to know more about the algorithm the published paper can be found here (https://arxiv.org/pdf/1506.01497.pdf)
 
  
### Steps to deploy Web Application
1.    Now that you know how CNTK and Faster-RCNN work now we can get into the Web
Application. To start you’ll need to create an Azure subscription at
(https://azure.microsoft.com/en-us/free/). If you’re a student, then you’ll get $200 worth of credits which will be more than enough to pay for the base service.  First you should download the content from the repository () and unzip to some folder on your computer.
2.    Step 2 requires you to set up Python, it’s environment and the dependencies by CNTK on your local Windows machine. Luckily Microsoft provides us with the following tutorial
(https://docs.microsoft.com/en-us/cognitive-toolkit/setup-windows-binary-script)
3.    Next open Anaconda and run the following command pip install azure-cli
Azure CLI is Microsoft’s command-line tool for managing Azure resources. We will be using it to deploy and manage our web application
az
 login
.


4.    Now run the following command You will be prompted for your Azure account credentials, however, keep in mind that sometimes it takes a few minutes for the prompt to appear.
5.    Now we’re going to set up variable names in our environment as they’ll be used throughout the next couple of steps. Although not required it is highly recommended as this will avoid errors and allow you to copy and paste the commands with no alterations. Run the following commands:
a.    set uname=[username]
b.    set pass=[password]
c.     set appn = [web app name]
d.    set resgname = [resource group name]
Replace all instances of [] with whatever you wish. I recommend making your resource group name [web app name]_resource_group. I.e. If
appn=60499Project then resgname=60499Project_Resource_Group
az
webapp
 deployment user set –user-name %
uname
% --password %pass%
6.    Run  this command will setup the credentials for your web application
Run
az
 group create –location
eastus
 –name
7.    Next lets set up a resource group.
%
rgname
%
 For more information on resource groups please follow this link
8.    Now create an Azure App Service Plan and an Azure Web App. For more information about these services please see here and here. Run the following commands:
a.    Az appservice plan create –name %appn% --resource-group %rgname% --sku
S1
b.    Az webapp create –name %appn% --resource-group %rgname% --plan %apppn%
9.    By default, web apps only support Python 2.7 & 3.4 but we require 3.5. Therefore, we need to use an extension in our web application environment. To do so go to the Azure Portal at http://www.portal.azure.com and log in using the credentials during step 1. In the left side bar choose App Services and select the web app that you’ve created this will be the main page to configure and monitor your web app. You’ll see a new column with the first entry being “Overview”. In the search bar above it type the following “extension” and select the option “Extensions”. There should be no extensions installed at this time. Choose Add, then under choose extension select “Python 3.5.4 x64”. Accept terms and conditions then you OK. You should get a message that it’s being installed and may take a few minutes.
10.  Now in your Python Environment run the following command az webapp deployment source config-local-git –name %appn% --resource-group %rgname% --query url –output tsv
This command will output the URL of your web application. It should look something like this “https://xxx@yyyyyyyy.scm.azurewebsites.net/zzzzzz.git” Make sure to copy this website down as it’ll be used in a later step.
11.  Run the following commands
Git
init
 
Git remote add azure
https://xxx@yyyyyyyy.scm.azurewebsites.net/zzzzzz.git








Git add -A
Git commit -m “
init
”
Git push azure master
There’s a script in the repo that will install all the required dependencies from the requirements.txt file.
12.  You’re all done.  In the Azure Portal restart your application. Wait 5 minutes and you should see the following web page.

## How does the Web App work?
The web application is written in Python and uses the microframework Flask. We chose Flask as it’s less bloated than Django and therefore can be picked up much more quickly. Since we didn’t want to spend a lot of time learning a framework it was the obvious choice. To look at the files you can either look at the repository you download, or you can view the files from the Web App itself. In the Azure Portal and the App Service you should be able to find an option called “Advanced Tools”, if not use the search bar. Select “Go”, this should bring up a new tab which gives some details about the environment. Select the dropdown “Debug Console” then CMD. This will pull up the command prompt where your web app is being held. Feel free to use the command prompt or the file navigator on top to go through the files. Under the directory D:\home\site\wwwroot\ you will file all the files from the repository you downloaded. The most important files are “app.py”, “config.py”,”evaluate.py” and “web.config”.
 
 
 
 
 
### Config.py
This file is very important as it sets the variable names and values that will be used throughout our program. These are variables the will set paths of certain files, file names, etc. Be careful you alter variables from this file as can be used throughout the execution of the program.
 
 
### App.py
This is the file that gets called on startup. You can see with the “@app.route…” calls we specify what actions to take with each URL. Depending on the URL either an image or JSON will be returned. If you wish to add another route, simply use the routes as a template.
***Double check to make sure that that the “os.environ[‘PATH’]” variable is set correctly. Python 2.7 is installed automatically and therefore we need to add the path of the Python version that we added as an extension. The path should be “D:\home\python354x64;”*****
 
### Evaluate.py
This is the main driving force behind the image classifier. It uses several functions and several variables from config.py to perform its task. It also uses the helper functions from cntk_helpers.py, plot_helpers.py and scripts from the utils folder. Keep in mind that most are copied from the official CNTK repository on Github.
 
### Webconfig.py
Standard web config file. Just make sure that the paths are set correctly.
 
 
## Q&A:
Q: What is a CNTK Model?
A: It’s the file that’s used to train our deep learning algorithm (Faster R-CNN).
 
Q: What can the current model detect?
A: The current model can detect the following: lamp, toilet, towel, sink, bathtub, tap, bed, pillow and faucet
 
Q: How can I train my own model?
A: Follow the instructions on this link. Note that you may need to scroll down a little.
 
Q: Can I use somebody else’s model?
A: There’s no reason why you can’t as long as it’s trained for FASTER R-CNN. Here’s an example of a model you can use. Once you have a model add it to the CNTKMODELS folder. If you wish to only use that model remove the current model and add yours using the exact same name. You can have both files in the folder however you’ll need to make an entry in config.py with the path and name and make configuration changes to all the files mentioned in this document.

Front End
Technologies
PHP
JavaScript
HTML 
CSS

Flow 

The user goes to the homepage 
From there, they can either login with there credentials or register for an account
If register
User fills out registration form
Data added into the Database
Goes to login page
If login 
Validate credentials, display error messages if needed 
Go to portal
In the portal, the user can view the images, details regarding the image and change account information 
Features
User registration/login 
View images captured
View details of the image (coordinates of where it was taken, item name, match percent) 
Change password 

Code Explained High Level + Screenshots
index
Home page for the app
Shows the project details (overview, technology stack) 
Login/Registration buttons


Register
user fills out form, checks for form validation 
checks database if username exists already, show the error
if successful, show success message and redirect to login






Login
two cases
user registers, redirected to the login with the message 
normal login page from clicking login in the navigation bar or index page
invalid credentials -> JavaScript popup telling the user 
Portal
Includes new navigation bar for a logged in user 
Welcome message 
view details -> shows image information (coordinates, item name, score)
view images -> shows images 



View Details


View Images




My Account
shows the user there account information
allows the user to change their password

account information of the user

change password


successfully change password
