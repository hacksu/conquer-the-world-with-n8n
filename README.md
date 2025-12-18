# conquer-the-world-with-n8n

Currently a work in progress. N8n is a really powerful program for automating all sorts of tasks. We will cover how to automate a few cool things!

As a note, the cloud portion of n8n does cost money, but they have a 1 week free trial that we can utilize at the meeting, or I can set up a local n8n environment on a server or machine.

And actually, I want to try to have this lesson not be very advanced and something anyone can follow along on.

There is a very easy to set up free n8n 2 week long trial that we can use for this lesson: https://n8n.io/


## Demo ##
For the demo with n8n we will be building a custom daily newsletter, which showcases the weather for the next 5 days as well as a summary of some of the top tech related articules that have been written for the day.

The process first of all requiers signing up for a free 2 week trial with n8n and creating a workspace.

https://app.n8n.cloud/register

We will make our workflow from scratch, as this will give us the most flexibility in the beginning.

So once you are in your workflow, we will select a "Trigger Manually" node, which will allow for manual testing and making sure everything is working correctly.

Now, for the purposes of our news letter we want to have 2 different details piped in. Weather data and article data. We will start by obtaining weather data.

### Getting the weather data
For getting current accurate weather data, we will use the OpenWeatherMap Node set, specifically the "5 Day Forecast" Operation, as this is free for us to use with the OpenWeatherMap API.

Now, when we select this node, you will see we have a few different options of drop down menus. We firstly want to take a look at "Credential to connect with". This is when we will create our own custome credential to access the OpenWeatherMap API, which is a way to obtain current weather data.

We will need to go to this website: https://openweathermap.org/price
and scroll down until you see the "Free Access" table, and follow the process for "Free for everyone" to create an account and obtain your first API key.

Now, we will let this sit for a little bit as it takes some time to activate and be used in our workflow.

### Article Data
So now lets go work on obtaining the article data for our project.

This will first require us to create a "RSS Read" node. This node will utilize something called an RSS Feed

And according to Google AI summary, a RSS (Really Simple Syndication) feed is "a web feed that allows users to receive updates from websites in a standardized format. It helps users keep track of new content from multiple sites without having to visit each one individually." 

This is really useful for us as we want to get access to a bunch of up to date articles around technology!

So to find out lovely RSS feed, we will go to: https://rss.feedspot.com/technology_rss_feeds/
Which provides lists of a bunch of free technology feeds.

For our case we will use an RSS feed from a website called engadget that showcases articles that have a more general overview of technology: https://www.engadget.com/rss.xml

So we will copy this URL into our URL parameter field.

Now that we have two nodes, we can split them up into two streams:
<img width="554" height="489" alt="image" src="https://github.com/user-attachments/assets/811d40cb-9bff-42db-b941-4fb43aeee25f" />

To continue off our of RSS Reader, we can see that it returns us back 50 different articles. Let us take a deep dive and see what type of data is returned from this node.
<img width="946" height="98" alt="image" src="https://github.com/user-attachments/assets/d304e081-3644-4f0e-a206-0c569136a27b" />

In addition, we get returned back to us 50 articles, which is a lot to deal with. So let us limit that amount with a "Limit" node. This node limits the amount of items that we are working with. Lets limit the amount of items to 5.

Okay awesome, now we have a lot of different data fields that we do not need in our final newsletter, so lets choose only the ones we want to keep. We will use the "Edit fields" node and after running the previous nodes to get the data, we can drag and drop over the different fields to keep in our new data. In this case we should keep "title", "creator", "link", and "contentSnipper". 
<img width="551" height="857" alt="image" src="https://github.com/user-attachments/assets/3447468a-d547-4fc0-b74c-ec75a7476c5c" />


Perfect! So now we are at a happy spot with our current article data. Lets move back to our weather data.

We can fill out the weather data parameters, and successfully pull in weather data!
<img width="546" height="869" alt="image" src="https://github.com/user-attachments/assets/e00559b8-d59a-427b-a0e9-326a89b4cf21" />


Which after taking a look at the data we get, we can filter out some of the data to a set of information we are happy with utilizng the "Edit fields" node again. We can choose all the weather fields we would wish to have in our ending application, and then execute the node to make sure things are edited properly!
<img width="549" height="867" alt="image" src="https://github.com/user-attachments/assets/fd7cc7b9-bde5-4787-bf0e-0c6274604140" />


## Combining the data and creating our email
Now that we have grabbed and filtered out our data to be more focused for what we would like, we can finally put it together and build a newsletter!

To put all of our data together, we can use a "Merge" node with basic settings and then immedietly after we can use an aggregate node to combine everything together into one item field. Which make sure to select "All Item Data" as our aggregated set.

Now for the most important piece. We will incorporate a basic LLM into our workflow to take all of the data and piece it together into a newly formated email for our inbox.

In order to accomplish this we will utilize a "Basic LLM Chain" node. This will allow us to create a prompt as well as send over our collected data to an LLM for it to generate an email.

Now for this Basic LLM chain to work, we need to connect an LLM to this node. Which we will utilize Gemini for, Googles LLM. Gemini has a free API tier, which will allow us to make calls to the Gemini LLM for free!

Go here: https://aistudio.google.com/u/1/projects

Now, on this page, we need to quickly make a new project. We will name it n8nDailyNewsletter. And once this project is created we will go to the section labeled API Keys, and we will create a new API key for the n8n project we just created!

https://aistudio.google.com/u/3/api-keys

We will name the key n8nDailyNewsletterKey and then select your project.

Now that we have an API key created, we can copy the API key and lets go back to n8n.
<img width="507" height="338" alt="image" src="https://github.com/user-attachments/assets/2b93a84e-17e4-4492-b055-1b5c76808322" />

We will select the model area of this node and we will select "Google Gemini API Account" for our model. With pasting in our API key in the API key field.

And now we can set up our LLM!

This will require us to first select "Define below" from the "Source for Prompt" section. And now we can give our own prompt to the LLM.

This prompt should essentially say something like this:

```
You are a costume email designer who specializes in making visually appealing and colorful emails using html. I have collected the next 5 days worth of weather data, as well as news from 5 cool articles! I was wondering if you could make me an email that gives me an overview of the weather as well as a summary that showcases the title, author, important details and the link to view the actual article. When summarizing the articles, do not make up your own infomration, only use the information provided in the content. 

This email is used as my daily summary, and it will be sent to myself. You can use a light purple color scheme and please make the email look appealing in both desktop and mobile view as I will primarily be reading this from my phone.

Please only include the HTML, not any filler text before the html starts. Plus make the weather more consice, try to keep the weather data all together in one or two columns/boxes

{{ JSON.stringify($json.data, null, 2) }}

```


Once our LLM is all set up, the final piece is to set up our email client for sending emails to ourself every morning for our daily newsletter. So we will choose the "Gmail account node" and more specifically we want to send a message to select the "Send a message" node. Once you choose the node, we need to make a new credential to allow n8n to access your gmail. So go to make a credential and login in google.

Once login successful and the credential good to go, we can officially fill out the gmail email fields and send an email ourselves or whoever else we would like!!!!

We can input our email address, and then now the part of accessing the LLM email. We can see on the left side with the input, the LLM chat data. We can drag this over to the email field and we can give it a subject. 

## Run the workflow and get our email
Now this should be all that is necessary for our application! Lets make sure eveyrthing is connected and run the "Execute Workflow" button to see what heppens. 

After some time we check out email, and viala we recieved an email!!!!

## Lesson over
That is the jist of this n8n automation tool. You can do so much with it, so feel free to explore and mess around with more features. You can do so many amazing things, and I am excited to see what you all create.
